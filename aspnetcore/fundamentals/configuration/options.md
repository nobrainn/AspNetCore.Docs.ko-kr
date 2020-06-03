---
<span data-ttu-id="238cf-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="238cf-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="238cf-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="238cf-102">'Blazor'</span></span>
- <span data-ttu-id="238cf-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="238cf-103">'Identity'</span></span>
- <span data-ttu-id="238cf-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="238cf-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="238cf-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="238cf-105">'Razor'</span></span>
- <span data-ttu-id="238cf-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="238cf-106">'SignalR' uid:</span></span> 

--- 
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="238cf-107">ASP.NET Core의 옵션 패턴</span><span class="sxs-lookup"><span data-stu-id="238cf-107">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="238cf-108">작성자: [Kirk Larkin](https://twitter.com/serpent5) 및 [Rick Anderson](https://twitter.com/RickAndMSFT).</span><span class="sxs-lookup"><span data-stu-id="238cf-108">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="238cf-109">옵션 패턴은 클래스를 사용하여 관련 설정 그룹에 대한 강력한 형식의 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-109">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="238cf-110">[구성 설정](xref:fundamentals/configuration/index)이 시나리오에 따라 별도 클래스로 격리된 경우 앱은 두 가지 중요한 소프트웨어 엔지니어링 원칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-110">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="238cf-111">[ISP(Interface Segregation Principle, 인터페이스 분리 원칙) 또는 캡슐화](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): 구성 설정에 종속된 시나리오(클래스)는 사용하는 구성 설정에 의해서만 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-111">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="238cf-112">[문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) 앱의 다른 부분에 대한 설정은 다른 설정에 종속되거나 연결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-112">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="238cf-113">옵션은 구성 데이터의 유효성을 검사하는 메커니즘도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-113">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="238cf-114">자세한 내용은 [옵션 유효성 검사](#options-validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="238cf-114">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="238cf-115">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="238cf-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="238cf-116">계층적 구성 바인딩</span><span class="sxs-lookup"><span data-stu-id="238cf-116">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="238cf-117">옵션 인터페이스</span><span class="sxs-lookup"><span data-stu-id="238cf-117">Options interfaces</span></span>

<span data-ttu-id="238cf-118"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="238cf-118"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="238cf-119">다음을 지원하지 \*\*\*\*\*\* 않습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-119">Does ***not*** support:</span></span>
  * <span data-ttu-id="238cf-120">앱이 시작된 후 구성 데이터 읽기</span><span class="sxs-lookup"><span data-stu-id="238cf-120">Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="238cf-121">명명된 옵션</span><span class="sxs-lookup"><span data-stu-id="238cf-121">Named options</span></span>](#named)
* <span data-ttu-id="238cf-122">[Singleton](xref:fundamentals/dependency-injection#singleton)으로 등록되며 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)에 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-122">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="238cf-123"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="238cf-123"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="238cf-124">모든 요청에서 옵션을 다시 계산해야 하는 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-124">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="238cf-125">자세한 내용은 [IOptionsSnapshot을 사용하여 업데이트된 데이터](#ios)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="238cf-125">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="238cf-126">[범위 지정됨](xref:fundamentals/dependency-injection#scoped)으로 등록되므로 Singleton 서비스에 주입할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-126">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="238cf-127">[명명된 옵션](#named)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-127">Supports [named options](#named)</span></span>

<span data-ttu-id="238cf-128"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="238cf-128"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="238cf-129">옵션을 검색하고 `TOptions` 인스턴스에 대한 옵션 알림을 관리하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-129">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="238cf-130">[Singleton](xref:fundamentals/dependency-injection#singleton)으로 등록되며 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)에 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-130">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="238cf-131">지원:</span><span class="sxs-lookup"><span data-stu-id="238cf-131">Supports:</span></span>
  * <span data-ttu-id="238cf-132">변경 알림</span><span class="sxs-lookup"><span data-stu-id="238cf-132">Change notifications</span></span>
  * [<span data-ttu-id="238cf-133">명명된 옵션</span><span class="sxs-lookup"><span data-stu-id="238cf-133">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="238cf-134">다시 로드할 수 있는 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-134">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="238cf-135">선택적 옵션 무효화(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="238cf-135">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="238cf-136">[사후 구성](#options-post-configuration) 시나리오에서는 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 발생한 후에 옵션을 설정하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-136">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="238cf-137"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>는 새로운 옵션 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-137"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="238cf-138">단일 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-138">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="238cf-139">기본 구현에서는 등록된 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 및 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하며 먼저 구성을 모두 실행한 다음, 사후 구성을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-139">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="238cf-140"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>와 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>를 구별하며 적절한 인터페이스만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-140">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="238cf-141"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 `TOptions` 인스턴스를 캐시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-141"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="238cf-142"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 모니터의 옵션 인스턴스를 무효화하므로 값이 다시 계산됩니다(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="238cf-142">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="238cf-143"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>를 사용하여 값을 수동으로 도입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-143">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="238cf-144">모든 명명된 인스턴스를 필요에 따라 다시 만들어야 하는 경우 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 메서드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-144">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="238cf-145">IOptionsSnapshot을 사용하여 업데이트된 데이터 읽기</span><span class="sxs-lookup"><span data-stu-id="238cf-145">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="238cf-146"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하면, 옵션은 엑세스될 때 요청 당 한 번씩 계산되고 요청의 수명 동안 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-146">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="238cf-147">업데이트된 구성 값 읽기를 지원하는 구성 공급자를 사용하는 경우 앱을 시작한 후 구성 변경 내용을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-147">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="238cf-148">`IOptionsMonitor`와 `IOptionsSnapshot`의 차이점은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-148">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="238cf-149">`IOptionsMonitor`는 언제든지 현재 옵션 값을 검색하는 [싱글톤 서비스](xref:fundamentals/dependency-injection#singleton)로, 싱글톤 종속성에서 특히 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-149">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="238cf-150">`IOptionsSnapshot`은 [범위가 지정된 서비스](xref:fundamentals/dependency-injection#scoped)이며 `IOptionsSnapshot<T>` 개체가 생성될 때 옵션의 스냅샷을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-150">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="238cf-151">옵션 스냅숏은 임시 및 범위가 지정된 종속성과 함께 사용하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-151">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="238cf-152">다음 코드에서는 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-152">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="238cf-153">다음 코드에서는 `MyOptions`가 바인딩되는 구성 인스턴스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-153">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="238cf-154">위 코드에서는 앱을 시작한 후 JSON 구성 파일 변경 사항을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-154">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="238cf-155">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="238cf-155">IOptionsMonitor</span></span>

<span data-ttu-id="238cf-156">다음 코드는 `MyOptions`가 바인딩되는 구성 인스턴스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-156">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="238cf-157">다음 예제에서는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-157">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="238cf-158">위 코드에서는 기본적으로 앱을 시작한 후의 JSON 구성 파일의 변경 사항을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-158">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="238cf-159">명명된 옵션은 IConfigureNamedOptions 사용을 지원</span><span class="sxs-lookup"><span data-stu-id="238cf-159">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="238cf-160">명명된 옵션:</span><span class="sxs-lookup"><span data-stu-id="238cf-160">Named options:</span></span>

* <span data-ttu-id="238cf-161">여러 구성 섹션이 동일한 속성에 바인딩되는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-161">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="238cf-162">대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-162">Are case sensitive.</span></span>

<span data-ttu-id="238cf-163">다음 *appsettings.json* 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="238cf-163">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="238cf-164">`TopItem:Month` 및 `TopItem:Year`를 바인딩하는 두 개의 클래스를 만드는 대신 각 섹션에 다음 클래스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-164">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="238cf-165">다음 코드는 명명된 옵션을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-165">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="238cf-166">다음 코드는 명명된 옵션을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-166">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="238cf-167">모든 옵션은 명명된 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-167">All options are named instances.</span></span> <span data-ttu-id="238cf-168"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 인스턴스는 `string.Empty`인 `Options.DefaultName` 인스턴스를 대상으로 지정한 것처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-168"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="238cf-169"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>는 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>도 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-169"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="238cf-170"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>의 기본 구현에는 각 옵션을 적절하게 사용하기 위한 논리가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-170">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="238cf-171">`null` 명명된 옵션은 특정 명명된 인스턴스 대신 모든 명명된 인스턴스를 대상으로 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-171">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="238cf-172"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 및 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>에서 이 규칙을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-172"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="238cf-173">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="238cf-173">OptionsBuilder API</span></span>

<span data-ttu-id="238cf-174"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>는 `TOptions` 인스턴스를 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-174"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="238cf-175">`OptionsBuilder`는 `AddOptions<TOptions>(string optionsName)` 호출에 대한 단일 매개 변수이므로 모든 후속 호출에 나타나는 대신 명명된 옵션 생성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-175">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="238cf-176">옵션 유효성 검사 및 서비스 종속성을 허용하는 `ConfigureOptions` 오버로드는 `OptionsBuilder`를 통해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-176">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="238cf-177">`OptionsBuilder`는 [옵션 유효성 검사](#val) 섹션에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-177">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="238cf-178">DI 서비스를 사용하여 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-178">Use DI services to configure options</span></span>

<span data-ttu-id="238cf-179">다음 두 가지 방법으로 옵션을 구성하는 동안 종속성 주입에서 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-179">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="238cf-180">구성 대리자를 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)의 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-180">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="238cf-181">`OptionsBuilder<TOptions>`는 최대 5개의 서비스를 사용하여 옵션을 구성할 수 있는 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)의 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-181">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="238cf-182"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 또는 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>를 구현하는 형식을 만들고 해당 형식을 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-182">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="238cf-183">서비스를 만드는 것이 더 복잡하기 때문에 구성 대리자를 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-183">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="238cf-184">형식을 만드는 작업은 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)를 호출할 때 프레임워크에서 수행하는 작업에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-184">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="238cf-185">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)을 호출하면 지정된 일반 서비스 유형을 허용하는 생성자가 있는 일시적인 일반 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-185">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="238cf-186">옵션 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="238cf-186">Options validation</span></span>

<span data-ttu-id="238cf-187">옵션 유효성 검사를 통해 옵션 값의 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-187">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="238cf-188">다음 *appsettings.json* 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="238cf-188">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="238cf-189">다음 클래스는 `"MyConfig"` 구성 섹션에 바인딩되고 몇 가지 `DataAnnotations` 규칙을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-189">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="238cf-190">다음 코드는 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A>를 호출하여 `MyConfigOptions` 클래스에 바인딩하고 `DataAnnotations` 유효성 검사를 사용하도록 설정하는 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-190">The following code calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an  [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class and enables `DataAnnotations` validation:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="238cf-191">다음 코드는 구성 값 또는 유효성 검사 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-191">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="238cf-192">다음 코드는 대리자를 사용하여 보다 복잡한 유효성 검사 규칙을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-192">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="238cf-193">복잡한 유효성 검사를 위한 IValidateOptions</span><span class="sxs-lookup"><span data-stu-id="238cf-193">IValidateOptions for complex validation</span></span>

<span data-ttu-id="238cf-194">다음 클래스는 <xref:Microsoft.Extensions.Options.IValidateOptions`1>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-194">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="238cf-195">`IValidateOptions`를 사용하면 유효성 검사 코드를 `StartUp`에서 클래스로 이동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-195">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="238cf-196">위의 코드에서는 다음 코드를 사용하여 `Startup.ConfigureServices`에서 유효성 검사를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-196">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a><span data-ttu-id="238cf-197">옵션 사후 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-197">Options post-configuration</span></span>

<span data-ttu-id="238cf-198"><xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하여 사후 구성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-198">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="238cf-199">사후 구성은 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-199">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="238cf-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>를 사용하여 명명된 옵션을 사후 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="238cf-201">모든 구성 인스턴스를 사후 구성하려면 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-201">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="238cf-202">시작하는 동안 옵션 액세스</span><span class="sxs-lookup"><span data-stu-id="238cf-202">Accessing options during startup</span></span>

<span data-ttu-id="238cf-203">`Configure` 메서드가 실행되기 전에 서비스가 만들어지므로 `Startup.Configure`에서 <xref:Microsoft.Extensions.Options.IOptions%601> 및 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-203"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="238cf-204">`Startup.ConfigureServices`에서는 <xref:Microsoft.Extensions.Options.IOptions%601> 또는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="238cf-204">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="238cf-205">서비스 등록의 순서 지정으로 인해 일관성 없는 옵션 상태가 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-205">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="238cf-206">Options.ConfigurationExtensions NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="238cf-206">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="238cf-207">[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 패키지는 ASP.NET Core 앱에서 암시적으로 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-207">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="238cf-208">옵션 패턴은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-208">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="238cf-209">[구성 설정](xref:fundamentals/configuration/index)이 시나리오에 따라 별도 클래스로 격리된 경우 앱은 두 가지 중요한 소프트웨어 엔지니어링 원칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-209">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="238cf-210">[ISP(Interface Segregation Principle, 인터페이스 분리 원칙) 또는 캡슐화](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): 구성 설정에 종속된 시나리오(클래스)는 사용하는 구성 설정에 의해서만 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-210">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="238cf-211">[문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) 앱의 다른 부분에 대한 설정은 다른 설정에 종속되거나 연결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-211">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="238cf-212">옵션은 구성 데이터의 유효성을 검사하는 메커니즘도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-212">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="238cf-213">자세한 내용은 [옵션 유효성 검사](#options-validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="238cf-213">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="238cf-214">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="238cf-214">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="238cf-215">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="238cf-215">Prerequisites</span></span>

<span data-ttu-id="238cf-216">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하거나 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-216">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="238cf-217">옵션 인터페이스</span><span class="sxs-lookup"><span data-stu-id="238cf-217">Options interfaces</span></span>

<span data-ttu-id="238cf-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>는 옵션을 검색하고 `TOptions` 인스턴스에 대한 옵션 알림을 관리하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="238cf-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>은 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="238cf-220">변경 알림</span><span class="sxs-lookup"><span data-stu-id="238cf-220">Change notifications</span></span>
* [<span data-ttu-id="238cf-221">명명된 옵션</span><span class="sxs-lookup"><span data-stu-id="238cf-221">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="238cf-222">다시 로드할 수 있는 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-222">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="238cf-223">선택적 옵션 무효화(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="238cf-223">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="238cf-224">[사후 구성](#options-post-configuration) 시나리오에서는 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후 옵션을 설정하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-224">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="238cf-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>는 새로운 옵션 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="238cf-226">단일 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-226">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="238cf-227">기본 구현에서는 등록된 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 및 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하며 먼저 구성을 모두 실행한 다음, 사후 구성을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-227">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="238cf-228"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>와 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>를 구별하며 적절한 인터페이스만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-228">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="238cf-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 `TOptions` 인스턴스를 캐시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="238cf-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 모니터의 옵션 인스턴스를 무효화하므로 값이 다시 계산됩니다(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="238cf-230">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="238cf-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>를 사용하여 값을 수동으로 도입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-231">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="238cf-232">모든 명명된 인스턴스를 필요에 따라 다시 만들어야 하는 경우 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 메서드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-232">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="238cf-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 모든 요청에서 옵션을 다시 계산해야 하는 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="238cf-234">자세한 내용은 [IOptionsSnapshot을 사용하여 구성 데이터 다시 로드](#reload-configuration-data-with-ioptionssnapshot)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="238cf-234">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="238cf-235"><xref:Microsoft.Extensions.Options.IOptions%601>를 사용하여 옵션을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-235"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="238cf-236">그러나 <xref:Microsoft.Extensions.Options.IOptions%601>는 앞에서 설명한 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>의 시나리오를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-236">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="238cf-237"><xref:Microsoft.Extensions.Options.IOptions%601> 인터페이스를 이미 사용하고 있으며 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 제공하는 시나리오가 필요하지 않은 기존 프레임워크 및 라이브러리에서는 <xref:Microsoft.Extensions.Options.IOptions%601>를 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-237">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="238cf-238">일반 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-238">General options configuration</span></span>

<span data-ttu-id="238cf-239">일반 옵션 구성은 샘플 앱에 예제 1로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-239">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="238cf-240">옵션 클래스는 매개 변수가 없는 public 생성자를 사용하는 비추상이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-240">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="238cf-241">다음 클래스 `MyOptions`에는 `Option1` 및 `Option2`의 두 가지 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-241">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="238cf-242">기본 값 설정은 선택 사항이지만, 다음 예제의 클래스 생성자는 `Option1`의 기본값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-242">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="238cf-243">`Option2`에는 직접 속성을 초기화하여 설정된 기본값이 있습니다(*Models/MyOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="238cf-243">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="238cf-244">`MyOptions` 클래스는 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*>를 통해 서비스 컨테이너에 추가되고 구성에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-244">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="238cf-245">다음 페이지 모델은 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 통해 [생성자 종속성 주입](xref:mvc/controllers/dependency-injection)을 사용하여 설정에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="238cf-245">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="238cf-246">샘플의 *appsettings.json* 파일은 `option1` 및 `option2`에 대한 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-246">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="238cf-247">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-247">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="238cf-248">사용자 지정 <xref:System.Configuration.ConfigurationBuilder>를 사용하여 설정 파일에서 옵션 구성을 로드하는 경우 기본 경로가 올바르게 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-248">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="238cf-249"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 통해 설정 파일에서 옵션 구성을 로드하는 경우에는 명시적으로 기본 경로를 설정하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-249">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="238cf-250">대리자로 간단한 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-250">Configure simple options with a delegate</span></span>

<span data-ttu-id="238cf-251">대리자를 사용한 간단한 옵션 구성은 샘플 앱에 예제 2로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-251">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="238cf-252">대리자를 사용하여 옵션 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-252">Use a delegate to set options values.</span></span> <span data-ttu-id="238cf-253">샘플 앱에서는 `MyOptionsWithDelegateConfig` 클래스(*Models/MyOptionsWithDelegateConfig.cs*)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-253">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="238cf-254">다음 코드에서 두 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-254">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="238cf-255">`MyOptionsWithDelegateConfig`를 통해 바인딩을 구성하는 데 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-255">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="238cf-256">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="238cf-256">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="238cf-257">여러 구성 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-257">You can add multiple configuration providers.</span></span> <span data-ttu-id="238cf-258">구성 공급자는 NuGet 패키지에서 사용할 수 있으며 등록된 순서대로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-258">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="238cf-259">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="238cf-259">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="238cf-260"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*>를 호출할 때마다 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-260">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="238cf-261">위의 예제에서는 `Option1` 및 `Option2`의 값은 모두 *appsettings.json*에서 지정되지만, `Option1` 및 `Option2`의 값은 구성된 대리자에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-261">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="238cf-262">둘 이상의 구성 서비스를 활성화한 경우 마지막 지정된 구성 소스가 *승리*하고 구성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-262">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="238cf-263">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-263">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="238cf-264">하위 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-264">Suboptions configuration</span></span>

<span data-ttu-id="238cf-265">하위 옵션 구성은 샘플 앱에 예제 3으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-265">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="238cf-266">앱은 앱의 특정 시나리오 그룹(클래스)에 적합한 옵션 클래스를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-266">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="238cf-267">구성 값을 필요로 하는 앱의 부분은 사용하는 구성 값에만 액세스할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-267">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="238cf-268">옵션을 구성에 바인딩하는 경우 옵션 형식의 각 속성은 `property[:sub-property:]` 양식의 구성 키에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-268">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="238cf-269">예를 들어 `MyOptions.Option1` 속성은 키 `Option1`에 바인딩되어 *appsettings.json*의 `option1` 속성에서 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-269">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="238cf-270">다음 코드에서 세 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-270">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="238cf-271">이 코드는 `MySubOptions`를 *appsettings.json* 파일의 `subsection` 섹션에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-271">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="238cf-272">`GetSection` 메서드를 사용하려면 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 네임스페이스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-272">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="238cf-273">샘플의 *appsettings.json* 파일은 `suboption1` 및 `suboption2`에 대한 키로 `subsection` 멤버를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-273">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="238cf-274">`MySubOptions` 클래스는 `SubOption1` 및 `SubOption2` 속성을 정의하여 옵션 값을 유지합니다(*Models/MySubOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="238cf-274">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="238cf-275">페이지 모델의 `OnGet` 메서드는 옵션 값이 포함된 문자열을 반환합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="238cf-275">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="238cf-276">앱을 실행할 때 `OnGet` 메서드는 하위 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-276">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="238cf-277">옵션 삽입</span><span class="sxs-lookup"><span data-stu-id="238cf-277">Options injection</span></span>

<span data-ttu-id="238cf-278">옵션 삽입은 샘플 앱에 예제 4로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-278">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="238cf-279">다음에 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-279">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="238cf-280">[`@inject`](xref:mvc/views/razor#inject) Razor 지시문이 포함된 Razor 페이지 또는 MVC 뷰.</span><span class="sxs-lookup"><span data-stu-id="238cf-280">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="238cf-281">페이지 또는 뷰 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-281">A page or view model.</span></span>

<span data-ttu-id="238cf-282">샘플 앱의 다음 예제는 페이지 모델(*Pages/Index.cshtml.cs*)에 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-282">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="238cf-283">샘플 앱은 `@inject` 지시문을 사용하여 `IOptionsMonitor<MyOptions>`를 주입하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-283">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="238cf-284">앱을 실행하면 옵션 값이 렌더링된 페이지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-284">When the app is run, the options values are shown in the rendered page:</span></span>

![옵션 값 Option1: value1_from_json 및 Option2: -1은 모델에서 로드되며 보기에 주입됩니다.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="238cf-286">IOptionsSnapshot을 사용하여 구성 데이터 다시 로드</span><span class="sxs-lookup"><span data-stu-id="238cf-286">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="238cf-287"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하여 구성 데이터를 다시 로드하는 방법은 샘플 앱에 예제 5로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-287">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="238cf-288"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하면, 옵션은 엑세스될 때 요청 당 한 번씩 계산되고 요청의 수명 동안 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-288">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="238cf-289">`IOptionsMonitor`와 `IOptionsSnapshot`의 차이점은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-289">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="238cf-290">`IOptionsMonitor`는 언제든지 현재 옵션 값을 검색하는 [싱글톤 서비스](xref:fundamentals/dependency-injection#singleton)로, 싱글톤 종속성에서 특히 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-290">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="238cf-291">`IOptionsSnapshot`은 [범위가 지정된 서비스](xref:fundamentals/dependency-injection#scoped)이며 `IOptionsSnapshot<T>` 개체가 생성될 때 옵션의 스냅샷을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-291">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="238cf-292">옵션 스냅숏은 임시 및 범위가 지정된 종속성과 함께 사용하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-292">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="238cf-293">다음 예제에는 *appsettings.json*이 변경된 후 새 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 만드는 방법이 설명되어 있습니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="238cf-293">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="238cf-294">서버에 대한 여러 요청은 파일이 변경되고 구성이 다시 로드될 때까지 *appsettings.json* 파일에서 제공하는 상수 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-294">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="238cf-295">다음 이미지는 *appsettings.json* 파일에서 로드된 초기 `option1` 및 `option2` 값을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-295">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="238cf-296">*appsettings.json* 파일의 값을 `value1_from_json UPDATED` 및 `200`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-296">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="238cf-297">*appsettings.json* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-297">Save the *appsettings.json* file.</span></span> <span data-ttu-id="238cf-298">옵션 값이 변경되었음을 확인하려면 브라우저를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-298">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="238cf-299">IConfigureNamedOptions로 명명된 옵션 지원</span><span class="sxs-lookup"><span data-stu-id="238cf-299">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="238cf-300"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>로 명명된 옵션 지원은 샘플 앱에 예제 6으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-300">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="238cf-301">앱은 명명된 옵션 지원을 통해 명명된 옵션 구성들을 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-301">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="238cf-302">샘플 앱에서 명명된 옵션은 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 확장 메서드를 호출하는 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)를 사용하여 선언됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-302">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="238cf-303">명명된 옵션은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-303">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="238cf-304">샘플 앱은 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*>을 사용하여 명명된 옵션에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="238cf-304">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="238cf-305">샘플 앱을 실행하면 명명된 옵션이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-305">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="238cf-306">`named_options_1` 값은 *appsettings.json* 파일에서 로드되는 구성에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-306">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="238cf-307">`named_options_2` 값은 다음에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-307">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="238cf-308">`Option1`에 대한 `ConfigureServices`의 `named_options_2` 대리자.</span><span class="sxs-lookup"><span data-stu-id="238cf-308">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="238cf-309">`MyOptions` 클래스에서 제공되는 `Option2`에 대한 기본값.</span><span class="sxs-lookup"><span data-stu-id="238cf-309">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="238cf-310">ConfigureAll 메서드를 사용하여 모든 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-310">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="238cf-311"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 메서드를 사용하여 모든 옵션 인스턴스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-311">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="238cf-312">다음 코드는 모든 구성 인스턴스에 대해 `Option1`을 공통 값으로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-312">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="238cf-313">다음 코드를 `Startup.ConfigureServices` 메서드에 직접 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-313">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="238cf-314">코드를 추가한 후 샘플 앱을 실행하면 다음과 같은 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-314">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="238cf-315">모든 옵션은 명명된 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-315">All options are named instances.</span></span> <span data-ttu-id="238cf-316">기존 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 인스턴스는 `string.Empty`인 `Options.DefaultName` 인스턴스를 대상으로 지정한 것처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-316">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="238cf-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>는 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>도 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="238cf-318"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>의 기본 구현에는 각 옵션을 적절하게 사용하기 위한 논리가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-318">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="238cf-319">`null` 명명된 옵션은 특정 명명된 인스턴스 대신 모든 명명된 인스턴스를 대상으로 지정하는 데 사용됩니다(<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 및 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>에서 이 규칙을 사용함).</span><span class="sxs-lookup"><span data-stu-id="238cf-319">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="238cf-320">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="238cf-320">OptionsBuilder API</span></span>

<span data-ttu-id="238cf-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>는 `TOptions` 인스턴스를 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="238cf-322">`OptionsBuilder`는 `AddOptions<TOptions>(string optionsName)` 호출에 대한 단일 매개 변수이므로 모든 후속 호출에 나타나는 대신 명명된 옵션 생성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-322">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="238cf-323">옵션 유효성 검사 및 서비스 종속성을 허용하는 `ConfigureOptions` 오버로드는 `OptionsBuilder`를 통해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-323">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="238cf-324">DI 서비스를 사용하여 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-324">Use DI services to configure options</span></span>

<span data-ttu-id="238cf-325">다음과 같은 두 가지 방법으로 옵션을 구성하는 동안 종속성 주입을 통해 다른 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-325">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="238cf-326">구성 대리자를 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)의 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-326">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="238cf-327">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)는 최대 5개의 서비스를 사용하여 옵션을 구성할 수 있는 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)의 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-327">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="238cf-328"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 또는 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>를 구현하는 고유의 형식을 만들고 해당 형식을 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-328">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="238cf-329">서비스를 만드는 것이 더 복잡하기 때문에 구성 대리자를 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-329">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="238cf-330">고유의 형식을 만드는 것은 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)를 사용할 때 프레임워크가 수행하는 것과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-330">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="238cf-331">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)을 호출하면 지정된 일반 서비스 유형을 허용하는 생성자가 있는 일시적인 일반 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-331">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="238cf-332">옵션 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="238cf-332">Options validation</span></span>

<span data-ttu-id="238cf-333">옵션 유효성 검사를 사용하면 옵션이 구성될 때 옵션의 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-333">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="238cf-334">옵션이 유효하면 `true`를 반환하고 옵션이 유효하지 않으면 `false`를 반환하는 유효성 검사 메서드로 `Validate`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-334">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="238cf-335">위의 예제에서는 명명된 옵션 인스턴스를 `optionalOptionsName`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-335">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="238cf-336">기본 옵션 인스턴스는 `Options.DefaultName`입니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-336">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="238cf-337">유효성 검사는 옵션 인스턴스가 만들어지면 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-337">Validation runs when the options instance is created.</span></span> <span data-ttu-id="238cf-338">옵션 인스턴스는 처음 액세스되면 유효성 검사를 통과하도록 보장됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-338">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="238cf-339">옵션 유효성 검사는 옵션 인스턴스가 생성된 후 옵션 수정에 대해 보호하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-339">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="238cf-340">예를 들어 옵션이 처음 액세스될 때 요청마다 한 번씩 `IOptionsSnapshot` 옵션이 생성되고 유효성이 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-340">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="238cf-341">`IOptionsSnapshot` 옵션은 *동일한 요청에 대한* 다음 액세스 시도에서 다시 검사되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-341">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="238cf-342">`Validate` 메서드는 `Func<TOptions, bool>`을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-342">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="238cf-343">유효성 검사를 완전히 사용자 지정하려면 다음을 허용하는 `IValidateOptions<TOptions>`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-343">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="238cf-344">여러 옵션 형식의 유효성 검사: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="238cf-344">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="238cf-345">다른 옵션 형식에 의존하는 유효성 검사: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="238cf-345">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="238cf-346">`IValidateOptions`는 다음의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-346">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="238cf-347">특정 명명된 옵션 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="238cf-347">A specific named options instance.</span></span>
* <span data-ttu-id="238cf-348">`name`이 `null`인 경우 모든 옵션.</span><span class="sxs-lookup"><span data-stu-id="238cf-348">All options when `name` is `null`.</span></span>

<span data-ttu-id="238cf-349">인터페이스의 구현에서 `ValidateOptionsResult`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-349">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="238cf-350">데이터 주석 기반 유효성 검사는 `OptionsBuilder<TOptions>`의 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> 메서드를 호출하여 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-350">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="238cf-351">`Microsoft.Extensions.Options.DataAnnotations`는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-351">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="238cf-352">즉시 유효성 검사(시작 시 빠른 실패)는 향후 릴리스에서 고려 중입니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-352">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="238cf-353">옵션 사후 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-353">Options post-configuration</span></span>

<span data-ttu-id="238cf-354"><xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하여 사후 구성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-354">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="238cf-355">사후 구성은 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-355">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="238cf-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>를 사용하여 명명된 옵션을 사후 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="238cf-357">모든 구성 인스턴스를 사후 구성하려면 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-357">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="238cf-358">시작하는 동안 옵션 액세스</span><span class="sxs-lookup"><span data-stu-id="238cf-358">Accessing options during startup</span></span>

<span data-ttu-id="238cf-359">`Configure` 메서드가 실행되기 전에 서비스가 만들어지므로 `Startup.Configure`에서 <xref:Microsoft.Extensions.Options.IOptions%601> 및 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-359"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="238cf-360">`Startup.ConfigureServices`에서는 <xref:Microsoft.Extensions.Options.IOptions%601> 또는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="238cf-360">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="238cf-361">서비스 등록의 순서 지정으로 인해 일관성 없는 옵션 상태가 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-361">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="238cf-362">옵션 패턴은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-362">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="238cf-363">[구성 설정](xref:fundamentals/configuration/index)이 시나리오에 따라 별도 클래스로 격리된 경우 앱은 두 가지 중요한 소프트웨어 엔지니어링 원칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-363">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="238cf-364">[ISP(Interface Segregation Principle, 인터페이스 분리 원칙) 또는 캡슐화](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): 구성 설정에 종속된 시나리오(클래스)는 사용하는 구성 설정에 의해서만 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-364">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="238cf-365">[문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) 앱의 다른 부분에 대한 설정은 다른 설정에 종속되거나 연결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-365">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="238cf-366">옵션은 구성 데이터의 유효성을 검사하는 메커니즘도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-366">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="238cf-367">자세한 내용은 [옵션 유효성 검사](#options-validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="238cf-367">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="238cf-368">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="238cf-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="238cf-369">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="238cf-369">Prerequisites</span></span>

<span data-ttu-id="238cf-370">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하거나 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-370">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="238cf-371">옵션 인터페이스</span><span class="sxs-lookup"><span data-stu-id="238cf-371">Options interfaces</span></span>

<span data-ttu-id="238cf-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>는 옵션을 검색하고 `TOptions` 인스턴스에 대한 옵션 알림을 관리하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="238cf-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>은 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="238cf-374">변경 알림</span><span class="sxs-lookup"><span data-stu-id="238cf-374">Change notifications</span></span>
* [<span data-ttu-id="238cf-375">명명된 옵션</span><span class="sxs-lookup"><span data-stu-id="238cf-375">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="238cf-376">다시 로드할 수 있는 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-376">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="238cf-377">선택적 옵션 무효화(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="238cf-377">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="238cf-378">[사후 구성](#options-post-configuration) 시나리오에서는 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후 옵션을 설정하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-378">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="238cf-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>는 새로운 옵션 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="238cf-380">단일 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-380">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="238cf-381">기본 구현에서는 등록된 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 및 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하며 먼저 구성을 모두 실행한 다음, 사후 구성을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-381">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="238cf-382"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>와 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>를 구별하며 적절한 인터페이스만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-382">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="238cf-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 `TOptions` 인스턴스를 캐시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="238cf-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 모니터의 옵션 인스턴스를 무효화하므로 값이 다시 계산됩니다(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="238cf-384">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="238cf-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>를 사용하여 값을 수동으로 도입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-385">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="238cf-386">모든 명명된 인스턴스를 필요에 따라 다시 만들어야 하는 경우 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 메서드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-386">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="238cf-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 모든 요청에서 옵션을 다시 계산해야 하는 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="238cf-388">자세한 내용은 [IOptionsSnapshot을 사용하여 구성 데이터 다시 로드](#reload-configuration-data-with-ioptionssnapshot)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="238cf-388">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="238cf-389"><xref:Microsoft.Extensions.Options.IOptions%601>를 사용하여 옵션을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-389"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="238cf-390">그러나 <xref:Microsoft.Extensions.Options.IOptions%601>는 앞에서 설명한 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>의 시나리오를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-390">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="238cf-391"><xref:Microsoft.Extensions.Options.IOptions%601> 인터페이스를 이미 사용하고 있으며 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 제공하는 시나리오가 필요하지 않은 기존 프레임워크 및 라이브러리에서는 <xref:Microsoft.Extensions.Options.IOptions%601>를 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-391">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="238cf-392">일반 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-392">General options configuration</span></span>

<span data-ttu-id="238cf-393">일반 옵션 구성은 샘플 앱에 예제 1로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-393">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="238cf-394">옵션 클래스는 매개 변수가 없는 public 생성자를 사용하는 비추상이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-394">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="238cf-395">다음 클래스 `MyOptions`에는 `Option1` 및 `Option2`의 두 가지 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-395">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="238cf-396">기본 값 설정은 선택 사항이지만, 다음 예제의 클래스 생성자는 `Option1`의 기본값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-396">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="238cf-397">`Option2`에는 직접 속성을 초기화하여 설정된 기본값이 있습니다(*Models/MyOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="238cf-397">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="238cf-398">`MyOptions` 클래스는 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*>를 통해 서비스 컨테이너에 추가되고 구성에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-398">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="238cf-399">다음 페이지 모델은 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 통해 [생성자 종속성 주입](xref:mvc/controllers/dependency-injection)을 사용하여 설정에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="238cf-399">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="238cf-400">샘플의 *appsettings.json* 파일은 `option1` 및 `option2`에 대한 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-400">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="238cf-401">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-401">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="238cf-402">사용자 지정 <xref:System.Configuration.ConfigurationBuilder>를 사용하여 설정 파일에서 옵션 구성을 로드하는 경우 기본 경로가 올바르게 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-402">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="238cf-403"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 통해 설정 파일에서 옵션 구성을 로드하는 경우에는 명시적으로 기본 경로를 설정하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-403">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="238cf-404">대리자로 간단한 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-404">Configure simple options with a delegate</span></span>

<span data-ttu-id="238cf-405">대리자를 사용한 간단한 옵션 구성은 샘플 앱에 예제 2로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-405">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="238cf-406">대리자를 사용하여 옵션 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-406">Use a delegate to set options values.</span></span> <span data-ttu-id="238cf-407">샘플 앱에서는 `MyOptionsWithDelegateConfig` 클래스(*Models/MyOptionsWithDelegateConfig.cs*)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-407">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="238cf-408">다음 코드에서 두 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-408">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="238cf-409">`MyOptionsWithDelegateConfig`를 통해 바인딩을 구성하는 데 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-409">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="238cf-410">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="238cf-410">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="238cf-411">여러 구성 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-411">You can add multiple configuration providers.</span></span> <span data-ttu-id="238cf-412">구성 공급자는 NuGet 패키지에서 사용할 수 있으며 등록된 순서대로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-412">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="238cf-413">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="238cf-413">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="238cf-414"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*>를 호출할 때마다 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-414">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="238cf-415">위의 예제에서는 `Option1` 및 `Option2`의 값은 모두 *appsettings.json*에서 지정되지만, `Option1` 및 `Option2`의 값은 구성된 대리자에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-415">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="238cf-416">둘 이상의 구성 서비스를 활성화한 경우 마지막 지정된 구성 소스가 *승리*하고 구성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-416">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="238cf-417">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-417">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="238cf-418">하위 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-418">Suboptions configuration</span></span>

<span data-ttu-id="238cf-419">하위 옵션 구성은 샘플 앱에 예제 3으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-419">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="238cf-420">앱은 앱의 특정 시나리오 그룹(클래스)에 적합한 옵션 클래스를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-420">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="238cf-421">구성 값을 필요로 하는 앱의 부분은 사용하는 구성 값에만 액세스할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-421">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="238cf-422">옵션을 구성에 바인딩하는 경우 옵션 형식의 각 속성은 `property[:sub-property:]` 양식의 구성 키에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-422">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="238cf-423">예를 들어 `MyOptions.Option1` 속성은 키 `Option1`에 바인딩되어 *appsettings.json*의 `option1` 속성에서 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-423">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="238cf-424">다음 코드에서 세 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-424">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="238cf-425">이 코드는 `MySubOptions`를 *appsettings.json* 파일의 `subsection` 섹션에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-425">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="238cf-426">`GetSection` 메서드를 사용하려면 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 네임스페이스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-426">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="238cf-427">샘플의 *appsettings.json* 파일은 `suboption1` 및 `suboption2`에 대한 키로 `subsection` 멤버를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-427">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="238cf-428">`MySubOptions` 클래스는 `SubOption1` 및 `SubOption2` 속성을 정의하여 옵션 값을 유지합니다(*Models/MySubOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="238cf-428">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="238cf-429">페이지 모델의 `OnGet` 메서드는 옵션 값이 포함된 문자열을 반환합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="238cf-429">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="238cf-430">앱을 실행할 때 `OnGet` 메서드는 하위 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-430">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="238cf-431">직접 보기 주입 또는 보기 모델에 의해 제공되는 옵션</span><span class="sxs-lookup"><span data-stu-id="238cf-431">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="238cf-432">직접 보기 주입 또는 보기 모델에 의해 제공되는 옵션은 샘플 앱에 예제 4로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-432">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="238cf-433">옵션은 뷰 모델 또는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 보기에 직접 주입하여 제공할 수 있습니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="238cf-433">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="238cf-434">샘플 앱은 `@inject` 지시문을 사용하여 `IOptionsMonitor<MyOptions>`를 주입하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-434">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="238cf-435">앱을 실행하면 옵션 값이 렌더링된 페이지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-435">When the app is run, the options values are shown in the rendered page:</span></span>

![옵션 값 Option1: value1_from_json 및 Option2: -1은 모델에서 로드되며 보기에 주입됩니다.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="238cf-437">IOptionsSnapshot을 사용하여 구성 데이터 다시 로드</span><span class="sxs-lookup"><span data-stu-id="238cf-437">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="238cf-438"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하여 구성 데이터를 다시 로드하는 방법은 샘플 앱에 예제 5로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-438">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="238cf-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 최소한의 처리 오버헤드로 옵션 다시 로드를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="238cf-440">옵션은 엑세스될 때 요청 당 한 번씩 계산되고 요청의 수명 동안 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-440">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="238cf-441">다음 예제에는 *appsettings.json*이 변경된 후 새 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 만드는 방법이 설명되어 있습니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="238cf-441">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="238cf-442">서버에 대한 여러 요청은 파일이 변경되고 구성이 다시 로드될 때까지 *appsettings.json* 파일에서 제공하는 상수 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-442">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="238cf-443">다음 이미지는 *appsettings.json* 파일에서 로드된 초기 `option1` 및 `option2` 값을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-443">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="238cf-444">*appsettings.json* 파일의 값을 `value1_from_json UPDATED` 및 `200`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-444">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="238cf-445">*appsettings.json* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-445">Save the *appsettings.json* file.</span></span> <span data-ttu-id="238cf-446">옵션 값이 변경되었음을 확인하려면 브라우저를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-446">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="238cf-447">IConfigureNamedOptions로 명명된 옵션 지원</span><span class="sxs-lookup"><span data-stu-id="238cf-447">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="238cf-448"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>로 명명된 옵션 지원은 샘플 앱에 예제 6으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-448">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="238cf-449">앱은 명명된 옵션 지원을 통해 명명된 옵션 구성들을 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-449">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="238cf-450">샘플 앱에서 명명된 옵션은 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 확장 메서드를 호출하는 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)를 사용하여 선언됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-450">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="238cf-451">명명된 옵션은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-451">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="238cf-452">샘플 앱은 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*>을 사용하여 명명된 옵션에 액세스합니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="238cf-452">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="238cf-453">샘플 앱을 실행하면 명명된 옵션이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-453">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="238cf-454">`named_options_1` 값은 *appsettings.json* 파일에서 로드되는 구성에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-454">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="238cf-455">`named_options_2` 값은 다음에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-455">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="238cf-456">`Option1`에 대한 `ConfigureServices`의 `named_options_2` 대리자.</span><span class="sxs-lookup"><span data-stu-id="238cf-456">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="238cf-457">`MyOptions` 클래스에서 제공되는 `Option2`에 대한 기본값.</span><span class="sxs-lookup"><span data-stu-id="238cf-457">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="238cf-458">ConfigureAll 메서드를 사용하여 모든 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-458">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="238cf-459"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 메서드를 사용하여 모든 옵션 인스턴스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-459">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="238cf-460">다음 코드는 모든 구성 인스턴스에 대해 `Option1`을 공통 값으로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-460">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="238cf-461">다음 코드를 `Startup.ConfigureServices` 메서드에 직접 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-461">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="238cf-462">코드를 추가한 후 샘플 앱을 실행하면 다음과 같은 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-462">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="238cf-463">모든 옵션은 명명된 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-463">All options are named instances.</span></span> <span data-ttu-id="238cf-464">기존 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 인스턴스는 `string.Empty`인 `Options.DefaultName` 인스턴스를 대상으로 지정한 것처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-464">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="238cf-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>는 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>도 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="238cf-466"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>의 기본 구현에는 각 옵션을 적절하게 사용하기 위한 논리가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-466">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="238cf-467">`null` 명명된 옵션은 특정 명명된 인스턴스 대신 모든 명명된 인스턴스를 대상으로 지정하는 데 사용됩니다(<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 및 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>에서 이 규칙을 사용함).</span><span class="sxs-lookup"><span data-stu-id="238cf-467">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="238cf-468">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="238cf-468">OptionsBuilder API</span></span>

<span data-ttu-id="238cf-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>는 `TOptions` 인스턴스를 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="238cf-470">`OptionsBuilder`는 `AddOptions<TOptions>(string optionsName)` 호출에 대한 단일 매개 변수이므로 모든 후속 호출에 나타나는 대신 명명된 옵션 생성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-470">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="238cf-471">옵션 유효성 검사 및 서비스 종속성을 허용하는 `ConfigureOptions` 오버로드는 `OptionsBuilder`를 통해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-471">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="238cf-472">DI 서비스를 사용하여 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-472">Use DI services to configure options</span></span>

<span data-ttu-id="238cf-473">다음과 같은 두 가지 방법으로 옵션을 구성하는 동안 종속성 주입을 통해 다른 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-473">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="238cf-474">구성 대리자를 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)의 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-474">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="238cf-475">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)는 최대 5개의 서비스를 사용하여 옵션을 구성할 수 있는 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)의 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-475">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="238cf-476"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 또는 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>를 구현하는 고유의 형식을 만들고 해당 형식을 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-476">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="238cf-477">서비스를 만드는 것이 더 복잡하기 때문에 구성 대리자를 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-477">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="238cf-478">고유의 형식을 만드는 것은 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)를 사용할 때 프레임워크가 수행하는 것과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-478">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="238cf-479">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)을 호출하면 지정된 일반 서비스 유형을 허용하는 생성자가 있는 일시적인 일반 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-479">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="238cf-480">옵션 사후 구성</span><span class="sxs-lookup"><span data-stu-id="238cf-480">Options post-configuration</span></span>

<span data-ttu-id="238cf-481"><xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하여 사후 구성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-481">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="238cf-482">사후 구성은 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-482">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="238cf-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>를 사용하여 명명된 옵션을 사후 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="238cf-484">모든 구성 인스턴스를 사후 구성하려면 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-484">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="238cf-485">시작하는 동안 옵션 액세스</span><span class="sxs-lookup"><span data-stu-id="238cf-485">Accessing options during startup</span></span>

<span data-ttu-id="238cf-486">`Configure` 메서드가 실행되기 전에 서비스가 만들어지므로 `Startup.Configure`에서 <xref:Microsoft.Extensions.Options.IOptions%601> 및 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-486"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="238cf-487">`Startup.ConfigureServices`에서는 <xref:Microsoft.Extensions.Options.IOptions%601> 또는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="238cf-487">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="238cf-488">서비스 등록의 순서 지정으로 인해 일관성 없는 옵션 상태가 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="238cf-488">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="238cf-489">추가 자료</span><span class="sxs-lookup"><span data-stu-id="238cf-489">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
