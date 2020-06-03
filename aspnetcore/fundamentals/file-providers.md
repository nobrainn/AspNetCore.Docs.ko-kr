---
<span data-ttu-id="89fbd-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-102">'Blazor'</span></span>
- <span data-ttu-id="89fbd-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-103">'Identity'</span></span>
- <span data-ttu-id="89fbd-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-105">'Razor'</span></span>
- <span data-ttu-id="89fbd-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-106">'SignalR' uid:</span></span> 

---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="89fbd-107">ASP.NET Core의 파일 공급자</span><span class="sxs-lookup"><span data-stu-id="89fbd-107">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="89fbd-108">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="89fbd-108">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="89fbd-109">ASP.NET Core에서 파일 공급자를 사용하여 파일 시스템 액세스를 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-109">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="89fbd-110">파일 공급자는 ASP.NET Core 프레임워크 전체에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-110">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="89fbd-111">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="89fbd-111">For example:</span></span>

* <span data-ttu-id="89fbd-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>는 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root)와 [웹 루트](xref:fundamentals/index#web-root)를 `IFileProvider` 형식으로 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="89fbd-113">[정적 파일 미들웨어](xref:fundamentals/static-files)는 파일 공급자를 사용해서 정적 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-113">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="89fbd-114">[Razor](xref:mvc/views/razor)는 파일 공급자를 사용하여 페이지 및 뷰를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-114">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="89fbd-115">.NET Core 도구는 파일 공급자와 GLOB 패턴을 사용해서 게시해야 할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-115">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="89fbd-116">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="89fbd-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="89fbd-117">파일 공급자 인터페이스</span><span class="sxs-lookup"><span data-stu-id="89fbd-117">File Provider interfaces</span></span>

