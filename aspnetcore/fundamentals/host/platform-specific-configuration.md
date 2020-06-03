---
<span data-ttu-id="77102-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="77102-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="77102-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="77102-102">'Blazor'</span></span>
- <span data-ttu-id="77102-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="77102-103">'Identity'</span></span>
- <span data-ttu-id="77102-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="77102-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="77102-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="77102-105">'Razor'</span></span>
- <span data-ttu-id="77102-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="77102-106">'SignalR' uid:</span></span> 

---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="77102-107">ASP.NET Core에서 호스팅 시작 어셈블리 사용</span><span class="sxs-lookup"><span data-stu-id="77102-107">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="77102-108">작성자: [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="77102-108">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="77102-109"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>(호스팅 시작) 구현에서는 외부 어셈블리에서 시작할 때 앱에 향상된 기능을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-109">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="77102-110">예를 들어 외부 라이브러리는 호스팅 시작 구현을 사용하여 앱에 추가 구성 공급자 또는 서비스를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-110">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="77102-111">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="77102-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="77102-112">HostingStartup 특성</span><span class="sxs-lookup"><span data-stu-id="77102-112">HostingStartup attribute</span></span>

<span data-ttu-id="77102-113">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성은 런타임에 활성화할 호스팅 시작 어셈블리의 현재 상태를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="77102-113">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="77102-114">항목 어셈블리 또는 `Startup` 클래스가 포함된 어셈블리에서는 `HostingStartup` 특성을 자동으로 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-114">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="77102-115">`HostingStartup` 특성을 검색할 어셈블리 목록은 [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)의 구성에서 런타임에 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-115">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="77102-116">검색에서 제외할 어셈블리 목록은 [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-116">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="77102-117">다음 예제에서는 호스팅 시작 어셈블리의 네임스페이스가 `StartupEnhancement`입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-117">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="77102-118">호스팅 시작 코드를 포함하는 클래스는 `StartupEnhancementHostingStartup`입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-118">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="77102-119">`HostingStartup` 특성은 일반적으로 호스팅 시작 어셈블리의 `IHostingStartup` 구현 클래스 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-119">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="77102-120">로드된 호스팅 시작 어셈블리 검색</span><span class="sxs-lookup"><span data-stu-id="77102-120">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="77102-121">로드된 호스팅 시작 어셈블리를 검색하려면 로깅을 사용하도록 설정하고 앱의 로그를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-121">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="77102-122">어셈블리를 로드할 때 발생하는 오류가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-122">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="77102-123">로드된 호스팅 시작 어셈블리는 디버그 수준에서 기록되고 모든 오류가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-123">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="77102-124">호스팅 시작 어셈블리의 자동 로드 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="77102-124">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="77102-125">호스팅 시작 어셈블리의 자동 로딩을 사용하지 않으려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-125">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="77102-126">모든 호스팅 시작 어셈블리가 로드되지 않도록 하려면 다음 중 하나를 `true` 또는 `1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-126">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="77102-127">호스팅 시작 방지 호스트 구성 설정:</span><span class="sxs-lookup"><span data-stu-id="77102-127">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="77102-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="77102-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="77102-129">특정 호스팅 시작 어셈블리가 로드되지 않도록 하려면 시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열로 다음 중 하나를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-129">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="77102-130">호스팅 시작 어셈블리 제외 호스트 구성 설정:</span><span class="sxs-lookup"><span data-stu-id="77102-130">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="77102-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="77102-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="77102-132">호스트 구성 설정과 환경 변수가 모두 설정되면 호스트 설정이 동작을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-132">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="77102-133">호스트 설정 또는 환경 변수를 사용하여 호스팅 시작 어셈블리를 사용하지 않도록 설정하면 어셈블리가 전역적으로 해제되고 앱의 여러 특징을 해제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-133">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="77102-134">프로젝트</span><span class="sxs-lookup"><span data-stu-id="77102-134">Project</span></span>

<span data-ttu-id="77102-135">다음 프로젝트 형식 중 하나를 사용하여 호스팅 시작을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77102-135">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="77102-136">클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="77102-136">Class library</span></span>](#class-library)
* [<span data-ttu-id="77102-137">진입점 없는 콘솔 앱</span><span class="sxs-lookup"><span data-stu-id="77102-137">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="77102-138">클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="77102-138">Class library</span></span>

<span data-ttu-id="77102-139">클래스 라이브러리에서 호스팅 시작 기능 향상을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-139">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="77102-140">라이브러리에는 `HostingStartup` 특성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-140">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="77102-141">[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)에는 Razor 페이지 앱, *HostingStartupApp* 및 클래스 라이브러리, *HostingStartupLibrary*가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-141">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="77102-142">클래스 라이브러리:</span><span class="sxs-lookup"><span data-stu-id="77102-142">The class library:</span></span>

* <span data-ttu-id="77102-143">`IHostingStartup`을 구현하는 호스트 시작 클래스(`ServiceKeyInjection`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-143">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="77102-144">`ServiceKeyInjection`은 메모리 내 구성 공급자([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*))를 사용하여 앱의 구성에 서비스 문자열 쌍을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-144">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="77102-145">호스팅 시작의 네임스페이스 및 클래스를 식별하는 `HostingStartup` 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-145">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="77102-146">`ServiceKeyInjection` 클래스의 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>를 사용하여 향상된 기능을 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-146">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="77102-147">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="77102-147">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="77102-148">앱의 인덱스 페이지는 클래스 라이브러리의 호스팅 시작 어셈블리에 의해 설정된 두 키에 대한 구성 값을 읽고 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-148">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="77102-149">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="77102-149">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="77102-150">[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)에는 별도의 호스팅 시작인 *HostingStartupPackage*를 제공하는 NuGet 패키지 프로젝트도 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="77102-151">패키지는 앞에서 설명한 클래스 라이브러리와 같은 특징이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-151">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="77102-152">패키지:</span><span class="sxs-lookup"><span data-stu-id="77102-152">The package:</span></span>

* <span data-ttu-id="77102-153">`IHostingStartup`을 구현하는 호스트 시작 클래스(`ServiceKeyInjection`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-153">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="77102-154">`ServiceKeyInjection`은 앱의 구성에 서비스 문자열 쌍을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-154">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="77102-155">`HostingStartup` 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-155">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="77102-156">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="77102-156">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="77102-157">앱의 인덱스 페이지는 패키지의 호스팅 시작 어셈블리에 의해 설정된 두 키에 대한 구성 값을 읽고 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-157">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="77102-158">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="77102-158">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="77102-159">진입점 없는 콘솔 앱</span><span class="sxs-lookup"><span data-stu-id="77102-159">Console app without an entry point</span></span>

<span data-ttu-id="77102-160">*이 방법은 .NET Framework가 아닌 .NET Core 앱에서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="77102-160">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="77102-161">활성화에 대한 컴파일 시간 참조가 필요하지 않은 동적 호스팅 시작 기능 향상은 `HostingStartup` 특성이 포함되는 진입점 없는 콘솔 앱에서 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-161">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="77102-162">콘솔 앱을 게시하면 런타임 저장소에서 사용할 수 있는 호스팅 시작 어셈블리가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-162">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="77102-163">다음과 같은 이유로 이 프로세스에서는 진입점 없는 콘솔 앱이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-163">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="77102-164">호스팅 시작 어셈블리에서 호스팅 시작을 사용하려면 종속성 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-164">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="77102-165">종속성 파일은 라이브러리가 아닌 앱을 게시하여 생성된 실행 가능한 앱 자산입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-165">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="77102-166">라이브러리를 [런타임 패키지 저장소](/dotnet/core/deploying/runtime-store)에 직접 추가할 수 없습니다. 이 저장소에는 공유 런타임을 대상으로 하는 실행 가능한 프로젝트가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-166">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="77102-167">동적 호스팅 시작 만들기:</span><span class="sxs-lookup"><span data-stu-id="77102-167">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="77102-168">호스팅 시작 어셈블리는 다음과 같은 진입점 없는 콘솔 앱에서 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="77102-168">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="77102-169">`IHostingStartup` 구현이 포함되는 클래스를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-169">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="77102-170">`IHostingStartup` 구현 클래스를 식별하는 [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-170">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="77102-171">콘솔 앱은 호스팅 시작의 종속성을 얻기 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-171">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="77102-172">콘솔 앱을 게시하면 사용되지 않는 종속성이 종속성 파일에서 트리밍됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-172">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="77102-173">종속성 파일은 호스팅 시작 어셈블리의 런타임 위치를 설정하도록 수정됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-173">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="77102-174">호스팅 시작 어셈블리 및 해당 종속성 파일은 런타임 패키지 저장소에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-174">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="77102-175">호스팅 시작 어셈블리 및 해당 종속성 파일을 검색하기 위해 한 쌍의 환경 변수에 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-175">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="77102-176">콘솔 앱은 [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-176">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="77102-177">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성은 <xref:Microsoft.AspNetCore.Hosting.IWebHost>를 빌드할 때 로드 및 실행을 위해 `IHostingStartup`의 구현으로 클래스를 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-177">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="77102-178">다음 예제에서 네임스페이스는 `StartupEnhancement`이고 클래스는 `StartupEnhancementHostingStartup`입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-178">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="77102-179">클래스는 `IHostingStartup`을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-179">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="77102-180">클래스의 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>를 사용하여 향상된 기능을 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-180">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="77102-181">호스팅 시작 어셈블리의 `IHostingStartup.Configure`는 사용자 코드로 `Startup.Configure` 전에 런타임에서 호출됩니다. 그러면 사용자 코드가 호스팅 시작 어셈블리에서 제공하는 모든 구성을 덮어쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-181">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="77102-182">`IHostingStartup` 프로젝트를 빌드할 때 종속성 파일( *.deps.json*)은 어셈블리의 `runtime` 위치를 *bin* 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-182">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="77102-183">파일의 일부만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-183">Only part of the file is shown.</span></span> <span data-ttu-id="77102-184">이 예제의 어셈블리 이름은 `StartupEnhancement`입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-184">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="77102-185">호스팅 시작으로 제공하는 구성</span><span class="sxs-lookup"><span data-stu-id="77102-185">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="77102-186">호스팅 시작의 구성을 우선할지, 앱의 구성을 우선할지에 따라 두 가지 방법으로 구성을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-186">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="77102-187">앱의 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 대리자가 실행된 후 구성을 로드하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>을 사용하여 앱에 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-187">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="77102-188">이 방법을 사용하면 호스팅 시작 구성이 앱의 구성보다 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-188">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="77102-189">앱의 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 대리자가 실행되기 전에 구성을 로드하려면 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 사용하여 앱에 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-189">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="77102-190">이 방법을 사용하면 호스팅 시작으로 제공된 값보다 앱의 구성 값이 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-190">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="77102-191">호스팅 시작 어셈블리 지정</span><span class="sxs-lookup"><span data-stu-id="77102-191">Specify the hosting startup assembly</span></span>

<span data-ttu-id="77102-192">클래스 라이브러리 또는 콘솔 앱 제공 호스팅 시작의 경우 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 호스팅 시작 어셈블리 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-192">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="77102-193">환경 변수는 세미콜론으로 구분된 어셈블리 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-193">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="77102-194">호스팅 시작 어셈블리만 `HostingStartup` 특성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-194">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="77102-195">샘플 앱(*HostingStartupApp*)의 경우 앞에서 설명한 호스팅 시작을 검색하기 위해 환경 변수가 다음 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-195">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="77102-196">호스팅 시작 어셈블리는 호스팅 시작 어셈블리 호스트 구성 설정을 사용하여 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-196">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="77102-197">여러 개의 호스팅 시작 어셈블이 있는 경우 어셈블리가 나열되는 순서대로 해당 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-197">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="77102-198">활성화</span><span class="sxs-lookup"><span data-stu-id="77102-198">Activation</span></span>

<span data-ttu-id="77102-199">호스팅 시작 활성화 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-199">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="77102-200">[런타임 저장소](#runtime-store): 활성화에는 활성화를 위한 컴파일 시간 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-200">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="77102-201">샘플 앱은 호스팅 시작 어셈블리 및 종속성 파일을 *배포* 폴더 안에 배치하여 다중 머신 환경에서 호스팅 시작의 배포를 용이하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-201">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="77102-202">*배포* 폴더에는 호스팅 시작을 사용하도록 배포 시스템에 환경 변수를 만들거나 수정하는 PowerShell 스크립트도 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-202">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="77102-203">활성화에 필요한 컴파일 시간 참조</span><span class="sxs-lookup"><span data-stu-id="77102-203">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="77102-204">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="77102-204">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="77102-205">프로젝트 bin 폴더</span><span class="sxs-lookup"><span data-stu-id="77102-205">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="77102-206">런타임 저장소</span><span class="sxs-lookup"><span data-stu-id="77102-206">Runtime store</span></span>

<span data-ttu-id="77102-207">호스팅 시작 구현은 [런타임 저장소](/dotnet/core/deploying/runtime-store)에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-207">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="77102-208">향상된 앱에서는 어셈블리에 대한 컴파일 시간 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-208">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="77102-209">호스팅 시작이 빌드된 후에는 매니페스트 프로젝트 파일과 [dotnet store](/dotnet/core/tools/dotnet-store) 명령을 사용하여 런타임 저장소가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-209">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="77102-210">샘플 앱(*RuntimeStore* 프로젝트)에서 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-210">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="77102-211">런타임에서 런타임 저장소를 검색하기 위해 런타임 저장소의 위치가 `DOTNET_SHARED_STORE` 환경 변수에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-211">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="77102-212">**호스팅 시작의 종속성 파일 수정 및 배치**</span><span class="sxs-lookup"><span data-stu-id="77102-212">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="77102-213">향상된 기능에 대한 패키지 참조 없이 향상된 기능을 활성화하려면 `additionalDeps`를 사용하여 런타임에 추가 종속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-213">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="77102-214">`additionalDeps`를 사용하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-214">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="77102-215">시작 시 앱의 고유한 *.deps.json* 파일과 병합하기 위해 추가 *.deps.json* 파일 집합을 제공하여 앱의 라이브러리 그래프를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-215">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="77102-216">호스팅 시작 어셈블리를 검색 가능하고 로드 가능하게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77102-216">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="77102-217">추가 종속성 파일을 생성하기 위해 권장되는 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-217">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="77102-218">이전 섹션에서 참조한 런타임 저장소 매니페스트 파일에서 `dotnet publish`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-218">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="77102-219">결과 *.deps.json* 파일의 `runtime` 섹션 및 라이브러리에서 매니페스트 참조를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-219">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="77102-220">예제 프로젝트에서 `store.manifest/1.0.0` 속성은 `targets` 및 `libraries` 섹션에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-220">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="77102-221">*.deps.json* 파일을 다음 위치에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-221">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="77102-222">`{ADDITIONAL DEPENDENCIES PATH}`: `DOTNET_ADDITIONAL_DEPS` 환경 변수에 추가되는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-222">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="77102-223">`{SHARED FRAMEWORK NAME}`: 이 추가 종속성 파일에 대한 공유 프레임워크가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-223">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="77102-224">`{SHARED FRAMEWORK VERSION}`: 최소 공유 프레임워크 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-224">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="77102-225">`{ENHANCEMENT ASSEMBLY NAME}`: 향상된 기능의 어셈블리 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-225">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="77102-226">샘플 앱(*RuntimeStore* 프로젝트)에서 추가 종속성 파일은 다음 위치에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-226">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="77102-227">런타임에서 런타임 저장소 위치를 검색하기 위해 추가 종속성 파일 위치가 `DOTNET_ADDITIONAL_DEPS` 환경 변수에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-227">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="77102-228">샘플 앱( *RuntimeStore* 프로젝트)에서 런타임 저장소 빌드 및 추가 종속성 파일 생성은 [PowerShell](/powershell/scripting/powershell-scripting) 스크립트를 사용하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-228">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="77102-229">다양한 운영 체제에 대한 환경 변수를 설정하는 방법의 예제는 [여러 환경 사용](xref:fundamentals/environments)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77102-229">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="77102-230">**배포**</span><span class="sxs-lookup"><span data-stu-id="77102-230">**Deployment**</span></span>

<span data-ttu-id="77102-231">다중 머신 환경에서 호스팅 시작의 배포를 용이하게 하기 위해 샘플 앱은 다음을 포함하는 게시된 출력에 *배포* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77102-231">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="77102-232">호스팅 시작 런타임 저장소.</span><span class="sxs-lookup"><span data-stu-id="77102-232">The hosting startup runtime store.</span></span>
* <span data-ttu-id="77102-233">호스팅 시작 종속성 파일.</span><span class="sxs-lookup"><span data-stu-id="77102-233">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="77102-234">호스팅 시작 활성화를 지원하기 위해 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` 및 `DOTNET_ADDITIONAL_DEPS` 를 만들거나 수정하는 PowerShell 스크립트입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-234">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="77102-235">배포 시스템의 관리 PowerShell 명령 프롬프트에서 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-235">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="77102-236">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="77102-236">NuGet package</span></span>

<span data-ttu-id="77102-237">NuGet 패키지에서 호스팅 시작 기능 향상을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-237">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="77102-238">패키지에 `HostingStartup` 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-238">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="77102-239">패키지에서 제공하는 호스팅 시작 형식은 다음 방법 중 하나를 통해 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-239">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="77102-240">향상된 앱의 프로젝트 파일은 앱의 프로젝트 파일(컴파일 시간 참조)에서 호스팅 시작을 위한 패키지 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77102-240">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="77102-241">이곳에서 컴파일 시간 참조를 사용하면 호스팅 시작 어셈블리 및 모든 종속성이 앱의 종속성 파일( *.deps.json*)에 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-241">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="77102-242">이 방식은 [nuget.org](https://www.nuget.org/)에 게시된 호스팅 시작 어셈블리 패키지에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-242">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="77102-243">호스팅 시작 종속성 파일은 [런타임 저장소](#runtime-store) 섹션에 설명된 대로 향상된 앱에서 사용할 수 있습니다(컴파일 시간 참조 없이).</span><span class="sxs-lookup"><span data-stu-id="77102-243">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="77102-244">NuGet 패키지 및 런타임 저장소에 대한 자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77102-244">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="77102-245">플랫폼 간 도구로 NuGet 패키지를 만드는 방법</span><span class="sxs-lookup"><span data-stu-id="77102-245">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="77102-246">패키지 게시</span><span class="sxs-lookup"><span data-stu-id="77102-246">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="77102-247">런타임 패키지 저장소</span><span class="sxs-lookup"><span data-stu-id="77102-247">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="77102-248">프로젝트 bin 폴더</span><span class="sxs-lookup"><span data-stu-id="77102-248">Project bin folder</span></span>

<span data-ttu-id="77102-249">호스팅 시작 향상 기능은 향상된 앱의 *bin* 배포 어셈블리에 의해 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-249">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="77102-250">어셈블리에서 제공하는 호스팅 시작 형식은 다음 방법 중 하나를 통해 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-250">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="77102-251">향상된 앱의 프로젝트 파일은 호스팅 시작에 대한 어셈블리 참조를 만듭니다(컴파일 시간 참조).</span><span class="sxs-lookup"><span data-stu-id="77102-251">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="77102-252">이곳에서 컴파일 시간 참조를 사용하면 호스팅 시작 어셈블리 및 모든 종속성이 앱의 종속성 파일( *.deps.json*)에 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-252">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="77102-253">이 방법은 배포 시나리오에서 호스팅 시작 어셈블리( *.dll* 파일)에 대한 컴파일 시간 참조를 수행하고 어셈블리를 다음 중 하나로 이동해야 할 때 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-253">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="77102-254">사용하는 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-254">The consuming project.</span></span>
  * <span data-ttu-id="77102-255">사용하는 프로젝트에서 액세스할 수 있는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-255">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="77102-256">호스팅 시작 종속성 파일은 [런타임 저장소](#runtime-store) 섹션에 설명된 대로 향상된 앱에서 사용할 수 있습니다(컴파일 시간 참조 없이).</span><span class="sxs-lookup"><span data-stu-id="77102-256">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="77102-257">.NET Framework를 대상으로 지정하는 경우 어셈블리는 기본 로드 컨텍스트에 로드될 수 있습니다. 즉, .NET Framework는 어셈블리가 다음 위치 중 하나에 있는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-257">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="77102-258">애플리케이션 기본 경로: 애플리케이션의 실행 파일( *.exe*)이 있는 *bin* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-258">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="77102-259">GAC(전역 어셈블리 캐시): GAC에는 여러 .NET Framework 앱이 공유하는 어셈블리가 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-259">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="77102-260">자세한 내용은 [방법: .NET Framework 설명서의 전역 어셈블리 캐시](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)에 어셈블리를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-260">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="77102-261">샘플 코드</span><span class="sxs-lookup"><span data-stu-id="77102-261">Sample code</span></span>

<span data-ttu-id="77102-262">[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)([다운로드 방법](xref:index#how-to-download-a-sample))는 호스팅 시작 구현 시나리오를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="77102-262">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="77102-263">두 개의 호스팅 시작 어셈블리(클래스 라이브러리)는 각각 한 쌍의 메모리 내 구성 키-값 쌍을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-263">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="77102-264">NuGet 패키지(*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="77102-264">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="77102-265">클래스 라이브러리(*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="77102-265">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="77102-266">호스팅 시작은 런타임 저장소 배포 어셈블리에서 활성화됩니다(*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="77102-266">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="77102-267">어셈블리는 시작 시 다음과 같은 진단 정보를 제공하는 두 개의 미들웨어를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-267">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="77102-268">등록된 서비스</span><span class="sxs-lookup"><span data-stu-id="77102-268">Registered services</span></span>
  * <span data-ttu-id="77102-269">주소(구성표, 호스트, 기본 경로, 경로, 쿼리 문자열)</span><span class="sxs-lookup"><span data-stu-id="77102-269">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="77102-270">연결(원격 IP, 원격 포트, 로컬 IP, 로컬 포트, 클라이언트 인증서)</span><span class="sxs-lookup"><span data-stu-id="77102-270">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="77102-271">요청 헤더</span><span class="sxs-lookup"><span data-stu-id="77102-271">Request headers</span></span>
  * <span data-ttu-id="77102-272">환경 변수</span><span class="sxs-lookup"><span data-stu-id="77102-272">Environment variables</span></span>

<span data-ttu-id="77102-273">샘플을 실행하려면:</span><span class="sxs-lookup"><span data-stu-id="77102-273">To run the sample:</span></span>

<span data-ttu-id="77102-274">**NuGet 패키지에서 활성화**</span><span class="sxs-lookup"><span data-stu-id="77102-274">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="77102-275">[dotnet pack](/dotnet/core/tools/dotnet-pack) 명령을 사용하여 *HostingStartupPackage* 패키지를 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-275">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="77102-276">*HostingStartupPackage* 패키지의 어셈블리 이름을 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-276">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="77102-277">앱을 컴파일하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-277">Compile and run the app.</span></span> <span data-ttu-id="77102-278">패키지 참조가 향상된 앱(컴파일 시간 참조)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-278">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="77102-279">앱의 프로젝트 파일에 있는 `<PropertyGroup>`에서는 패키지 프로젝트의 출력( *../HostingStartupPackage/bin/Debug*)을 패키지 원본으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-279">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="77102-280">이렇게 하면 [nuget.org](https://www.nuget.org/)에 패키지를 업로드하지 않고 패키지를 사용할 수 있습니다. 자세한 내용은 HostingStartupApp의 프로젝트 파일에 있는 정보를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77102-280">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="77102-281">인덱스 페이지에 의해 렌더링된 서비스 구성 키 값이 패키지의 `ServiceKeyInjection.Configure` 메서드에서 설정된 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-281">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="77102-282">*HostingStartupPackage* 프로젝트를 변경하고 다시 컴파일하는 경우, 로컬 NuGet 패키지 캐시의 선택을 취소하여 *HostingStartupApp*이 로컬 캐시에서 부실 패키지가 아닌 업데이트된 패키지를 수신하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-282">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="77102-283">로컬 NuGet 캐시를 지우려면 다음 [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-283">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="77102-284">**클래스 라이브러리에서 활성화**</span><span class="sxs-lookup"><span data-stu-id="77102-284">**Activation from a class library**</span></span>

1. <span data-ttu-id="77102-285">[dotnet build](/dotnet/core/tools/dotnet-build) 명령을 사용하여 *HostingStartupLibrary* 클래스 라이브러리를 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-285">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="77102-286">*HostingStartupLibrary* 클래스 라이브러리의 어셈블리 이름을 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-286">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="77102-287">*bin* - *HostingStartupLibrary.dll* 파일을 클래스 라이브러리의 컴파일된 출력에서 앱의 *bin/Debug* 폴더로 복사하여 클래스 라이브러리의 어셈블리를 앱에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-287">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="77102-288">앱을 컴파일하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-288">Compile and run the app.</span></span> <span data-ttu-id="77102-289">앱의 프로젝트 파일에 있는 `<ItemGroup>`은 클래스 라이브러리의 어셈블리( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*)(컴파일 시간 참조)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-289">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="77102-290">자세한 내용은 HostingStartupApp의 프로젝트 파일에 있는 정보를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77102-290">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="77102-291">인덱스 페이지에 의해 렌더링된 서비스 구성 키 값이 클래스 라이브러리의 `ServiceKeyInjection.Configure` 메서드에서 설정된 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-291">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="77102-292">**런타임 저장소 배포 어셈블리에서 활성화**</span><span class="sxs-lookup"><span data-stu-id="77102-292">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="77102-293">*StartupDiagnostics* 프로젝트는 [PowerShell](/powershell/scripting/powershell-scripting)을 사용하여 해당 *StartupDiagnostics.deps.json* 파일을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-293">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="77102-294">PowerShell은 Windows 7 SP1 및 Windows Server 2008 R2 SP1부터 Windows에서 기본적으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-294">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="77102-295">다른 플랫폼에서 PowerShell을 가져오려면 [다양한 버전의 PowerShell 설치](/powershell/scripting/install/installing-powershell)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77102-295">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="77102-296">*RuntimeStore* 폴더에서 *build.ps1* 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-296">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="77102-297">스크립트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-297">The script:</span></span>
   * <span data-ttu-id="77102-298">*obj\packages* 폴더에 `StartupDiagnostics` 패키지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-298">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="77102-299">*store* 폴더에서 `StartupDiagnostics`의 런타임 저장소를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-299">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="77102-300">해당 스크립트에서 `dotnet store` 명령은 Windows에 배포된 호스팅 시작에서 `win7-x64` [RID(런타임 식별자)](/dotnet/core/rid-catalog)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-300">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="77102-301">다른 런타임에 호스팅 시작을 제공할 때 스크립트의 줄 37에서 올바른 RID로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-301">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="77102-302">`StartupDiagnostics`의 런타임 저장소는 나중에 어셈블리가 사용될 컴퓨터의 사용자 또는 시스템의 런타임 저장소로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-302">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="77102-303">`StartupDiagnostics` 어셈블리의 사용자 런타임 저장소 설치 위치는 *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-303">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="77102-304">*additionalDeps* 폴더에 `StartupDiagnostics`의 `additionalDeps`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-304">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="77102-305">추가 종속성은 나중에 사용자 또는 시스템의 추가 종속성으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-305">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="77102-306">사용자 `StartupDiagnostics` 추가 종속성 설치 위치는 *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-306">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="77102-307">*deployment* 폴더에 *deploy.ps1* 파일을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-307">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="77102-308">*배포* 폴더에서 *deploy.ps1* 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-308">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="77102-309">스크립트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-309">The script appends:</span></span>
   * <span data-ttu-id="77102-310">`StartupDiagnostics`를 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-310">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="77102-311">`DOTNET_ADDITIONAL_DEPS` 환경 변수의 호스팅 시작 종속성 경로(RuntimeStore 프로젝트의 *deployment* 폴더에 있음).</span><span class="sxs-lookup"><span data-stu-id="77102-311">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="77102-312">`DOTNET_SHARED_STORE` 환경 변수의 런타임 저장소 경로(RuntimeStore 프로젝트의 *deployment* 폴더에 있음).</span><span class="sxs-lookup"><span data-stu-id="77102-312">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="77102-313">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-313">Run the sample app.</span></span>
1. <span data-ttu-id="77102-314">`/services` 엔드포인트가 앱의 등록된 서비스를 확인하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-314">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="77102-315">`/diag` 엔드포인트가 진단 정보를 확인하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-315">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="77102-316"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>(호스팅 시작) 구현에서는 외부 어셈블리에서 시작할 때 앱에 향상된 기능을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-316">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="77102-317">예를 들어 외부 라이브러리는 호스팅 시작 구현을 사용하여 앱에 추가 구성 공급자 또는 서비스를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-317">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="77102-318">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="77102-318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="77102-319">HostingStartup 특성</span><span class="sxs-lookup"><span data-stu-id="77102-319">HostingStartup attribute</span></span>

<span data-ttu-id="77102-320">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성은 런타임에 활성화할 호스팅 시작 어셈블리의 현재 상태를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="77102-320">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="77102-321">항목 어셈블리 또는 `Startup` 클래스가 포함된 어셈블리에서는 `HostingStartup` 특성을 자동으로 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-321">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="77102-322">`HostingStartup` 특성을 검색할 어셈블리 목록은 [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)의 구성에서 런타임에 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-322">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="77102-323">검색에서 제외할 어셈블리 목록은 [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-323">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="77102-324">자세한 내용은 [웹 호스트: 호스팅 시작 어셈블리](xref:fundamentals/host/web-host#hosting-startup-assemblies) 및 [웹 호스트: 호스팅 시작 어셈블리 제외](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77102-324">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="77102-325">다음 예제에서는 호스팅 시작 어셈블리의 네임스페이스가 `StartupEnhancement`입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-325">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="77102-326">호스팅 시작 코드를 포함하는 클래스는 `StartupEnhancementHostingStartup`입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-326">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="77102-327">`HostingStartup` 특성은 일반적으로 호스팅 시작 어셈블리의 `IHostingStartup` 구현 클래스 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-327">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="77102-328">로드된 호스팅 시작 어셈블리 검색</span><span class="sxs-lookup"><span data-stu-id="77102-328">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="77102-329">로드된 호스팅 시작 어셈블리를 검색하려면 로깅을 사용하도록 설정하고 앱의 로그를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-329">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="77102-330">어셈블리를 로드할 때 발생하는 오류가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-330">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="77102-331">로드된 호스팅 시작 어셈블리는 디버그 수준에서 기록되고 모든 오류가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-331">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="77102-332">호스팅 시작 어셈블리의 자동 로드 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="77102-332">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="77102-333">호스팅 시작 어셈블리의 자동 로딩을 사용하지 않으려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-333">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="77102-334">모든 호스팅 시작 어셈블리가 로드되지 않도록 하려면 다음 중 하나를 `true` 또는 `1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-334">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="77102-335">[호스팅 시작 방지](xref:fundamentals/host/web-host#prevent-hosting-startup) 호스트 구성 설정.</span><span class="sxs-lookup"><span data-stu-id="77102-335">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="77102-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="77102-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="77102-337">특정 호스팅 시작 어셈블리가 로드되지 않도록 하려면 시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열로 다음 중 하나를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-337">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="77102-338">[호스팅 시작 어셈블리 제외](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) 호스트 구성 설정.</span><span class="sxs-lookup"><span data-stu-id="77102-338">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="77102-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="77102-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="77102-340">호스트 구성 설정과 환경 변수가 모두 설정되면 호스트 설정이 동작을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-340">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="77102-341">호스트 설정 또는 환경 변수를 사용하여 호스팅 시작 어셈블리를 사용하지 않도록 설정하면 어셈블리가 전역적으로 해제되고 앱의 여러 특징을 해제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-341">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="77102-342">프로젝트</span><span class="sxs-lookup"><span data-stu-id="77102-342">Project</span></span>

<span data-ttu-id="77102-343">다음 프로젝트 형식 중 하나를 사용하여 호스팅 시작을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77102-343">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="77102-344">클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="77102-344">Class library</span></span>](#class-library)
* [<span data-ttu-id="77102-345">진입점 없는 콘솔 앱</span><span class="sxs-lookup"><span data-stu-id="77102-345">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="77102-346">클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="77102-346">Class library</span></span>

<span data-ttu-id="77102-347">클래스 라이브러리에서 호스팅 시작 기능 향상을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-347">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="77102-348">라이브러리에는 `HostingStartup` 특성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-348">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="77102-349">[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)에는 Razor 페이지 앱, *HostingStartupApp* 및 클래스 라이브러리, *HostingStartupLibrary*가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-349">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="77102-350">클래스 라이브러리:</span><span class="sxs-lookup"><span data-stu-id="77102-350">The class library:</span></span>

* <span data-ttu-id="77102-351">`IHostingStartup`을 구현하는 호스트 시작 클래스(`ServiceKeyInjection`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-351">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="77102-352">`ServiceKeyInjection`은 메모리 내 구성 공급자([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*))를 사용하여 앱의 구성에 서비스 문자열 쌍을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-352">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="77102-353">호스팅 시작의 네임스페이스 및 클래스를 식별하는 `HostingStartup` 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-353">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="77102-354">`ServiceKeyInjection` 클래스의 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>를 사용하여 향상된 기능을 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-354">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="77102-355">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="77102-355">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="77102-356">앱의 인덱스 페이지는 클래스 라이브러리의 호스팅 시작 어셈블리에 의해 설정된 두 키에 대한 구성 값을 읽고 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-356">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="77102-357">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="77102-357">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="77102-358">[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)에는 별도의 호스팅 시작인 *HostingStartupPackage*를 제공하는 NuGet 패키지 프로젝트도 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-358">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="77102-359">패키지는 앞에서 설명한 클래스 라이브러리와 같은 특징이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-359">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="77102-360">패키지:</span><span class="sxs-lookup"><span data-stu-id="77102-360">The package:</span></span>

* <span data-ttu-id="77102-361">`IHostingStartup`을 구현하는 호스트 시작 클래스(`ServiceKeyInjection`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-361">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="77102-362">`ServiceKeyInjection`은 앱의 구성에 서비스 문자열 쌍을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-362">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="77102-363">`HostingStartup` 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-363">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="77102-364">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="77102-364">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="77102-365">앱의 인덱스 페이지는 패키지의 호스팅 시작 어셈블리에 의해 설정된 두 키에 대한 구성 값을 읽고 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-365">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="77102-366">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="77102-366">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="77102-367">진입점 없는 콘솔 앱</span><span class="sxs-lookup"><span data-stu-id="77102-367">Console app without an entry point</span></span>

<span data-ttu-id="77102-368">*이 방법은 .NET Framework가 아닌 .NET Core 앱에서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="77102-368">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="77102-369">활성화에 대한 컴파일 시간 참조가 필요하지 않은 동적 호스팅 시작 기능 향상은 `HostingStartup` 특성이 포함되는 진입점 없는 콘솔 앱에서 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-369">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="77102-370">콘솔 앱을 게시하면 런타임 저장소에서 사용할 수 있는 호스팅 시작 어셈블리가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-370">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="77102-371">다음과 같은 이유로 이 프로세스에서는 진입점 없는 콘솔 앱이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-371">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="77102-372">호스팅 시작 어셈블리에서 호스팅 시작을 사용하려면 종속성 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-372">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="77102-373">종속성 파일은 라이브러리가 아닌 앱을 게시하여 생성된 실행 가능한 앱 자산입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-373">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="77102-374">라이브러리를 [런타임 패키지 저장소](/dotnet/core/deploying/runtime-store)에 직접 추가할 수 없습니다. 이 저장소에는 공유 런타임을 대상으로 하는 실행 가능한 프로젝트가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-374">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="77102-375">동적 호스팅 시작 만들기:</span><span class="sxs-lookup"><span data-stu-id="77102-375">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="77102-376">호스팅 시작 어셈블리는 다음과 같은 진입점 없는 콘솔 앱에서 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="77102-376">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="77102-377">`IHostingStartup` 구현이 포함되는 클래스를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-377">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="77102-378">`IHostingStartup` 구현 클래스를 식별하는 [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-378">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="77102-379">콘솔 앱은 호스팅 시작의 종속성을 얻기 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-379">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="77102-380">콘솔 앱을 게시하면 사용되지 않는 종속성이 종속성 파일에서 트리밍됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-380">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="77102-381">종속성 파일은 호스팅 시작 어셈블리의 런타임 위치를 설정하도록 수정됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-381">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="77102-382">호스팅 시작 어셈블리 및 해당 종속성 파일은 런타임 패키지 저장소에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-382">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="77102-383">호스팅 시작 어셈블리 및 해당 종속성 파일을 검색하기 위해 한 쌍의 환경 변수에 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-383">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="77102-384">콘솔 앱은 [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-384">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="77102-385">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성은 <xref:Microsoft.AspNetCore.Hosting.IWebHost>를 빌드할 때 로드 및 실행을 위해 `IHostingStartup`의 구현으로 클래스를 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-385">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="77102-386">다음 예제에서 네임스페이스는 `StartupEnhancement`이고 클래스는 `StartupEnhancementHostingStartup`입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-386">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="77102-387">클래스는 `IHostingStartup`을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-387">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="77102-388">클래스의 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>를 사용하여 향상된 기능을 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-388">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="77102-389">호스팅 시작 어셈블리의 `IHostingStartup.Configure`는 사용자 코드로 `Startup.Configure` 전에 런타임에서 호출됩니다. 그러면 사용자 코드가 호스팅 시작 어셈블리에서 제공하는 모든 구성을 덮어쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-389">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="77102-390">`IHostingStartup` 프로젝트를 빌드할 때 종속성 파일( *.deps.json*)은 어셈블리의 `runtime` 위치를 *bin* 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-390">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="77102-391">파일의 일부만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-391">Only part of the file is shown.</span></span> <span data-ttu-id="77102-392">이 예제의 어셈블리 이름은 `StartupEnhancement`입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-392">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="77102-393">호스팅 시작으로 제공하는 구성</span><span class="sxs-lookup"><span data-stu-id="77102-393">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="77102-394">호스팅 시작의 구성을 우선할지, 앱의 구성을 우선할지에 따라 두 가지 방법으로 구성을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-394">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="77102-395">앱의 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 대리자가 실행된 후 구성을 로드하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>을 사용하여 앱에 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-395">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="77102-396">이 방법을 사용하면 호스팅 시작 구성이 앱의 구성보다 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-396">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="77102-397">앱의 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 대리자가 실행되기 전에 구성을 로드하려면 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 사용하여 앱에 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-397">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="77102-398">이 방법을 사용하면 호스팅 시작으로 제공된 값보다 앱의 구성 값이 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-398">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="77102-399">호스팅 시작 어셈블리 지정</span><span class="sxs-lookup"><span data-stu-id="77102-399">Specify the hosting startup assembly</span></span>

<span data-ttu-id="77102-400">클래스 라이브러리 또는 콘솔 앱 제공 호스팅 시작의 경우 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 호스팅 시작 어셈블리 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-400">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="77102-401">환경 변수는 세미콜론으로 구분된 어셈블리 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-401">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="77102-402">호스팅 시작 어셈블리만 `HostingStartup` 특성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-402">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="77102-403">샘플 앱(*HostingStartupApp*)의 경우 앞에서 설명한 호스팅 시작을 검색하기 위해 환경 변수가 다음 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-403">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="77102-404">호스팅 시작 어셈블리는 [호스팅 시작 어셈블리](xref:fundamentals/host/web-host#hosting-startup-assemblies) 호스트 구성 설정을 사용하여 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-404">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="77102-405">여러 개의 호스팅 시작 어셈블이 있는 경우 어셈블리가 나열되는 순서대로 해당 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-405">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="77102-406">활성화</span><span class="sxs-lookup"><span data-stu-id="77102-406">Activation</span></span>

<span data-ttu-id="77102-407">호스팅 시작 활성화 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-407">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="77102-408">[런타임 저장소](#runtime-store): 활성화에는 활성화를 위한 컴파일 시간 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-408">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="77102-409">샘플 앱은 호스팅 시작 어셈블리 및 종속성 파일을 *배포* 폴더 안에 배치하여 다중 머신 환경에서 호스팅 시작의 배포를 용이하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-409">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="77102-410">*배포* 폴더에는 호스팅 시작을 사용하도록 배포 시스템에 환경 변수를 만들거나 수정하는 PowerShell 스크립트도 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-410">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="77102-411">활성화에 필요한 컴파일 시간 참조</span><span class="sxs-lookup"><span data-stu-id="77102-411">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="77102-412">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="77102-412">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="77102-413">프로젝트 bin 폴더</span><span class="sxs-lookup"><span data-stu-id="77102-413">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="77102-414">런타임 저장소</span><span class="sxs-lookup"><span data-stu-id="77102-414">Runtime store</span></span>

<span data-ttu-id="77102-415">호스팅 시작 구현은 [런타임 저장소](/dotnet/core/deploying/runtime-store)에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-415">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="77102-416">향상된 앱에서는 어셈블리에 대한 컴파일 시간 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-416">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="77102-417">호스팅 시작이 빌드된 후에는 매니페스트 프로젝트 파일과 [dotnet store](/dotnet/core/tools/dotnet-store) 명령을 사용하여 런타임 저장소가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-417">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="77102-418">샘플 앱(*RuntimeStore* 프로젝트)에서 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-418">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="77102-419">런타임에서 런타임 저장소를 검색하기 위해 런타임 저장소의 위치가 `DOTNET_SHARED_STORE` 환경 변수에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-419">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="77102-420">**호스팅 시작의 종속성 파일 수정 및 배치**</span><span class="sxs-lookup"><span data-stu-id="77102-420">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="77102-421">향상된 기능에 대한 패키지 참조 없이 향상된 기능을 활성화하려면 `additionalDeps`를 사용하여 런타임에 추가 종속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-421">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="77102-422">`additionalDeps`를 사용하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-422">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="77102-423">시작 시 앱의 고유한 *.deps.json* 파일과 병합하기 위해 추가 *.deps.json* 파일 집합을 제공하여 앱의 라이브러리 그래프를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-423">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="77102-424">호스팅 시작 어셈블리를 검색 가능하고 로드 가능하게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77102-424">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="77102-425">추가 종속성 파일을 생성하기 위해 권장되는 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-425">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="77102-426">이전 섹션에서 참조한 런타임 저장소 매니페스트 파일에서 `dotnet publish`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-426">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="77102-427">결과 *.deps.json* 파일의 `runtime` 섹션 및 라이브러리에서 매니페스트 참조를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-427">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="77102-428">예제 프로젝트에서 `store.manifest/1.0.0` 속성은 `targets` 및 `libraries` 섹션에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-428">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="77102-429">*.deps.json* 파일을 다음 위치에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-429">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="77102-430">`{ADDITIONAL DEPENDENCIES PATH}`: `DOTNET_ADDITIONAL_DEPS` 환경 변수에 추가되는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-430">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="77102-431">`{SHARED FRAMEWORK NAME}`: 이 추가 종속성 파일에 대한 공유 프레임워크가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-431">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="77102-432">`{SHARED FRAMEWORK VERSION}`: 최소 공유 프레임워크 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-432">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="77102-433">`{ENHANCEMENT ASSEMBLY NAME}`: 향상된 기능의 어셈블리 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-433">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="77102-434">샘플 앱(*RuntimeStore* 프로젝트)에서 추가 종속성 파일은 다음 위치에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-434">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="77102-435">런타임에서 런타임 저장소 위치를 검색하기 위해 추가 종속성 파일 위치가 `DOTNET_ADDITIONAL_DEPS` 환경 변수에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-435">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="77102-436">샘플 앱( *RuntimeStore* 프로젝트)에서 런타임 저장소 빌드 및 추가 종속성 파일 생성은 [PowerShell](/powershell/scripting/powershell-scripting) 스크립트를 사용하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-436">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="77102-437">다양한 운영 체제에 대한 환경 변수를 설정하는 방법의 예제는 [여러 환경 사용](xref:fundamentals/environments)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77102-437">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="77102-438">**배포**</span><span class="sxs-lookup"><span data-stu-id="77102-438">**Deployment**</span></span>

<span data-ttu-id="77102-439">다중 머신 환경에서 호스팅 시작의 배포를 용이하게 하기 위해 샘플 앱은 다음을 포함하는 게시된 출력에 *배포* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77102-439">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="77102-440">호스팅 시작 런타임 저장소.</span><span class="sxs-lookup"><span data-stu-id="77102-440">The hosting startup runtime store.</span></span>
* <span data-ttu-id="77102-441">호스팅 시작 종속성 파일.</span><span class="sxs-lookup"><span data-stu-id="77102-441">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="77102-442">호스팅 시작 활성화를 지원하기 위해 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` 및 `DOTNET_ADDITIONAL_DEPS` 를 만들거나 수정하는 PowerShell 스크립트입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-442">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="77102-443">배포 시스템의 관리 PowerShell 명령 프롬프트에서 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-443">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="77102-444">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="77102-444">NuGet package</span></span>

<span data-ttu-id="77102-445">NuGet 패키지에서 호스팅 시작 기능 향상을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-445">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="77102-446">패키지에 `HostingStartup` 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-446">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="77102-447">패키지에서 제공하는 호스팅 시작 형식은 다음 방법 중 하나를 통해 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-447">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="77102-448">향상된 앱의 프로젝트 파일은 앱의 프로젝트 파일(컴파일 시간 참조)에서 호스팅 시작을 위한 패키지 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77102-448">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="77102-449">이곳에서 컴파일 시간 참조를 사용하면 호스팅 시작 어셈블리 및 모든 종속성이 앱의 종속성 파일( *.deps.json*)에 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-449">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="77102-450">이 방식은 [nuget.org](https://www.nuget.org/)에 게시된 호스팅 시작 어셈블리 패키지에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-450">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="77102-451">호스팅 시작 종속성 파일은 [런타임 저장소](#runtime-store) 섹션에 설명된 대로 향상된 앱에서 사용할 수 있습니다(컴파일 시간 참조 없이).</span><span class="sxs-lookup"><span data-stu-id="77102-451">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="77102-452">NuGet 패키지 및 런타임 저장소에 대한 자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77102-452">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="77102-453">플랫폼 간 도구로 NuGet 패키지를 만드는 방법</span><span class="sxs-lookup"><span data-stu-id="77102-453">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="77102-454">패키지 게시</span><span class="sxs-lookup"><span data-stu-id="77102-454">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="77102-455">런타임 패키지 저장소</span><span class="sxs-lookup"><span data-stu-id="77102-455">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="77102-456">프로젝트 bin 폴더</span><span class="sxs-lookup"><span data-stu-id="77102-456">Project bin folder</span></span>

<span data-ttu-id="77102-457">호스팅 시작 향상 기능은 향상된 앱의 *bin* 배포 어셈블리에 의해 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-457">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="77102-458">어셈블리에서 제공하는 호스팅 시작 형식은 다음 방법 중 하나를 통해 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-458">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="77102-459">향상된 앱의 프로젝트 파일은 호스팅 시작에 대한 어셈블리 참조를 만듭니다(컴파일 시간 참조).</span><span class="sxs-lookup"><span data-stu-id="77102-459">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="77102-460">이곳에서 컴파일 시간 참조를 사용하면 호스팅 시작 어셈블리 및 모든 종속성이 앱의 종속성 파일( *.deps.json*)에 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-460">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="77102-461">이 방법은 배포 시나리오에서 호스팅 시작 어셈블리( *.dll* 파일)에 대한 컴파일 시간 참조를 수행하고 어셈블리를 다음 중 하나로 이동해야 할 때 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-461">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="77102-462">사용하는 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-462">The consuming project.</span></span>
  * <span data-ttu-id="77102-463">사용하는 프로젝트에서 액세스할 수 있는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-463">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="77102-464">호스팅 시작 종속성 파일은 [런타임 저장소](#runtime-store) 섹션에 설명된 대로 향상된 앱에서 사용할 수 있습니다(컴파일 시간 참조 없이).</span><span class="sxs-lookup"><span data-stu-id="77102-464">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="77102-465">.NET Framework를 대상으로 지정하는 경우 어셈블리는 기본 로드 컨텍스트에 로드될 수 있습니다. 즉, .NET Framework는 어셈블리가 다음 위치 중 하나에 있는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-465">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="77102-466">애플리케이션 기본 경로: 애플리케이션의 실행 파일( *.exe*)이 있는 *bin* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-466">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="77102-467">GAC(전역 어셈블리 캐시): GAC에는 여러 .NET Framework 앱이 공유하는 어셈블리가 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-467">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="77102-468">자세한 내용은 [방법: .NET Framework 설명서의 전역 어셈블리 캐시](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)에 어셈블리를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-468">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="77102-469">샘플 코드</span><span class="sxs-lookup"><span data-stu-id="77102-469">Sample code</span></span>

<span data-ttu-id="77102-470">[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)([다운로드 방법](xref:index#how-to-download-a-sample))는 호스팅 시작 구현 시나리오를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="77102-470">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="77102-471">두 개의 호스팅 시작 어셈블리(클래스 라이브러리)는 각각 한 쌍의 메모리 내 구성 키-값 쌍을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-471">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="77102-472">NuGet 패키지(*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="77102-472">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="77102-473">클래스 라이브러리(*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="77102-473">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="77102-474">호스팅 시작은 런타임 저장소 배포 어셈블리에서 활성화됩니다(*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="77102-474">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="77102-475">어셈블리는 시작 시 다음과 같은 진단 정보를 제공하는 두 개의 미들웨어를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-475">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="77102-476">등록된 서비스</span><span class="sxs-lookup"><span data-stu-id="77102-476">Registered services</span></span>
  * <span data-ttu-id="77102-477">주소(구성표, 호스트, 기본 경로, 경로, 쿼리 문자열)</span><span class="sxs-lookup"><span data-stu-id="77102-477">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="77102-478">연결(원격 IP, 원격 포트, 로컬 IP, 로컬 포트, 클라이언트 인증서)</span><span class="sxs-lookup"><span data-stu-id="77102-478">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="77102-479">요청 헤더</span><span class="sxs-lookup"><span data-stu-id="77102-479">Request headers</span></span>
  * <span data-ttu-id="77102-480">환경 변수</span><span class="sxs-lookup"><span data-stu-id="77102-480">Environment variables</span></span>

<span data-ttu-id="77102-481">샘플을 실행하려면:</span><span class="sxs-lookup"><span data-stu-id="77102-481">To run the sample:</span></span>

<span data-ttu-id="77102-482">**NuGet 패키지에서 활성화**</span><span class="sxs-lookup"><span data-stu-id="77102-482">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="77102-483">[dotnet pack](/dotnet/core/tools/dotnet-pack) 명령을 사용하여 *HostingStartupPackage* 패키지를 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-483">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="77102-484">*HostingStartupPackage* 패키지의 어셈블리 이름을 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-484">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="77102-485">앱을 컴파일하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-485">Compile and run the app.</span></span> <span data-ttu-id="77102-486">패키지 참조가 향상된 앱(컴파일 시간 참조)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77102-486">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="77102-487">앱의 프로젝트 파일에 있는 `<PropertyGroup>`에서는 패키지 프로젝트의 출력( *../HostingStartupPackage/bin/Debug*)을 패키지 원본으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-487">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="77102-488">이렇게 하면 [nuget.org](https://www.nuget.org/)에 패키지를 업로드하지 않고 패키지를 사용할 수 있습니다. 자세한 내용은 HostingStartupApp의 프로젝트 파일에 있는 정보를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77102-488">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="77102-489">인덱스 페이지에 의해 렌더링된 서비스 구성 키 값이 패키지의 `ServiceKeyInjection.Configure` 메서드에서 설정된 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-489">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="77102-490">*HostingStartupPackage* 프로젝트를 변경하고 다시 컴파일하는 경우, 로컬 NuGet 패키지 캐시의 선택을 취소하여 *HostingStartupApp*이 로컬 캐시에서 부실 패키지가 아닌 업데이트된 패키지를 수신하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-490">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="77102-491">로컬 NuGet 캐시를 지우려면 다음 [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-491">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="77102-492">**클래스 라이브러리에서 활성화**</span><span class="sxs-lookup"><span data-stu-id="77102-492">**Activation from a class library**</span></span>

1. <span data-ttu-id="77102-493">[dotnet build](/dotnet/core/tools/dotnet-build) 명령을 사용하여 *HostingStartupLibrary* 클래스 라이브러리를 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-493">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="77102-494">*HostingStartupLibrary* 클래스 라이브러리의 어셈블리 이름을 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-494">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="77102-495">*bin* - *HostingStartupLibrary.dll* 파일을 클래스 라이브러리의 컴파일된 출력에서 앱의 *bin/Debug* 폴더로 복사하여 클래스 라이브러리의 어셈블리를 앱에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-495">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="77102-496">앱을 컴파일하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-496">Compile and run the app.</span></span> <span data-ttu-id="77102-497">앱의 프로젝트 파일에 있는 `<ItemGroup>`는 클래스 라이브러리의 어셈블리( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (컴파일 시간 참조)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77102-497">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="77102-498">자세한 내용은 HostingStartupApp의 프로젝트 파일에 있는 정보를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77102-498">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="77102-499">인덱스 페이지에 의해 렌더링된 서비스 구성 키 값이 클래스 라이브러리의 `ServiceKeyInjection.Configure` 메서드에서 설정된 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-499">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="77102-500">**런타임 저장소 배포 어셈블리에서 활성화**</span><span class="sxs-lookup"><span data-stu-id="77102-500">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="77102-501">*StartupDiagnostics* 프로젝트는 [PowerShell](/powershell/scripting/powershell-scripting)을 사용하여 해당 *StartupDiagnostics.deps.json* 파일을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-501">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="77102-502">PowerShell은 Windows 7 SP1 및 Windows Server 2008 R2 SP1부터 Windows에서 기본적으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-502">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="77102-503">다른 플랫폼에서 PowerShell을 가져오려면 [다양한 버전의 PowerShell 설치](/powershell/scripting/install/installing-powershell)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="77102-503">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="77102-504">*RuntimeStore* 폴더에서 *build.ps1* 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-504">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="77102-505">스크립트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-505">The script:</span></span>
   * <span data-ttu-id="77102-506">*obj\packages* 폴더에 `StartupDiagnostics` 패키지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-506">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="77102-507">*store* 폴더에서 `StartupDiagnostics`의 런타임 저장소를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-507">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="77102-508">해당 스크립트에서 `dotnet store` 명령은 Windows에 배포된 호스팅 시작에서 `win7-x64` [RID(런타임 식별자)](/dotnet/core/rid-catalog)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-508">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="77102-509">다른 런타임에 호스팅 시작을 제공할 때 스크립트의 줄 37에서 올바른 RID로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-509">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="77102-510">`StartupDiagnostics`의 런타임 저장소는 나중에 어셈블리가 사용될 컴퓨터의 사용자 또는 시스템의 런타임 저장소로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-510">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="77102-511">`StartupDiagnostics` 어셈블리의 사용자 런타임 저장소 설치 위치는 *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-511">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="77102-512">*additionalDeps* 폴더에 `StartupDiagnostics`의 `additionalDeps`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-512">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="77102-513">추가 종속성은 나중에 사용자 또는 시스템의 추가 종속성으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="77102-513">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="77102-514">사용자 `StartupDiagnostics` 추가 종속성 설치 위치는 *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*입니다.</span><span class="sxs-lookup"><span data-stu-id="77102-514">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="77102-515">*deployment* 폴더에 *deploy.ps1* 파일을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-515">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="77102-516">*배포* 폴더에서 *deploy.ps1* 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-516">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="77102-517">스크립트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-517">The script appends:</span></span>
   * <span data-ttu-id="77102-518">`StartupDiagnostics`를 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-518">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="77102-519">`DOTNET_ADDITIONAL_DEPS` 환경 변수의 호스팅 시작 종속성 경로(RuntimeStore 프로젝트의 *deployment* 폴더에 있음).</span><span class="sxs-lookup"><span data-stu-id="77102-519">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="77102-520">`DOTNET_SHARED_STORE` 환경 변수의 런타임 저장소 경로(RuntimeStore 프로젝트의 *deployment* 폴더에 있음).</span><span class="sxs-lookup"><span data-stu-id="77102-520">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="77102-521">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-521">Run the sample app.</span></span>
1. <span data-ttu-id="77102-522">`/services` 엔드포인트가 앱의 등록된 서비스를 확인하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-522">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="77102-523">`/diag` 엔드포인트가 진단 정보를 확인하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="77102-523">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
