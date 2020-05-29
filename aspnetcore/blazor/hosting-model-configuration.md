---
<span data-ttu-id="cdaf9-101">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-103">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-104">'Identity'</span></span>
- <span data-ttu-id="cdaf9-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-106">'Razor'</span></span>
- <span data-ttu-id="cdaf9-107">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="cdaf9-108">ASP.NET Core Blazor 호스팅 모델 구성</span><span class="sxs-lookup"><span data-stu-id="cdaf9-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="cdaf9-109">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cdaf9-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cdaf9-110">이 문서에서는 호스팅 모델 구성에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="cdaf9-111"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="cdaf9-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="cdaf9-112">환경</span><span class="sxs-lookup"><span data-stu-id="cdaf9-112">Environment</span></span>

<span data-ttu-id="cdaf9-113">앱을 로컬에서 실행하면 환경이 기본적으로 개발로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="cdaf9-114">앱이 게시되면 환경이 기본적으로 프로덕션으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="cdaf9-115">호스트된 Blazor WebAssembly 앱은 `blazor-environment` 헤더를 추가하여 브라우저에 환경을 전달하는 미들웨어를 통해 서버에서 환경을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="cdaf9-116">헤더의 값은 해당 환경입니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-116">The value of the header is the environment.</span></span> <span data-ttu-id="cdaf9-117">호스트된 Blazor 앱과 서버 앱은 동일한 환경을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="cdaf9-118">환경을 구성하는 방법을 비롯한 자세한 내용은 <xref:fundamentals/environments> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="cdaf9-119">로컬에서 실행되는 독립 실행형 앱의 경우 개발 서버는 `blazor-environment` 헤더를 추가하여 개발 환경을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="cdaf9-120">다른 호스팅 환경을 위한 환경을 지정하려면 `blazor-environment` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="cdaf9-121">다음 IIS 예제에서는 게시된 *web.config* 파일에 사용자 지정 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="cdaf9-122">*web.config* 파일은 *bin/Release/{대상 프레임워크}/publish* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="cdaf9-123">앱이 *publish* 폴더에 게시될 때 덮어쓰지 않는 IIS용 사용자 지정 *web.config* 파일을 사용하려면 <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="cdaf9-124"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>를 삽입하고 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> 속성을 읽어 구성 요소에서 앱의 환경을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="cdaf9-125">시작하는 동안 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder>는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 속성을 통해 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>를 노출하므로 개발자가 환경 관련 논리를 코드에 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="cdaf9-126">다음과 같은 편리한 확장 메서드를 통해 현재 환경이 개발, 프로덕션, 스테이징 및 사용자 지정 환경 이름용인지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="cdaf9-127"><xref:Microsoft.AspNetCore.Components.NavigationManager> 서비스를 사용할 수 없는 경우 시작하는 동안 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 속성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="cdaf9-128">Configuration</span><span class="sxs-lookup"><span data-stu-id="cdaf9-128">Configuration</span></span>