<span data-ttu-id="89fbd-118">기본 인터페이스는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>입니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-118">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="89fbd-119">`IFileProvider`는 다음을 수행하는 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-119">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="89fbd-120">파일 정보를 가져옵니다(<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="89fbd-120">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="89fbd-121">디렉터리 정보를 가져옵니다(<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="89fbd-121">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="89fbd-122">변경 알림을 설정합니다(<xref:Microsoft.Extensions.Primitives.IChangeToken> 사용).</span><span class="sxs-lookup"><span data-stu-id="89fbd-122">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="89fbd-123">`IFileInfo`는 파일 작업에 대한 메서드 및 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-123">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="89fbd-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(바이트)</span><span class="sxs-lookup"><span data-stu-id="89fbd-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="89fbd-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> 날짜</span><span class="sxs-lookup"><span data-stu-id="89fbd-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="89fbd-126"><xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> 메서드를 사용해서 파일을 읽을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-126">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="89fbd-127">*FileProviderSample* 샘플 앱은 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱 전체에서 사용하기 위해 `Startup.ConfigureServices`에서 파일 공급자를 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-127">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="89fbd-128">파일 공급자 구현</span><span class="sxs-lookup"><span data-stu-id="89fbd-128">File Provider implementations</span></span>

<span data-ttu-id="89fbd-129">다음 표에서는 `IFileProvider`의 구현을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-129">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="89fbd-130">구현</span><span class="sxs-lookup"><span data-stu-id="89fbd-130">Implementation</span></span> | <span data-ttu-id="89fbd-131">설명</span><span class="sxs-lookup"><span data-stu-id="89fbd-131">Description</span></span> |
| ---
<span data-ttu-id="89fbd-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-133">'Blazor'</span></span>
- <span data-ttu-id="89fbd-134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-134">'Identity'</span></span>
- <span data-ttu-id="89fbd-135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-135">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-136">'Razor'</span></span>
- <span data-ttu-id="89fbd-137">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-139">'Blazor'</span></span>
- <span data-ttu-id="89fbd-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-140">'Identity'</span></span>
- <span data-ttu-id="89fbd-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-142">'Razor'</span></span>
- <span data-ttu-id="89fbd-143">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-145">'Blazor'</span></span>
- <span data-ttu-id="89fbd-146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-146">'Identity'</span></span>
- <span data-ttu-id="89fbd-147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-147">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-148">'Razor'</span></span>
- <span data-ttu-id="89fbd-149">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-151">'Blazor'</span></span>
- <span data-ttu-id="89fbd-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-152">'Identity'</span></span>
- <span data-ttu-id="89fbd-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-154">'Razor'</span></span>
- <span data-ttu-id="89fbd-155">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-157">'Blazor'</span></span>
- <span data-ttu-id="89fbd-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-158">'Identity'</span></span>
- <span data-ttu-id="89fbd-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-160">'Razor'</span></span>
- <span data-ttu-id="89fbd-161">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-161">'SignalR' uid:</span></span> 

<span data-ttu-id="89fbd-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-163">'Blazor'</span></span>
- <span data-ttu-id="89fbd-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-164">'Identity'</span></span>
- <span data-ttu-id="89fbd-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-166">'Razor'</span></span>
- <span data-ttu-id="89fbd-167">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-169">'Blazor'</span></span>
- <span data-ttu-id="89fbd-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-170">'Identity'</span></span>
- <span data-ttu-id="89fbd-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-172">'Razor'</span></span>
- <span data-ttu-id="89fbd-173">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-175">'Blazor'</span></span>
- <span data-ttu-id="89fbd-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-176">'Identity'</span></span>
- <span data-ttu-id="89fbd-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-178">'Razor'</span></span>
- <span data-ttu-id="89fbd-179">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-179">'SignalR' uid:</span></span> 

<span data-ttu-id="89fbd-180">------ | | [CompositeFileProvider](#compositefileprovider) | 하나 이상의 개별 공급자로부터 얻어진 파일 및 디렉터리에 대한 결합된 액세스를 제공하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-180">------ | | [CompositeFileProvider](#compositefileprovider) | Used to provide combined access to files and directories from one or more other providers.</span></span> <span data-ttu-id="89fbd-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | 어셈블리에 포함된 파일에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Used to access files embedded in assemblies.</span></span> <span data-ttu-id="89fbd-182">| | [PhysicalFileProvider](#physicalfileprovider) |시스템의 물리적 파일에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-182">| | [PhysicalFileProvider](#physicalfileprovider) | Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="89fbd-183">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="89fbd-183">PhysicalFileProvider</span></span>

<span data-ttu-id="89fbd-184"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 는 실제 파일 시스템에 대한 접근을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-184">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="89fbd-185">`PhysicalFileProvider`는 <xref:System.IO.File?displayProperty=fullName> 형식(물리적 공급자에 대해)을 사용하고 디렉터리와 그 하위 자식에 대한 모든 경로의 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-185">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="89fbd-186">이 범위 지정은 지정된 디렉터리와 그 하위 자식 이외의 파일 시스템에 대한 액세스를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-186">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="89fbd-187">가장 공통적인 `PhysicalFileProvider` 생성 및 사용 시나리오는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 생성자 내에 `IFileProvider`를 요청하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-187">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="89fbd-188">이 공급자를 직접 인스턴스화하는 경우 절대 디렉터리 경로가 필요합니다. 이 경로는 공급자를 사용하여 수행한 모든 요청에 대한 기본 경로로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-188">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="89fbd-189">디렉터리 경로에서는 GLOB 패턴이 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-189">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="89fbd-190">다음 코드는 `PhysicalFileProvider`를 사용하여 디렉터리 콘텐츠와 파일 정보를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-190">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="89fbd-191">이전 예제의 형식:</span><span class="sxs-lookup"><span data-stu-id="89fbd-191">Types in the preceding example:</span></span>

* <span data-ttu-id="89fbd-192">`provider`는 `IFileProvider`입니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-192">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="89fbd-193">`contents`는 `IDirectoryContents`입니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-193">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="89fbd-194">`fileInfo`는 `IFileInfo`입니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-194">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="89fbd-195">파일 공급자는 `applicationRoot`로 지정된 디렉터리를 반복하거나 `GetFileInfo`를 호출하여 파일 정보를 가져오는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-195">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="89fbd-196">GLOB 패턴은 `GetFileInfo` 메서드에 전달할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-196">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="89fbd-197">파일 공급자는 `applicationRoot` 디렉터리 외에는 액세스 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-197">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="89fbd-198">*FileProviderSample* 샘플 앱은 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>를 사용하여 `Startup.ConfigureServices` 메서드에 공급자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-198">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="89fbd-199">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="89fbd-199">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="89fbd-200"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider>는 어셈블리에 포함된 파일에 접근하기 위한 용도로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-200">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="89fbd-201">`ManifestEmbeddedFileProvider`는 어셈블리로 컴파일된 매니페스트를 사용하여 포함된 파일의 원래 경로를 재구성합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-201">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="89fbd-202">포함된 파일의 매니페스트를 생성하려면</span><span class="sxs-lookup"><span data-stu-id="89fbd-202">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="89fbd-203">프로젝트에 [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-203">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="89fbd-204">`<GenerateEmbeddedFilesManifest>` 속성을 `true`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-204">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="89fbd-205">[\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)에 포함할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-205">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="89fbd-206">[GLOB 패턴](#glob-patterns)을 사용하여 어셈블리에 포함할 파일을 하나 이상 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-206">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="89fbd-207">*FileProviderSample* 샘플 앱은 `ManifestEmbeddedFileProvider`를 만들고 현재 실행 중인 어셈블리를 생성자에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-207">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="89fbd-208">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="89fbd-208">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="89fbd-209">추가 오버로드를 사용하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-209">Additional overloads allow you to:</span></span>

* <span data-ttu-id="89fbd-210">상대 파일 경로 지정</span><span class="sxs-lookup"><span data-stu-id="89fbd-210">Specify a relative file path.</span></span>
* <span data-ttu-id="89fbd-211">마지막으로 수정한 날짜로 파일의 범위 지정</span><span class="sxs-lookup"><span data-stu-id="89fbd-211">Scope files to a last modified date.</span></span>
* <span data-ttu-id="89fbd-212">포함된 파일 매니페스트를 포함하는 포함 리소스의 이름 지정</span><span class="sxs-lookup"><span data-stu-id="89fbd-212">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="89fbd-213">오버로드</span><span class="sxs-lookup"><span data-stu-id="89fbd-213">Overload</span></span> | <span data-ttu-id="89fbd-214">설명</span><span class="sxs-lookup"><span data-stu-id="89fbd-214">Description</span></span> |
| ---
<span data-ttu-id="89fbd-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-216">'Blazor'</span></span>
- <span data-ttu-id="89fbd-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-217">'Identity'</span></span>
- <span data-ttu-id="89fbd-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-219">'Razor'</span></span>
- <span data-ttu-id="89fbd-220">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-222">'Blazor'</span></span>
- <span data-ttu-id="89fbd-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-223">'Identity'</span></span>
- <span data-ttu-id="89fbd-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-225">'Razor'</span></span>
- <span data-ttu-id="89fbd-226">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-226">'SignalR' uid:</span></span> 

<span data-ttu-id="89fbd-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-228">'Blazor'</span></span>
- <span data-ttu-id="89fbd-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-229">'Identity'</span></span>
- <span data-ttu-id="89fbd-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-231">'Razor'</span></span>
- <span data-ttu-id="89fbd-232">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-234">'Blazor'</span></span>
- <span data-ttu-id="89fbd-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-235">'Identity'</span></span>
- <span data-ttu-id="89fbd-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-237">'Razor'</span></span>
- <span data-ttu-id="89fbd-238">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-240">'Blazor'</span></span>
- <span data-ttu-id="89fbd-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-241">'Identity'</span></span>
- <span data-ttu-id="89fbd-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-243">'Razor'</span></span>
- <span data-ttu-id="89fbd-244">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-244">'SignalR' uid:</span></span> 

<span data-ttu-id="89fbd-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | 선택적인 `root` 상대 경로 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="89fbd-246">`root`를 지정하여 <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*>에 대한 호출의 범위를 제공된 경로의 해당 리소스로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-246">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="89fbd-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | 선택적인 `root` 상대 경로 매개 변수 및 `lastModified` 날짜(<xref:System.DateTimeOffset>) 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="89fbd-248">`lastModified` 날짜는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>가 반환한 <xref:Microsoft.Extensions.FileProviders.IFileInfo> 인스턴스에 대한 마지막 수정 날짜의 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-248">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="89fbd-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | 선택적인 `root` 상대 경로, `lastModified` 날짜 및 `manifestName` 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="89fbd-250">`manifestName`은 매니페스트가 포함된 포함 리소스의 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-250">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="89fbd-251">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="89fbd-251">CompositeFileProvider</span></span>

<span data-ttu-id="89fbd-252"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> 는 `IFileProvider` 의 인스턴스들을 결합해서 다수의 공급자를 이용한 파일 작업을 처리할 수 있는 단일 인터페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-252">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="89fbd-253">`CompositeFileProvider`를 생성할 때는 생성자에 하나 이상의 `IFileProvider` 인스턴스를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-253">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="89fbd-254">*FileProviderSample* 샘플 앱에서 `PhysicalFileProvider`와 `ManifestEmbeddedFileProvider`는 앱의 서비스 컨테이너에 등록된 `CompositeFileProvider`에 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-254">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="89fbd-255">프로젝트의 `Startup.ConfigureServices` 메서드에는 다음과 같은 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-255">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="89fbd-256">변경 내용 관찰</span><span class="sxs-lookup"><span data-stu-id="89fbd-256">Watch for changes</span></span>

<span data-ttu-id="89fbd-257"><xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> 메서드는 하나 이상의 파일 또는 디렉터리의 변경 사항을 살펴보는 시나리오를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-257">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="89fbd-258">`Watch` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-258">The `Watch` method:</span></span>

* <span data-ttu-id="89fbd-259">파일 경로 문자열을 받습니다. 이 문자열은 [GLOB 패턴](#glob-patterns)을 사용하여 여러 파일을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-259">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="89fbd-260"><xref:Microsoft.Extensions.Primitives.IChangeToken>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-260">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="89fbd-261">결과 변경 토큰은 다음을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-261">The resulting change token exposes:</span></span>

* <span data-ttu-id="89fbd-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: 변경이 발생했는지 확인하기 위해 검사할 수 있는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="89fbd-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: 지정한 경로 문자열에 대한 변경 내용이 검색되면 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="89fbd-264">각 변경 토큰은 단일 변경에 대한 응답으로 자신과 연결된 콜백만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-264">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="89fbd-265">모니터링을 지속적으로 수행하기 위해서는 다음 예처럼 <xref:System.Threading.Tasks.TaskCompletionSource`1>를 사용하거나 변경 사항에 대한 응답에서 `IChangeToken` 인스턴스를 다시 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-265">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="89fbd-266">*WatchConsole* 샘플 앱은 *TextFiles* 디렉터리의 *.txt* 파일이 수정될 때마다 메시지를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-266">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="89fbd-267">Docker 컨테이너나 네트워크 공유 같은 일부 파일 시스템은 변경 알림을 안정적으로 전송할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-267">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="89fbd-268">`DOTNET_USE_POLLING_FILE_WATCHER` 환경 변수를 `1` 또는 `true`로 설정하여 변경에 대해 파일 시스템을 4초마다 폴링합니다(구성할 수 없음).</span><span class="sxs-lookup"><span data-stu-id="89fbd-268">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="89fbd-269">GLOB 패턴</span><span class="sxs-lookup"><span data-stu-id="89fbd-269">Glob patterns</span></span>

<span data-ttu-id="89fbd-270">파일 시스템 경로는 *GLOB(또는 와일드카드 사용) 패턴*이라고 부르는 와일드카드 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-270">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="89fbd-271">이러한 패턴을 사용하여 파일 그룹을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-271">Specify groups of files with these patterns.</span></span> <span data-ttu-id="89fbd-272">두 개의 와일드카드 문자는 `*`와 `**`입니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-272">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="89fbd-273">현재 폴더 수준의 모든 항목, 모든 파일명 또는 모든 파일 확장자를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-273">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="89fbd-274">파일 경로의 `/` 및 `.` 문자에 의해서 일치가 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-274">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="89fbd-275">여러 디렉터리 수준에서 모든 것을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-275">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="89fbd-276">디렉터리 계층 구조 내의 여러 파일과 일치시키는 데 재귀적으로 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-276">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="89fbd-277">다음 표에서는 GLOB 패턴의 일반적인 예를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-277">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="89fbd-278">무늬</span><span class="sxs-lookup"><span data-stu-id="89fbd-278">Pattern</span></span>  |<span data-ttu-id="89fbd-279">설명</span><span class="sxs-lookup"><span data-stu-id="89fbd-279">Description</span></span>  |
|---
<span data-ttu-id="89fbd-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-281">'Blazor'</span></span>
- <span data-ttu-id="89fbd-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-282">'Identity'</span></span>
- <span data-ttu-id="89fbd-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-284">'Razor'</span></span>
- <span data-ttu-id="89fbd-285">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-287">'Blazor'</span></span>
- <span data-ttu-id="89fbd-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-288">'Identity'</span></span>
- <span data-ttu-id="89fbd-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-290">'Razor'</span></span>
- <span data-ttu-id="89fbd-291">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-291">'SignalR' uid:</span></span> 

<span data-ttu-id="89fbd-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-293">'Blazor'</span></span>
- <span data-ttu-id="89fbd-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-294">'Identity'</span></span>
- <span data-ttu-id="89fbd-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-296">'Razor'</span></span>
- <span data-ttu-id="89fbd-297">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-299">'Blazor'</span></span>
- <span data-ttu-id="89fbd-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-300">'Identity'</span></span>
- <span data-ttu-id="89fbd-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-302">'Razor'</span></span>
- <span data-ttu-id="89fbd-303">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-303">'SignalR' uid:</span></span> 

<span data-ttu-id="89fbd-304">-----|
|`directory/file.txt`|특정 디렉터리의 특정 파일과 일치합니다.| |`directory/*.txt`|특정 디렉터리에서 확장명이 *.txt*인 모든 파일과 일치합니다.| |`directory/*/appsettings.json`|*directory* 폴더의 한 수준 아래 디렉터리의 모든 *appsettings.json* 파일과 정확히 일치합니다.| |`directory/**/*.txt`|*directory* 폴더 아래의 모든 위치에서 발견되는 모든 *.txt* 파일과 일치합니다.|</span><span class="sxs-lookup"><span data-stu-id="89fbd-304">-----|
|`directory/file.txt`|Matches a specific file in a specific directory.| |`directory/*.txt`|Matches all files with *.txt* extension in a specific directory.| |`directory/*/appsettings.json`|Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.| |`directory/**/*.txt`|Matches all files with a *.txt* extension found anywhere under the *directory* folder.|</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="89fbd-305">ASP.NET Core에서 파일 공급자를 사용하여 파일 시스템 액세스를 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-305">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="89fbd-306">파일 공급자는 ASP.NET Core 프레임워크 전체에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-306">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="89fbd-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>는 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root)와 [웹 루트](xref:fundamentals/index#web-root)를 `IFileProvider` 형식으로 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="89fbd-308">[정적 파일 미들웨어](xref:fundamentals/static-files)는 파일 공급자를 사용해서 정적 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-308">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="89fbd-309">[Razor](xref:mvc/views/razor)는 파일 공급자를 사용하여 페이지 및 뷰를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-309">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="89fbd-310">.NET Core 도구는 파일 공급자와 GLOB 패턴을 사용해서 게시해야 할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-310">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="89fbd-311">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="89fbd-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="89fbd-312">파일 공급자 인터페이스</span><span class="sxs-lookup"><span data-stu-id="89fbd-312">File Provider interfaces</span></span>

<span data-ttu-id="89fbd-313">기본 인터페이스는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>입니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-313">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="89fbd-314">`IFileProvider`는 다음을 수행하는 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-314">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="89fbd-315">파일 정보를 가져옵니다(<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="89fbd-315">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="89fbd-316">디렉터리 정보를 가져옵니다(<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="89fbd-316">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="89fbd-317">변경 알림을 설정합니다(<xref:Microsoft.Extensions.Primitives.IChangeToken> 사용).</span><span class="sxs-lookup"><span data-stu-id="89fbd-317">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="89fbd-318">`IFileInfo`는 파일 작업에 대한 메서드 및 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-318">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="89fbd-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(바이트)</span><span class="sxs-lookup"><span data-stu-id="89fbd-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="89fbd-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> 날짜</span><span class="sxs-lookup"><span data-stu-id="89fbd-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="89fbd-321">[IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) 메서드를 사용하여 파일에서 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-321">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="89fbd-322">샘플 앱은 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱 전체에서 사용하기 위해 `Startup.ConfigureServices`에 파일 공급자를 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-322">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="89fbd-323">파일 공급자 구현</span><span class="sxs-lookup"><span data-stu-id="89fbd-323">File Provider implementations</span></span>

<span data-ttu-id="89fbd-324">`IFileProvider`의 세 가지 구현을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-324">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="89fbd-325">구현</span><span class="sxs-lookup"><span data-stu-id="89fbd-325">Implementation</span></span> | <span data-ttu-id="89fbd-326">설명</span><span class="sxs-lookup"><span data-stu-id="89fbd-326">Description</span></span> |
| ---
<span data-ttu-id="89fbd-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-328">'Blazor'</span></span>
- <span data-ttu-id="89fbd-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-329">'Identity'</span></span>
- <span data-ttu-id="89fbd-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-331">'Razor'</span></span>
- <span data-ttu-id="89fbd-332">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-334">'Blazor'</span></span>
- <span data-ttu-id="89fbd-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-335">'Identity'</span></span>
- <span data-ttu-id="89fbd-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-337">'Razor'</span></span>
- <span data-ttu-id="89fbd-338">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-340">'Blazor'</span></span>
- <span data-ttu-id="89fbd-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-341">'Identity'</span></span>
- <span data-ttu-id="89fbd-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-343">'Razor'</span></span>
- <span data-ttu-id="89fbd-344">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-346">'Blazor'</span></span>
- <span data-ttu-id="89fbd-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-347">'Identity'</span></span>
- <span data-ttu-id="89fbd-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-349">'Razor'</span></span>
- <span data-ttu-id="89fbd-350">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-352">'Blazor'</span></span>
- <span data-ttu-id="89fbd-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-353">'Identity'</span></span>
- <span data-ttu-id="89fbd-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-355">'Razor'</span></span>
- <span data-ttu-id="89fbd-356">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-356">'SignalR' uid:</span></span> 

<span data-ttu-id="89fbd-357">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-357">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-358">'Blazor'</span></span>
- <span data-ttu-id="89fbd-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-359">'Identity'</span></span>
- <span data-ttu-id="89fbd-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-361">'Razor'</span></span>
- <span data-ttu-id="89fbd-362">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-364">'Blazor'</span></span>
- <span data-ttu-id="89fbd-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-365">'Identity'</span></span>
- <span data-ttu-id="89fbd-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-367">'Razor'</span></span>
- <span data-ttu-id="89fbd-368">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-370">'Blazor'</span></span>
- <span data-ttu-id="89fbd-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-371">'Identity'</span></span>
- <span data-ttu-id="89fbd-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-373">'Razor'</span></span>
- <span data-ttu-id="89fbd-374">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-374">'SignalR' uid:</span></span> 

<span data-ttu-id="89fbd-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | 물리적 공급자는 시스템의 물리적 파일에 액세스하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | The physical provider is used to access the system's physical files.</span></span> <span data-ttu-id="89fbd-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | 매니페스트 포함 공급자는 어셈블리에 포함된 파일에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | The manifest embedded provider is used to access files embedded in assemblies.</span></span> <span data-ttu-id="89fbd-377">| | [CompositeFileProvider](#compositefileprovider) | 복합 공급자는 하나 이상의 개별 공급자로부터 얻어진 파일 및 디렉터리에 대한 결합된 액세스를 접근을 지원하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-377">| | [CompositeFileProvider](#compositefileprovider) | The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="89fbd-378">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="89fbd-378">PhysicalFileProvider</span></span>

<span data-ttu-id="89fbd-379"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 는 실제 파일 시스템에 대한 접근을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-379">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="89fbd-380">`PhysicalFileProvider`는 <xref:System.IO.File?displayProperty=fullName> 형식(물리적 공급자에 대해)을 사용하고 디렉터리와 그 하위 자식에 대한 모든 경로의 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-380">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="89fbd-381">이 범위 지정은 지정된 디렉터리와 그 하위 자식 이외의 파일 시스템에 대한 액세스를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-381">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="89fbd-382">가장 공통적인 `PhysicalFileProvider` 생성 및 사용 시나리오는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 생성자 내에 `IFileProvider`를 요청하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-382">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="89fbd-383">이 공급자를 직접 인스턴스화하는 경우 디렉터리 경로가 필요하며, 공급자를 사용하여 수행한 모든 요청에 대한 기본 경로로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-383">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="89fbd-384">다음 코드는 `PhysicalFileProvider`를 생성하고 이를 사용하여 디렉터리 콘텐츠 및 파일 정보를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-384">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="89fbd-385">이전 예제의 형식:</span><span class="sxs-lookup"><span data-stu-id="89fbd-385">Types in the preceding example:</span></span>

* <span data-ttu-id="89fbd-386">`provider`는 `IFileProvider`입니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-386">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="89fbd-387">`contents`는 `IDirectoryContents`입니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-387">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="89fbd-388">`fileInfo`는 `IFileInfo`입니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-388">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="89fbd-389">파일 공급자는 `applicationRoot`로 지정된 디렉터리를 반복하거나 `GetFileInfo`를 호출하여 파일 정보를 가져오는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-389">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="89fbd-390">파일 공급자는 `applicationRoot` 디렉터리 외에는 액세스 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-390">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="89fbd-391">샘플 앱은 [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider)를 사용하여 앱의 `Startup.ConfigureServices` 클래스에 공급자를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-391">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="89fbd-392">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="89fbd-392">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="89fbd-393"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider>는 어셈블리에 포함된 파일에 접근하기 위한 용도로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-393">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="89fbd-394">`ManifestEmbeddedFileProvider`는 어셈블리로 컴파일된 매니페스트를 사용하여 포함된 파일의 원래 경로를 재구성합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-394">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="89fbd-395">포함된 파일의 매니페스트를 생성하려면 `<GenerateEmbeddedFilesManifest>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-395">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="89fbd-396">[&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)를 사용하여 포함할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-396">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="89fbd-397">[GLOB 패턴](#glob-patterns)을 사용하여 어셈블리에 포함할 파일을 하나 이상 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-397">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="89fbd-398">샘플 앱은 `ManifestEmbeddedFileProvider`를 생성하고 현재 실행 중인 어셈블리를 생성자에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-398">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="89fbd-399">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="89fbd-399">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="89fbd-400">추가 오버로드를 사용하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-400">Additional overloads allow you to:</span></span>

* <span data-ttu-id="89fbd-401">상대 파일 경로 지정</span><span class="sxs-lookup"><span data-stu-id="89fbd-401">Specify a relative file path.</span></span>
* <span data-ttu-id="89fbd-402">마지막으로 수정한 날짜로 파일의 범위 지정</span><span class="sxs-lookup"><span data-stu-id="89fbd-402">Scope files to a last modified date.</span></span>
* <span data-ttu-id="89fbd-403">포함된 파일 매니페스트를 포함하는 포함 리소스의 이름 지정</span><span class="sxs-lookup"><span data-stu-id="89fbd-403">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="89fbd-404">오버로드</span><span class="sxs-lookup"><span data-stu-id="89fbd-404">Overload</span></span> | <span data-ttu-id="89fbd-405">설명</span><span class="sxs-lookup"><span data-stu-id="89fbd-405">Description</span></span> |
| ---
<span data-ttu-id="89fbd-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-407">'Blazor'</span></span>
- <span data-ttu-id="89fbd-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-408">'Identity'</span></span>
- <span data-ttu-id="89fbd-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-410">'Razor'</span></span>
- <span data-ttu-id="89fbd-411">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-413">'Blazor'</span></span>
- <span data-ttu-id="89fbd-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-414">'Identity'</span></span>
- <span data-ttu-id="89fbd-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-416">'Razor'</span></span>
- <span data-ttu-id="89fbd-417">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-417">'SignalR' uid:</span></span> 

<span data-ttu-id="89fbd-418">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-418">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-419">'Blazor'</span></span>
- <span data-ttu-id="89fbd-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-420">'Identity'</span></span>
- <span data-ttu-id="89fbd-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-422">'Razor'</span></span>
- <span data-ttu-id="89fbd-423">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-424">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-424">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-425">'Blazor'</span></span>
- <span data-ttu-id="89fbd-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-426">'Identity'</span></span>
- <span data-ttu-id="89fbd-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-428">'Razor'</span></span>
- <span data-ttu-id="89fbd-429">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="89fbd-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="89fbd-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="89fbd-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-431">'Blazor'</span></span>
- <span data-ttu-id="89fbd-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89fbd-432">'Identity'</span></span>
- <span data-ttu-id="89fbd-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89fbd-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="89fbd-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89fbd-434">'Razor'</span></span>
- <span data-ttu-id="89fbd-435">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="89fbd-435">'SignalR' uid:</span></span> 

<span data-ttu-id="89fbd-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | 선택적인 `root` 상대 경로 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="89fbd-437">`root`를 지정하여 <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*>에 대한 호출의 범위를 제공된 경로의 해당 리소스로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-437">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="89fbd-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | 선택적인 `root` 상대 경로 매개 변수 및 `lastModified` 날짜(<xref:System.DateTimeOffset>) 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="89fbd-439">`lastModified` 날짜는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>가 반환한 <xref:Microsoft.Extensions.FileProviders.IFileInfo> 인스턴스에 대한 마지막 수정 날짜의 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-439">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="89fbd-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | 선택적인 `root` 상대 경로, `lastModified` 날짜 및 `manifestName` 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="89fbd-441">`manifestName`은 매니페스트가 포함된 포함 리소스의 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-441">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="89fbd-442">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="89fbd-442">CompositeFileProvider</span></span>

<span data-ttu-id="89fbd-443"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> 는 `IFileProvider` 의 인스턴스들을 결합해서 다수의 공급자를 이용한 파일 작업을 처리할 수 있는 단일 인터페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-443">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="89fbd-444">`CompositeFileProvider`를 생성할 때는 생성자에 하나 이상의 `IFileProvider` 인스턴스를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-444">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="89fbd-445">샘플 앱에서 `PhysicalFileProvider` 및 `ManifestEmbeddedFileProvider`는 앱의 서비스 컨테이너에 등록된 `CompositeFileProvider`에 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-445">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="89fbd-446">변경 내용 관찰</span><span class="sxs-lookup"><span data-stu-id="89fbd-446">Watch for changes</span></span>

<span data-ttu-id="89fbd-447">[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 메서드는 하나 이상의 파일 또는 디렉터리의 변경 내용을 관찰하는 시나리오를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-447">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="89fbd-448">`Watch`는 [GLOB 패턴](#glob-patterns)을 사용하여 여러 파일을 지정할 수 있는 경로 문자열을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-448">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="89fbd-449">`Watch`는 <xref:Microsoft.Extensions.Primitives.IChangeToken>을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-449">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="89fbd-450">변경 토큰은 다음을 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-450">The change token exposes:</span></span>

* <span data-ttu-id="89fbd-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: 변경이 발생했는지 확인하기 위해 검사할 수 있는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="89fbd-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: 지정한 경로 문자열에 대한 변경 내용이 검색되면 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="89fbd-453">각 변경 토큰은 단일 변경에 대한 응답으로 자신과 연결된 콜백만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-453">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="89fbd-454">모니터링을 지속적으로 수행하기 위해서는 다음 예처럼 <xref:System.Threading.Tasks.TaskCompletionSource`1>를 사용하거나 변경 사항에 대한 응답에서 `IChangeToken` 인스턴스를 다시 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-454">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="89fbd-455">샘플 앱에서 *WatchConsole* 콘솔 앱은 텍스트 파일이 수정될 때마다 메시지를 표시하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-455">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="89fbd-456">Docker 컨테이너나 네트워크 공유 같은 일부 파일 시스템은 변경 알림을 안정적으로 전송할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-456">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="89fbd-457">`DOTNET_USE_POLLING_FILE_WATCHER` 환경 변수를 `1` 또는 `true`로 설정하여 변경에 대해 파일 시스템을 4초마다 폴링합니다(구성할 수 없음).</span><span class="sxs-lookup"><span data-stu-id="89fbd-457">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="89fbd-458">GLOB 패턴</span><span class="sxs-lookup"><span data-stu-id="89fbd-458">Glob patterns</span></span>

<span data-ttu-id="89fbd-459">파일 시스템 경로는 *GLOB(또는 와일드카드 사용) 패턴*이라고 부르는 와일드카드 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-459">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="89fbd-460">이러한 패턴을 사용하여 파일 그룹을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-460">Specify groups of files with these patterns.</span></span> <span data-ttu-id="89fbd-461">두 개의 와일드카드 문자는 `*`와 `**`입니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-461">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="89fbd-462">현재 폴더 수준의 모든 항목, 모든 파일명 또는 모든 파일 확장자를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-462">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="89fbd-463">파일 경로의 `/` 및 `.` 문자에 의해서 일치가 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-463">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="89fbd-464">여러 디렉터리 수준에서 모든 것을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-464">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="89fbd-465">디렉터리 계층 구조 내의 여러 파일과 일치시키는 데 재귀적으로 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-465">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="89fbd-466">**GLOB 패턴 예제**</span><span class="sxs-lookup"><span data-stu-id="89fbd-466">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="89fbd-467">특정 디렉터리에 있는 특정 파일을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-467">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="89fbd-468">특정 디렉터리에서 확장명이 *.txt*인 파일을 모두 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-468">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="89fbd-469">*directory* 폴더보다 정확히 한 수준 아래의 디렉터리에서 모든 `appsettings.json` 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-469">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="89fbd-470">*directory* 폴더 아래의 모든 곳에서 찾은 확장명이 *.txt*인 파일을 모두 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="89fbd-470">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
