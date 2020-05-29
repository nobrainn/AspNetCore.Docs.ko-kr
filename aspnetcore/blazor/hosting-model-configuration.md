---
<span data-ttu-id="b6106-101">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-103">'Blazor'</span></span>
- <span data-ttu-id="b6106-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-104">'Identity'</span></span>
- <span data-ttu-id="b6106-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-106">'Razor'</span></span>
- <span data-ttu-id="b6106-107">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="b6106-108">ASP.NET Core Blazor 호스팅 모델 구성</span><span class="sxs-lookup"><span data-stu-id="b6106-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="b6106-109">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b6106-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b6106-110">이 문서에서는 호스팅 모델 구성에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="b6106-111"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b6106-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="b6106-112">환경</span><span class="sxs-lookup"><span data-stu-id="b6106-112">Environment</span></span>

<span data-ttu-id="b6106-113">앱을 로컬에서 실행하면 환경이 기본적으로 개발로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="b6106-114">앱이 게시되면 환경이 기본적으로 프로덕션으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="b6106-115">호스트된 Blazor WebAssembly 앱은 `blazor-environment` 헤더를 추가하여 브라우저에 환경을 전달하는 미들웨어를 통해 서버에서 환경을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="b6106-116">헤더의 값은 해당 환경입니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-116">The value of the header is the environment.</span></span> <span data-ttu-id="b6106-117">호스트된 Blazor 앱과 서버 앱은 동일한 환경을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="b6106-118">환경을 구성하는 방법을 비롯한 자세한 내용은 <xref:fundamentals/environments> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b6106-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b6106-119">로컬에서 실행되는 독립 실행형 앱의 경우 개발 서버는 `blazor-environment` 헤더를 추가하여 개발 환경을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="b6106-120">다른 호스팅 환경을 위한 환경을 지정하려면 `blazor-environment` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="b6106-121">다음 IIS 예제에서는 게시된 *web.config* 파일에 사용자 지정 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="b6106-122">*web.config* 파일은 *bin/Release/{대상 프레임워크}/publish* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="b6106-123">앱이 *publish* 폴더에 게시될 때 덮어쓰지 않는 IIS용 사용자 지정 *web.config* 파일을 사용하려면 <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b6106-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="b6106-124">`IWebAssemblyHostEnvironment`를 삽입하고 `Environment` 속성을 읽어 구성 요소에서 앱의 환경을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-124">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="b6106-125">시작하는 동안 `WebAssemblyHostBuilder`는 `HostEnvironment` 속성을 통해 `IWebAssemblyHostEnvironment`를 노출하므로 개발자가 환경 관련 논리를 코드에 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-125">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="b6106-126">다음과 같은 편리한 확장 메서드를 통해 현재 환경이 개발, 프로덕션, 스테이징 및 사용자 지정 환경 이름용인지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="b6106-127">\`IsEnvironment(“{ENVIRONMENT NAME}”)</span><span class="sxs-lookup"><span data-stu-id="b6106-127">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="b6106-128">`NavigationManager` 서비스를 사용할 수 없는 경우 시작하는 동안 `IWebAssemblyHostEnvironment.BaseAddress` 속성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-128">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="b6106-129">Configuration</span><span class="sxs-lookup"><span data-stu-id="b6106-129">Configuration</span></span>

Blazor<span data-ttu-id="b6106-130"> WebAssembly는 다음에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-130"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="b6106-131">기본적인 앱 설정 파일:</span><span class="sxs-lookup"><span data-stu-id="b6106-131">App settings files by default:</span></span>
  * <span data-ttu-id="b6106-132">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="b6106-132">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="b6106-133">*wwwroot/appsettings.{ENVIRONMENT}.json*</span><span class="sxs-lookup"><span data-stu-id="b6106-133">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="b6106-134">앱에 등록된 다른 [구성 공급자](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b6106-134">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="b6106-135">일부 공급자는 Blazor WebAssembly 앱에 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-135">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="b6106-136">Blazor WebAssembly를 지원하는 공급자에 관한 설명은 [Blazor WASM의 구성 공급자 설명(dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134)에서 추적됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-136">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="b6106-137">Blazor WebAssembly 앱의 구성은 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-137">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="b6106-138">**구성에 앱 비밀이나 자격 증명을 저장하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="b6106-138">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="b6106-139">구성 공급자에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b6106-139">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="b6106-140">앱 설정 구성</span><span class="sxs-lookup"><span data-stu-id="b6106-140">App settings configuration</span></span>

<span data-ttu-id="b6106-141">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b6106-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="b6106-142"><xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-142">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="b6106-143">공급자 구성</span><span class="sxs-lookup"><span data-stu-id="b6106-143">Provider configuration</span></span>

<span data-ttu-id="b6106-144">다음 예제에서는 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>를 사용하여 추가 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-144">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="b6106-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b6106-145">`Program.Main`:</span></span>

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

<span data-ttu-id="b6106-146"><xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-146">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="b6106-147">*wwwroot* 폴더에서 구성으로 다른 구성 파일을 읽으려면 `HttpClient`를 사용하여 파일 콘텐츠를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-147">To read other configuration files from the *wwwroot* folder into configuration, use an `HttpClient` to obtain the file's content.</span></span> <span data-ttu-id="b6106-148">이 방법을 사용하는 경우 다음 예제와 같이 기존 `HttpClient` 서비스 등록은 생성된 로컬 클라이언트를 사용하여 파일을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-148">When using this approach, the existing `HttpClient` service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="b6106-149">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="b6106-149">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="b6106-150">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b6106-150">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="b6106-151">인증 구성</span><span class="sxs-lookup"><span data-stu-id="b6106-151">Authentication configuration</span></span>

<span data-ttu-id="b6106-152">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b6106-152">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="b6106-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b6106-153">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="b6106-154">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="b6106-154">Logging configuration</span></span>

<span data-ttu-id="b6106-155">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b6106-155">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="b6106-156">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b6106-156">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="b6106-157">호스트 빌더 구성</span><span class="sxs-lookup"><span data-stu-id="b6106-157">Host builder configuration</span></span>

<span data-ttu-id="b6106-158">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b6106-158">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="b6106-159">캐시된 구성</span><span class="sxs-lookup"><span data-stu-id="b6106-159">Cached configuration</span></span>

<span data-ttu-id="b6106-160">구성 파일은 오프라인으로 사용할 수 있도록 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-160">Configuration files are cached for offline use.</span></span> <span data-ttu-id="b6106-161">[PWA(프로그레시브 웹 애플리케이션)](xref:blazor/progressive-web-app)의 경우 새 배포를 만들 때만 구성 파일을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-161">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="b6106-162">여러 배포 간에 구성 파일을 편집하는 것은 다음과 같은 이유로 인해 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-162">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="b6106-163">사용자에게 계속해서 사용할 수 있는 캐시된 버전의 파일이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-163">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="b6106-164">사용자가 다음번에 온라인으로 방문할 때 앱이 다시 배포되었음을 앱에 알려 주는 PWA의 *service-worker.js* 파일과 *service-worker-assets.js* 파일은 컴파일 시 다시 빌드되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-164">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="b6106-165">PWA가 백그라운드 업데이트를 처리하는 방식에 대한 자세한 내용은 <xref:blazor/progressive-web-app#background-updates> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b6106-165">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="b6106-166">로깅</span><span class="sxs-lookup"><span data-stu-id="b6106-166">Logging</span></span>

<span data-ttu-id="b6106-167">Blazor WebAssembly 로깅 지원에 대한 자세한 내용은 <xref:fundamentals/logging/index#create-logs-in-blazor>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b6106-167">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="b6106-168"> 서버</span><span class="sxs-lookup"><span data-stu-id="b6106-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="b6106-169">UI에 연결 상태 반영</span><span class="sxs-lookup"><span data-stu-id="b6106-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="b6106-170">클라이언트에서 연결이 끊어진 것을 감지하면, 클라이언트가 다시 연결하는 동안 기본 UI가 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="b6106-171">다시 연결하지 못한 경우 사용자에게 다시 시도 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="b6106-172">UI를 사용자 지정하려면 *_Host.cshtml* Razor 페이지의 `<body>`에서 `components-reconnect-modal`의 `id`를 사용하여 요소를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="b6106-173">다음 표에서는 `components-reconnect-modal` 요소에 적용된 CSS 클래스에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="b6106-174">CSS 클래스</span><span class="sxs-lookup"><span data-stu-id="b6106-174">CSS class</span></span>                       | <span data-ttu-id="b6106-175">표시&hellip;</span><span class="sxs-lookup"><span data-stu-id="b6106-175">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="b6106-176">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-176">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-178">'Blazor'</span></span>
- <span data-ttu-id="b6106-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-179">'Identity'</span></span>
- <span data-ttu-id="b6106-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-181">'Razor'</span></span>
- <span data-ttu-id="b6106-182">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-183">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-183">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-185">'Blazor'</span></span>
- <span data-ttu-id="b6106-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-186">'Identity'</span></span>
- <span data-ttu-id="b6106-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-188">'Razor'</span></span>
- <span data-ttu-id="b6106-189">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-190">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-190">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-192">'Blazor'</span></span>
- <span data-ttu-id="b6106-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-193">'Identity'</span></span>
- <span data-ttu-id="b6106-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-195">'Razor'</span></span>
- <span data-ttu-id="b6106-196">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-197">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-197">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-199">'Blazor'</span></span>
- <span data-ttu-id="b6106-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-200">'Identity'</span></span>
- <span data-ttu-id="b6106-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-202">'Razor'</span></span>
- <span data-ttu-id="b6106-203">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-204">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-204">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-206">'Blazor'</span></span>
- <span data-ttu-id="b6106-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-207">'Identity'</span></span>
- <span data-ttu-id="b6106-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-209">'Razor'</span></span>
- <span data-ttu-id="b6106-210">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-211">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-211">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-213">'Blazor'</span></span>
- <span data-ttu-id="b6106-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-214">'Identity'</span></span>
- <span data-ttu-id="b6106-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-216">'Razor'</span></span>
- <span data-ttu-id="b6106-217">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-218">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-218">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-220">'Blazor'</span></span>
- <span data-ttu-id="b6106-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-221">'Identity'</span></span>
- <span data-ttu-id="b6106-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-223">'Razor'</span></span>
- <span data-ttu-id="b6106-224">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-225">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-225">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-227">'Blazor'</span></span>
- <span data-ttu-id="b6106-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-228">'Identity'</span></span>
- <span data-ttu-id="b6106-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-230">'Razor'</span></span>
- <span data-ttu-id="b6106-231">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-232">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-232">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-234">'Blazor'</span></span>
- <span data-ttu-id="b6106-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-235">'Identity'</span></span>
- <span data-ttu-id="b6106-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-237">'Razor'</span></span>
- <span data-ttu-id="b6106-238">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-239">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-239">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-241">'Blazor'</span></span>
- <span data-ttu-id="b6106-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-242">'Identity'</span></span>
- <span data-ttu-id="b6106-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-244">'Razor'</span></span>
- <span data-ttu-id="b6106-245">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-246">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-246">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-248">'Blazor'</span></span>
- <span data-ttu-id="b6106-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-249">'Identity'</span></span>
- <span data-ttu-id="b6106-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-251">'Razor'</span></span>
- <span data-ttu-id="b6106-252">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-253">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-253">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-255">'Blazor'</span></span>
- <span data-ttu-id="b6106-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-256">'Identity'</span></span>
- <span data-ttu-id="b6106-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-258">'Razor'</span></span>
- <span data-ttu-id="b6106-259">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-260">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-260">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-262">'Blazor'</span></span>
- <span data-ttu-id="b6106-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-263">'Identity'</span></span>
- <span data-ttu-id="b6106-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-265">'Razor'</span></span>
- <span data-ttu-id="b6106-266">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-266">'SignalR' uid:</span></span> 

<span data-ttu-id="b6106-267">---------------- | --- title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-267">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-269">'Blazor'</span></span>
- <span data-ttu-id="b6106-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-270">'Identity'</span></span>
- <span data-ttu-id="b6106-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-272">'Razor'</span></span>
- <span data-ttu-id="b6106-273">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-274">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-274">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-276">'Blazor'</span></span>
- <span data-ttu-id="b6106-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-277">'Identity'</span></span>
- <span data-ttu-id="b6106-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-279">'Razor'</span></span>
- <span data-ttu-id="b6106-280">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-281">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-281">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-283">'Blazor'</span></span>
- <span data-ttu-id="b6106-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-284">'Identity'</span></span>
- <span data-ttu-id="b6106-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-286">'Razor'</span></span>
- <span data-ttu-id="b6106-287">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-288">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-288">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-290">'Blazor'</span></span>
- <span data-ttu-id="b6106-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-291">'Identity'</span></span>
- <span data-ttu-id="b6106-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-293">'Razor'</span></span>
- <span data-ttu-id="b6106-294">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-295">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-295">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-297">'Blazor'</span></span>
- <span data-ttu-id="b6106-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-298">'Identity'</span></span>
- <span data-ttu-id="b6106-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-300">'Razor'</span></span>
- <span data-ttu-id="b6106-301">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-302">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-302">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-304">'Blazor'</span></span>
- <span data-ttu-id="b6106-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-305">'Identity'</span></span>
- <span data-ttu-id="b6106-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-307">'Razor'</span></span>
- <span data-ttu-id="b6106-308">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-308">'SignalR' uid:</span></span> 

<span data-ttu-id="b6106-309">--------- | | `components-reconnect-show` | 연결이 끊어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-309">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="b6106-310">클라이언트가 다시 연결하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-310">The client is attempting to reconnect.</span></span> <span data-ttu-id="b6106-311">모달을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-311">Show the modal.</span></span> <span data-ttu-id="b6106-312">| | `components-reconnect-hide`     | 서버에 대해 활성 연결이 다시 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-312">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="b6106-313">모달을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-313">Hide the modal.</span></span> <span data-ttu-id="b6106-314">| | `components-reconnect-failed`   | 네트워크 오류로 인해 다시 연결하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-314">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="b6106-315">다시 연결하려면 `window.Blazor.reconnect()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-315">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="b6106-316">| | `components-reconnect-rejected` | 다시 연결이 거부되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-316">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="b6106-317">서버에 접근했지만 연결이 거부되었으며, 서버의 사용자 상태가 손실되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-317">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="b6106-318">앱을 다시 로드하려면 `location.reload()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-318">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="b6106-319">이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-319">This connection state may result when:</span></span><ul><li><span data-ttu-id="b6106-320">서버 쪽 회로에서 크래시가 발생한 경우</span><span class="sxs-lookup"><span data-stu-id="b6106-320">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="b6106-321">서버에서 사용자 상태를 삭제하기에 충분한 기간에 클라이언트 연결이 끊긴 경우.</span><span class="sxs-lookup"><span data-stu-id="b6106-321">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="b6106-322">사용자가 조작 중인 구성 요소 인스턴스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-322">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="b6106-323">서버가 다시 시작되었거나, 앱의 작업자 프로세스가 재활용된 경우</span><span class="sxs-lookup"><span data-stu-id="b6106-323">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="b6106-324">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="b6106-324">Render mode</span></span>

