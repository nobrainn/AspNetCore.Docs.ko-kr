---
<span data-ttu-id="9182f-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-102">'Blazor'</span></span>
- <span data-ttu-id="9182f-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-103">'Identity'</span></span>
- <span data-ttu-id="9182f-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-105">'Razor'</span></span>
- <span data-ttu-id="9182f-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="9182f-107">ASP.NET Core의 구성</span><span class="sxs-lookup"><span data-stu-id="9182f-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="9182f-108">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="9182f-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9182f-109">ASP.NET Core에서 구성은 하나 이상의 [구성 공급자](#cp)를 사용하여 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="9182f-110">구성 공급자는 다음과 같은 다양한 구성 소스를 사용하여 키-값 쌍에서 구성 데이터를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="9182f-111">설정 파일(예: *appsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="9182f-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="9182f-112">환경 변수</span><span class="sxs-lookup"><span data-stu-id="9182f-112">Environment variables</span></span>
* <span data-ttu-id="9182f-113">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9182f-113">Azure Key Vault</span></span>
* <span data-ttu-id="9182f-114">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="9182f-114">Azure App Configuration</span></span>
* <span data-ttu-id="9182f-115">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="9182f-115">Command-line arguments</span></span>
* <span data-ttu-id="9182f-116">설치되거나 만들어진 사용자 지정 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="9182f-117">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="9182f-117">Directory files</span></span>
* <span data-ttu-id="9182f-118">메모리 내 .NET 개체</span><span class="sxs-lookup"><span data-stu-id="9182f-118">In-memory .NET objects</span></span>

<span data-ttu-id="9182f-119">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9182f-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="9182f-120">기본 구성</span><span class="sxs-lookup"><span data-stu-id="9182f-120">Default configuration</span></span>

<span data-ttu-id="9182f-121">[dotnet new](/dotnet/core/tools/dotnet-new) 또는 Visual Studio를 사용하여 만든 ASP.NET Core 웹앱은 다음 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="9182f-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>는 다음 순서로 앱에 대한 기본 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="9182f-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource):  기존 `IConfiguration`을 소스로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="9182f-124">기본 구성 사례에서는 [호스트](#hvac) 구성을 추가하고 앱 구성의 첫 번째 소스로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="9182f-125">[JSON 구성 공급자](#file-configuration-provider)를 사용하는 [appsettings.json](#appsettingsjson)</span><span class="sxs-lookup"><span data-stu-id="9182f-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="9182f-126">[JSON 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="9182f-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="9182f-127">예: *appsettings*.***Production***.*json* 및 *appsettings*.***Development***.*json*</span><span class="sxs-lookup"><span data-stu-id="9182f-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="9182f-128">앱이 `Development` 환경에서 실행되는 경우 [앱 비밀](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="9182f-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="9182f-129">[환경 변수 구성 공급자](#evcp)를 사용하는 환경 변수</span><span class="sxs-lookup"><span data-stu-id="9182f-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="9182f-130">[명령줄 구성 공급자](#command-line)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="9182f-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="9182f-131">나중에 추가된 구성 공급자는 이전 키 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="9182f-132">예를 들어 `MyKey`가 *appsettings.json*과 환경 모두에서 설정된 경우 환경 값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="9182f-133">기본 구성 공급자를 사용하여 [명령줄 구성 공급자](#command-line-configuration-provider)는 다른 모든 공급자를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="9182f-134">`CreateDefaultBuilder`에 대한 자세한 내용은 [기본 작성기 설정](xref:fundamentals/host/generic-host#default-builder-settings)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="9182f-135">다음 코드는 추가된 순서대로 사용하도록 설정된 구성 공급자를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="9182f-136">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="9182f-136">appsettings.json</span></span>

<span data-ttu-id="9182f-137">다음 *appsettings.json* 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="9182f-138">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)의 다음 코드는 위의 몇 가지 구성 설정을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="9182f-139">기본 <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>는 다음과 같은 순서로 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="9182f-140">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="9182f-140">*appsettings.json*</span></span>
1. <span data-ttu-id="9182f-141">*appsettings.* `Environment` *.json*: *appsettings*.***Production***.*json* 및 *appsettings*.***Development***.*json* 파일을 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="9182f-142">파일의 환경 버전은 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="9182f-143">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="9182f-144">*appsettings*.`Environment`.*json* 값은 *appsettings.json*의 키를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="9182f-145">예를 들어 기본적으로 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-145">For example, by default:</span></span>

