---
<span data-ttu-id="772db-101">title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-101">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-103">'Blazor'</span></span>
- <span data-ttu-id="772db-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-104">'Identity'</span></span>
- <span data-ttu-id="772db-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-106">'Razor'</span></span>
- <span data-ttu-id="772db-107">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-107">'SignalR' uid:</span></span> 

---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="772db-108">ASP.NET Core Blazor용 링커 구성</span><span class="sxs-lookup"><span data-stu-id="772db-108">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="772db-109">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="772db-109">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="772db-110"> WebAssembly는 빌드 중에 [IL(중간 언어)](/dotnet/standard/managed-code#intermediate-language--execution) 연결을 수행하여 앱의 출력 어셈블리에서 불필요한 IL을 잘라냅니다.</span><span class="sxs-lookup"><span data-stu-id="772db-110"> WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="772db-111">디버그 구성에서 빌드할 때 링커를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="772db-111">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="772db-112">링커를 사용하도록 설정하려면 앱이 릴리스 구성으로 빌드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="772db-112">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="772db-113">Blazor WebAssembly 앱을 배포할 때 릴리스에서 빌드하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="772db-113">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="772db-114">앱 연결은 크기에 맞게 최적화되지만 부작용이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="772db-114">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="772db-115">링커는 이 동적 동작에 대한 정보를 알지 못하고 일반적으로 런타임에서 리플렉션에 필요한 형식을 결정할 수 없기 때문에, 리플렉션 또는 관련 동적 기능을 사용하는 앱은 잘린 경우 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="772db-115">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="772db-116">이러한 앱을 잘라내려면 링커에서는 앱이 의존하는 코드 및 패키지 또는 프레임워크의 리플렉션에서 필요로 하는 형식에 대해 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="772db-116">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="772db-117">잘린 앱이 배포된 후 올바로 작동하도록 하려면 개발하는 동안 앱의 릴리스 빌드를 자주 테스트하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="772db-117">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="772db-118">다음 MSBuild 기능을 사용하여 Blazor 앱에 대한 연결을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="772db-118">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="772db-119">[MSBuild 속성](#control-linking-with-an-msbuild-property)을 사용하여 전역적으로 연결을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="772db-119">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="772db-120">[구성 파일](#control-linking-with-a-configuration-file)을 사용하여 어셈블리별로 연결을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="772db-120">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="772db-121">MSBuild 속성을 사용하여 연결 제어</span><span class="sxs-lookup"><span data-stu-id="772db-121">Control linking with an MSBuild property</span></span>

<span data-ttu-id="772db-122">앱이 `Release` 구성에서 빌드될 때 연결이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="772db-122">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="772db-123">이를 변경하려면 프로젝트 파일에서 `BlazorWebAssemblyEnableLinking` MSBuild 속성을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="772db-123">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="772db-124">구성 파일과의 연결 제어</span><span class="sxs-lookup"><span data-stu-id="772db-124">Control linking with a configuration file</span></span>

<span data-ttu-id="772db-125">XML 구성 파일을 제공하고 프로젝트 파일에서 해당 파일을 MSBuild 항목으로 지정하여 어셈블리별로 연결을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="772db-125">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="772db-126">*LinkerConfig.xml*:</span><span class="sxs-lookup"><span data-stu-id="772db-126">*LinkerConfig.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="772db-127">자세한 내용 및 예제는 [데이터 형식](https://github.com/mono/linker/blob/master/docs/data-formats.md)(mono/linker GitHub 리포지토리)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="772db-127">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="772db-128">라이브러리에 XML 링커 구성 파일 추가</span><span class="sxs-lookup"><span data-stu-id="772db-128">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="772db-129">특정 라이브러리의 링커를 구성하려면 XML 링커 구성 파일을 포함 리소스로 라이브러리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="772db-129">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="772db-130">포함 리소스의 이름은 어셈블리와 동일해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="772db-130">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="772db-131">다음 예제에서 *LinkerConfig.xml* 파일은 라이브러리의 어셈블리와 이름이 동일한 포함 리소스로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="772db-131">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="772db-132">국제화를 위한 링커 구성</span><span class="sxs-lookup"><span data-stu-id="772db-132">Configure the linker for internationalization</span></span>

<span data-ttu-id="772db-133">기본적으로 Blazor WebAssembly 앱에 대한 Blazor의 링커 구성은 명시적으로 요청된 로캘을 제외하고 국제화 정보를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="772db-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="772db-134">이 어셈블리를 제거하면 앱 크기를 최소화합니다.</span><span class="sxs-lookup"><span data-stu-id="772db-134">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="772db-135">유지되는 I18N 어셈블리 종류를 제어하려면 프로젝트 파일에서 `<BlazorWebAssemblyI18NAssemblies>` MSBuild 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="772db-135">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="772db-136">지역 값</span><span class="sxs-lookup"><span data-stu-id="772db-136">Region Value</span></span>     | <span data-ttu-id="772db-137">Mono 지역 어셈블리</span><span class="sxs-lookup"><span data-stu-id="772db-137">Mono region assembly</span></span>    |
| ---
<span data-ttu-id="772db-138">title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-138">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-140">'Blazor'</span></span>
- <span data-ttu-id="772db-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-141">'Identity'</span></span>
- <span data-ttu-id="772db-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-143">'Razor'</span></span>
- <span data-ttu-id="772db-144">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-144">'SignalR' uid:</span></span> 

-
<span data-ttu-id="772db-145">title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-145">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-147">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-147">'Blazor'</span></span>
- <span data-ttu-id="772db-148">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-148">'Identity'</span></span>
- <span data-ttu-id="772db-149">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-149">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-150">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-150">'Razor'</span></span>
- <span data-ttu-id="772db-151">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-151">'SignalR' uid:</span></span> 

-
<span data-ttu-id="772db-152">title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-152">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-154">'Blazor'</span></span>
- <span data-ttu-id="772db-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-155">'Identity'</span></span>
- <span data-ttu-id="772db-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-157">'Razor'</span></span>
- <span data-ttu-id="772db-158">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="772db-159">title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-159">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-161">'Blazor'</span></span>
- <span data-ttu-id="772db-162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-162">'Identity'</span></span>
- <span data-ttu-id="772db-163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-163">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-164">'Razor'</span></span>
- <span data-ttu-id="772db-165">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="772db-166">title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-166">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-168">'Blazor'</span></span>
- <span data-ttu-id="772db-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-169">'Identity'</span></span>
- <span data-ttu-id="772db-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-171">'Razor'</span></span>
- <span data-ttu-id="772db-172">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="772db-173">title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-173">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-175">'Blazor'</span></span>
- <span data-ttu-id="772db-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-176">'Identity'</span></span>
- <span data-ttu-id="772db-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-178">'Razor'</span></span>
- <span data-ttu-id="772db-179">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-179">'SignalR' uid:</span></span> 

<span data-ttu-id="772db-180">-------- | --- title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-180">-------- | --- title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-182">'Blazor'</span></span>
- <span data-ttu-id="772db-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-183">'Identity'</span></span>
- <span data-ttu-id="772db-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-185">'Razor'</span></span>
- <span data-ttu-id="772db-186">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="772db-187">title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-187">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-189">'Blazor'</span></span>
- <span data-ttu-id="772db-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-190">'Identity'</span></span>
- <span data-ttu-id="772db-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-192">'Razor'</span></span>
- <span data-ttu-id="772db-193">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="772db-194">title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-194">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-196">'Blazor'</span></span>
- <span data-ttu-id="772db-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-197">'Identity'</span></span>
- <span data-ttu-id="772db-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-199">'Razor'</span></span>
- <span data-ttu-id="772db-200">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="772db-201">title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-201">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-203">'Blazor'</span></span>
- <span data-ttu-id="772db-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-204">'Identity'</span></span>
- <span data-ttu-id="772db-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-206">'Razor'</span></span>
- <span data-ttu-id="772db-207">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="772db-208">title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-208">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-210">'Blazor'</span></span>
- <span data-ttu-id="772db-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-211">'Identity'</span></span>
- <span data-ttu-id="772db-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-213">'Razor'</span></span>
- <span data-ttu-id="772db-214">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="772db-215">title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-215">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-217">'Blazor'</span></span>
- <span data-ttu-id="772db-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-218">'Identity'</span></span>
- <span data-ttu-id="772db-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-220">'Razor'</span></span>
- <span data-ttu-id="772db-221">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="772db-222">title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-222">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-224">'Blazor'</span></span>
- <span data-ttu-id="772db-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-225">'Identity'</span></span>
- <span data-ttu-id="772db-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-227">'Razor'</span></span>
- <span data-ttu-id="772db-228">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="772db-229">title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-229">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-231">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-231">'Blazor'</span></span>
- <span data-ttu-id="772db-232">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-232">'Identity'</span></span>
- <span data-ttu-id="772db-233">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-233">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-234">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-234">'Razor'</span></span>
- <span data-ttu-id="772db-235">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-235">'SignalR' uid:</span></span> 

-
<span data-ttu-id="772db-236">title: 'ASP.NET Core Blazor용 링커 구성' author: description: 'Blazor 앱을 빌드할 때 IL(중간 언어) 링커를 제어하는 방법을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="772db-236">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="772db-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="772db-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="772db-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="772db-238">'Blazor'</span></span>
- <span data-ttu-id="772db-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="772db-239">'Identity'</span></span>
- <span data-ttu-id="772db-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="772db-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="772db-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="772db-241">'Razor'</span></span>
- <span data-ttu-id="772db-242">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="772db-242">'SignalR' uid:</span></span> 

<span data-ttu-id="772db-243">------------ | | `all`            | 모든 어셈블리 포함 | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none`(기본값) | 없음                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span><span class="sxs-lookup"><span data-stu-id="772db-243">------------ | | `all`            | All assemblies included | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (default) | None                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span></span>

<span data-ttu-id="772db-244">여러 값을 지정할 경우 쉼표를 사용하여 구분합니다(예: `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="772db-244">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="772db-245">자세한 내용은 [I18N: Pnetlib 국제화 프레임워크 라이브러리(mono/mono GitHub 리포지토리)](https://github.com/mono/mono/tree/master/mcs/class/I18N)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="772db-245">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="772db-246">추가 자료</span><span class="sxs-lookup"><span data-stu-id="772db-246">Additional resources</span></span>

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>