Blazor<span data-ttu-id="b6106-325"> 서버 앱은 기본적으로 클라이언트가 서버에 연결되기 전에 서버에서 UI를 미리 렌더링하도록 설정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-325"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="b6106-326">이 옵션은 *_Host.cshtml* Razor 페이지에서 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-326">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="b6106-327">`RenderMode`는 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-327">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="b6106-328">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="b6106-328">Is prerendered into the page.</span></span>
* <span data-ttu-id="b6106-329">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="b6106-329">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="b6106-330">설명</span><span class="sxs-lookup"><span data-stu-id="b6106-330">Description</span></span> |
| ---
<span data-ttu-id="b6106-331">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-331">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-332">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-332">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-333">'Blazor'</span></span>
- <span data-ttu-id="b6106-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-334">'Identity'</span></span>
- <span data-ttu-id="b6106-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-336">'Razor'</span></span>
- <span data-ttu-id="b6106-337">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-338">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-338">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-339">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-339">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-340">'Blazor'</span></span>
- <span data-ttu-id="b6106-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-341">'Identity'</span></span>
- <span data-ttu-id="b6106-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-343">'Razor'</span></span>
- <span data-ttu-id="b6106-344">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-345">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-345">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-346">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-346">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-347">'Blazor'</span></span>
- <span data-ttu-id="b6106-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-348">'Identity'</span></span>
- <span data-ttu-id="b6106-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-350">'Razor'</span></span>
- <span data-ttu-id="b6106-351">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-352">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-352">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-353">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-353">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-354">'Blazor'</span></span>
- <span data-ttu-id="b6106-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-355">'Identity'</span></span>
- <span data-ttu-id="b6106-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-357">'Razor'</span></span>
- <span data-ttu-id="b6106-358">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-359">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-359">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-360">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-360">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-361">'Blazor'</span></span>
- <span data-ttu-id="b6106-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-362">'Identity'</span></span>
- <span data-ttu-id="b6106-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-364">'Razor'</span></span>
- <span data-ttu-id="b6106-365">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-366">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-366">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-367">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-367">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-368">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-368">'Blazor'</span></span>
- <span data-ttu-id="b6106-369">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-369">'Identity'</span></span>
- <span data-ttu-id="b6106-370">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-370">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-371">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-371">'Razor'</span></span>
- <span data-ttu-id="b6106-372">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-372">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-373">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-373">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-374">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-374">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-375">'Blazor'</span></span>
- <span data-ttu-id="b6106-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-376">'Identity'</span></span>
- <span data-ttu-id="b6106-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-378">'Razor'</span></span>
- <span data-ttu-id="b6106-379">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-379">'SignalR' uid:</span></span> 