* <span data-ttu-id="9182f-146">개발 환경에서는 *appsettings*.***Development***.*json* 구성이 *appsettings.json*에서 찾은 값을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="9182f-147">프로덕션 환경에서는 *appsettings*.***Production***.*json* 구성이 *appsettings.json*에서 찾은 값을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="9182f-148">Azure에 앱을 배포하는 경우를 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="9182f-149">옵션 패턴을 사용하여 계층적 구성 데이터 바인딩</span><span class="sxs-lookup"><span data-stu-id="9182f-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="9182f-150">[기본](#default) 구성을 사용하여 *appsettings.json* 및 *appsettings.* `Environment` *.json* 파일은 [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75)를 통해 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="9182f-151">앱이 시작된 후 *appsettings.json* 및 *appsettings.* `Environment` *.json* 파일에 대한 변경 내용을 [JSON 구성 공급자](#jcp)에서 읽습니다.\*\*\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="9182f-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="9182f-152">추가 JSON 구성 파일 추가에 대한 자세한 내용은 이 문서의 [JSON 구성 공급자](#jcp)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="9182f-153">보안 및 비밀 관리자</span><span class="sxs-lookup"><span data-stu-id="9182f-153">Security and secret manager</span></span>

<span data-ttu-id="9182f-154">구성 데이터 지침:</span><span class="sxs-lookup"><span data-stu-id="9182f-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="9182f-155">구성 공급자 코드 또는 일반 텍스트 구성 파일에 암호 또는 기타 중요한 데이터를 절대 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="9182f-156">[비밀 관리자](xref:security/app-secrets)를 사용하여 개발에 사용되는 비밀을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="9182f-157">개발 또는 테스트 환경에서 프로덕션 비밀을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="9182f-158">의도치 않게 소스 코드 리포지토리에 커밋되는 일이 없도록 프로젝트 외부에서 비밀을 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="9182f-159">[기본](#default)적으로, [비밀 관리자](xref:security/app-secrets)는 *appsettings.json* 및 *appsettings.* `Environment` *.json* 뒤에 구성 설정을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="9182f-160">암호 또는 기타 중요한 데이터 저장에 대한 자세한 정보:</span><span class="sxs-lookup"><span data-stu-id="9182f-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="9182f-161"><xref:security/app-secrets>:  환경 변수를 사용하여 중요한 데이터를 저장하는 방법에 대한 조언을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="9182f-162">비밀 관리자는 [파일 구성 공급자](#fcp)를 사용하여 사용자 비밀을 로컬 시스템의 JSON 파일에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="9182f-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)가 ASP.NET Core 앱에 대한 앱 비밀을 안전하게 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="9182f-164">자세한 내용은 <xref:security/key-vault-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="9182f-165">환경 변수</span><span class="sxs-lookup"><span data-stu-id="9182f-165">Environment variables</span></span>

<span data-ttu-id="9182f-166">[기본](#default) 구성을 사용하여 <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>는 *appsettings.json*, *appsettings.* `Environment` *.json* 및 [비밀 관리자](xref:security/app-secrets)를 읽은 후 환경 변수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="9182f-167">따라서 환경에서 읽은 키 값이 *appsettings.json*, *appsettings.* `Environment` *.json* 및 비밀 관리자에서 읽은 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="9182f-168">다음 `set` 명령은,</span><span class="sxs-lookup"><span data-stu-id="9182f-168">The following `set` commands:</span></span>

* <span data-ttu-id="9182f-169">Windows에서 [위의 예제](#appsettingsjson)에 나오는 환경 키 및 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="9182f-170">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)를 사용하는 경우 설정을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="9182f-171">`dotnet run` 명령은 프로젝트 디렉터리에서 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="9182f-172">위의 환경 설정은,</span><span class="sxs-lookup"><span data-stu-id="9182f-172">The preceding environment settings:</span></span>

* <span data-ttu-id="9182f-173">설정된 명령 창에서 시작된 프로세스에서만 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="9182f-174">Visual Studio에서 시작된 브라우저에서는 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="9182f-175">다음 [setx](/windows-server/administration/windows-commands/setx) 명령을 사용하여 Windows에서 환경 키 및 값을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="9182f-176">`set`와 달리, `setx` 설정은 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="9182f-177">`/M`은 시스템 환경에서 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="9182f-178">`/M` 스위치를 사용하지 않으면 사용자 환경 변수가 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="9182f-179">위의 명령이 *apsettings.json* 및 *appsettings.* `Environment` *.json*을 재정의하는지 테스트하려면:</span><span class="sxs-lookup"><span data-stu-id="9182f-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="9182f-180">Visual Studio를 사용하는 경우: Visual Studio를 종료했다가 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="9182f-181">CLI를 사용하는 경우: 새 명령 창을 시작하고 `dotnet run`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="9182f-182">환경 변수의 접두사를 지정하는 문자열을 사용하여 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="9182f-183">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="9182f-183">In the preceding code:</span></span>

* <span data-ttu-id="9182f-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`는 [기본 구성 공급자](#default) 뒤에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="9182f-185">구성 공급자 순서 지정 예제는 [JSON 구성 공급자](#jcp)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="9182f-186">`MyCustomPrefix_` 접두사로 설정된 환경 변수는 [기본 구성 공급자](#default)를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="9182f-187">여기에는 접두사 없는 환경 변수가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="9182f-188">구성 키-값 쌍을 읽으면 접두사는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="9182f-189">다음 명령은 사용자 지정 접두사를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="9182f-190">[기본 구성](#default)은 `DOTNET_` 및 `ASPNETCORE_` 접두사가 붙은 환경 변수 및 명령줄 인수를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="9182f-191">`DOTNET_` 및 `ASPNETCORE_` 접두사는 ASP.NET Core에서 [호스트 및 앱 구성](xref:fundamentals/host/generic-host#host-configuration)에 사용되지만, 사용자 구성에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="9182f-192">호스트 및 앱 구성에 대한 자세한 내용은 [.NET 제네릭 호스트](xref:fundamentals/host/generic-host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="9182f-193">[Azure App Service](https://azure.microsoft.com/services/app-service/)의 **설정 > 구성** 페이지에서 **새 애플리케이션 설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="9182f-194">Azure App Service 애플리케이션 설정은,</span><span class="sxs-lookup"><span data-stu-id="9182f-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="9182f-195">미사용 시 암호화되고 암호화된 채널을 통해 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="9182f-196">환경 변수로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-196">Exposed as environment variables.</span></span>

<span data-ttu-id="9182f-197">자세한 내용은 [Azure 앱: Azure Portal을 사용하여 앱 구성 재정의](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="9182f-198">Azure 데이터베이스 연결 문자열에 대한 자세한 내용은 [연결 문자열 접두사](#constr)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="9182f-199">명령줄</span><span class="sxs-lookup"><span data-stu-id="9182f-199">Command-line</span></span>

<span data-ttu-id="9182f-200">[기본](#default) 구성을 사용하여 <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>는 다음 구성 소스 뒤에 명령줄 인수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="9182f-201">*appsettings.json* 및 *appsettings*.`Environment`.*json* 파일</span><span class="sxs-lookup"><span data-stu-id="9182f-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="9182f-202">개발 환경의 [앱 비밀(비밀 관리자)](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="9182f-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9182f-203">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="9182f-203">Environment variables.</span></span>

<span data-ttu-id="9182f-204">[기본](#default)적으로, 명령줄에 설정된 구성 값은 다른 모든 구성 공급자를 사용하여 설정된 구성 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="9182f-205">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="9182f-205">Command-line arguments</span></span>

<span data-ttu-id="9182f-206">다음 명령은 `=`을 사용하여 키 및 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="9182f-207">다음 명령은 `/`를 사용하여 키 및 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="9182f-208">다음 명령은 `--`를 사용하여 키 및 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="9182f-209">키 값은,</span><span class="sxs-lookup"><span data-stu-id="9182f-209">The key value:</span></span>

* <span data-ttu-id="9182f-210">`=` 다음에 와야 합니다. 또는 값이 공백에 다음에 오는 경우 키에 `--` 또는 `/` 접두사가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="9182f-211">`=`이 사용된 경우 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="9182f-212">예: `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="9182f-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="9182f-213">같은 명령 내에서 `=`을 사용하는 명령줄 인수 키-값 쌍을 공백을 사용하는 키-값 쌍과 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="9182f-214">스위치 매핑</span><span class="sxs-lookup"><span data-stu-id="9182f-214">Switch mappings</span></span>

<span data-ttu-id="9182f-215">스위치 매핑은 **키** 이름 교체 논리를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="9182f-216"><xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 메서드에 스위치 교체 사전을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="9182f-217">스위치 매핑 사전을 사용하면 명령줄 인수를 통해 제공된 키와 일치하는 키에 대해 사전을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="9182f-218">사전에서 명령줄 키가 발견되면 사전 값이 다시 전달되어 앱 구성의 키-값 쌍이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="9182f-219">단일 대시(`-`) 접두사가 붙은 명령줄 키에는 스위치 매핑이 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="9182f-220">스위치 매핑 사전 키 규칙:</span><span class="sxs-lookup"><span data-stu-id="9182f-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="9182f-221">스위치는 `-` 또는 `--`으로 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="9182f-222">스위치 매핑 사전이 중복 키를 포함하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="9182f-223">스위치 매핑 사전을 사용하려면 `AddCommandLine`에 대한 호출에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="9182f-224">다음 코드는 교체된 키의 키 값을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="9182f-225">다음 명령을 실행하여 키 교체를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="9182f-226">참고: 현재 `=`을 사용하여 단일 대시(`-`)가 붙은 키 교체 값을 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="9182f-227">이 [GitHub 문제](https://github.com/dotnet/extensions/issues/3059)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="9182f-228">다음 명령은 작동하여 키 교체를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="9182f-229">스위치 매핑을 사용하는 앱의 경우 `CreateDefaultBuilder`에 대한 호출은 인수를 전달하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="9182f-230">`CreateDefaultBuilder` 메서드의 `AddCommandLine` 호출에는 매핑된 스위치가 포함되지 않으며 `CreateDefaultBuilder`에 스위치 매핑 사전을 전달할 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9182f-231">해결 방법으로 `CreateDefaultBuilder`에 인수를 전달하지 않고 대신 `ConfigurationBuilder` 메서드의 `AddCommandLine` 메서드에서 인수와 스위치 매핑 사전을 모두 처리하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="9182f-232">계층적 구성 데이터</span><span class="sxs-lookup"><span data-stu-id="9182f-232">Hierarchical configuration data</span></span>

<span data-ttu-id="9182f-233">구성 API는 구성 키에 구분 기호를 사용해 계층적 데이터를 평면화하여 계층적 구성 데이터를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="9182f-234">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)에는 다음 *appsettings.json* 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="9182f-235">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)의 다음 코드는 몇 가지 구성 설정을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="9182f-236">계층적 구성 데이터를 읽는 기본 방법은 옵션 패턴을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="9182f-237">자세한 내용은 이 문서의 [계층적 구성 데이터 바인딩](#optpat)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="9182f-238">구성 데이터에서는 <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 및 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 메서드를 사용하여 섹션과 섹션의 자식을 격리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="9182f-239">이러한 메서드에 대해서는 나중에 [GetSection, GetChildren 및 Exists](#getsection)에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="9182f-240">구성 키 및 값</span><span class="sxs-lookup"><span data-stu-id="9182f-240">Configuration keys and values</span></span>

<span data-ttu-id="9182f-241">구성 키는,</span><span class="sxs-lookup"><span data-stu-id="9182f-241">Configuration keys:</span></span>

* <span data-ttu-id="9182f-242">대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-242">Are case-insensitive.</span></span> <span data-ttu-id="9182f-243">예를 들어 `ConnectionString`과 `connectionstring`은 동일한 키로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="9182f-244">두 개 이상의 구성 공급자에 키와 값이 설정되어 있으면 추가된 마지막 공급자의 값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="9182f-245">자세한 내용은 [기본 구성](#default)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="9182f-246">계층적 키</span><span class="sxs-lookup"><span data-stu-id="9182f-246">Hierarchical keys</span></span>
  * <span data-ttu-id="9182f-247">구성 API 내에서는 콜론 구분 기호(`:`)가 모든 플랫폼에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="9182f-248">환경 변수에서는 콜론 구분 기호가 일부 플랫폼에 적용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="9182f-249">두 개의 밑줄(`__`)은 모든 플랫폼에서 지원되며 콜론(`:`)으로 자동 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="9182f-250">Azure Key Vault에서 계층적 키는 `--`를 구분 기호로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="9182f-251">[Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)는 암호를 앱의 구성으로 로드할 때 `--`를 자동으로 `:`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="9182f-252"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9182f-253">배열 바인딩에 대해서는 [클래스에 배열 바인딩](#boa) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="9182f-254">구성 값은,</span><span class="sxs-lookup"><span data-stu-id="9182f-254">Configuration values:</span></span>

* <span data-ttu-id="9182f-255">문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-255">Are strings.</span></span>
* <span data-ttu-id="9182f-256">Null 값은 구성에 저장하거나 개체에 바인딩할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="9182f-257">구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-257">Configuration providers</span></span>

<span data-ttu-id="9182f-258">다음 표에서는 ASP.NET Core 앱에서 사용할 수 있는 구성 공급자를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="9182f-259">공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-259">Provider</span></span> | <span data-ttu-id="9182f-260">다음에서 구성 제공</span><span class="sxs-lookup"><span data-stu-id="9182f-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="9182f-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-262">'Blazor'</span></span>
- <span data-ttu-id="9182f-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-263">'Identity'</span></span>
- <span data-ttu-id="9182f-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-265">'Razor'</span></span>
- <span data-ttu-id="9182f-266">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-268">'Blazor'</span></span>
- <span data-ttu-id="9182f-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-269">'Identity'</span></span>
- <span data-ttu-id="9182f-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-271">'Razor'</span></span>
- <span data-ttu-id="9182f-272">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-272">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-274">'Blazor'</span></span>
- <span data-ttu-id="9182f-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-275">'Identity'</span></span>
- <span data-ttu-id="9182f-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-277">'Razor'</span></span>
- <span data-ttu-id="9182f-278">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-280">'Blazor'</span></span>
- <span data-ttu-id="9182f-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-281">'Identity'</span></span>
- <span data-ttu-id="9182f-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-283">'Razor'</span></span>
- <span data-ttu-id="9182f-284">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-286">'Blazor'</span></span>
- <span data-ttu-id="9182f-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-287">'Identity'</span></span>
- <span data-ttu-id="9182f-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-289">'Razor'</span></span>
- <span data-ttu-id="9182f-290">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-292">'Blazor'</span></span>
- <span data-ttu-id="9182f-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-293">'Identity'</span></span>
- <span data-ttu-id="9182f-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-295">'Razor'</span></span>
- <span data-ttu-id="9182f-296">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-298">'Blazor'</span></span>
- <span data-ttu-id="9182f-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-299">'Identity'</span></span>
- <span data-ttu-id="9182f-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-301">'Razor'</span></span>
- <span data-ttu-id="9182f-302">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-304">'Blazor'</span></span>
- <span data-ttu-id="9182f-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-305">'Identity'</span></span>
- <span data-ttu-id="9182f-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-307">'Razor'</span></span>
- <span data-ttu-id="9182f-308">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-310">'Blazor'</span></span>
- <span data-ttu-id="9182f-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-311">'Identity'</span></span>
- <span data-ttu-id="9182f-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-313">'Razor'</span></span>
- <span data-ttu-id="9182f-314">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-316">'Blazor'</span></span>
- <span data-ttu-id="9182f-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-317">'Identity'</span></span>
- <span data-ttu-id="9182f-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-319">'Razor'</span></span>
- <span data-ttu-id="9182f-320">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-322">'Blazor'</span></span>
- <span data-ttu-id="9182f-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-323">'Identity'</span></span>
- <span data-ttu-id="9182f-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-325">'Razor'</span></span>
- <span data-ttu-id="9182f-326">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-328">'Blazor'</span></span>
- <span data-ttu-id="9182f-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-329">'Identity'</span></span>
- <span data-ttu-id="9182f-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-331">'Razor'</span></span>
- <span data-ttu-id="9182f-332">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-334">'Blazor'</span></span>
- <span data-ttu-id="9182f-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-335">'Identity'</span></span>
- <span data-ttu-id="9182f-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-337">'Razor'</span></span>
- <span data-ttu-id="9182f-338">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-340">'Blazor'</span></span>
- <span data-ttu-id="9182f-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-341">'Identity'</span></span>
- <span data-ttu-id="9182f-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-343">'Razor'</span></span>
- <span data-ttu-id="9182f-344">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-346">'Blazor'</span></span>
- <span data-ttu-id="9182f-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-347">'Identity'</span></span>
- <span data-ttu-id="9182f-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-349">'Razor'</span></span>
- <span data-ttu-id="9182f-350">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-352">'Blazor'</span></span>
- <span data-ttu-id="9182f-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-353">'Identity'</span></span>
- <span data-ttu-id="9182f-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-355">'Razor'</span></span>
- <span data-ttu-id="9182f-356">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-358">'Blazor'</span></span>
- <span data-ttu-id="9182f-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-359">'Identity'</span></span>
- <span data-ttu-id="9182f-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-361">'Razor'</span></span>
- <span data-ttu-id="9182f-362">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-362">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-363">------------------ | | [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App 구성 공급자](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [명령줄 구성 공급자](#clcp) | 명령줄 매개 변수 | | [사용자 지정 구성 공급자](#custom-configuration-provider) | 사용자 지정 소스 | | [환경 변수 구성 공급자](#evcp) | 환경 변수 | | [파일 구성 공급자](#file-configuration-provider) | INI, JSON, XML 파일 | | [파일별 키 구성 공급자](#key-per-file-configuration-provider) | 디렉터리 파일 | | [메모리 구성 파일](#memory-configuration-provider) | 메모리 내 컬렉션 | | [비밀 관리자](xref:security/app-secrets)  | 사용자 프로필 디렉터리의 파일 |</span><span class="sxs-lookup"><span data-stu-id="9182f-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="9182f-364">구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="9182f-365">앱에 필요한 기본 구성 소스에 대한 우선 순위에 맞게 구성 공급자를 코드에 정렬하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="9182f-366">구성 공급자의 일반적인 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="9182f-367">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="9182f-367">*appsettings.json*</span></span>
1. <span data-ttu-id="9182f-368">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="9182f-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="9182f-369">비밀 관리자</span><span class="sxs-lookup"><span data-stu-id="9182f-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="9182f-370">[환경 변수 구성 공급자](#evcp)를 사용하는 환경 변수</span><span class="sxs-lookup"><span data-stu-id="9182f-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="9182f-371">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="9182f-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="9182f-372">일반적인 방식은 명령줄 구성 공급자를 일련의 공급자에서 마지막에 추가하는 것이므로, 다른 공급자에서 설정한 구성을 명령줄 인수로 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="9182f-373">위의 공급자 시퀀스는 [기본 구성](#default)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="9182f-374">연결 문자열 접두사</span><span class="sxs-lookup"><span data-stu-id="9182f-374">Connection string prefixes</span></span>

<span data-ttu-id="9182f-375">구성 API에는 네 개의 연결 문자열 환경 변수에 대한 특별한 처리 규칙이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="9182f-376">해당 연결 문자열은 앱 환경의 Azure 연결 문자열을 구성하는 데 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="9182f-377">[기본 구성](#default)을 사용하거나 `AddEnvironmentVariables`에 제공된 접두사가 없는 경우 표에 표시된 접두사가 붙은 환경 변수가 앱에 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="9182f-378">연결 문자열 접두사</span><span class="sxs-lookup"><span data-stu-id="9182f-378">Connection string prefix</span></span> | <span data-ttu-id="9182f-379">공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-379">Provider</span></span> |
| ---
<span data-ttu-id="9182f-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-381">'Blazor'</span></span>
- <span data-ttu-id="9182f-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-382">'Identity'</span></span>
- <span data-ttu-id="9182f-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-384">'Razor'</span></span>
- <span data-ttu-id="9182f-385">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-387">'Blazor'</span></span>
- <span data-ttu-id="9182f-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-388">'Identity'</span></span>
- <span data-ttu-id="9182f-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-390">'Razor'</span></span>
- <span data-ttu-id="9182f-391">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-393">'Blazor'</span></span>
- <span data-ttu-id="9182f-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-394">'Identity'</span></span>
- <span data-ttu-id="9182f-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-396">'Razor'</span></span>
- <span data-ttu-id="9182f-397">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-399">'Blazor'</span></span>
- <span data-ttu-id="9182f-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-400">'Identity'</span></span>
- <span data-ttu-id="9182f-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-402">'Razor'</span></span>
- <span data-ttu-id="9182f-403">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-405">'Blazor'</span></span>
- <span data-ttu-id="9182f-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-406">'Identity'</span></span>
- <span data-ttu-id="9182f-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-408">'Razor'</span></span>
- <span data-ttu-id="9182f-409">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-411">'Blazor'</span></span>
- <span data-ttu-id="9182f-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-412">'Identity'</span></span>
- <span data-ttu-id="9182f-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-414">'Razor'</span></span>
- <span data-ttu-id="9182f-415">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-417">'Blazor'</span></span>
- <span data-ttu-id="9182f-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-418">'Identity'</span></span>
- <span data-ttu-id="9182f-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-420">'Razor'</span></span>
- <span data-ttu-id="9182f-421">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-423">'Blazor'</span></span>
- <span data-ttu-id="9182f-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-424">'Identity'</span></span>
- <span data-ttu-id="9182f-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-426">'Razor'</span></span>
- <span data-ttu-id="9182f-427">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-429">'Blazor'</span></span>
- <span data-ttu-id="9182f-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-430">'Identity'</span></span>
- <span data-ttu-id="9182f-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-432">'Razor'</span></span>
- <span data-ttu-id="9182f-433">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-435">'Blazor'</span></span>
- <span data-ttu-id="9182f-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-436">'Identity'</span></span>
- <span data-ttu-id="9182f-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-438">'Razor'</span></span>
- <span data-ttu-id="9182f-439">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-439">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-441">'Blazor'</span></span>
- <span data-ttu-id="9182f-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-442">'Identity'</span></span>
- <span data-ttu-id="9182f-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-444">'Razor'</span></span>
- <span data-ttu-id="9182f-445">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-447">'Blazor'</span></span>
- <span data-ttu-id="9182f-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-448">'Identity'</span></span>
- <span data-ttu-id="9182f-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-450">'Razor'</span></span>
- <span data-ttu-id="9182f-451">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-451">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-452">---- | | `CUSTOMCONNSTR_` | 사용자 지정 공급자 | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="9182f-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="9182f-453">표에 표시된 네 개 접두사 중 하나가 붙은 환경 변수가 검색되어 구성으로 로드되면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="9182f-454">환경 변수 접두사를 제거하고 구성 키 섹션(`ConnectionStrings`)을 추가하여 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="9182f-455">데이터베이스 연결 제공자(지정된 공급자가 없는 `CUSTOMCONNSTR_` 제외)를 나타내는 새 구성 키-값 쌍이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="9182f-456">환경 변수 키</span><span class="sxs-lookup"><span data-stu-id="9182f-456">Environment variable key</span></span> | <span data-ttu-id="9182f-457">변환된 구성 키</span><span class="sxs-lookup"><span data-stu-id="9182f-457">Converted configuration key</span></span> | <span data-ttu-id="9182f-458">공급자 구성 항목</span><span class="sxs-lookup"><span data-stu-id="9182f-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="9182f-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-460">'Blazor'</span></span>
- <span data-ttu-id="9182f-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-461">'Identity'</span></span>
- <span data-ttu-id="9182f-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-463">'Razor'</span></span>
- <span data-ttu-id="9182f-464">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-466">'Blazor'</span></span>
- <span data-ttu-id="9182f-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-467">'Identity'</span></span>
- <span data-ttu-id="9182f-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-469">'Razor'</span></span>
- <span data-ttu-id="9182f-470">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-472">'Blazor'</span></span>
- <span data-ttu-id="9182f-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-473">'Identity'</span></span>
- <span data-ttu-id="9182f-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-475">'Razor'</span></span>
- <span data-ttu-id="9182f-476">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-478">'Blazor'</span></span>
- <span data-ttu-id="9182f-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-479">'Identity'</span></span>
- <span data-ttu-id="9182f-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-481">'Razor'</span></span>
- <span data-ttu-id="9182f-482">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-484">'Blazor'</span></span>
- <span data-ttu-id="9182f-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-485">'Identity'</span></span>
- <span data-ttu-id="9182f-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-487">'Razor'</span></span>
- <span data-ttu-id="9182f-488">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-490">'Blazor'</span></span>
- <span data-ttu-id="9182f-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-491">'Identity'</span></span>
- <span data-ttu-id="9182f-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-493">'Razor'</span></span>
- <span data-ttu-id="9182f-494">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-496">'Blazor'</span></span>
- <span data-ttu-id="9182f-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-497">'Identity'</span></span>
- <span data-ttu-id="9182f-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-499">'Razor'</span></span>
- <span data-ttu-id="9182f-500">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-502">'Blazor'</span></span>
- <span data-ttu-id="9182f-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-503">'Identity'</span></span>
- <span data-ttu-id="9182f-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-505">'Razor'</span></span>
- <span data-ttu-id="9182f-506">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-508">'Blazor'</span></span>
- <span data-ttu-id="9182f-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-509">'Identity'</span></span>
- <span data-ttu-id="9182f-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-511">'Razor'</span></span>
- <span data-ttu-id="9182f-512">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-514">'Blazor'</span></span>
- <span data-ttu-id="9182f-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-515">'Identity'</span></span>
- <span data-ttu-id="9182f-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-517">'Razor'</span></span>
- <span data-ttu-id="9182f-518">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-518">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-520">'Blazor'</span></span>
- <span data-ttu-id="9182f-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-521">'Identity'</span></span>
- <span data-ttu-id="9182f-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-523">'Razor'</span></span>
- <span data-ttu-id="9182f-524">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-526">'Blazor'</span></span>
- <span data-ttu-id="9182f-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-527">'Identity'</span></span>
- <span data-ttu-id="9182f-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-529">'Razor'</span></span>
- <span data-ttu-id="9182f-530">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-532">'Blazor'</span></span>
- <span data-ttu-id="9182f-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-533">'Identity'</span></span>
- <span data-ttu-id="9182f-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-535">'Razor'</span></span>
- <span data-ttu-id="9182f-536">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-538">'Blazor'</span></span>
- <span data-ttu-id="9182f-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-539">'Identity'</span></span>
- <span data-ttu-id="9182f-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-541">'Razor'</span></span>
- <span data-ttu-id="9182f-542">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-544">'Blazor'</span></span>
- <span data-ttu-id="9182f-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-545">'Identity'</span></span>
- <span data-ttu-id="9182f-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-547">'Razor'</span></span>
- <span data-ttu-id="9182f-548">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-550">'Blazor'</span></span>
- <span data-ttu-id="9182f-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-551">'Identity'</span></span>
- <span data-ttu-id="9182f-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-553">'Razor'</span></span>
- <span data-ttu-id="9182f-554">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-556">'Blazor'</span></span>
- <span data-ttu-id="9182f-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-557">'Identity'</span></span>
- <span data-ttu-id="9182f-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-559">'Razor'</span></span>
- <span data-ttu-id="9182f-560">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-562">'Blazor'</span></span>
- <span data-ttu-id="9182f-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-563">'Identity'</span></span>
- <span data-ttu-id="9182f-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-565">'Razor'</span></span>
- <span data-ttu-id="9182f-566">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-568">'Blazor'</span></span>
- <span data-ttu-id="9182f-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-569">'Identity'</span></span>
- <span data-ttu-id="9182f-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-571">'Razor'</span></span>
- <span data-ttu-id="9182f-572">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-574">'Blazor'</span></span>
- <span data-ttu-id="9182f-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-575">'Identity'</span></span>
- <span data-ttu-id="9182f-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-577">'Razor'</span></span>
- <span data-ttu-id="9182f-578">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-580">'Blazor'</span></span>
- <span data-ttu-id="9182f-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-581">'Identity'</span></span>
- <span data-ttu-id="9182f-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-583">'Razor'</span></span>
- <span data-ttu-id="9182f-584">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-584">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-586">'Blazor'</span></span>
- <span data-ttu-id="9182f-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-587">'Identity'</span></span>
- <span data-ttu-id="9182f-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-589">'Razor'</span></span>
- <span data-ttu-id="9182f-590">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-592">'Blazor'</span></span>
- <span data-ttu-id="9182f-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-593">'Identity'</span></span>
- <span data-ttu-id="9182f-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-595">'Razor'</span></span>
- <span data-ttu-id="9182f-596">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-598">'Blazor'</span></span>
- <span data-ttu-id="9182f-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-599">'Identity'</span></span>
- <span data-ttu-id="9182f-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-601">'Razor'</span></span>
- <span data-ttu-id="9182f-602">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-604">'Blazor'</span></span>
- <span data-ttu-id="9182f-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-605">'Identity'</span></span>
- <span data-ttu-id="9182f-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-607">'Razor'</span></span>
- <span data-ttu-id="9182f-608">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-610">'Blazor'</span></span>
- <span data-ttu-id="9182f-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-611">'Identity'</span></span>
- <span data-ttu-id="9182f-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-613">'Razor'</span></span>
- <span data-ttu-id="9182f-614">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-616">'Blazor'</span></span>
- <span data-ttu-id="9182f-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-617">'Identity'</span></span>
- <span data-ttu-id="9182f-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-619">'Razor'</span></span>
- <span data-ttu-id="9182f-620">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-622">'Blazor'</span></span>
- <span data-ttu-id="9182f-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-623">'Identity'</span></span>
- <span data-ttu-id="9182f-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-625">'Razor'</span></span>
- <span data-ttu-id="9182f-626">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-628">'Blazor'</span></span>
- <span data-ttu-id="9182f-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-629">'Identity'</span></span>
- <span data-ttu-id="9182f-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-631">'Razor'</span></span>
- <span data-ttu-id="9182f-632">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-634">'Blazor'</span></span>
- <span data-ttu-id="9182f-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-635">'Identity'</span></span>
- <span data-ttu-id="9182f-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-637">'Razor'</span></span>
- <span data-ttu-id="9182f-638">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-640">'Blazor'</span></span>
- <span data-ttu-id="9182f-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-641">'Identity'</span></span>
- <span data-ttu-id="9182f-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-643">'Razor'</span></span>
- <span data-ttu-id="9182f-644">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-646">'Blazor'</span></span>
- <span data-ttu-id="9182f-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-647">'Identity'</span></span>
- <span data-ttu-id="9182f-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-649">'Razor'</span></span>
- <span data-ttu-id="9182f-650">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-652">'Blazor'</span></span>
- <span data-ttu-id="9182f-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-653">'Identity'</span></span>
- <span data-ttu-id="9182f-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-655">'Razor'</span></span>
- <span data-ttu-id="9182f-656">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-658">'Blazor'</span></span>
- <span data-ttu-id="9182f-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-659">'Identity'</span></span>
- <span data-ttu-id="9182f-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-661">'Razor'</span></span>
- <span data-ttu-id="9182f-662">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-664">'Blazor'</span></span>
- <span data-ttu-id="9182f-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-665">'Identity'</span></span>
- <span data-ttu-id="9182f-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-667">'Razor'</span></span>
- <span data-ttu-id="9182f-668">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-670">'Blazor'</span></span>
- <span data-ttu-id="9182f-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-671">'Identity'</span></span>
- <span data-ttu-id="9182f-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-673">'Razor'</span></span>
- <span data-ttu-id="9182f-674">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-676">'Blazor'</span></span>
- <span data-ttu-id="9182f-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-677">'Identity'</span></span>
- <span data-ttu-id="9182f-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-679">'Razor'</span></span>
- <span data-ttu-id="9182f-680">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-682">'Blazor'</span></span>
- <span data-ttu-id="9182f-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-683">'Identity'</span></span>
- <span data-ttu-id="9182f-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-685">'Razor'</span></span>
- <span data-ttu-id="9182f-686">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-688">'Blazor'</span></span>
- <span data-ttu-id="9182f-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-689">'Identity'</span></span>
- <span data-ttu-id="9182f-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-691">'Razor'</span></span>
- <span data-ttu-id="9182f-692">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-694">'Blazor'</span></span>
- <span data-ttu-id="9182f-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-695">'Identity'</span></span>
- <span data-ttu-id="9182f-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-697">'Razor'</span></span>
- <span data-ttu-id="9182f-698">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-700">'Blazor'</span></span>
- <span data-ttu-id="9182f-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-701">'Identity'</span></span>
- <span data-ttu-id="9182f-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-703">'Razor'</span></span>
- <span data-ttu-id="9182f-704">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-706">'Blazor'</span></span>
- <span data-ttu-id="9182f-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-707">'Identity'</span></span>
- <span data-ttu-id="9182f-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-709">'Razor'</span></span>
- <span data-ttu-id="9182f-710">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-712">'Blazor'</span></span>
- <span data-ttu-id="9182f-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-713">'Identity'</span></span>
- <span data-ttu-id="9182f-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-715">'Razor'</span></span>
- <span data-ttu-id="9182f-716">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-718">'Blazor'</span></span>
- <span data-ttu-id="9182f-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-719">'Identity'</span></span>
- <span data-ttu-id="9182f-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-721">'Razor'</span></span>
- <span data-ttu-id="9182f-722">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-724">'Blazor'</span></span>
- <span data-ttu-id="9182f-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-725">'Identity'</span></span>
- <span data-ttu-id="9182f-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-727">'Razor'</span></span>
- <span data-ttu-id="9182f-728">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-730">'Blazor'</span></span>
- <span data-ttu-id="9182f-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-731">'Identity'</span></span>
- <span data-ttu-id="9182f-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-733">'Razor'</span></span>
- <span data-ttu-id="9182f-734">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-736">'Blazor'</span></span>
- <span data-ttu-id="9182f-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-737">'Identity'</span></span>
- <span data-ttu-id="9182f-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-739">'Razor'</span></span>
- <span data-ttu-id="9182f-740">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-742">'Blazor'</span></span>
- <span data-ttu-id="9182f-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-743">'Identity'</span></span>
- <span data-ttu-id="9182f-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-745">'Razor'</span></span>
- <span data-ttu-id="9182f-746">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-748">'Blazor'</span></span>
- <span data-ttu-id="9182f-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-749">'Identity'</span></span>
- <span data-ttu-id="9182f-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-751">'Razor'</span></span>
- <span data-ttu-id="9182f-752">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-754">'Blazor'</span></span>
- <span data-ttu-id="9182f-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-755">'Identity'</span></span>
- <span data-ttu-id="9182f-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-757">'Razor'</span></span>
- <span data-ttu-id="9182f-758">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-760">'Blazor'</span></span>
- <span data-ttu-id="9182f-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-761">'Identity'</span></span>
- <span data-ttu-id="9182f-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-763">'Razor'</span></span>
- <span data-ttu-id="9182f-764">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-766">'Blazor'</span></span>
- <span data-ttu-id="9182f-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-767">'Identity'</span></span>
- <span data-ttu-id="9182f-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-769">'Razor'</span></span>
- <span data-ttu-id="9182f-770">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-772">'Blazor'</span></span>
- <span data-ttu-id="9182f-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-773">'Identity'</span></span>
- <span data-ttu-id="9182f-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-775">'Razor'</span></span>
- <span data-ttu-id="9182f-776">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-778">'Blazor'</span></span>
- <span data-ttu-id="9182f-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-779">'Identity'</span></span>
- <span data-ttu-id="9182f-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-781">'Razor'</span></span>
- <span data-ttu-id="9182f-782">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-784">'Blazor'</span></span>
- <span data-ttu-id="9182f-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-785">'Identity'</span></span>
- <span data-ttu-id="9182f-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-787">'Razor'</span></span>
- <span data-ttu-id="9182f-788">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-790">'Blazor'</span></span>
- <span data-ttu-id="9182f-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-791">'Identity'</span></span>
- <span data-ttu-id="9182f-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-793">'Razor'</span></span>
- <span data-ttu-id="9182f-794">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-796">'Blazor'</span></span>
- <span data-ttu-id="9182f-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-797">'Identity'</span></span>
- <span data-ttu-id="9182f-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-799">'Razor'</span></span>
- <span data-ttu-id="9182f-800">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-802">'Blazor'</span></span>
- <span data-ttu-id="9182f-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-803">'Identity'</span></span>
- <span data-ttu-id="9182f-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-805">'Razor'</span></span>
- <span data-ttu-id="9182f-806">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-806">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | 구성 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="9182f-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}` | 키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9182f-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9182f-809">값: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | 키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9182f-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9182f-810">값: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | 키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9182f-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9182f-811">값: `System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="9182f-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="9182f-812">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-812">JSON configuration provider</span></span>

<span data-ttu-id="9182f-813"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>는 JSON 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="9182f-814">오버로드는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-814">Overloads can specify:</span></span>

* <span data-ttu-id="9182f-815">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="9182f-815">Whether the file is optional.</span></span>
* <span data-ttu-id="9182f-816">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="9182f-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="9182f-817">다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="9182f-818">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="9182f-818">The preceding code:</span></span>

* <span data-ttu-id="9182f-819">다음 옵션을 사용하여 *MyConfig.json* 파일을 로드하도록 JSON 구성 공급자를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="9182f-820">`optional: true`: 파일은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="9182f-821">`reloadOnChange: true`은: 변경 내용이 저장되면 파일이 다시 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="9182f-822">*MyConfig.json* 파일 전에 [기본 구성 공급자](#default)를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="9182f-823">*MyConfig.json* 파일의 설정은 [환경 변수 구성 공급자](#evcp) 및 [명령줄 구성 공급자](#clcp)를 비롯한 기본 구성 공급자의 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="9182f-824">일반적으로 사용자 지정 JSON 파일이 [환경 변수 구성 공급자](#evcp) 및 [명령줄 구성 공급자](#clcp)에 설정된 값을 재정의하기를 원치 않습니다.\*\*\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="9182f-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="9182f-825">다음 코드는 모든 구성 공급자를 지우고 여러 구성 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="9182f-826">위의 코드에서 *MyConfig.json* 및 *MyConfig*.`Environment`.*json* 파일의 설정은,</span><span class="sxs-lookup"><span data-stu-id="9182f-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="9182f-827">*appsettings.json* 및 *appsettings*.`Environment`.*json* 파일의 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="9182f-828">[환경 변수 구성 공급자](#evcp) 및 [명령줄 구성 공급자](#clcp)의 설정에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="9182f-829">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)에는 다음 *MyConfig.json* 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="9182f-830">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)의 다음 코드는 위의 몇 가지 구성 설정을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="9182f-831">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-831">File configuration provider</span></span>

<span data-ttu-id="9182f-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>는 파일 시스템에서 구성을 로드하기 위한 기본 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="9182f-833">다음 구성 공급자는 `FileConfigurationProvider`에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="9182f-834">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="9182f-835">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="9182f-836">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="9182f-837">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-837">INI configuration provider</span></span>

<span data-ttu-id="9182f-838"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>는 런타임에 INI 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="9182f-839">다음 코드는 모든 구성 공급자를 지우고 여러 구성 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="9182f-840">위의 코드에서 *MyIniConfig.ini* 및 *MyIniConfig*.`Environment`.*ini* 파일의 설정은 다음의 설정에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="9182f-841">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="9182f-842">[명령줄 구성 공급자](#clcp)</span><span class="sxs-lookup"><span data-stu-id="9182f-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="9182f-843">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)에는 다음 *MyIniConfig.ini* 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="9182f-844">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)의 다음 코드는 위의 몇 가지 구성 설정을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="9182f-845">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-845">XML configuration provider</span></span>

<span data-ttu-id="9182f-846"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>는 런타임에 XML 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="9182f-847">다음 코드는 모든 구성 공급자를 지우고 여러 구성 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="9182f-848">위의 코드에서 *MyXMLFile.xml* 및 *MyXMLFile*.`Environment`.*xml* 파일의 설정은 다음의 설정에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="9182f-849">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="9182f-850">[명령줄 구성 공급자](#clcp)</span><span class="sxs-lookup"><span data-stu-id="9182f-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="9182f-851">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)에는 다음 *MyXMLFile.xml* 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="9182f-852">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)의 다음 코드는 위의 몇 가지 구성 설정을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="9182f-853">같은 요소 이름을 사용하는 반복 요소는 다음과 같이 `name` 특성을 사용하여 요소를 구분하면 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="9182f-854">다음 코드는 이전 구성 파일을 읽고 키 및 값을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="9182f-855">특성을 사용하여 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="9182f-856">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9182f-857">key:attribute</span><span class="sxs-lookup"><span data-stu-id="9182f-857">key:attribute</span></span>
* <span data-ttu-id="9182f-858">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="9182f-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="9182f-859">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="9182f-860"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>는 디렉터리의 파일을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="9182f-861">키는 파일 이름이고,</span><span class="sxs-lookup"><span data-stu-id="9182f-861">The key is the file name.</span></span> <span data-ttu-id="9182f-862">값은 파일의 콘텐츠를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-862">The value contains the file's contents.</span></span> <span data-ttu-id="9182f-863">파일별 키 구성 공급자는 Docker 호스팅 시나리오에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="9182f-864">파일별 키 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="9182f-865">파일의 `directoryPath`는 절대 경로여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="9182f-866">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="9182f-867">소스를 구성하는 `Action<KeyPerFileConfigurationSource>` 대리자</span><span class="sxs-lookup"><span data-stu-id="9182f-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="9182f-868">디렉터리가 선택 사항인지 여부와 디렉터리의 경로</span><span class="sxs-lookup"><span data-stu-id="9182f-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="9182f-869">두 개의 밑줄(`__`)은 파일 이름에서 구성 키 구분 기호로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="9182f-870">예를 들어, 파일 이름 `Logging__LogLevel__System`은 구성 키 `Logging:LogLevel:System`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="9182f-871">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="9182f-872">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-872">Memory configuration provider</span></span>

<span data-ttu-id="9182f-873"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>는 메모리 내 컬렉션을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="9182f-874">다음 코드는 구성 시스템에 메모리 컬렉션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="9182f-875">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)의 다음 코드는 위의 구성 설정을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="9182f-876">위의 코드에서 `config.AddInMemoryCollection(Dict)`는 [기본 구성 공급자](#default) 뒤에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="9182f-877">구성 공급자 순서 지정 예제는 [JSON 구성 공급자](#jcp)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="9182f-878">`MemoryConfigurationProvider`를 사용하는 또 다른 예제는 [배열 바인딩](#boa)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="9182f-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="9182f-879">GetValue</span></span>

<span data-ttu-id="9182f-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)는 지정된 키를 사용하여 구성에서 단일 값을 추출하고 해당 값을 지정된 형식으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="9182f-881">위의 코드에서 구성에 `NumberKey`가 없는 경우 기본값 `99`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="9182f-882">GetSection, GetChildren 및 Exists</span><span class="sxs-lookup"><span data-stu-id="9182f-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="9182f-883">이어지는 예제에서는 다음 *MySubsection.json* 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="9182f-884">다음 코드는 구성 공급자에 *MySubsection.json*을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="9182f-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="9182f-885">GetSection</span></span>

<span data-ttu-id="9182f-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*)은 지정된 하위 섹션 키를 사용하여 구성 하위 섹션을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="9182f-887">다음 코드는 `section1`의 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="9182f-888">다음 코드는 `section2:subsection0`의 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="9182f-889">`GetSection`은 `null`을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="9182f-890">일치하는 섹션을 찾을 수 없으면 빈 `IConfigurationSection`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="9182f-891">`GetSection`이 일치하는 섹션을 반환할 때 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>는 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="9182f-892">섹션이 존재하면 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 및 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="9182f-893">GetChildren 및 Exists</span><span class="sxs-lookup"><span data-stu-id="9182f-893">GetChildren and Exists</span></span>

<span data-ttu-id="9182f-894">다음 코드는 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*)을 호출하고 `section2:subsection0`의 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="9182f-895">위의 코드는 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*)를 호출하여 섹션이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="9182f-896">배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="9182f-896">Bind an array</span></span>

<span data-ttu-id="9182f-897">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*)는 구성 키에서 배열 인덱스를 사용하여 배열을 개체에 바인딩할 수 있도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9182f-898">숫자 키 세그먼트를 노출하는 모든 배열 형식은 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 클래스 배열에 배열을 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="9182f-899">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)의 *MyArray.json*을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="9182f-900">다음 코드는 구성 공급자에 *MyArray.json*을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="9182f-901">다음 코드는 구성을 읽고 값을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="9182f-902">위의 코드는 다음 출력을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="9182f-903">위의 출력에서 Index 3은 *MyArray.json*의 `"4": "value40",`에 해당하는 `value40` 값을 가집니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="9182f-904">바인딩된 배열 인덱스는 연속되며 구성 키 인덱스에 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="9182f-905">구성 바인더는 null 값을 바인딩하거나 바인딩된 개체에 null 항목을 만들 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="9182f-906">다음 코드는 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 확장 메서드를 사용하여 `array:entries` 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="9182f-907">다음 코드는 `arrayDict` `Dictionary`의 구성을 읽고 값을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="9182f-908">위의 코드는 다음 출력을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="9182f-909">바인딩된 개체의 인덱스 &num;3은 `array:4` 구성 키에 대한 구성 데이터와 `value4`의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="9182f-910">배열이 포함된 구성 데이터를 바인딩할 때 구성 키의 배열 인덱스는 개체를 만들 때 구성 데이터를 반복하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="9182f-911">구성 데이터에 null 값을 유지할 수 없으며, 구성 키의 배열에서 하나 이상의 인덱스를 건너뛰더라도 바인딩된 개체에 null 값 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="9182f-912">Index &num;3에 대한 누락된 구성 항목은 Index &num;3 키/값 쌍을 읽는 모든 구성 공급자에서 `ArrayExample` 인스턴스에 바인딩하기 전에 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="9182f-913">샘플 다운로드의 다음 *Value3.json* 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="9182f-914">다음 코드에는 *Value3.json* 및 `arrayDict` `Dictionary`의 구성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="9182f-915">다음 코드는 위의 구성을 읽고 값을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="9182f-916">위의 코드는 다음 출력을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="9182f-917">배열 바인딩을 구현하기 위해 사용자 지정 구성 공급자가 필요하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="9182f-918">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-918">Custom configuration provider</span></span>

<span data-ttu-id="9182f-919">샘플 앱에서는 [EF(Entity Framework)](/ef/core/)를 사용하여 데이터베이스에서 구성 키-값 쌍을 읽는 기본 구성 공급자를 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="9182f-920">이 공급자의 특징은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="9182f-921">데모용으로 EF 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="9182f-922">연결 문자열이 필요한 데이터베이스를 사용하려면 보조 `ConfigurationBuilder`를 구현하여 다른 구성 공급자에서 연결 문자열을 제공하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="9182f-923">이 공급자는 시작 시 데이터베이스 테이블을 구성으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="9182f-924">이 공급자는 키별 기준으로 데이터베이스를 쿼리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="9182f-925">변경 시 다시 로드가 구현되지 않으므로 앱 시작 후 데이터베이스를 업데이트해도 앱 구성에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="9182f-926">데이터베이스에 구성 값을 저장하는 `EFConfigurationValue` 엔터티를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="9182f-927">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="9182f-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="9182f-928">구성된 값을 저장 및 액세스하는 `EFConfigurationContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="9182f-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="9182f-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="9182f-930"><xref:Microsoft.Extensions.Configuration.IConfigurationSource>를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="9182f-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="9182f-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="9182f-932"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider>에서 상속하여 사용자 지정 구성 공급자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="9182f-933">구성 공급자는 비어 있는 데이터베이스를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="9182f-934">[구성 키는 대/소문자를](#keys)구분하지 않으므로 데이터베이스를 초기화하는 데 사용되는 사전은 대/소문자를 구분하지 않는 비교자([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase))를 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="9182f-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="9182f-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="9182f-936">`AddEFConfiguration` 확장 메서드를 사용하여 구성 소스를 `ConfigurationBuilder`에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="9182f-937">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="9182f-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="9182f-938">다음 코드는 *Program.cs*에서 사용자 지정 `EFConfigurationProvider`를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="9182f-939">시작 시 구성 액세스</span><span class="sxs-lookup"><span data-stu-id="9182f-939">Access configuration in Startup</span></span>

<span data-ttu-id="9182f-940">다음 코드는 `Startup` 메서드의 구성 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="9182f-941">시작 편의 메서드를 사용하여 구성에 액세스하는 방법의 예는 [앱 시작: 편리한 메서드](xref:fundamentals/startup#convenience-methods) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="9182f-942">Razor Pages의 구성 액세스</span><span class="sxs-lookup"><span data-stu-id="9182f-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="9182f-943">다음 코드는 Razor의 구성 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="9182f-944">다음 코드에서 `MyOptions`는 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*>를 통해 서비스 컨테이너에 추가되고 구성에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="9182f-945">다음 표시는 [`@inject`](xref:mvc/views/razor#inject) Razor 지시문을 사용하여 옵션 값을 확인하고 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="9182f-946">MVC 뷰 파일의 구성 액세스</span><span class="sxs-lookup"><span data-stu-id="9182f-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="9182f-947">다음 코드는 MVC 뷰의 구성 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="9182f-948">대리자로 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="9182f-948">Configure options with a delegate</span></span>

<span data-ttu-id="9182f-949">대리자에서 구성된 옵션은 구성 공급자에 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="9182f-950">대리자를 사용한 옵션 구성은 샘플 앱에 예제 2로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="9182f-951">다음 코드에서 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="9182f-952">`MyOptions`의 값을 구성하는 데 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="9182f-953">다음 코드는 다음과 같은 옵션 값을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="9182f-954">위의 예제에서 `Option1` 및 `Option2`의 값은 모두 *appsettings.json*에서 지정되고 구성된 대리자에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="9182f-955">호스트 대 앱 구성</span><span class="sxs-lookup"><span data-stu-id="9182f-955">Host versus app configuration</span></span>

<span data-ttu-id="9182f-956">앱을 구성하고 시작하기 전에 *호스트*를 구성하고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="9182f-957">호스트는 앱 시작 및 수명 관리를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="9182f-958">앱과 호스트 모두 이 항목에서 설명하는 구성 관리자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="9182f-959">호스트 구성 키-값 쌍은 앱의 구성에도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="9182f-960">호스트를 빌드할 때 구성 공급자를 사용하는 방법과 구성 소스가 호스트 구성에 미치는 영향에 대한 자세한 내용은 <xref:fundamentals/index#host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="9182f-961">기본 호스트 구성</span><span class="sxs-lookup"><span data-stu-id="9182f-961">Default host configuration</span></span>

<span data-ttu-id="9182f-962">[웹 호스트](xref:fundamentals/host/web-host)를 사용하는 경우 기본 구성에 대한 자세한 내용은 [이 항목의 ASP.NET Core 2.2 버전](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="9182f-963">호스트 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="9182f-964">[환경 변수 구성 공급자](#environment-variables-configuration-provider)를 사용하는 `DOTNET_` 접두사가 붙은 환경 변수(예: `DOTNET_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="9182f-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="9182f-965">구성 키-값 쌍이 로드되면 접두사(`DOTNET_`)는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="9182f-966">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="9182f-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="9182f-967">웹 호스트 기본 구성이 설정됩니다(`ConfigureWebHostDefaults`).</span><span class="sxs-lookup"><span data-stu-id="9182f-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="9182f-968">Kestrel은 웹 서버로 사용되며 앱의 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="9182f-969">호스트 필터링 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="9182f-970">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 환경 변수가 `true`로 설정된 경우 전달된 헤더 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="9182f-971">IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="9182f-972">기타 구성</span><span class="sxs-lookup"><span data-stu-id="9182f-972">Other configuration</span></span>

<span data-ttu-id="9182f-973">이 항목에서는 *앱 구성*에 관련된 내용만 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="9182f-974">ASP.NET Core 앱을 실행하고 호스팅하는 다른 요소는 이 항목에서 다루지 않는 구성 파일을 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="9182f-975">*launch.json*/*launchSettings.json*은 다음에 설명된 개발 환경에 대한 도구 구성 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="9182f-976"><xref:fundamentals/environments#development> 문서 내</span><span class="sxs-lookup"><span data-stu-id="9182f-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="9182f-977">개발 시나리오에서 ASP.NET Core 앱을 구성하는 데 해당 파일이 사용되는 설명서 집합 전체</span><span class="sxs-lookup"><span data-stu-id="9182f-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="9182f-978">*web.config*는 다음 항목에 설명된 서버 구성 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="9182f-979">이전 버전의 ASP.NET에서 앱 구성을 마이그레이션하는 방법에 대한 자세한 정보는 <xref:migration/proper-to-2x/index#store-configurations>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="9182f-980">외부 어셈블리의 구성 추가</span><span class="sxs-lookup"><span data-stu-id="9182f-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="9182f-981"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 구현은 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="9182f-982">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9182f-983">추가 자료</span><span class="sxs-lookup"><span data-stu-id="9182f-983">Additional resources</span></span>

* [<span data-ttu-id="9182f-984">구성 소스 코드</span><span class="sxs-lookup"><span data-stu-id="9182f-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9182f-985">ASP.NET Core의 앱 구성은 ‘구성 공급자’가 설정한 키-값 쌍을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="9182f-986">구성 공급자는 다양한 구성 소스에서 구성 데이터를 키-값 쌍으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="9182f-987">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9182f-987">Azure Key Vault</span></span>
* <span data-ttu-id="9182f-988">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="9182f-988">Azure App Configuration</span></span>
* <span data-ttu-id="9182f-989">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="9182f-989">Command-line arguments</span></span>
* <span data-ttu-id="9182f-990">사용자 지정 공급자(설치 또는 생성된)</span><span class="sxs-lookup"><span data-stu-id="9182f-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="9182f-991">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="9182f-991">Directory files</span></span>
* <span data-ttu-id="9182f-992">환경 변수</span><span class="sxs-lookup"><span data-stu-id="9182f-992">Environment variables</span></span>
* <span data-ttu-id="9182f-993">메모리 내 .NET 개체</span><span class="sxs-lookup"><span data-stu-id="9182f-993">In-memory .NET objects</span></span>
* <span data-ttu-id="9182f-994">설정 파일</span><span class="sxs-lookup"><span data-stu-id="9182f-994">Settings files</span></span>

<span data-ttu-id="9182f-995">일반적인 구성 공급자 시나리오에 대한 구성 패키지([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="9182f-996">샘플 앱에서 <xref:Microsoft.Extensions.Configuration> 네임스페이스를 사용하는 다음 코드 예제:</span><span class="sxs-lookup"><span data-stu-id="9182f-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="9182f-997">‘옵션 패턴’은 이 항목에 설명된 구성 개념의 확장입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="9182f-998">옵션은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="9182f-999">자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="9182f-1000">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9182f-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="9182f-1001">호스트 대 앱 구성</span><span class="sxs-lookup"><span data-stu-id="9182f-1001">Host versus app configuration</span></span>

<span data-ttu-id="9182f-1002">앱을 구성하고 시작하기 전에 *호스트*를 구성하고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="9182f-1003">호스트는 앱 시작 및 수명 관리를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="9182f-1004">앱과 호스트 모두 이 항목에서 설명하는 구성 관리자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="9182f-1005">호스트 구성 키-값 쌍은 앱의 구성에도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="9182f-1006">호스트를 빌드할 때 구성 공급자를 사용하는 방법과 구성 소스가 호스트 구성에 미치는 영향에 대한 자세한 내용은 <xref:fundamentals/index#host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="9182f-1007">기타 구성</span><span class="sxs-lookup"><span data-stu-id="9182f-1007">Other configuration</span></span>

<span data-ttu-id="9182f-1008">이 항목에서는 *앱 구성*에 관련된 내용만 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="9182f-1009">ASP.NET Core 앱을 실행하고 호스팅하는 다른 요소는 이 항목에서 다루지 않는 구성 파일을 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="9182f-1010">*launch.json*/*launchSettings.json*은 다음에 설명된 개발 환경에 대한 도구 구성 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="9182f-1011"><xref:fundamentals/environments#development> 문서 내</span><span class="sxs-lookup"><span data-stu-id="9182f-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="9182f-1012">개발 시나리오에서 ASP.NET Core 앱을 구성하는 데 해당 파일이 사용되는 설명서 집합 전체</span><span class="sxs-lookup"><span data-stu-id="9182f-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="9182f-1013">*web.config*는 다음 항목에 설명된 서버 구성 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="9182f-1014">이전 버전의 ASP.NET에서 앱 구성을 마이그레이션하는 방법에 대한 자세한 정보는 <xref:migration/proper-to-2x/index#store-configurations>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="9182f-1015">기본 구성</span><span class="sxs-lookup"><span data-stu-id="9182f-1015">Default configuration</span></span>

<span data-ttu-id="9182f-1016">호스트를 빌드할 때 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿을 기반으로 하는 웹앱은 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="9182f-1017">`CreateDefaultBuilder`는 다음 순서로 앱에 대한 기본 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="9182f-1018">[웹호스트](xref:fundamentals/host/web-host)를 사용하는 앱에는 다음이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="9182f-1019">[제네릭 호스트](xref:fundamentals/host/generic-host) 사용 시 기본 구성에 대한 자세한 내용은 [이 토픽의 최신 버전](xref:fundamentals/configuration/index)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="9182f-1020">호스트 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="9182f-1021">[환경 변수 구성 공급자](#environment-variables-configuration-provider)를 사용하여 `ASPNETCORE_`를 접두사로 사용하는 환경 변수(예: `ASPNETCORE_ENVIRONMENT`)</span><span class="sxs-lookup"><span data-stu-id="9182f-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="9182f-1022">구성 키-값 쌍이 로드되면 접두사(`ASPNETCORE_`)는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="9182f-1023">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="9182f-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="9182f-1024">앱 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="9182f-1025">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="9182f-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9182f-1026">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="9182f-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="9182f-1027">앱이 항목 어셈블리를 사용하여 `Development` 환경에서 실행되는 경우 [Secret Manager](xref:security/app-secrets)입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="9182f-1028">[환경 변수 구성 공급 기업](#environment-variables-configuration-provider)을 사용하는 환경 변수</span><span class="sxs-lookup"><span data-stu-id="9182f-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="9182f-1029">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="9182f-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="9182f-1030">보안</span><span class="sxs-lookup"><span data-stu-id="9182f-1030">Security</span></span>

<span data-ttu-id="9182f-1031">중요한 구성 데이터를 보호하려면 다음 방법을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="9182f-1032">구성 공급자 코드 또는 일반 텍스트 구성 파일에 암호 또는 기타 중요한 데이터를 절대 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="9182f-1033">개발 또는 테스트 환경에서 프로덕션 비밀을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="9182f-1034">의도치 않게 소스 코드 리포지토리에 커밋되는 일이 없도록 프로젝트 외부에서 비밀을 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="9182f-1035">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="9182f-1036"><xref:security/app-secrets> &ndash; 환경 변수를 사용하여 중요한 데이터를 저장하는 방법에 대한 조언을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1036"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="9182f-1037">비밀 관리자는 파일 구성 공급자를 사용하여 사용자 비밀을 로컬 시스템의 JSON 파일에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="9182f-1038">파일 구성 공급자에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="9182f-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)가 ASP.NET Core 앱에 대한 앱 비밀을 안전하게 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="9182f-1040">자세한 내용은 <xref:security/key-vault-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="9182f-1041">계층적 구성 데이터</span><span class="sxs-lookup"><span data-stu-id="9182f-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="9182f-1042">구성 API에서는 구성 키에 구분 기호를 사용해 계층적 데이터를 평면화하여 계층적 구성 데이터를 유지 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="9182f-1043">다음 JSON 파일에서는 두 섹션으로 이루어진 구조적 계층에 네 개의 키가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="9182f-1044">파일을 구성으로 읽어 들이면 구성 소스의 원래 계층적 데이터 구조를 유지하기 위해 고유 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="9182f-1045">콜론(`:`)을 사용해 섹션과 키를 평면화하여 원래 구조를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="9182f-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9182f-1046">section0:key0</span></span>
* <span data-ttu-id="9182f-1047">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9182f-1047">section0:key1</span></span>
* <span data-ttu-id="9182f-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9182f-1048">section1:key0</span></span>
* <span data-ttu-id="9182f-1049">section1:key1</span><span class="sxs-lookup"><span data-stu-id="9182f-1049">section1:key1</span></span>

<span data-ttu-id="9182f-1050">구성 데이터에서는 <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 및 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 메서드를 사용하여 섹션과 섹션의 자식을 격리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="9182f-1051">이러한 메서드에 대해서는 나중에 [GetSection, GetChildren 및 Exists](#getsection-getchildren-and-exists)에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="9182f-1052">규칙</span><span class="sxs-lookup"><span data-stu-id="9182f-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="9182f-1053">소스 및 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-1053">Sources and providers</span></span>

<span data-ttu-id="9182f-1054">앱 시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="9182f-1055">변경 검색을 구현하는 구성 공급자는 기본 설정 파일이 변경되면 구성을 다시 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="9182f-1056">예를 들어 파일 구성 공급자(이 항목의 뒷부분에서 설명됨) 및 [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)는 변경 검색을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="9182f-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration>은 앱의 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9182f-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration>을 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 또는 MVC <xref:Microsoft.AspNetCore.Mvc.Controller>에 삽입하여 클래스에 대한 구성을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="9182f-1059">다음 예제에서는 `_config` 필드가 구성 값에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="9182f-1060">구성 공급자는 호스트에서 설정될 때 DI가 제공되지 않으므로 DI를 활용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="9182f-1061">키</span><span class="sxs-lookup"><span data-stu-id="9182f-1061">Keys</span></span>

<span data-ttu-id="9182f-1062">구성키는 다음 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="9182f-1063">키는 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="9182f-1064">예를 들어 `ConnectionString`과 `connectionstring`은 동일한 키로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="9182f-1065">같은 키의 값을 같거나 다른 여러 구성 공급자에서 설정하는 경우 키에 설정된 마지막 값이 사용되는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="9182f-1066">계층적 키</span><span class="sxs-lookup"><span data-stu-id="9182f-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="9182f-1067">구성 API 내에서는 콜론 구분 기호(`:`)가 모든 플랫폼에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="9182f-1068">환경 변수에서는 콜론 구분 기호가 일부 플랫폼에 적용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="9182f-1069">두 개의 밑줄(`__`)은 모든 플랫폼에서 지원되며 콜론으로 자동 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="9182f-1070">Azure Key Vault에서 계층적 키는 `--`(두 개의 대시)를 구분 기호로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="9182f-1071">비밀을 앱의 구성으로 로드할 때 대시를 콜론으로 바꾸는 코드를 작성하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="9182f-1072"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9182f-1073">배열 바인딩에 대해서는 [클래스에 배열 바인딩](#bind-an-array-to-a-class) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="9182f-1074">값</span><span class="sxs-lookup"><span data-stu-id="9182f-1074">Values</span></span>

<span data-ttu-id="9182f-1075">구성 값은 다음 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="9182f-1076">값은 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1076">Values are strings.</span></span>
* <span data-ttu-id="9182f-1077">Null 값은 구성에 저장하거나 개체에 바인딩할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="9182f-1078">공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-1078">Providers</span></span>

<span data-ttu-id="9182f-1079">다음 표에서는 ASP.NET Core 앱에서 사용할 수 있는 구성 공급자를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="9182f-1080">공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-1080">Provider</span></span> | <span data-ttu-id="9182f-1081">다음에서 구성 제공&hellip;</span><span class="sxs-lookup"><span data-stu-id="9182f-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="9182f-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1083">'Blazor'</span></span>
- <span data-ttu-id="9182f-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1084">'Identity'</span></span>
- <span data-ttu-id="9182f-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1086">'Razor'</span></span>
- <span data-ttu-id="9182f-1087">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1089">'Blazor'</span></span>
- <span data-ttu-id="9182f-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1090">'Identity'</span></span>
- <span data-ttu-id="9182f-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1092">'Razor'</span></span>
- <span data-ttu-id="9182f-1093">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1095">'Blazor'</span></span>
- <span data-ttu-id="9182f-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1096">'Identity'</span></span>
- <span data-ttu-id="9182f-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1098">'Razor'</span></span>
- <span data-ttu-id="9182f-1099">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1101">'Blazor'</span></span>
- <span data-ttu-id="9182f-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1102">'Identity'</span></span>
- <span data-ttu-id="9182f-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1104">'Razor'</span></span>
- <span data-ttu-id="9182f-1105">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1107">'Blazor'</span></span>
- <span data-ttu-id="9182f-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1108">'Identity'</span></span>
- <span data-ttu-id="9182f-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1110">'Razor'</span></span>
- <span data-ttu-id="9182f-1111">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1113">'Blazor'</span></span>
- <span data-ttu-id="9182f-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1114">'Identity'</span></span>
- <span data-ttu-id="9182f-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1116">'Razor'</span></span>
- <span data-ttu-id="9182f-1117">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1119">'Blazor'</span></span>
- <span data-ttu-id="9182f-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1120">'Identity'</span></span>
- <span data-ttu-id="9182f-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1122">'Razor'</span></span>
- <span data-ttu-id="9182f-1123">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1125">'Blazor'</span></span>
- <span data-ttu-id="9182f-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1126">'Identity'</span></span>
- <span data-ttu-id="9182f-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1128">'Razor'</span></span>
- <span data-ttu-id="9182f-1129">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1131">'Blazor'</span></span>
- <span data-ttu-id="9182f-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1132">'Identity'</span></span>
- <span data-ttu-id="9182f-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1134">'Razor'</span></span>
- <span data-ttu-id="9182f-1135">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1137">'Blazor'</span></span>
- <span data-ttu-id="9182f-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1138">'Identity'</span></span>
- <span data-ttu-id="9182f-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1140">'Razor'</span></span>
- <span data-ttu-id="9182f-1141">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1143">'Blazor'</span></span>
- <span data-ttu-id="9182f-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1144">'Identity'</span></span>
- <span data-ttu-id="9182f-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1146">'Razor'</span></span>
- <span data-ttu-id="9182f-1147">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1149">'Blazor'</span></span>
- <span data-ttu-id="9182f-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1150">'Identity'</span></span>
- <span data-ttu-id="9182f-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1152">'Razor'</span></span>
- <span data-ttu-id="9182f-1153">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1155">'Blazor'</span></span>
- <span data-ttu-id="9182f-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1156">'Identity'</span></span>
- <span data-ttu-id="9182f-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1158">'Razor'</span></span>
- <span data-ttu-id="9182f-1159">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1161">'Blazor'</span></span>
- <span data-ttu-id="9182f-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1162">'Identity'</span></span>
- <span data-ttu-id="9182f-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1164">'Razor'</span></span>
- <span data-ttu-id="9182f-1165">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1167">'Blazor'</span></span>
- <span data-ttu-id="9182f-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1168">'Identity'</span></span>
- <span data-ttu-id="9182f-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1170">'Razor'</span></span>
- <span data-ttu-id="9182f-1171">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1173">'Blazor'</span></span>
- <span data-ttu-id="9182f-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1174">'Identity'</span></span>
- <span data-ttu-id="9182f-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1176">'Razor'</span></span>
- <span data-ttu-id="9182f-1177">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1179">'Blazor'</span></span>
- <span data-ttu-id="9182f-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1180">'Identity'</span></span>
- <span data-ttu-id="9182f-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1182">'Razor'</span></span>
- <span data-ttu-id="9182f-1183">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-1184">------------------ | | [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration) (*보안* 항목) | Azure Key Vault | | [Azure App Configuration 공급자](/azure/azure-app-configuration/quickstart-aspnet-core-app)(Azure 문서) | Azure App Configuration | | [명령줄 구성 공급자](#command-line-configuration-provider) | 명령줄 매개 변수 | | [사용자 지정 구성 공급자 ](#custom-configuration-provider) | 사용자 지정 소스 | | [환경 변수 구성 공급자](#environment-variables-configuration-provider) | 환경 변수 | | [파일 구성 공급자](#file-configuration-provider) | 파일(INI, JSON, XML) | | [파일별 키 구성 공급자](#key-per-file-configuration-provider) | 디렉터리 파일 | | [메모리 구성 공급자](#memory-configuration-provider) | 메모리 내 컬렉션 | | [사용자 비밀(비밀 관리자)](xref:security/app-secrets) (*보안* 항목) | 사용자 프로필 디렉터리의 파일 |</span><span class="sxs-lookup"><span data-stu-id="9182f-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="9182f-1185">시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="9182f-1186">이 항목의 구성 공급자는 코드에서 정렬하는 순서가 아니라 사전 순으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="9182f-1187">앱에 필요한 기본 구성 소스에 대한 우선 순위에 맞게 구성 공급자를 코드에 정렬하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="9182f-1188">구성 공급자의 일반적인 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="9182f-1189">파일(*appsettings.json*, *appsettings.{Environment}.json*, 여기서 `{Environment}`는 앱의 현재 호스팅 환경)</span><span class="sxs-lookup"><span data-stu-id="9182f-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="9182f-1190">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="9182f-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="9182f-1191">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서만)</span><span class="sxs-lookup"><span data-stu-id="9182f-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="9182f-1192">환경 변수</span><span class="sxs-lookup"><span data-stu-id="9182f-1192">Environment variables</span></span>
1. <span data-ttu-id="9182f-1193">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="9182f-1193">Command-line arguments</span></span>

<span data-ttu-id="9182f-1194">일반적인 방식은 명령줄 구성 공급자를 일련의 공급자에서 마지막에 배치하는 것이므로, 다른 공급자에서 설정한 구성을 명령줄 인수로 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="9182f-1195">`CreateDefaultBuilder`로 새 호스트 작성기가 초기화될 때 이전 공급자 시퀀스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9182f-1196">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="9182f-1197">UseConfiguration을 사용하여 호스트 작성기 구성</span><span class="sxs-lookup"><span data-stu-id="9182f-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="9182f-1198">호스트 작성기를 구성하려면 구성과 함께 호스트 작성기에서 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="9182f-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="9182f-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="9182f-1200">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 `CreateDefaultBuilder`에 의해 자동으로 추가된 구성 공급자 외에도 앱의 구성 공급자를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="9182f-1201">명령줄 인수를 사용하여 이전 구성 재정의</span><span class="sxs-lookup"><span data-stu-id="9182f-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="9182f-1202">명령줄 인수를 사용하여 재정의할 수 있는 앱 구성을 제공하려면 마지막으로 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="9182f-1203">CreateDefaultBuilder에 의해 추가된 공급자 제거</span><span class="sxs-lookup"><span data-stu-id="9182f-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="9182f-1204">`CreateDefaultBuilder`에 의해 추가된 공급자를 제거하려면 먼저 [IConfigurationBuilder](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)에서 [Clear](/dotnet/api/system.collections.generic.icollection-1.clear)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="9182f-1205">앱 시작 중 구성 사용</span><span class="sxs-lookup"><span data-stu-id="9182f-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="9182f-1206">`Startup.ConfigureServices`를 포함하여 `ConfigureAppConfiguration`에서 앱에 제공되는 구성은 앱을 시작하는 동안 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9182f-1207">자세한 내용은 [시작하는 동안 구성에 액세스](#access-configuration-during-startup) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="9182f-1208">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="9182f-1209"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>은 런타임 시 명령줄 인수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="9182f-1210">명령줄 구성을 활성화하기 위해 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 확장 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9182f-1211">`CreateDefaultBuilder(string [])`가 호출되면 `AddCommandLine`도 자동으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="9182f-1212">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9182f-1213">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9182f-1214">*appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성</span><span class="sxs-lookup"><span data-stu-id="9182f-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="9182f-1215">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="9182f-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9182f-1216">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="9182f-1216">Environment variables.</span></span>

<span data-ttu-id="9182f-1217">`CreateDefaultBuilder`는 명령줄 구성 공급자를 마지막에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="9182f-1218">따라서 런타임에 전달된 명령줄 인수가 다른 공급자에서 설정한 구성을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="9182f-1219">`CreateDefaultBuilder`는 호스트를 생성할 때 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="9182f-1220">따라서 `CreateDefaultBuilder`에서 활성화한 명령줄 구성이 호스트 생성 방식에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="9182f-1221">ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 `AddCommandLine`은 `CreateDefaultBuilder`에서 이미 호출되었습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9182f-1222">추가 구성 공급자를 추가하고 명령줄 인수를 사용하여 해당 공급자의 구성을 재정의하는 기능을 유지하려면 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 마지막으로 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="9182f-1223">**예제**</span><span class="sxs-lookup"><span data-stu-id="9182f-1223">**Example**</span></span>

<span data-ttu-id="9182f-1224">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="9182f-1225">프로젝트의 디렉터리에서 명령 프롬프트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="9182f-1226">`dotnet run` 명령에 명령줄 인수를 제공합니다(`dotnet run CommandLineKey=CommandLineValue`).</span><span class="sxs-lookup"><span data-stu-id="9182f-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="9182f-1227">앱이 실행되면 브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9182f-1228">`dotnet run`에 제공한 구성 명령줄 인수에 대한 키-값 쌍이 출력에 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="9182f-1229">인수</span><span class="sxs-lookup"><span data-stu-id="9182f-1229">Arguments</span></span>

<span data-ttu-id="9182f-1230">값은 등호(`=`) 다음에 와야 합니다. 또는 값이 공백 다음에 오는 경우 키에 접두사(`--` 또는 `/`)가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="9182f-1231">등호를 사용하는 경우 값이 필요하지 않습니다(예: `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="9182f-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="9182f-1232">키 접두사</span><span class="sxs-lookup"><span data-stu-id="9182f-1232">Key prefix</span></span>               | <span data-ttu-id="9182f-1233">예제</span><span class="sxs-lookup"><span data-stu-id="9182f-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="9182f-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1235">'Blazor'</span></span>
- <span data-ttu-id="9182f-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1236">'Identity'</span></span>
- <span data-ttu-id="9182f-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1238">'Razor'</span></span>
- <span data-ttu-id="9182f-1239">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1241">'Blazor'</span></span>
- <span data-ttu-id="9182f-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1242">'Identity'</span></span>
- <span data-ttu-id="9182f-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1244">'Razor'</span></span>
- <span data-ttu-id="9182f-1245">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1247">'Blazor'</span></span>
- <span data-ttu-id="9182f-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1248">'Identity'</span></span>
- <span data-ttu-id="9182f-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1250">'Razor'</span></span>
- <span data-ttu-id="9182f-1251">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1253">'Blazor'</span></span>
- <span data-ttu-id="9182f-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1254">'Identity'</span></span>
- <span data-ttu-id="9182f-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1256">'Razor'</span></span>
- <span data-ttu-id="9182f-1257">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1259">'Blazor'</span></span>
- <span data-ttu-id="9182f-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1260">'Identity'</span></span>
- <span data-ttu-id="9182f-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1262">'Razor'</span></span>
- <span data-ttu-id="9182f-1263">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1265">'Blazor'</span></span>
- <span data-ttu-id="9182f-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1266">'Identity'</span></span>
- <span data-ttu-id="9182f-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1268">'Razor'</span></span>
- <span data-ttu-id="9182f-1269">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1271">'Blazor'</span></span>
- <span data-ttu-id="9182f-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1272">'Identity'</span></span>
- <span data-ttu-id="9182f-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1274">'Razor'</span></span>
- <span data-ttu-id="9182f-1275">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1277">'Blazor'</span></span>
- <span data-ttu-id="9182f-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1278">'Identity'</span></span>
- <span data-ttu-id="9182f-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1280">'Razor'</span></span>
- <span data-ttu-id="9182f-1281">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1283">'Blazor'</span></span>
- <span data-ttu-id="9182f-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1284">'Identity'</span></span>
- <span data-ttu-id="9182f-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1286">'Razor'</span></span>
- <span data-ttu-id="9182f-1287">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1289">'Blazor'</span></span>
- <span data-ttu-id="9182f-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1290">'Identity'</span></span>
- <span data-ttu-id="9182f-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1292">'Razor'</span></span>
- <span data-ttu-id="9182f-1293">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1295">'Blazor'</span></span>
- <span data-ttu-id="9182f-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1296">'Identity'</span></span>
- <span data-ttu-id="9182f-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1298">'Razor'</span></span>
- <span data-ttu-id="9182f-1299">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1301">'Blazor'</span></span>
- <span data-ttu-id="9182f-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1302">'Identity'</span></span>
- <span data-ttu-id="9182f-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1304">'Razor'</span></span>
- <span data-ttu-id="9182f-1305">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1307">'Blazor'</span></span>
- <span data-ttu-id="9182f-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1308">'Identity'</span></span>
- <span data-ttu-id="9182f-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1310">'Razor'</span></span>
- <span data-ttu-id="9182f-1311">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1313">'Blazor'</span></span>
- <span data-ttu-id="9182f-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1314">'Identity'</span></span>
- <span data-ttu-id="9182f-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1316">'Razor'</span></span>
- <span data-ttu-id="9182f-1317">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1319">'Blazor'</span></span>
- <span data-ttu-id="9182f-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1320">'Identity'</span></span>
- <span data-ttu-id="9182f-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1322">'Razor'</span></span>
- <span data-ttu-id="9182f-1323">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1325">'Blazor'</span></span>
- <span data-ttu-id="9182f-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1326">'Identity'</span></span>
- <span data-ttu-id="9182f-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1328">'Razor'</span></span>
- <span data-ttu-id="9182f-1329">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1331">'Blazor'</span></span>
- <span data-ttu-id="9182f-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1332">'Identity'</span></span>
- <span data-ttu-id="9182f-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1334">'Razor'</span></span>
- <span data-ttu-id="9182f-1335">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1337">'Blazor'</span></span>
- <span data-ttu-id="9182f-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1338">'Identity'</span></span>
- <span data-ttu-id="9182f-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1340">'Razor'</span></span>
- <span data-ttu-id="9182f-1341">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1343">'Blazor'</span></span>
- <span data-ttu-id="9182f-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1344">'Identity'</span></span>
- <span data-ttu-id="9182f-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1346">'Razor'</span></span>
- <span data-ttu-id="9182f-1347">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1349">'Blazor'</span></span>
- <span data-ttu-id="9182f-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1350">'Identity'</span></span>
- <span data-ttu-id="9182f-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1352">'Razor'</span></span>
- <span data-ttu-id="9182f-1353">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1355">'Blazor'</span></span>
- <span data-ttu-id="9182f-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1356">'Identity'</span></span>
- <span data-ttu-id="9182f-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1358">'Razor'</span></span>
- <span data-ttu-id="9182f-1359">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1361">'Blazor'</span></span>
- <span data-ttu-id="9182f-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1362">'Identity'</span></span>
- <span data-ttu-id="9182f-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1364">'Razor'</span></span>
- <span data-ttu-id="9182f-1365">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1367">'Blazor'</span></span>
- <span data-ttu-id="9182f-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1368">'Identity'</span></span>
- <span data-ttu-id="9182f-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1370">'Razor'</span></span>
- <span data-ttu-id="9182f-1371">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1373">'Blazor'</span></span>
- <span data-ttu-id="9182f-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1374">'Identity'</span></span>
- <span data-ttu-id="9182f-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1376">'Razor'</span></span>
- <span data-ttu-id="9182f-1377">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1379">'Blazor'</span></span>
- <span data-ttu-id="9182f-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1380">'Identity'</span></span>
- <span data-ttu-id="9182f-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1382">'Razor'</span></span>
- <span data-ttu-id="9182f-1383">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1385">'Blazor'</span></span>
- <span data-ttu-id="9182f-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1386">'Identity'</span></span>
- <span data-ttu-id="9182f-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1388">'Razor'</span></span>
- <span data-ttu-id="9182f-1389">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1391">'Blazor'</span></span>
- <span data-ttu-id="9182f-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1392">'Identity'</span></span>
- <span data-ttu-id="9182f-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1394">'Razor'</span></span>
- <span data-ttu-id="9182f-1395">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1397">'Blazor'</span></span>
- <span data-ttu-id="9182f-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1398">'Identity'</span></span>
- <span data-ttu-id="9182f-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1400">'Razor'</span></span>
- <span data-ttu-id="9182f-1401">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1403">'Blazor'</span></span>
- <span data-ttu-id="9182f-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1404">'Identity'</span></span>
- <span data-ttu-id="9182f-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1406">'Razor'</span></span>
- <span data-ttu-id="9182f-1407">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1409">'Blazor'</span></span>
- <span data-ttu-id="9182f-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1410">'Identity'</span></span>
- <span data-ttu-id="9182f-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1412">'Razor'</span></span>
- <span data-ttu-id="9182f-1413">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1415">'Blazor'</span></span>
- <span data-ttu-id="9182f-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1416">'Identity'</span></span>
- <span data-ttu-id="9182f-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1418">'Razor'</span></span>
- <span data-ttu-id="9182f-1419">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1421">'Blazor'</span></span>
- <span data-ttu-id="9182f-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1422">'Identity'</span></span>
- <span data-ttu-id="9182f-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1424">'Razor'</span></span>
- <span data-ttu-id="9182f-1425">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1427">'Blazor'</span></span>
- <span data-ttu-id="9182f-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1428">'Identity'</span></span>
- <span data-ttu-id="9182f-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1430">'Razor'</span></span>
- <span data-ttu-id="9182f-1431">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1433">'Blazor'</span></span>
- <span data-ttu-id="9182f-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1434">'Identity'</span></span>
- <span data-ttu-id="9182f-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1436">'Razor'</span></span>
- <span data-ttu-id="9182f-1437">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1439">'Blazor'</span></span>
- <span data-ttu-id="9182f-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1440">'Identity'</span></span>
- <span data-ttu-id="9182f-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1442">'Razor'</span></span>
- <span data-ttu-id="9182f-1443">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-1444">--------------------------- | | 접두사 없음                | `CommandLineKey1=value1`                               |
| 대시 두 개(`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| 슬래시(`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="9182f-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="9182f-1445">같은 명령 내에서 등호를 사용하는 명령줄 인수 키-값 쌍을 공백을 사용하는 키-값 쌍과 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="9182f-1446">명령 예:</span><span class="sxs-lookup"><span data-stu-id="9182f-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="9182f-1447">스위치 매핑</span><span class="sxs-lookup"><span data-stu-id="9182f-1447">Switch mappings</span></span>

<span data-ttu-id="9182f-1448">스위치 매핑은 키 이름 교체 논리를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="9182f-1449"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>를 사용하여 구성을 수동으로 빌드하는 경우에는 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 메서드에 대체 스위치를 포함하는 사전을 제공하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="9182f-1450">스위치 매핑 사전을 사용하면 명령줄 인수를 통해 제공된 키와 일치하는 키에 대해 사전을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="9182f-1451">사전에서 명령줄 키가 발견되면 사전 값(대체 키)이 다시 전달되어 앱 구성의 키-값 쌍이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="9182f-1452">단일 대시(`-`) 접두사가 붙은 명령줄 키에는 스위치 매핑이 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="9182f-1453">스위치 매핑 사전 키 규칙:</span><span class="sxs-lookup"><span data-stu-id="9182f-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="9182f-1454">스위치는 단일 대시(`-`) 또는 이중 대시(`--`)로 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="9182f-1455">스위치 매핑 사전이 중복 키를 포함하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="9182f-1456">스위치 매핑 사전을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="9182f-1457">다음 예에서는 두 개의 스위치 매핑이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="9182f-1458">호스트가 빌드될 때 스위치 매핑 사전과 함께 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="9182f-1459">스위치 매핑을 사용하는 앱의 경우 `CreateDefaultBuilder`에 대한 호출은 인수를 전달하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="9182f-1460">`CreateDefaultBuilder` 메서드의 `AddCommandLine` 호출에는 매핑된 스위치가 포함되지 않으며 `CreateDefaultBuilder`에 스위치 매핑 사전을 전달할 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9182f-1461">해결 방법으로 `CreateDefaultBuilder`에 인수를 전달하지 않고 대신 `ConfigurationBuilder` 메서드의 `AddCommandLine` 메서드에서 인수와 스위치 매핑 사전을 모두 처리하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="9182f-1462">생성된 스위치 매핑 사전은 다음 표의 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="9182f-1463">Key</span><span class="sxs-lookup"><span data-stu-id="9182f-1463">Key</span></span>       | <span data-ttu-id="9182f-1464">값</span><span class="sxs-lookup"><span data-stu-id="9182f-1464">Value</span></span>             |
| ---
<span data-ttu-id="9182f-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1466">'Blazor'</span></span>
- <span data-ttu-id="9182f-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1467">'Identity'</span></span>
- <span data-ttu-id="9182f-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1469">'Razor'</span></span>
- <span data-ttu-id="9182f-1470">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1472">'Blazor'</span></span>
- <span data-ttu-id="9182f-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1473">'Identity'</span></span>
- <span data-ttu-id="9182f-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1475">'Razor'</span></span>
- <span data-ttu-id="9182f-1476">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1478">'Blazor'</span></span>
- <span data-ttu-id="9182f-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1479">'Identity'</span></span>
- <span data-ttu-id="9182f-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1481">'Razor'</span></span>
- <span data-ttu-id="9182f-1482">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1484">'Blazor'</span></span>
- <span data-ttu-id="9182f-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1485">'Identity'</span></span>
- <span data-ttu-id="9182f-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1487">'Razor'</span></span>
- <span data-ttu-id="9182f-1488">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1490">'Blazor'</span></span>
- <span data-ttu-id="9182f-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1491">'Identity'</span></span>
- <span data-ttu-id="9182f-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1493">'Razor'</span></span>
- <span data-ttu-id="9182f-1494">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1496">'Blazor'</span></span>
- <span data-ttu-id="9182f-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1497">'Identity'</span></span>
- <span data-ttu-id="9182f-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1499">'Razor'</span></span>
- <span data-ttu-id="9182f-1500">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1502">'Blazor'</span></span>
- <span data-ttu-id="9182f-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1503">'Identity'</span></span>
- <span data-ttu-id="9182f-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1505">'Razor'</span></span>
- <span data-ttu-id="9182f-1506">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1508">'Blazor'</span></span>
- <span data-ttu-id="9182f-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1509">'Identity'</span></span>
- <span data-ttu-id="9182f-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1511">'Razor'</span></span>
- <span data-ttu-id="9182f-1512">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="9182f-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="9182f-1514">앱을 시작할 때 스위치 매핑된 키가 사용되는 경우 구성은 사전에서 제공하는 키의 구성 값을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="9182f-1515">앞의 명령을 실행한 후 구성에는 다음 표에 표시된 값이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="9182f-1516">Key</span><span class="sxs-lookup"><span data-stu-id="9182f-1516">Key</span></span>               | <span data-ttu-id="9182f-1517">값</span><span class="sxs-lookup"><span data-stu-id="9182f-1517">Value</span></span>    |
| ---
<span data-ttu-id="9182f-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1519">'Blazor'</span></span>
- <span data-ttu-id="9182f-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1520">'Identity'</span></span>
- <span data-ttu-id="9182f-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1522">'Razor'</span></span>
- <span data-ttu-id="9182f-1523">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1525">'Blazor'</span></span>
- <span data-ttu-id="9182f-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1526">'Identity'</span></span>
- <span data-ttu-id="9182f-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1528">'Razor'</span></span>
- <span data-ttu-id="9182f-1529">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1531">'Blazor'</span></span>
- <span data-ttu-id="9182f-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1532">'Identity'</span></span>
- <span data-ttu-id="9182f-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1534">'Razor'</span></span>
- <span data-ttu-id="9182f-1535">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1537">'Blazor'</span></span>
- <span data-ttu-id="9182f-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1538">'Identity'</span></span>
- <span data-ttu-id="9182f-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1540">'Razor'</span></span>
- <span data-ttu-id="9182f-1541">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1543">'Blazor'</span></span>
- <span data-ttu-id="9182f-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1544">'Identity'</span></span>
- <span data-ttu-id="9182f-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1546">'Razor'</span></span>
- <span data-ttu-id="9182f-1547">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1549">'Blazor'</span></span>
- <span data-ttu-id="9182f-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1550">'Identity'</span></span>
- <span data-ttu-id="9182f-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1552">'Razor'</span></span>
- <span data-ttu-id="9182f-1553">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1555">'Blazor'</span></span>
- <span data-ttu-id="9182f-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1556">'Identity'</span></span>
- <span data-ttu-id="9182f-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1558">'Razor'</span></span>
- <span data-ttu-id="9182f-1559">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1561">'Blazor'</span></span>
- <span data-ttu-id="9182f-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1562">'Identity'</span></span>
- <span data-ttu-id="9182f-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1564">'Razor'</span></span>
- <span data-ttu-id="9182f-1565">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="9182f-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="9182f-1567">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="9182f-1568"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>는 런타임에 환경 변수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="9182f-1569">환경 변수 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="9182f-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/)를 사용하면 Azure Portal에서 환경 변수를 설정할 수 있으므로 환경 변수 구성 공급자를 사용한 앱 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="9182f-1571">자세한 내용은 [Azure 앱: Azure Portal을 사용하여 앱 구성 재정의](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="9182f-1572">새 호스트 작성기가 [웹 호스트](xref:fundamentals/host/web-host)로 초기화되고 `CreateDefaultBuilder`가 호출될 때 `AddEnvironmentVariables`는 [호스트 구성](#host-versus-app-configuration)에 대해 접두사가 `ASPNETCORE_`인 환경 변수를 로드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="9182f-1573">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9182f-1574">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9182f-1575">접두사가 없는 `AddEnvironmentVariables`의 호출을 통한 접두사가 없는 환경 변수의 앱 구성</span><span class="sxs-lookup"><span data-stu-id="9182f-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="9182f-1576">*appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성</span><span class="sxs-lookup"><span data-stu-id="9182f-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="9182f-1577">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="9182f-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9182f-1578">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="9182f-1578">Command-line arguments.</span></span>

<span data-ttu-id="9182f-1579">사용자 비밀 및 *appsettings* 파일을 통해 구성을 설정한 후 환경 변수 구성 공급자를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="9182f-1580">이 위치에서 공급자를 호출하면 런타임에 환경 변수를 읽어 들여 사용자 비밀 및 *appsettings* 파일로 설정한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="9182f-1581">추가 환경 변수에서 앱 구성을 제공하려면 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 접두사가 있는 `AddEnvironmentVariables`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="9182f-1582">지정된 접두사를 포함하는 환경 변수에서 다른 공급자의 값을 재정의할 수 있도록 하려면 `AddEnvironmentVariables`를 마지막으로 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="9182f-1583">**예제**</span><span class="sxs-lookup"><span data-stu-id="9182f-1583">**Example**</span></span>

<span data-ttu-id="9182f-1584">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddEnvironmentVariables` 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="9182f-1585">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1585">Run the sample app.</span></span> <span data-ttu-id="9182f-1586">브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9182f-1587">환경 변수 `ENVIRONMENT`에 대한 키-값 쌍이 출력에 포함되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="9182f-1588">이 값은 앱이 실행 중인 환경을 나타내며, 로컬에서 실행 중이면 일반적으로 `Development` 입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="9182f-1589">앱은 렌더링하는 환경 변수 목록을 짧게 유지하기 위해 환경 변수를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="9182f-1590">샘플 앱의 *Pages/Index.cshtml.cs* 파일을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="9182f-1591">앱에서 사용할 수 있는 모든 환경 변수를 표시하려면 *Pages/Index.cshtml.cs*에서 `FilteredConfiguration`을 다음으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="9182f-1592">접두사</span><span class="sxs-lookup"><span data-stu-id="9182f-1592">Prefixes</span></span>

<span data-ttu-id="9182f-1593">`AddEnvironmentVariables` 메서드에 접두사를 제공하면 앱의 구성에 로드된 환경 변수가 필터링됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="9182f-1594">예를 들어 환경 변수를 `CUSTOM_` 접두사로 필터링하려면 구성 공급자에 이 접두사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="9182f-1595">구성 키-값 쌍이 생성되면 접두사는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="9182f-1596">호스트 작성기를 만들 때 환경 변수에서 호스트 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="9182f-1597">환경 변수에 사용되는 접두사에 대한 자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9182f-1598">**연결 문자열 접두사**</span><span class="sxs-lookup"><span data-stu-id="9182f-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="9182f-1599">구성 API에는 앱 환경에 대한 Azure 연결 문자열 구성과 관련된 네 개의 연결 문자열 환경 변수에 대한 특별한 처리 규칙이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="9182f-1600">즉, `AddEnvironmentVariables`에 접두사를 제공하지 않으면 표에 표시된 접두사가 붙은 환경 변수가 앱으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="9182f-1601">연결 문자열 접두사</span><span class="sxs-lookup"><span data-stu-id="9182f-1601">Connection string prefix</span></span> | <span data-ttu-id="9182f-1602">공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-1602">Provider</span></span> |
| ---
<span data-ttu-id="9182f-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1604">'Blazor'</span></span>
- <span data-ttu-id="9182f-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1605">'Identity'</span></span>
- <span data-ttu-id="9182f-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1607">'Razor'</span></span>
- <span data-ttu-id="9182f-1608">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1610">'Blazor'</span></span>
- <span data-ttu-id="9182f-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1611">'Identity'</span></span>
- <span data-ttu-id="9182f-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1613">'Razor'</span></span>
- <span data-ttu-id="9182f-1614">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1616">'Blazor'</span></span>
- <span data-ttu-id="9182f-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1617">'Identity'</span></span>
- <span data-ttu-id="9182f-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1619">'Razor'</span></span>
- <span data-ttu-id="9182f-1620">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1622">'Blazor'</span></span>
- <span data-ttu-id="9182f-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1623">'Identity'</span></span>
- <span data-ttu-id="9182f-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1625">'Razor'</span></span>
- <span data-ttu-id="9182f-1626">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1628">'Blazor'</span></span>
- <span data-ttu-id="9182f-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1629">'Identity'</span></span>
- <span data-ttu-id="9182f-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1631">'Razor'</span></span>
- <span data-ttu-id="9182f-1632">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1634">'Blazor'</span></span>
- <span data-ttu-id="9182f-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1635">'Identity'</span></span>
- <span data-ttu-id="9182f-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1637">'Razor'</span></span>
- <span data-ttu-id="9182f-1638">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1640">'Blazor'</span></span>
- <span data-ttu-id="9182f-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1641">'Identity'</span></span>
- <span data-ttu-id="9182f-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1643">'Razor'</span></span>
- <span data-ttu-id="9182f-1644">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1646">'Blazor'</span></span>
- <span data-ttu-id="9182f-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1647">'Identity'</span></span>
- <span data-ttu-id="9182f-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1649">'Razor'</span></span>
- <span data-ttu-id="9182f-1650">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1652">'Blazor'</span></span>
- <span data-ttu-id="9182f-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1653">'Identity'</span></span>
- <span data-ttu-id="9182f-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1655">'Razor'</span></span>
- <span data-ttu-id="9182f-1656">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1658">'Blazor'</span></span>
- <span data-ttu-id="9182f-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1659">'Identity'</span></span>
- <span data-ttu-id="9182f-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1661">'Razor'</span></span>
- <span data-ttu-id="9182f-1662">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1664">'Blazor'</span></span>
- <span data-ttu-id="9182f-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1665">'Identity'</span></span>
- <span data-ttu-id="9182f-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1667">'Razor'</span></span>
- <span data-ttu-id="9182f-1668">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1670">'Blazor'</span></span>
- <span data-ttu-id="9182f-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1671">'Identity'</span></span>
- <span data-ttu-id="9182f-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1673">'Razor'</span></span>
- <span data-ttu-id="9182f-1674">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-1675">---- | | `CUSTOMCONNSTR_` | 사용자 지정 공급자 | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="9182f-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="9182f-1676">표에 표시된 네 개 접두사 중 하나가 붙은 환경 변수가 검색되어 구성으로 로드되면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="9182f-1677">환경 변수 접두사를 제거하고 구성 키 섹션(`ConnectionStrings`)을 추가하여 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="9182f-1678">데이터베이스 연결 제공자(지정된 공급자가 없는 `CUSTOMCONNSTR_` 제외)를 나타내는 새 구성 키-값 쌍이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="9182f-1679">환경 변수 키</span><span class="sxs-lookup"><span data-stu-id="9182f-1679">Environment variable key</span></span> | <span data-ttu-id="9182f-1680">변환된 구성 키</span><span class="sxs-lookup"><span data-stu-id="9182f-1680">Converted configuration key</span></span> | <span data-ttu-id="9182f-1681">공급자 구성 항목</span><span class="sxs-lookup"><span data-stu-id="9182f-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="9182f-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1683">'Blazor'</span></span>
- <span data-ttu-id="9182f-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1684">'Identity'</span></span>
- <span data-ttu-id="9182f-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1686">'Razor'</span></span>
- <span data-ttu-id="9182f-1687">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1689">'Blazor'</span></span>
- <span data-ttu-id="9182f-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1690">'Identity'</span></span>
- <span data-ttu-id="9182f-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1692">'Razor'</span></span>
- <span data-ttu-id="9182f-1693">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1695">'Blazor'</span></span>
- <span data-ttu-id="9182f-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1696">'Identity'</span></span>
- <span data-ttu-id="9182f-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1698">'Razor'</span></span>
- <span data-ttu-id="9182f-1699">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1701">'Blazor'</span></span>
- <span data-ttu-id="9182f-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1702">'Identity'</span></span>
- <span data-ttu-id="9182f-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1704">'Razor'</span></span>
- <span data-ttu-id="9182f-1705">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1707">'Blazor'</span></span>
- <span data-ttu-id="9182f-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1708">'Identity'</span></span>
- <span data-ttu-id="9182f-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1710">'Razor'</span></span>
- <span data-ttu-id="9182f-1711">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1713">'Blazor'</span></span>
- <span data-ttu-id="9182f-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1714">'Identity'</span></span>
- <span data-ttu-id="9182f-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1716">'Razor'</span></span>
- <span data-ttu-id="9182f-1717">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1719">'Blazor'</span></span>
- <span data-ttu-id="9182f-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1720">'Identity'</span></span>
- <span data-ttu-id="9182f-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1722">'Razor'</span></span>
- <span data-ttu-id="9182f-1723">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1725">'Blazor'</span></span>
- <span data-ttu-id="9182f-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1726">'Identity'</span></span>
- <span data-ttu-id="9182f-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1728">'Razor'</span></span>
- <span data-ttu-id="9182f-1729">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1731">'Blazor'</span></span>
- <span data-ttu-id="9182f-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1732">'Identity'</span></span>
- <span data-ttu-id="9182f-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1734">'Razor'</span></span>
- <span data-ttu-id="9182f-1735">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1737">'Blazor'</span></span>
- <span data-ttu-id="9182f-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1738">'Identity'</span></span>
- <span data-ttu-id="9182f-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1740">'Razor'</span></span>
- <span data-ttu-id="9182f-1741">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1743">'Blazor'</span></span>
- <span data-ttu-id="9182f-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1744">'Identity'</span></span>
- <span data-ttu-id="9182f-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1746">'Razor'</span></span>
- <span data-ttu-id="9182f-1747">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1749">'Blazor'</span></span>
- <span data-ttu-id="9182f-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1750">'Identity'</span></span>
- <span data-ttu-id="9182f-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1752">'Razor'</span></span>
- <span data-ttu-id="9182f-1753">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1755">'Blazor'</span></span>
- <span data-ttu-id="9182f-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1756">'Identity'</span></span>
- <span data-ttu-id="9182f-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1758">'Razor'</span></span>
- <span data-ttu-id="9182f-1759">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1761">'Blazor'</span></span>
- <span data-ttu-id="9182f-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1762">'Identity'</span></span>
- <span data-ttu-id="9182f-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1764">'Razor'</span></span>
- <span data-ttu-id="9182f-1765">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1767">'Blazor'</span></span>
- <span data-ttu-id="9182f-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1768">'Identity'</span></span>
- <span data-ttu-id="9182f-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1770">'Razor'</span></span>
- <span data-ttu-id="9182f-1771">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1773">'Blazor'</span></span>
- <span data-ttu-id="9182f-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1774">'Identity'</span></span>
- <span data-ttu-id="9182f-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1776">'Razor'</span></span>
- <span data-ttu-id="9182f-1777">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1779">'Blazor'</span></span>
- <span data-ttu-id="9182f-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1780">'Identity'</span></span>
- <span data-ttu-id="9182f-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1782">'Razor'</span></span>
- <span data-ttu-id="9182f-1783">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1785">'Blazor'</span></span>
- <span data-ttu-id="9182f-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1786">'Identity'</span></span>
- <span data-ttu-id="9182f-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1788">'Razor'</span></span>
- <span data-ttu-id="9182f-1789">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1791">'Blazor'</span></span>
- <span data-ttu-id="9182f-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1792">'Identity'</span></span>
- <span data-ttu-id="9182f-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1794">'Razor'</span></span>
- <span data-ttu-id="9182f-1795">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1797">'Blazor'</span></span>
- <span data-ttu-id="9182f-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1798">'Identity'</span></span>
- <span data-ttu-id="9182f-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1800">'Razor'</span></span>
- <span data-ttu-id="9182f-1801">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1803">'Blazor'</span></span>
- <span data-ttu-id="9182f-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1804">'Identity'</span></span>
- <span data-ttu-id="9182f-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1806">'Razor'</span></span>
- <span data-ttu-id="9182f-1807">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1809">'Blazor'</span></span>
- <span data-ttu-id="9182f-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1810">'Identity'</span></span>
- <span data-ttu-id="9182f-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1812">'Razor'</span></span>
- <span data-ttu-id="9182f-1813">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1815">'Blazor'</span></span>
- <span data-ttu-id="9182f-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1816">'Identity'</span></span>
- <span data-ttu-id="9182f-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1818">'Razor'</span></span>
- <span data-ttu-id="9182f-1819">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1821">'Blazor'</span></span>
- <span data-ttu-id="9182f-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1822">'Identity'</span></span>
- <span data-ttu-id="9182f-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1824">'Razor'</span></span>
- <span data-ttu-id="9182f-1825">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1827">'Blazor'</span></span>
- <span data-ttu-id="9182f-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1828">'Identity'</span></span>
- <span data-ttu-id="9182f-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1830">'Razor'</span></span>
- <span data-ttu-id="9182f-1831">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1833">'Blazor'</span></span>
- <span data-ttu-id="9182f-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1834">'Identity'</span></span>
- <span data-ttu-id="9182f-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1836">'Razor'</span></span>
- <span data-ttu-id="9182f-1837">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1839">'Blazor'</span></span>
- <span data-ttu-id="9182f-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1840">'Identity'</span></span>
- <span data-ttu-id="9182f-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1842">'Razor'</span></span>
- <span data-ttu-id="9182f-1843">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1845">'Blazor'</span></span>
- <span data-ttu-id="9182f-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1846">'Identity'</span></span>
- <span data-ttu-id="9182f-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1848">'Razor'</span></span>
- <span data-ttu-id="9182f-1849">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1851">'Blazor'</span></span>
- <span data-ttu-id="9182f-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1852">'Identity'</span></span>
- <span data-ttu-id="9182f-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1854">'Razor'</span></span>
- <span data-ttu-id="9182f-1855">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1857">'Blazor'</span></span>
- <span data-ttu-id="9182f-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1858">'Identity'</span></span>
- <span data-ttu-id="9182f-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1860">'Razor'</span></span>
- <span data-ttu-id="9182f-1861">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1863">'Blazor'</span></span>
- <span data-ttu-id="9182f-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1864">'Identity'</span></span>
- <span data-ttu-id="9182f-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1866">'Razor'</span></span>
- <span data-ttu-id="9182f-1867">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1869">'Blazor'</span></span>
- <span data-ttu-id="9182f-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1870">'Identity'</span></span>
- <span data-ttu-id="9182f-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1872">'Razor'</span></span>
- <span data-ttu-id="9182f-1873">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1875">'Blazor'</span></span>
- <span data-ttu-id="9182f-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1876">'Identity'</span></span>
- <span data-ttu-id="9182f-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1878">'Razor'</span></span>
- <span data-ttu-id="9182f-1879">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1881">'Blazor'</span></span>
- <span data-ttu-id="9182f-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1882">'Identity'</span></span>
- <span data-ttu-id="9182f-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1884">'Razor'</span></span>
- <span data-ttu-id="9182f-1885">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1887">'Blazor'</span></span>
- <span data-ttu-id="9182f-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1888">'Identity'</span></span>
- <span data-ttu-id="9182f-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1890">'Razor'</span></span>
- <span data-ttu-id="9182f-1891">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1893">'Blazor'</span></span>
- <span data-ttu-id="9182f-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1894">'Identity'</span></span>
- <span data-ttu-id="9182f-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1896">'Razor'</span></span>
- <span data-ttu-id="9182f-1897">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1899">'Blazor'</span></span>
- <span data-ttu-id="9182f-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1900">'Identity'</span></span>
- <span data-ttu-id="9182f-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1902">'Razor'</span></span>
- <span data-ttu-id="9182f-1903">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1905">'Blazor'</span></span>
- <span data-ttu-id="9182f-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1906">'Identity'</span></span>
- <span data-ttu-id="9182f-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1908">'Razor'</span></span>
- <span data-ttu-id="9182f-1909">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1911">'Blazor'</span></span>
- <span data-ttu-id="9182f-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1912">'Identity'</span></span>
- <span data-ttu-id="9182f-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1914">'Razor'</span></span>
- <span data-ttu-id="9182f-1915">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1917">'Blazor'</span></span>
- <span data-ttu-id="9182f-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1918">'Identity'</span></span>
- <span data-ttu-id="9182f-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1920">'Razor'</span></span>
- <span data-ttu-id="9182f-1921">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1923">'Blazor'</span></span>
- <span data-ttu-id="9182f-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1924">'Identity'</span></span>
- <span data-ttu-id="9182f-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1926">'Razor'</span></span>
- <span data-ttu-id="9182f-1927">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1929">'Blazor'</span></span>
- <span data-ttu-id="9182f-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1930">'Identity'</span></span>
- <span data-ttu-id="9182f-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1932">'Razor'</span></span>
- <span data-ttu-id="9182f-1933">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1935">'Blazor'</span></span>
- <span data-ttu-id="9182f-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1936">'Identity'</span></span>
- <span data-ttu-id="9182f-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1938">'Razor'</span></span>
- <span data-ttu-id="9182f-1939">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1941">'Blazor'</span></span>
- <span data-ttu-id="9182f-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1942">'Identity'</span></span>
- <span data-ttu-id="9182f-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1944">'Razor'</span></span>
- <span data-ttu-id="9182f-1945">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1947">'Blazor'</span></span>
- <span data-ttu-id="9182f-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1948">'Identity'</span></span>
- <span data-ttu-id="9182f-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1950">'Razor'</span></span>
- <span data-ttu-id="9182f-1951">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1953">'Blazor'</span></span>
- <span data-ttu-id="9182f-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1954">'Identity'</span></span>
- <span data-ttu-id="9182f-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1956">'Razor'</span></span>
- <span data-ttu-id="9182f-1957">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1959">'Blazor'</span></span>
- <span data-ttu-id="9182f-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1960">'Identity'</span></span>
- <span data-ttu-id="9182f-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1962">'Razor'</span></span>
- <span data-ttu-id="9182f-1963">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1965">'Blazor'</span></span>
- <span data-ttu-id="9182f-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1966">'Identity'</span></span>
- <span data-ttu-id="9182f-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1968">'Razor'</span></span>
- <span data-ttu-id="9182f-1969">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1971">'Blazor'</span></span>
- <span data-ttu-id="9182f-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1972">'Identity'</span></span>
- <span data-ttu-id="9182f-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1974">'Razor'</span></span>
- <span data-ttu-id="9182f-1975">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1977">'Blazor'</span></span>
- <span data-ttu-id="9182f-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1978">'Identity'</span></span>
- <span data-ttu-id="9182f-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1980">'Razor'</span></span>
- <span data-ttu-id="9182f-1981">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1983">'Blazor'</span></span>
- <span data-ttu-id="9182f-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1984">'Identity'</span></span>
- <span data-ttu-id="9182f-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1986">'Razor'</span></span>
- <span data-ttu-id="9182f-1987">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1989">'Blazor'</span></span>
- <span data-ttu-id="9182f-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1990">'Identity'</span></span>
- <span data-ttu-id="9182f-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1992">'Razor'</span></span>
- <span data-ttu-id="9182f-1993">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1995">'Blazor'</span></span>
- <span data-ttu-id="9182f-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-1996">'Identity'</span></span>
- <span data-ttu-id="9182f-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-1998">'Razor'</span></span>
- <span data-ttu-id="9182f-1999">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2001">'Blazor'</span></span>
- <span data-ttu-id="9182f-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2002">'Identity'</span></span>
- <span data-ttu-id="9182f-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2004">'Razor'</span></span>
- <span data-ttu-id="9182f-2005">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2007">'Blazor'</span></span>
- <span data-ttu-id="9182f-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2008">'Identity'</span></span>
- <span data-ttu-id="9182f-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2010">'Razor'</span></span>
- <span data-ttu-id="9182f-2011">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2013">'Blazor'</span></span>
- <span data-ttu-id="9182f-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2014">'Identity'</span></span>
- <span data-ttu-id="9182f-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2016">'Razor'</span></span>
- <span data-ttu-id="9182f-2017">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2019">'Blazor'</span></span>
- <span data-ttu-id="9182f-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2020">'Identity'</span></span>
- <span data-ttu-id="9182f-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2022">'Razor'</span></span>
- <span data-ttu-id="9182f-2023">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2025">'Blazor'</span></span>
- <span data-ttu-id="9182f-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2026">'Identity'</span></span>
- <span data-ttu-id="9182f-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2028">'Razor'</span></span>
- <span data-ttu-id="9182f-2029">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | 구성 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="9182f-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}` | 키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9182f-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9182f-2032">값: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | 키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9182f-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9182f-2033">값: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | 키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="9182f-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="9182f-2034">값: `System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="9182f-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="9182f-2035">**예제**</span><span class="sxs-lookup"><span data-stu-id="9182f-2035">**Example**</span></span>

<span data-ttu-id="9182f-2036">서버에 사용자 지정 연결 문자열 환경 변수가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="9182f-2037">이름 &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="9182f-2037">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="9182f-2038">값 &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="9182f-2038">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="9182f-2039">`IConfiguration`이 삽입되고 `_config`라는 필드에 할당된 경우 값을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="9182f-2040">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-2040">File Configuration Provider</span></span>

<span data-ttu-id="9182f-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>는 파일 시스템에서 구성을 로드하기 위한 기본 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="9182f-2042">다음 구성 공급자는 특정 파일 형식 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="9182f-2043">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="9182f-2044">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="9182f-2045">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="9182f-2046">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-2046">INI Configuration Provider</span></span>

<span data-ttu-id="9182f-2047"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>는 런타임에 INI 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="9182f-2048">INI 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9182f-2049">콜론은 INI 파일 구성에서 콜론을 섹션 구분 기호로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="9182f-2050">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="9182f-2051">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="9182f-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="9182f-2052">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="9182f-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9182f-2053">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="9182f-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9182f-2054">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9182f-2055">INI 구성 파일의 일반적인 예:</span><span class="sxs-lookup"><span data-stu-id="9182f-2055">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="9182f-2056">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9182f-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9182f-2057">section0:key0</span></span>
* <span data-ttu-id="9182f-2058">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9182f-2058">section0:key1</span></span>
* <span data-ttu-id="9182f-2059">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="9182f-2059">section1:subsection:key</span></span>
* <span data-ttu-id="9182f-2060">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="9182f-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="9182f-2061">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="9182f-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="9182f-2062">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="9182f-2063"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>는 런타임에 JSON 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="9182f-2064">JSON 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9182f-2065">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="9182f-2066">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="9182f-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="9182f-2067">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="9182f-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9182f-2068">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="9182f-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9182f-2069">`CreateDefaultBuilder`를 사용하여 새 호스트 작성기를 초기화할 때 `AddJsonFile`이 자동으로 두 번 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9182f-2070">이 메서드는 호출되면 다음에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="9182f-2071">*appsettings.json* &ndash; 이 파일을 먼저 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2071">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="9182f-2072">파일의 환경 버전이 *appsettings.json* 파일에서 제공한 값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="9182f-2073">*appsettings.{Environment}.json* &ndash; 파일의 환경 버전은 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)을 기반으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2073">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="9182f-2074">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="9182f-2075">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="9182f-2076">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="9182f-2076">Environment variables.</span></span>
* <span data-ttu-id="9182f-2077">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="9182f-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="9182f-2078">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="9182f-2078">Command-line arguments.</span></span>

<span data-ttu-id="9182f-2079">JSON 구성 공급자를 먼저 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="9182f-2080">따라서 사용자 비밀, 환경 변수 및 명령줄 인수가 *appsettings* 파일에서 설정한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="9182f-2081">*appsettings.json* 및 *appsettings.{Environment}.json* 이외의 파일에 대한 앱 구성을 지정하도록 호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9182f-2082">**예제**</span><span class="sxs-lookup"><span data-stu-id="9182f-2082">**Example**</span></span>

<span data-ttu-id="9182f-2083">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddJsonFile` 두 번 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="9182f-2084">`AddJsonFile`에 대한 첫 번째 호출은 *appsettings.json*에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="9182f-2085">`AddJsonFile`에 대한 두 번째 호출은 *appsettings.{Environment}.json*에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="9182f-2086">샘플 앱의 *appsettings.Development.json*의 경우 다음 파일이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="9182f-2087">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2087">Run the sample app.</span></span> <span data-ttu-id="9182f-2088">브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="9182f-2089">출력에는 앱 환경을 기반으로 하는 구성에 대한 키 값 쌍이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="9182f-2090">개발 환경에서 앱을 실행할 때 `Logging:LogLevel:Default` 키의 로그 수준은 `Debug`입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="9182f-2091">프로덕션 환경에서 샘플 앱을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="9182f-2092">*Properties/launchSettings.json* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="9182f-2093">`ConfigurationSample` 프로필에서 `ASPNETCORE_ENVIRONMENT` 환경 변수의 값을 `Production`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="9182f-2094">파일을 저장하고 명령 셸에서 `dotnet run`를 사용하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="9182f-2095">*appsettings.Development.json*의 설정에서 더 이상 *appsettings.json*의 설정을 재정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="9182f-2096">`Logging:LogLevel:Default` 키의 로그 수준은 `Warning`입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="9182f-2097">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-2097">XML Configuration Provider</span></span>

<span data-ttu-id="9182f-2098"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>는 런타임에 XML 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="9182f-2099">XML 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9182f-2100">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="9182f-2101">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="9182f-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="9182f-2102">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="9182f-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="9182f-2103">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="9182f-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="9182f-2104">구성 키-값 쌍이 생성되면 구성 파일의 루트 노드는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="9182f-2105">파일에 DTD(문서 종류 정의)나 네임스페이스는 지정하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="9182f-2106">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="9182f-2107">XML 구성 파일에서는 반복 섹션에 고유 요소 이름을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="9182f-2108">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9182f-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9182f-2109">section0:key0</span></span>
* <span data-ttu-id="9182f-2110">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9182f-2110">section0:key1</span></span>
* <span data-ttu-id="9182f-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9182f-2111">section1:key0</span></span>
* <span data-ttu-id="9182f-2112">section1:key1</span><span class="sxs-lookup"><span data-stu-id="9182f-2112">section1:key1</span></span>

<span data-ttu-id="9182f-2113">같은 요소 이름을 사용하는 반복 요소는 다음과 같이 `name` 특성을 사용하여 요소를 구분하면 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="9182f-2114">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9182f-2115">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="9182f-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="9182f-2116">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="9182f-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="9182f-2117">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="9182f-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="9182f-2118">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="9182f-2118">section:section1:key:key1</span></span>

<span data-ttu-id="9182f-2119">특성을 사용하여 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="9182f-2120">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="9182f-2121">key:attribute</span><span class="sxs-lookup"><span data-stu-id="9182f-2121">key:attribute</span></span>
* <span data-ttu-id="9182f-2122">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="9182f-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="9182f-2123">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="9182f-2124"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>는 디렉터리의 파일을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="9182f-2125">키는 파일 이름이고,</span><span class="sxs-lookup"><span data-stu-id="9182f-2125">The key is the file name.</span></span> <span data-ttu-id="9182f-2126">값은 파일의 콘텐츠를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2126">The value contains the file's contents.</span></span> <span data-ttu-id="9182f-2127">파일별 키 구성 공급자는 Docker 호스팅 시나리오에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="9182f-2128">파일별 키 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="9182f-2129">파일의 `directoryPath`는 절대 경로여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="9182f-2130">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="9182f-2131">소스를 구성하는 `Action<KeyPerFileConfigurationSource>` 대리자</span><span class="sxs-lookup"><span data-stu-id="9182f-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="9182f-2132">디렉터리가 선택 사항인지 여부와 디렉터리의 경로</span><span class="sxs-lookup"><span data-stu-id="9182f-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="9182f-2133">두 개의 밑줄(`__`)은 파일 이름에서 구성 키 구분 기호로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="9182f-2134">예를 들어, 파일 이름 `Logging__LogLevel__System`은 구성 키 `Logging:LogLevel:System`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="9182f-2135">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="9182f-2136">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="9182f-2137"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>는 메모리 내 컬렉션을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="9182f-2138">메모리 내 컬렉션 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="9182f-2139">`IEnumerable<KeyValuePair<String,String>>`을 사용하여 구성 공급자를 초기화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="9182f-2140">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="9182f-2141">다음 예제에서는 구성 사전이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="9182f-2142">사전은 `AddInMemoryCollection`을 호출하여 구성을 제공하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="9182f-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="9182f-2143">GetValue</span></span>

<span data-ttu-id="9182f-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)는 지정된 키를 사용하여 구성에서 단일 값을 추출하고 해당 값을 지정된 컬렉션이 아닌 형식으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="9182f-2145">오버로드는 기본값을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="9182f-2146">다음 예제가 하는 일:</span><span class="sxs-lookup"><span data-stu-id="9182f-2146">The following example:</span></span>

* <span data-ttu-id="9182f-2147">`NumberKey` 키를 사용하여 구성에서 문자열 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="9182f-2148">구성 키에서 `NumberKey`를 찾을 수 없으면 `99`의 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="9182f-2149">값을 `int` 형식으로 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="9182f-2150">페이지에 사용할 값을 `NumberConfig` 속성에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="9182f-2151">GetSection, GetChildren 및 Exists</span><span class="sxs-lookup"><span data-stu-id="9182f-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="9182f-2152">이어지는 예제에서는 다음 JSON 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="9182f-2153">네 개의 키가 두 개 섹션에 있고, 두 섹션 중 하나에는 하위 섹션 쌍이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="9182f-2154">파일을 구성으로 읽어 들이면 구성 값을 저장하기 위해 다음과 같은 고유한 계층 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="9182f-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="9182f-2155">section0:key0</span></span>
* <span data-ttu-id="9182f-2156">section0:key1</span><span class="sxs-lookup"><span data-stu-id="9182f-2156">section0:key1</span></span>
* <span data-ttu-id="9182f-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="9182f-2157">section1:key0</span></span>
* <span data-ttu-id="9182f-2158">section1:key1</span><span class="sxs-lookup"><span data-stu-id="9182f-2158">section1:key1</span></span>
* <span data-ttu-id="9182f-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="9182f-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="9182f-2160">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="9182f-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="9182f-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="9182f-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="9182f-2162">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="9182f-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="9182f-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="9182f-2163">GetSection</span></span>

<span data-ttu-id="9182f-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*)은 지정된 하위 섹션 키를 사용하여 구성 하위 섹션을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="9182f-2165">`section1`의 키-값 쌍만 포함하는 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>을 반환하려면 `GetSection`을 호출하고 섹션 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="9182f-2166">`configSection`에는 값이 포함되지 않고 키 및 경로만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="9182f-2167">마찬가지로 `section2:subsection0`의 키 값을 가져오려면 `GetSection`을 호출하고 섹션 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="9182f-2168">`GetSection`은 `null`을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="9182f-2169">일치하는 섹션을 찾을 수 없으면 빈 `IConfigurationSection`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="9182f-2170">`GetSection`이 일치하는 섹션을 반환할 때 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>는 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="9182f-2171">섹션이 존재하면 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 및 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="9182f-2172">GetChildren</span><span class="sxs-lookup"><span data-stu-id="9182f-2172">GetChildren</span></span>

<span data-ttu-id="9182f-2173">`section2`에서 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*)을 호출하면 다음을 포함하는 `IEnumerable<IConfigurationSection>`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="9182f-2174">있음</span><span class="sxs-lookup"><span data-stu-id="9182f-2174">Exists</span></span>

<span data-ttu-id="9182f-2175">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*)를 사용하면 구성 섹션이 있는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="9182f-2176">예제 데이터의 경우 구성 데이터에 `section2:subsection2` 섹션이 없으므로 `sectionExists`는 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="9182f-2177">개체 그래프에 바인딩</span><span class="sxs-lookup"><span data-stu-id="9182f-2177">Bind to an object graph</span></span>

<span data-ttu-id="9182f-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 전체 POCO 개체 그래프를 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="9182f-2179">단순 개체 바인딩과 마찬가지로 공용 읽기/쓰기 속성만 바인딩되었습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="9182f-2180">다음 샘플은 개체 그래프에`Metadata` 및 `Actors` 클래스가 포함된 `TvShow` 모델(*Models/TvShow.cs*)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="9182f-2181">샘플 앱의 *tvshow.xml* 파일에는 다음 구성 데이터가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="9182f-2182">`Bind` 메서드를 사용하여 전체 `TvShow` 개체 그래프에 구성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="9182f-2183">바인딩된 인스턴스는 렌더링할 속성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="9182f-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)는 지정된 형식을 바인딩하고 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="9182f-2185">`Get<T>`가 `Bind`보다 사용하기 더 간편합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="9182f-2186">다음 코드는 위의 예제에 `Get<T>`를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="9182f-2187">클래스에 배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="9182f-2187">Bind an array to a class</span></span>

<span data-ttu-id="9182f-2188">다음 샘플 앱은 이 섹션에서 설명하는 개념을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="9182f-2189"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="9182f-2190">숫자 키 세그먼트(`:0:`, `:1:`, &hellip; `:{n}:`)를 노출하는 모든 배열 형식은 POCO 클래스 배열에 배열 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="9182f-2191">바인딩은 규칙에 따라 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2191">Binding is provided by convention.</span></span> <span data-ttu-id="9182f-2192">배열 바인딩을 구현하기 위해 사용자 지정 구성 공급자가 필요하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="9182f-2193">**메모리 내 배열 처리**</span><span class="sxs-lookup"><span data-stu-id="9182f-2193">**In-memory array processing**</span></span>

<span data-ttu-id="9182f-2194">다음 표에 표시된 구성 키 및 값을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="9182f-2195">Key</span><span class="sxs-lookup"><span data-stu-id="9182f-2195">Key</span></span>             | <span data-ttu-id="9182f-2196">값</span><span class="sxs-lookup"><span data-stu-id="9182f-2196">Value</span></span>  |
| :---
<span data-ttu-id="9182f-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2198">'Blazor'</span></span>
- <span data-ttu-id="9182f-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2199">'Identity'</span></span>
- <span data-ttu-id="9182f-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2201">'Razor'</span></span>
- <span data-ttu-id="9182f-2202">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2204">'Blazor'</span></span>
- <span data-ttu-id="9182f-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2205">'Identity'</span></span>
- <span data-ttu-id="9182f-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2207">'Razor'</span></span>
- <span data-ttu-id="9182f-2208">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2210">'Blazor'</span></span>
- <span data-ttu-id="9182f-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2211">'Identity'</span></span>
- <span data-ttu-id="9182f-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2213">'Razor'</span></span>
- <span data-ttu-id="9182f-2214">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2216">'Blazor'</span></span>
- <span data-ttu-id="9182f-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2217">'Identity'</span></span>
- <span data-ttu-id="9182f-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2219">'Razor'</span></span>
- <span data-ttu-id="9182f-2220">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="9182f-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="9182f-2222">메모리 구성 공급자를 사용하는 샘플 앱에서는 다음과 같은 키 및 값이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="9182f-2223">배열은 인덱스 &num;3에 대한 값을 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="9182f-2224">구성 바인더는 null 값을 바인딩하거나 바인딩된 개체에 null 항목을 만들 수 없으며, 이 점은 이 배열을 개체에 바인딩한 결과를 설명할 때 명확해집니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="9182f-2225">샘플 앱에서는 POCO 클래스를 사용하여 바인딩된 구성 데이터를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="9182f-2226">구성 데이터는 개체에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="9182f-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 구문을 사용할 수도 있으며, 이 경우 코드가 더 간결해집니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="9182f-2228">바인딩된 개체 즉, `ArrayExample`의 인스턴스는 구성에서 배열 데이터를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="9182f-2229">`ArrayExample.Entries` 인덱스</span><span class="sxs-lookup"><span data-stu-id="9182f-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="9182f-2230">`ArrayExample.Entries` 값</span><span class="sxs-lookup"><span data-stu-id="9182f-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="9182f-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2232">'Blazor'</span></span>
- <span data-ttu-id="9182f-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2233">'Identity'</span></span>
- <span data-ttu-id="9182f-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2235">'Razor'</span></span>
- <span data-ttu-id="9182f-2236">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2238">'Blazor'</span></span>
- <span data-ttu-id="9182f-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2239">'Identity'</span></span>
- <span data-ttu-id="9182f-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2241">'Razor'</span></span>
- <span data-ttu-id="9182f-2242">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2244">'Blazor'</span></span>
- <span data-ttu-id="9182f-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2245">'Identity'</span></span>
- <span data-ttu-id="9182f-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2247">'Razor'</span></span>
- <span data-ttu-id="9182f-2248">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2250">'Blazor'</span></span>
- <span data-ttu-id="9182f-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2251">'Identity'</span></span>
- <span data-ttu-id="9182f-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2253">'Razor'</span></span>
- <span data-ttu-id="9182f-2254">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2256">'Blazor'</span></span>
- <span data-ttu-id="9182f-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2257">'Identity'</span></span>
- <span data-ttu-id="9182f-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2259">'Razor'</span></span>
- <span data-ttu-id="9182f-2260">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2262">'Blazor'</span></span>
- <span data-ttu-id="9182f-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2263">'Identity'</span></span>
- <span data-ttu-id="9182f-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2265">'Razor'</span></span>
- <span data-ttu-id="9182f-2266">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2268">'Blazor'</span></span>
- <span data-ttu-id="9182f-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2269">'Identity'</span></span>
- <span data-ttu-id="9182f-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2271">'Razor'</span></span>
- <span data-ttu-id="9182f-2272">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2274">'Blazor'</span></span>
- <span data-ttu-id="9182f-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2275">'Identity'</span></span>
- <span data-ttu-id="9182f-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2277">'Razor'</span></span>
- <span data-ttu-id="9182f-2278">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2280">'Blazor'</span></span>
- <span data-ttu-id="9182f-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2281">'Identity'</span></span>
- <span data-ttu-id="9182f-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2283">'Razor'</span></span>
- <span data-ttu-id="9182f-2284">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2286">'Blazor'</span></span>
- <span data-ttu-id="9182f-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2287">'Identity'</span></span>
- <span data-ttu-id="9182f-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2289">'Razor'</span></span>
- <span data-ttu-id="9182f-2290">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2292">'Blazor'</span></span>
- <span data-ttu-id="9182f-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2293">'Identity'</span></span>
- <span data-ttu-id="9182f-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2295">'Razor'</span></span>
- <span data-ttu-id="9182f-2296">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2298">'Blazor'</span></span>
- <span data-ttu-id="9182f-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2299">'Identity'</span></span>
- <span data-ttu-id="9182f-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2301">'Razor'</span></span>
- <span data-ttu-id="9182f-2302">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2304">'Blazor'</span></span>
- <span data-ttu-id="9182f-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2305">'Identity'</span></span>
- <span data-ttu-id="9182f-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2307">'Razor'</span></span>
- <span data-ttu-id="9182f-2308">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2310">'Blazor'</span></span>
- <span data-ttu-id="9182f-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2311">'Identity'</span></span>
- <span data-ttu-id="9182f-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2313">'Razor'</span></span>
- <span data-ttu-id="9182f-2314">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2316">'Blazor'</span></span>
- <span data-ttu-id="9182f-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2317">'Identity'</span></span>
- <span data-ttu-id="9182f-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2319">'Razor'</span></span>
- <span data-ttu-id="9182f-2320">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2322">'Blazor'</span></span>
- <span data-ttu-id="9182f-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2323">'Identity'</span></span>
- <span data-ttu-id="9182f-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2325">'Razor'</span></span>
- <span data-ttu-id="9182f-2326">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2328">'Blazor'</span></span>
- <span data-ttu-id="9182f-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2329">'Identity'</span></span>
- <span data-ttu-id="9182f-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2331">'Razor'</span></span>
- <span data-ttu-id="9182f-2332">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2334">'Blazor'</span></span>
- <span data-ttu-id="9182f-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2335">'Identity'</span></span>
- <span data-ttu-id="9182f-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2337">'Razor'</span></span>
- <span data-ttu-id="9182f-2338">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2340">'Blazor'</span></span>
- <span data-ttu-id="9182f-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2341">'Identity'</span></span>
- <span data-ttu-id="9182f-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2343">'Razor'</span></span>
- <span data-ttu-id="9182f-2344">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2346">'Blazor'</span></span>
- <span data-ttu-id="9182f-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2347">'Identity'</span></span>
- <span data-ttu-id="9182f-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2349">'Razor'</span></span>
- <span data-ttu-id="9182f-2350">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2352">'Blazor'</span></span>
- <span data-ttu-id="9182f-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2353">'Identity'</span></span>
- <span data-ttu-id="9182f-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2355">'Razor'</span></span>
- <span data-ttu-id="9182f-2356">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2358">'Blazor'</span></span>
- <span data-ttu-id="9182f-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2359">'Identity'</span></span>
- <span data-ttu-id="9182f-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2361">'Razor'</span></span>
- <span data-ttu-id="9182f-2362">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span><span class="sxs-lookup"><span data-stu-id="9182f-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="9182f-2364">바인딩된 개체의 인덱스 &num;3은 `array:4` 구성 키에 대한 구성 데이터와 `value4`의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="9182f-2365">배열이 포함된 구성 데이터를 바인딩할 때 구성 키의 배열 인덱스는 개체를 만들 때 구성 데이터를 반복하는 데에만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="9182f-2366">구성 데이터에 null 값을 유지할 수 없으며, 구성 키의 배열에서 하나 이상의 인덱스를 건너뛰더라도 바인딩된 개체에 null 값 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="9182f-2367">인덱스 &num;3에 대한 누락된 구성 항목은 `ArrayExample` 인스턴스에 바인딩하기 전에 구성에서 올바른 키-값 쌍을 생성하는 모든 구성 공급자에서 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="9182f-2368">샘플에 누락된 키-값 쌍이 있는 추가 JSON 구성 공급자가 포함된 경우 `ArrayExample.Entries`는 전체 구성 배열과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="9182f-2369">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="9182f-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="9182f-2370">`ConfigureAppConfiguration`의 경우</span><span class="sxs-lookup"><span data-stu-id="9182f-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="9182f-2371">표에 표시된 키-값 쌍이 구성으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="9182f-2372">Key</span><span class="sxs-lookup"><span data-stu-id="9182f-2372">Key</span></span>             | <span data-ttu-id="9182f-2373">값</span><span class="sxs-lookup"><span data-stu-id="9182f-2373">Value</span></span>  |
| :---
<span data-ttu-id="9182f-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2375">'Blazor'</span></span>
- <span data-ttu-id="9182f-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2376">'Identity'</span></span>
- <span data-ttu-id="9182f-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2378">'Razor'</span></span>
- <span data-ttu-id="9182f-2379">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2381">'Blazor'</span></span>
- <span data-ttu-id="9182f-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2382">'Identity'</span></span>
- <span data-ttu-id="9182f-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2384">'Razor'</span></span>
- <span data-ttu-id="9182f-2385">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2387">'Blazor'</span></span>
- <span data-ttu-id="9182f-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2388">'Identity'</span></span>
- <span data-ttu-id="9182f-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2390">'Razor'</span></span>
- <span data-ttu-id="9182f-2391">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2393">'Blazor'</span></span>
- <span data-ttu-id="9182f-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2394">'Identity'</span></span>
- <span data-ttu-id="9182f-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2396">'Razor'</span></span>
- <span data-ttu-id="9182f-2397">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-2398">-------: | :----: | | array:entries:3 | value3 |</span><span class="sxs-lookup"><span data-stu-id="9182f-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="9182f-2399">JSON 구성 공급자에 인덱스 &num;3에 대한 항목이 포함된 후 `ArrayExample` 클래스 인스턴스를 바인딩하는 경우 `ArrayExample.Entries` 배열에 이 값이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="9182f-2400">`ArrayExample.Entries` 인덱스</span><span class="sxs-lookup"><span data-stu-id="9182f-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="9182f-2401">`ArrayExample.Entries` 값</span><span class="sxs-lookup"><span data-stu-id="9182f-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="9182f-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2403">'Blazor'</span></span>
- <span data-ttu-id="9182f-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2404">'Identity'</span></span>
- <span data-ttu-id="9182f-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2406">'Razor'</span></span>
- <span data-ttu-id="9182f-2407">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2409">'Blazor'</span></span>
- <span data-ttu-id="9182f-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2410">'Identity'</span></span>
- <span data-ttu-id="9182f-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2412">'Razor'</span></span>
- <span data-ttu-id="9182f-2413">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2415">'Blazor'</span></span>
- <span data-ttu-id="9182f-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2416">'Identity'</span></span>
- <span data-ttu-id="9182f-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2418">'Razor'</span></span>
- <span data-ttu-id="9182f-2419">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2421">'Blazor'</span></span>
- <span data-ttu-id="9182f-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2422">'Identity'</span></span>
- <span data-ttu-id="9182f-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2424">'Razor'</span></span>
- <span data-ttu-id="9182f-2425">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2427">'Blazor'</span></span>
- <span data-ttu-id="9182f-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2428">'Identity'</span></span>
- <span data-ttu-id="9182f-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2430">'Razor'</span></span>
- <span data-ttu-id="9182f-2431">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2433">'Blazor'</span></span>
- <span data-ttu-id="9182f-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2434">'Identity'</span></span>
- <span data-ttu-id="9182f-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2436">'Razor'</span></span>
- <span data-ttu-id="9182f-2437">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2439">'Blazor'</span></span>
- <span data-ttu-id="9182f-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2440">'Identity'</span></span>
- <span data-ttu-id="9182f-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2442">'Razor'</span></span>
- <span data-ttu-id="9182f-2443">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2445">'Blazor'</span></span>
- <span data-ttu-id="9182f-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2446">'Identity'</span></span>
- <span data-ttu-id="9182f-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2448">'Razor'</span></span>
- <span data-ttu-id="9182f-2449">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2451">'Blazor'</span></span>
- <span data-ttu-id="9182f-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2452">'Identity'</span></span>
- <span data-ttu-id="9182f-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2454">'Razor'</span></span>
- <span data-ttu-id="9182f-2455">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2457">'Blazor'</span></span>
- <span data-ttu-id="9182f-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2458">'Identity'</span></span>
- <span data-ttu-id="9182f-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2460">'Razor'</span></span>
- <span data-ttu-id="9182f-2461">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2463">'Blazor'</span></span>
- <span data-ttu-id="9182f-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2464">'Identity'</span></span>
- <span data-ttu-id="9182f-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2466">'Razor'</span></span>
- <span data-ttu-id="9182f-2467">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2469">'Blazor'</span></span>
- <span data-ttu-id="9182f-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2470">'Identity'</span></span>
- <span data-ttu-id="9182f-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2472">'Razor'</span></span>
- <span data-ttu-id="9182f-2473">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2475">'Blazor'</span></span>
- <span data-ttu-id="9182f-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2476">'Identity'</span></span>
- <span data-ttu-id="9182f-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2478">'Razor'</span></span>
- <span data-ttu-id="9182f-2479">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2481">'Blazor'</span></span>
- <span data-ttu-id="9182f-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2482">'Identity'</span></span>
- <span data-ttu-id="9182f-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2484">'Razor'</span></span>
- <span data-ttu-id="9182f-2485">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2487">'Blazor'</span></span>
- <span data-ttu-id="9182f-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2488">'Identity'</span></span>
- <span data-ttu-id="9182f-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2490">'Razor'</span></span>
- <span data-ttu-id="9182f-2491">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2493">'Blazor'</span></span>
- <span data-ttu-id="9182f-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2494">'Identity'</span></span>
- <span data-ttu-id="9182f-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2496">'Razor'</span></span>
- <span data-ttu-id="9182f-2497">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2499">'Blazor'</span></span>
- <span data-ttu-id="9182f-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2500">'Identity'</span></span>
- <span data-ttu-id="9182f-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2502">'Razor'</span></span>
- <span data-ttu-id="9182f-2503">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2505">'Blazor'</span></span>
- <span data-ttu-id="9182f-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2506">'Identity'</span></span>
- <span data-ttu-id="9182f-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2508">'Razor'</span></span>
- <span data-ttu-id="9182f-2509">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2511">'Blazor'</span></span>
- <span data-ttu-id="9182f-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2512">'Identity'</span></span>
- <span data-ttu-id="9182f-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2514">'Razor'</span></span>
- <span data-ttu-id="9182f-2515">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2517">'Blazor'</span></span>
- <span data-ttu-id="9182f-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2518">'Identity'</span></span>
- <span data-ttu-id="9182f-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2520">'Razor'</span></span>
- <span data-ttu-id="9182f-2521">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2523">'Blazor'</span></span>
- <span data-ttu-id="9182f-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2524">'Identity'</span></span>
- <span data-ttu-id="9182f-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2526">'Razor'</span></span>
- <span data-ttu-id="9182f-2527">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2529">'Blazor'</span></span>
- <span data-ttu-id="9182f-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2530">'Identity'</span></span>
- <span data-ttu-id="9182f-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2532">'Razor'</span></span>
- <span data-ttu-id="9182f-2533">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span><span class="sxs-lookup"><span data-stu-id="9182f-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="9182f-2535">**JSON 배열 처리**</span><span class="sxs-lookup"><span data-stu-id="9182f-2535">**JSON array processing**</span></span>

<span data-ttu-id="9182f-2536">JSON 파일에 배열이 포함된 경우 0부터 시작하는 섹션 인덱스를 사용하여 배열 요소에 대한 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="9182f-2537">다음 구성 파일에서 `subsection`은 배열입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="9182f-2538">JSON 구성 공급자는 구성 데이터를 다음 키-값 쌍으로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="9182f-2539">Key</span><span class="sxs-lookup"><span data-stu-id="9182f-2539">Key</span></span>                     | <span data-ttu-id="9182f-2540">값</span><span class="sxs-lookup"><span data-stu-id="9182f-2540">Value</span></span>  |
| ---
<span data-ttu-id="9182f-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2542">'Blazor'</span></span>
- <span data-ttu-id="9182f-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2543">'Identity'</span></span>
- <span data-ttu-id="9182f-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2545">'Razor'</span></span>
- <span data-ttu-id="9182f-2546">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2548">'Blazor'</span></span>
- <span data-ttu-id="9182f-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2549">'Identity'</span></span>
- <span data-ttu-id="9182f-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2551">'Razor'</span></span>
- <span data-ttu-id="9182f-2552">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2554">'Blazor'</span></span>
- <span data-ttu-id="9182f-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2555">'Identity'</span></span>
- <span data-ttu-id="9182f-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2557">'Razor'</span></span>
- <span data-ttu-id="9182f-2558">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2560">'Blazor'</span></span>
- <span data-ttu-id="9182f-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2561">'Identity'</span></span>
- <span data-ttu-id="9182f-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2563">'Razor'</span></span>
- <span data-ttu-id="9182f-2564">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2566">'Blazor'</span></span>
- <span data-ttu-id="9182f-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2567">'Identity'</span></span>
- <span data-ttu-id="9182f-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2569">'Razor'</span></span>
- <span data-ttu-id="9182f-2570">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2572">'Blazor'</span></span>
- <span data-ttu-id="9182f-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2573">'Identity'</span></span>
- <span data-ttu-id="9182f-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2575">'Razor'</span></span>
- <span data-ttu-id="9182f-2576">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2578">'Blazor'</span></span>
- <span data-ttu-id="9182f-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2579">'Identity'</span></span>
- <span data-ttu-id="9182f-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2581">'Razor'</span></span>
- <span data-ttu-id="9182f-2582">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2584">'Blazor'</span></span>
- <span data-ttu-id="9182f-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2585">'Identity'</span></span>
- <span data-ttu-id="9182f-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2587">'Razor'</span></span>
- <span data-ttu-id="9182f-2588">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2590">'Blazor'</span></span>
- <span data-ttu-id="9182f-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2591">'Identity'</span></span>
- <span data-ttu-id="9182f-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2593">'Razor'</span></span>
- <span data-ttu-id="9182f-2594">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span><span class="sxs-lookup"><span data-stu-id="9182f-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="9182f-2596">샘플 앱에서는 다음 POCO 클래스를 사용하여 구성 키-값 쌍을 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="9182f-2597">바인딩 후 `JsonArrayExample.Key`는 `valueA` 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="9182f-2598">하위 섹션 값은 POCO 배열 속성 `Subsection`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="9182f-2599">`JsonArrayExample.Subsection` 인덱스</span><span class="sxs-lookup"><span data-stu-id="9182f-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="9182f-2600">`JsonArrayExample.Subsection` 값</span><span class="sxs-lookup"><span data-stu-id="9182f-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="9182f-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2602">'Blazor'</span></span>
- <span data-ttu-id="9182f-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2603">'Identity'</span></span>
- <span data-ttu-id="9182f-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2605">'Razor'</span></span>
- <span data-ttu-id="9182f-2606">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2608">'Blazor'</span></span>
- <span data-ttu-id="9182f-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2609">'Identity'</span></span>
- <span data-ttu-id="9182f-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2611">'Razor'</span></span>
- <span data-ttu-id="9182f-2612">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2614">'Blazor'</span></span>
- <span data-ttu-id="9182f-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2615">'Identity'</span></span>
- <span data-ttu-id="9182f-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2617">'Razor'</span></span>
- <span data-ttu-id="9182f-2618">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2620">'Blazor'</span></span>
- <span data-ttu-id="9182f-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2621">'Identity'</span></span>
- <span data-ttu-id="9182f-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2623">'Razor'</span></span>
- <span data-ttu-id="9182f-2624">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2626">'Blazor'</span></span>
- <span data-ttu-id="9182f-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2627">'Identity'</span></span>
- <span data-ttu-id="9182f-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2629">'Razor'</span></span>
- <span data-ttu-id="9182f-2630">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2632">'Blazor'</span></span>
- <span data-ttu-id="9182f-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2633">'Identity'</span></span>
- <span data-ttu-id="9182f-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2635">'Razor'</span></span>
- <span data-ttu-id="9182f-2636">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2638">'Blazor'</span></span>
- <span data-ttu-id="9182f-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2639">'Identity'</span></span>
- <span data-ttu-id="9182f-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2641">'Razor'</span></span>
- <span data-ttu-id="9182f-2642">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2644">'Blazor'</span></span>
- <span data-ttu-id="9182f-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2645">'Identity'</span></span>
- <span data-ttu-id="9182f-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2647">'Razor'</span></span>
- <span data-ttu-id="9182f-2648">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2650">'Blazor'</span></span>
- <span data-ttu-id="9182f-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2651">'Identity'</span></span>
- <span data-ttu-id="9182f-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2653">'Razor'</span></span>
- <span data-ttu-id="9182f-2654">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2656">'Blazor'</span></span>
- <span data-ttu-id="9182f-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2657">'Identity'</span></span>
- <span data-ttu-id="9182f-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2659">'Razor'</span></span>
- <span data-ttu-id="9182f-2660">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2662">'Blazor'</span></span>
- <span data-ttu-id="9182f-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2663">'Identity'</span></span>
- <span data-ttu-id="9182f-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2665">'Razor'</span></span>
- <span data-ttu-id="9182f-2666">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2668">'Blazor'</span></span>
- <span data-ttu-id="9182f-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2669">'Identity'</span></span>
- <span data-ttu-id="9182f-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2671">'Razor'</span></span>
- <span data-ttu-id="9182f-2672">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2674">'Blazor'</span></span>
- <span data-ttu-id="9182f-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2675">'Identity'</span></span>
- <span data-ttu-id="9182f-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2677">'Razor'</span></span>
- <span data-ttu-id="9182f-2678">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2680">'Blazor'</span></span>
- <span data-ttu-id="9182f-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2681">'Identity'</span></span>
- <span data-ttu-id="9182f-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2683">'Razor'</span></span>
- <span data-ttu-id="9182f-2684">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2686">'Blazor'</span></span>
- <span data-ttu-id="9182f-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2687">'Identity'</span></span>
- <span data-ttu-id="9182f-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2689">'Razor'</span></span>
- <span data-ttu-id="9182f-2690">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2692">'Blazor'</span></span>
- <span data-ttu-id="9182f-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2693">'Identity'</span></span>
- <span data-ttu-id="9182f-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2695">'Razor'</span></span>
- <span data-ttu-id="9182f-2696">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2698">'Blazor'</span></span>
- <span data-ttu-id="9182f-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2699">'Identity'</span></span>
- <span data-ttu-id="9182f-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2701">'Razor'</span></span>
- <span data-ttu-id="9182f-2702">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2704">'Blazor'</span></span>
- <span data-ttu-id="9182f-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2705">'Identity'</span></span>
- <span data-ttu-id="9182f-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2707">'Razor'</span></span>
- <span data-ttu-id="9182f-2708">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2710">'Blazor'</span></span>
- <span data-ttu-id="9182f-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2711">'Identity'</span></span>
- <span data-ttu-id="9182f-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2713">'Razor'</span></span>
- <span data-ttu-id="9182f-2714">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2716">'Blazor'</span></span>
- <span data-ttu-id="9182f-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2717">'Identity'</span></span>
- <span data-ttu-id="9182f-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2719">'Razor'</span></span>
- <span data-ttu-id="9182f-2720">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2722">'Blazor'</span></span>
- <span data-ttu-id="9182f-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2723">'Identity'</span></span>
- <span data-ttu-id="9182f-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2725">'Razor'</span></span>
- <span data-ttu-id="9182f-2726">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2728">'Blazor'</span></span>
- <span data-ttu-id="9182f-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2729">'Identity'</span></span>
- <span data-ttu-id="9182f-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2731">'Razor'</span></span>
- <span data-ttu-id="9182f-2732">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2734">'Blazor'</span></span>
- <span data-ttu-id="9182f-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2735">'Identity'</span></span>
- <span data-ttu-id="9182f-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2737">'Razor'</span></span>
- <span data-ttu-id="9182f-2738">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2740">'Blazor'</span></span>
- <span data-ttu-id="9182f-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2741">'Identity'</span></span>
- <span data-ttu-id="9182f-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2743">'Razor'</span></span>
- <span data-ttu-id="9182f-2744">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2746">'Blazor'</span></span>
- <span data-ttu-id="9182f-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2747">'Identity'</span></span>
- <span data-ttu-id="9182f-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2749">'Razor'</span></span>
- <span data-ttu-id="9182f-2750">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2752">'Blazor'</span></span>
- <span data-ttu-id="9182f-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2753">'Identity'</span></span>
- <span data-ttu-id="9182f-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2755">'Razor'</span></span>
- <span data-ttu-id="9182f-2756">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2758">'Blazor'</span></span>
- <span data-ttu-id="9182f-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2759">'Identity'</span></span>
- <span data-ttu-id="9182f-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2761">'Razor'</span></span>
- <span data-ttu-id="9182f-2762">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="9182f-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="9182f-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9182f-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2764">'Blazor'</span></span>
- <span data-ttu-id="9182f-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9182f-2765">'Identity'</span></span>
- <span data-ttu-id="9182f-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9182f-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="9182f-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9182f-2767">'Razor'</span></span>
- <span data-ttu-id="9182f-2768">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="9182f-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="9182f-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span><span class="sxs-lookup"><span data-stu-id="9182f-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="9182f-2770">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="9182f-2770">Custom configuration provider</span></span>

<span data-ttu-id="9182f-2771">샘플 앱에서는 [EF(Entity Framework)](/ef/core/)를 사용하여 데이터베이스에서 구성 키-값 쌍을 읽는 기본 구성 공급자를 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="9182f-2772">이 공급자의 특징은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="9182f-2773">데모용으로 EF 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="9182f-2774">연결 문자열이 필요한 데이터베이스를 사용하려면 보조 `ConfigurationBuilder`를 구현하여 다른 구성 공급자에서 연결 문자열을 제공하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="9182f-2775">이 공급자는 시작 시 데이터베이스 테이블을 구성으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="9182f-2776">이 공급자는 키별 기준으로 데이터베이스를 쿼리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="9182f-2777">변경 시 다시 로드가 구현되지 않으므로 앱 시작 후 데이터베이스를 업데이트해도 앱 구성에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="9182f-2778">데이터베이스에 구성 값을 저장하는 `EFConfigurationValue` 엔터티를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="9182f-2779">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="9182f-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="9182f-2780">구성된 값을 저장 및 액세스하는 `EFConfigurationContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="9182f-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="9182f-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="9182f-2782"><xref:Microsoft.Extensions.Configuration.IConfigurationSource>를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="9182f-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="9182f-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="9182f-2784"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider>에서 상속하여 사용자 지정 구성 공급자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="9182f-2785">구성 공급자는 비어 있는 데이터베이스를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="9182f-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="9182f-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="9182f-2787">`AddEFConfiguration` 확장 메서드를 사용하여 구성 소스를 `ConfigurationBuilder`에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="9182f-2788">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="9182f-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="9182f-2789">다음 코드는 *Program.cs*에서 사용자 지정 `EFConfigurationProvider`를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="9182f-2790">시작하는 동안 구성에 액세스</span><span class="sxs-lookup"><span data-stu-id="9182f-2790">Access configuration during startup</span></span>

<span data-ttu-id="9182f-2791">`Startup.ConfigureServices`의 구성 값에 액세스하려면 `IConfiguration`을 `Startup` 생성자에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9182f-2792">`Startup.Configure`의 구성에 액세스하려면 `IConfiguration`을 메서드에 직접 삽입하거나 생성자의 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="9182f-2793">시작 편의 메서드를 사용하여 구성에 액세스하는 방법의 예는 [앱 시작: 편리한 메서드](xref:fundamentals/startup#convenience-methods) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="9182f-2794">Razor Pages 페이지 또는 MVC 뷰에서 구성에 액세스</span><span class="sxs-lookup"><span data-stu-id="9182f-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="9182f-2795">Razor Pages 페이지나 MVC 뷰에서 구성 설정에 액세스하려면 [Microsoft.Extensions.Configuration 네임스페이스](xref:Microsoft.Extensions.Configuration)에 [using 지시문](xref:mvc/views/razor#using)([C# 참조: using 지시문](/dotnet/csharp/language-reference/keywords/using-directive))을 추가하고 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 페이지 또는 뷰로 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="9182f-2796">Razor Pages 페이지에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2796">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="9182f-2797">MVC 뷰에서:</span><span class="sxs-lookup"><span data-stu-id="9182f-2797">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="9182f-2798">외부 어셈블리의 구성 추가</span><span class="sxs-lookup"><span data-stu-id="9182f-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="9182f-2799"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 구현은 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9182f-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="9182f-2800">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9182f-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9182f-2801">추가 자료</span><span class="sxs-lookup"><span data-stu-id="9182f-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