Blazor<span data-ttu-id="cdaf9-129"> WebAssembly는 다음에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-129"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="cdaf9-130">기본적인 앱 설정 파일:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-130">App settings files by default:</span></span>
  * <span data-ttu-id="cdaf9-131">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="cdaf9-131">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="cdaf9-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span><span class="sxs-lookup"><span data-stu-id="cdaf9-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="cdaf9-133">앱에 등록된 다른 [구성 공급자](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="cdaf9-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="cdaf9-134">일부 공급자는 Blazor WebAssembly 앱에 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="cdaf9-135">Blazor WebAssembly를 지원하는 공급자에 관한 설명은 [Blazor WASM의 구성 공급자 설명(dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134)에서 추적됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="cdaf9-136">Blazor WebAssembly 앱의 구성은 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="cdaf9-137">**구성에 앱 비밀이나 자격 증명을 저장하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="cdaf9-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="cdaf9-138">구성 공급자에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="cdaf9-139">앱 설정 구성</span><span class="sxs-lookup"><span data-stu-id="cdaf9-139">App settings configuration</span></span>

<span data-ttu-id="cdaf9-140">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-140">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="cdaf9-141"><xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="cdaf9-142">공급자 구성</span><span class="sxs-lookup"><span data-stu-id="cdaf9-142">Provider configuration</span></span>

<span data-ttu-id="cdaf9-143">다음 예제에서는 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>를 사용하여 추가 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="cdaf9-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-144">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="cdaf9-145"><xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

<span data-ttu-id="cdaf9-146">*wwwroot* 폴더에서 구성으로 다른 구성 파일을 읽으려면 <xref:System.Net.Http.HttpClient>를 사용하여 파일 콘텐츠를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="cdaf9-147">이 방법을 사용하는 경우 다음 예제와 같이 기존 <xref:System.Net.Http.HttpClient> 서비스 등록은 생성된 로컬 클라이언트를 사용하여 파일을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="cdaf9-148">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-148">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="cdaf9-149">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-149">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

#### <a name="authentication-configuration"></a><span data-ttu-id="cdaf9-150">인증 구성</span><span class="sxs-lookup"><span data-stu-id="cdaf9-150">Authentication configuration</span></span>

<span data-ttu-id="cdaf9-151">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-151">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="cdaf9-152">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-152">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions);
```

#### <a name="logging-configuration"></a><span data-ttu-id="cdaf9-153">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="cdaf9-153">Logging configuration</span></span>

<span data-ttu-id="cdaf9-154">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-154">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="cdaf9-155">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-155">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="cdaf9-156">호스트 빌더 구성</span><span class="sxs-lookup"><span data-stu-id="cdaf9-156">Host builder configuration</span></span>

<span data-ttu-id="cdaf9-157">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-157">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="cdaf9-158">캐시된 구성</span><span class="sxs-lookup"><span data-stu-id="cdaf9-158">Cached configuration</span></span>

<span data-ttu-id="cdaf9-159">구성 파일은 오프라인으로 사용할 수 있도록 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-159">Configuration files are cached for offline use.</span></span> <span data-ttu-id="cdaf9-160">[PWA(프로그레시브 웹 애플리케이션)](xref:blazor/progressive-web-app)의 경우 새 배포를 만들 때만 구성 파일을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-160">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="cdaf9-161">여러 배포 간에 구성 파일을 편집하는 것은 다음과 같은 이유로 인해 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-161">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="cdaf9-162">사용자에게 계속해서 사용할 수 있는 캐시된 버전의 파일이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-162">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="cdaf9-163">사용자가 다음번에 온라인으로 방문할 때 앱이 다시 배포되었음을 앱에 알려 주는 PWA의 *service-worker.js* 파일과 *service-worker-assets.js* 파일은 컴파일 시 다시 빌드되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-163">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="cdaf9-164">PWA가 백그라운드 업데이트를 처리하는 방식에 대한 자세한 내용은 <xref:blazor/progressive-web-app#background-updates> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-164">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="cdaf9-165">로깅</span><span class="sxs-lookup"><span data-stu-id="cdaf9-165">Logging</span></span>

<span data-ttu-id="cdaf9-166">Blazor WebAssembly 로깅 지원에 대한 자세한 내용은 <xref:fundamentals/logging/index#create-logs-in-blazor>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-166">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="cdaf9-167"> 서버</span><span class="sxs-lookup"><span data-stu-id="cdaf9-167"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="cdaf9-168">UI에 연결 상태 반영</span><span class="sxs-lookup"><span data-stu-id="cdaf9-168">Reflect the connection state in the UI</span></span>

<span data-ttu-id="cdaf9-169">클라이언트에서 연결이 끊어진 것을 감지하면, 클라이언트가 다시 연결하는 동안 기본 UI가 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-169">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="cdaf9-170">다시 연결하지 못한 경우 사용자에게 다시 시도 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-170">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="cdaf9-171">UI를 사용자 지정하려면 *_Host.cshtml* Razor 페이지의 `<body>`에서 `components-reconnect-modal`의 `id`를 사용하여 요소를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-171">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="cdaf9-172">다음 표에서는 `components-reconnect-modal` 요소에 적용된 CSS 클래스에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-172">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="cdaf9-173">CSS 클래스</span><span class="sxs-lookup"><span data-stu-id="cdaf9-173">CSS class</span></span>                       | <span data-ttu-id="cdaf9-174">표시&hellip;</span><span class="sxs-lookup"><span data-stu-id="cdaf9-174">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="cdaf9-175">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-175">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-177">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-178">'Identity'</span></span>
- <span data-ttu-id="cdaf9-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-180">'Razor'</span></span>
- <span data-ttu-id="cdaf9-181">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-182">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-182">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-184">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-185">'Identity'</span></span>
- <span data-ttu-id="cdaf9-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-187">'Razor'</span></span>
- <span data-ttu-id="cdaf9-188">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-189">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-189">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-191">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-191">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-192">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-192">'Identity'</span></span>
- <span data-ttu-id="cdaf9-193">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-193">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-194">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-194">'Razor'</span></span>
- <span data-ttu-id="cdaf9-195">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-195">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-196">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-196">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-198">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-199">'Identity'</span></span>
- <span data-ttu-id="cdaf9-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-201">'Razor'</span></span>
- <span data-ttu-id="cdaf9-202">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-203">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-203">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-205">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-206">'Identity'</span></span>
- <span data-ttu-id="cdaf9-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-208">'Razor'</span></span>
- <span data-ttu-id="cdaf9-209">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-210">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-210">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-212">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-213">'Identity'</span></span>
- <span data-ttu-id="cdaf9-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-215">'Razor'</span></span>
- <span data-ttu-id="cdaf9-216">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-217">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-217">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-219">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-219">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-220">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-220">'Identity'</span></span>
- <span data-ttu-id="cdaf9-221">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-221">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-222">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-222">'Razor'</span></span>
- <span data-ttu-id="cdaf9-223">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-223">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-224">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-224">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-226">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-227">'Identity'</span></span>
- <span data-ttu-id="cdaf9-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-229">'Razor'</span></span>
- <span data-ttu-id="cdaf9-230">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-231">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-231">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-233">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-234">'Identity'</span></span>
- <span data-ttu-id="cdaf9-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-236">'Razor'</span></span>
- <span data-ttu-id="cdaf9-237">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-237">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-238">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-238">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-240">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-241">'Identity'</span></span>
- <span data-ttu-id="cdaf9-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-243">'Razor'</span></span>
- <span data-ttu-id="cdaf9-244">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-245">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-245">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-247">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-248">'Identity'</span></span>
- <span data-ttu-id="cdaf9-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-250">'Razor'</span></span>
- <span data-ttu-id="cdaf9-251">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-252">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-252">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-254">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-255">'Identity'</span></span>
- <span data-ttu-id="cdaf9-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-257">'Razor'</span></span>
- <span data-ttu-id="cdaf9-258">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-259">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-259">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-261">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-262">'Identity'</span></span>
- <span data-ttu-id="cdaf9-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-264">'Razor'</span></span>
- <span data-ttu-id="cdaf9-265">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-265">'SignalR' uid:</span></span> 

<span data-ttu-id="cdaf9-266">---------------- | --- title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-266">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-268">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-269">'Identity'</span></span>
- <span data-ttu-id="cdaf9-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-271">'Razor'</span></span>
- <span data-ttu-id="cdaf9-272">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-273">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-273">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-275">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-276">'Identity'</span></span>
- <span data-ttu-id="cdaf9-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-278">'Razor'</span></span>
- <span data-ttu-id="cdaf9-279">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-280">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-280">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-282">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-283">'Identity'</span></span>
- <span data-ttu-id="cdaf9-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-285">'Razor'</span></span>
- <span data-ttu-id="cdaf9-286">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-287">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-287">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-289">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-290">'Identity'</span></span>
- <span data-ttu-id="cdaf9-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-292">'Razor'</span></span>
- <span data-ttu-id="cdaf9-293">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-294">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-294">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-296">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-297">'Identity'</span></span>
- <span data-ttu-id="cdaf9-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-299">'Razor'</span></span>
- <span data-ttu-id="cdaf9-300">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdaf9-301">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-301">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="cdaf9-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdaf9-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-303">'Blazor'</span></span>
- <span data-ttu-id="cdaf9-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-304">'Identity'</span></span>
- <span data-ttu-id="cdaf9-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdaf9-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdaf9-306">'Razor'</span></span>
- <span data-ttu-id="cdaf9-307">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="cdaf9-307">'SignalR' uid:</span></span> 

<span data-ttu-id="cdaf9-308">--------- | | `components-reconnect-show` | 연결이 끊어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-308">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="cdaf9-309">클라이언트가 다시 연결하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-309">The client is attempting to reconnect.</span></span> <span data-ttu-id="cdaf9-310">모달을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-310">Show the modal.</span></span> <span data-ttu-id="cdaf9-311">| | `components-reconnect-hide`     | 서버에 대해 활성 연결이 다시 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-311">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="cdaf9-312">모달을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-312">Hide the modal.</span></span> <span data-ttu-id="cdaf9-313">| | `components-reconnect-failed`   | 네트워크 오류로 인해 다시 연결하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-313">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="cdaf9-314">다시 연결하려면 `window.Blazor.reconnect()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-314">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="cdaf9-315">| | `components-reconnect-rejected` | 다시 연결이 거부되었습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-315">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="cdaf9-316">서버에 접근했지만 연결이 거부되었으며, 서버의 사용자 상태가 손실되었습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-316">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="cdaf9-317">앱을 다시 로드하려면 `location.reload()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-317">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="cdaf9-318">이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-318">This connection state may result when:</span></span><ul><li><span data-ttu-id="cdaf9-319">서버 쪽 회로에서 크래시가 발생한 경우</span><span class="sxs-lookup"><span data-stu-id="cdaf9-319">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="cdaf9-320">서버에서 사용자 상태를 삭제하기에 충분한 기간에 클라이언트 연결이 끊긴 경우.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-320">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="cdaf9-321">사용자가 조작 중인 구성 요소 인스턴스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-321">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="cdaf9-322">서버가 다시 시작되었거나, 앱의 작업자 프로세스가 재활용된 경우</span><span class="sxs-lookup"><span data-stu-id="cdaf9-322">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="cdaf9-323">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="cdaf9-323">Render mode</span></span>