<span data-ttu-id="b6106-380">---------- | --- title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-380">---------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-381">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-381">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-382">'Blazor'</span></span>
- <span data-ttu-id="b6106-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-383">'Identity'</span></span>
- <span data-ttu-id="b6106-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-385">'Razor'</span></span>
- <span data-ttu-id="b6106-386">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-387">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-387">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-388">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-388">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-389">'Blazor'</span></span>
- <span data-ttu-id="b6106-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-390">'Identity'</span></span>
- <span data-ttu-id="b6106-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-392">'Razor'</span></span>
- <span data-ttu-id="b6106-393">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b6106-394">title: 'ASP.NET Core Blazor 호스팅 모델 구성' author: description: 'Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.'</span><span class="sxs-lookup"><span data-stu-id="b6106-394">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="b6106-395">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b6106-395">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b6106-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b6106-396">'Blazor'</span></span>
- <span data-ttu-id="b6106-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b6106-397">'Identity'</span></span>
- <span data-ttu-id="b6106-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b6106-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="b6106-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b6106-399">'Razor'</span></span>
- <span data-ttu-id="b6106-400">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b6106-400">'SignalR' uid:</span></span> 

<span data-ttu-id="b6106-401">------ | | `ServerPrerendered` | 구성 요소를 정적 HTML에 렌더링하고 Blazor 서버 앱의 표식을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-401">------ | | `ServerPrerendered` | Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="b6106-402">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-402">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="b6106-403">| | `Server`            | Blazor 서버 앱의 표식을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-403">| | `Server`            | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="b6106-404">구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-404">Output from the component isn't included.</span></span> <span data-ttu-id="b6106-405">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-405">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="b6106-406">| | `Static`            | 구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-406">| | `Static`            | Renders the component into static HTML.</span></span> |

<span data-ttu-id="b6106-407">정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-407">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="b6106-408">Blazor 서버 앱에 적합하게 SignalR 클라이언트 구성</span><span class="sxs-lookup"><span data-stu-id="b6106-408">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="b6106-409">Blazor 서버 앱에서 사용하는 SignalR 클라이언트를 구성해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-409">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="b6106-410">예를 들어 연결 문제를 진단하기 위해 SignalR 클라이언트에서 로깅을 구성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-410">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="b6106-411">*Pages/_Host.cshtml* 파일에서 SignalR 클라이언트를 구성하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-411">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="b6106-412">`blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-412">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="b6106-413">`Blazor.start`를 호출하고 SignalR 작성기를 지정하는 구성 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="b6106-413">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="b6106-414">로깅</span><span class="sxs-lookup"><span data-stu-id="b6106-414">Logging</span></span>

<span data-ttu-id="b6106-415">Blazor 서버 로깅 지원에 대한 자세한 내용은 <xref:fundamentals/logging/index#create-logs-in-blazor>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b6106-415">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