Blazor<span data-ttu-id="cdaf9-324"> 서버 앱은 기본적으로 클라이언트가 서버에 연결되기 전에 서버에서 UI를 미리 렌더링하도록 설정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-324"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="cdaf9-325">이 옵션은 *_Host.cshtml* Razor 페이지에서 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-325">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="cdaf9-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>는 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="cdaf9-327">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="cdaf9-327">Is prerendered into the page.</span></span>
* <span data-ttu-id="cdaf9-328">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="cdaf9-328">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="cdaf9-329">설명</span><span class="sxs-lookup"><span data-stu-id="cdaf9-329">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="cdaf9-330">구성 요소를 정적 HTML에 렌더링하고 Blazor 서버 앱의 표식을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-330">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="cdaf9-331">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-331">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="cdaf9-332">Blazor 서버 앱의 표식을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-332">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="cdaf9-333">구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-333">Output from the component isn't included.</span></span> <span data-ttu-id="cdaf9-334">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-334">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="cdaf9-335">구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-335">Renders the component into static HTML.</span></span> |

<span data-ttu-id="cdaf9-336">정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-336">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="cdaf9-337">Blazor 서버 앱에 적합하게 SignalR 클라이언트 구성</span><span class="sxs-lookup"><span data-stu-id="cdaf9-337">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="cdaf9-338">Blazor 서버 앱에서 사용하는 SignalR 클라이언트를 구성해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-338">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="cdaf9-339">예를 들어 연결 문제를 진단하기 위해 SignalR 클라이언트에서 로깅을 구성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-339">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="cdaf9-340">*Pages/_Host.cshtml* 파일에서 SignalR 클라이언트를 구성하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-340">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="cdaf9-341">`blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-341">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="cdaf9-342">`Blazor.start`를 호출하고 SignalR 작성기를 지정하는 구성 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-342">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

### <a name="logging"></a><span data-ttu-id="cdaf9-343">로깅</span><span class="sxs-lookup"><span data-stu-id="cdaf9-343">Logging</span></span>

<span data-ttu-id="cdaf9-344">Blazor 서버 로깅 지원에 대한 자세한 내용은 <xref:fundamentals/logging/index#create-logs-in-blazor>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cdaf9-344">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
