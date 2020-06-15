---
title: ASP.NET Core Blazor 호스팅 모델 구성
author: guardrex
description: Razor 구성 요소를 Razor Pages 및 MVC 앱에 통합하는 방법을 포함하여 Blazor 호스팅 모델 구성을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 3cef67806ce0e2e045122bdc962e93795be68572
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679581"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="cb34a-103">ASP.NET Core Blazor 호스팅 모델 구성</span><span class="sxs-lookup"><span data-stu-id="cb34a-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="cb34a-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cb34a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cb34a-105">이 문서에서는 호스팅 모델 구성에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="cb34a-106"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="cb34a-106"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="cb34a-107">환경</span><span class="sxs-lookup"><span data-stu-id="cb34a-107">Environment</span></span>

<span data-ttu-id="cb34a-108">앱을 로컬에서 실행하면 환경이 기본적으로 개발로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="cb34a-109">앱이 게시되면 환경이 기본적으로 프로덕션으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="cb34a-110">호스트된 Blazor WebAssembly 앱은 `blazor-environment` 헤더를 추가하여 브라우저에 환경을 전달하는 미들웨어를 통해 서버에서 환경을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="cb34a-111">헤더의 값은 해당 환경입니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-111">The value of the header is the environment.</span></span> <span data-ttu-id="cb34a-112">호스트된 Blazor 앱과 서버 앱은 동일한 환경을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="cb34a-113">환경을 구성하는 방법을 비롯한 자세한 내용은 <xref:fundamentals/environments> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb34a-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="cb34a-114">로컬에서 실행되는 독립 실행형 앱의 경우 개발 서버는 `blazor-environment` 헤더를 추가하여 개발 환경을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="cb34a-115">다른 호스팅 환경을 위한 환경을 지정하려면 `blazor-environment` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="cb34a-116">다음 IIS 예제에서는 게시된 *web.config* 파일에 사용자 지정 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="cb34a-117">*web.config* 파일은 *bin/Release/{대상 프레임워크}/publish* 폴더에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="cb34a-118">앱이 *publish* 폴더에 게시될 때 덮어쓰지 않는 IIS용 사용자 지정 *web.config* 파일을 사용하려면 <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb34a-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="cb34a-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>를 삽입하고 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> 속성을 읽어 구성 요소에서 앱의 환경을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-119">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="cb34a-120">시작하는 동안 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder>는 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 속성을 통해 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>를 노출하므로 개발자가 환경 관련 논리를 코드에 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-120">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="cb34a-121">다음과 같은 편리한 확장 메서드를 통해 현재 환경이 개발, 프로덕션, 스테이징 및 사용자 지정 환경 이름용인지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="cb34a-122"><xref:Microsoft.AspNetCore.Components.NavigationManager> 서비스를 사용할 수 없는 경우 시작하는 동안 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 속성을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-122">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="cb34a-123">Configuration</span><span class="sxs-lookup"><span data-stu-id="cb34a-123">Configuration</span></span>

Blazor<span data-ttu-id="cb34a-124"> WebAssembly는 다음에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-124"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="cb34a-125">기본적인 앱 설정 파일:</span><span class="sxs-lookup"><span data-stu-id="cb34a-125">App settings files by default:</span></span>
  * <span data-ttu-id="cb34a-126">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="cb34a-126">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="cb34a-127">*wwwroot/appsettings.{ENVIRONMENT}.json*</span><span class="sxs-lookup"><span data-stu-id="cb34a-127">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="cb34a-128">앱에 등록된 다른 [구성 공급자](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="cb34a-128">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="cb34a-129">일부 공급자는 Blazor WebAssembly 앱에 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-129">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="cb34a-130">Blazor WebAssembly를 지원하는 공급자에 관한 설명은 [Blazor WASM의 구성 공급자 설명(dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134)에서 추적됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-130">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="cb34a-131">Blazor WebAssembly 앱의 구성은 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-131">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="cb34a-132">**구성에 앱 비밀이나 자격 증명을 저장하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="cb34a-132">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="cb34a-133">구성 공급자에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb34a-133">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="cb34a-134">앱 설정 구성</span><span class="sxs-lookup"><span data-stu-id="cb34a-134">App settings configuration</span></span>

<span data-ttu-id="cb34a-135">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cb34a-135">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="cb34a-136"><xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-136">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="cb34a-137">공급자 구성</span><span class="sxs-lookup"><span data-stu-id="cb34a-137">Provider configuration</span></span>

<span data-ttu-id="cb34a-138">다음 예제에서는 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>를 사용하여 추가 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-138">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="cb34a-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cb34a-139">`Program.Main`:</span></span>

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

<span data-ttu-id="cb34a-140"><xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-140">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="cb34a-141">*wwwroot* 폴더에서 구성으로 다른 구성 파일을 읽으려면 <xref:System.Net.Http.HttpClient>를 사용하여 파일 콘텐츠를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-141">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="cb34a-142">이 방법을 사용하는 경우 다음 예제와 같이 기존 <xref:System.Net.Http.HttpClient> 서비스 등록은 생성된 로컬 클라이언트를 사용하여 파일을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-142">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="cb34a-143">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="cb34a-143">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="cb34a-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cb34a-144">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="cb34a-145">인증 구성</span><span class="sxs-lookup"><span data-stu-id="cb34a-145">Authentication configuration</span></span>

<span data-ttu-id="cb34a-146">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cb34a-146">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="cb34a-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cb34a-147">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a><span data-ttu-id="cb34a-148">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="cb34a-148">Logging configuration</span></span>

<span data-ttu-id="cb34a-149">[Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/)에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-149">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="cb34a-150">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="cb34a-150">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="cb34a-151">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cb34a-151">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="cb34a-152">호스트 빌더 구성</span><span class="sxs-lookup"><span data-stu-id="cb34a-152">Host builder configuration</span></span>

<span data-ttu-id="cb34a-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cb34a-153">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="cb34a-154">캐시된 구성</span><span class="sxs-lookup"><span data-stu-id="cb34a-154">Cached configuration</span></span>

<span data-ttu-id="cb34a-155">구성 파일은 오프라인으로 사용할 수 있도록 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="cb34a-156">[PWA(프로그레시브 웹 애플리케이션)](xref:blazor/progressive-web-app)의 경우 새 배포를 만들 때만 구성 파일을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="cb34a-157">여러 배포 간에 구성 파일을 편집하는 것은 다음과 같은 이유로 인해 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="cb34a-158">사용자에게 계속해서 사용할 수 있는 캐시된 버전의 파일이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="cb34a-159">사용자가 다음번에 온라인으로 방문할 때 앱이 다시 배포되었음을 앱에 알려 주는 PWA의 *service-worker.js* 파일과 *service-worker-assets.js* 파일은 컴파일 시 다시 빌드되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-159">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="cb34a-160">PWA가 백그라운드 업데이트를 처리하는 방식에 대한 자세한 내용은 <xref:blazor/progressive-web-app#background-updates> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb34a-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="cb34a-161">로깅</span><span class="sxs-lookup"><span data-stu-id="cb34a-161">Logging</span></span>

<span data-ttu-id="cb34a-162">Blazor WebAssembly 로깅 지원에 대한 자세한 내용은 <xref:fundamentals/logging/index#create-logs-in-blazor>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb34a-162">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="cb34a-163"> 인증에 대한 원본 간 협상</span><span class="sxs-lookup"><span data-stu-id="cb34a-163"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="cb34a-164">쿠키 또는 HTTP 인증 헤더와 같은 자격 증명을 보내도록 SignalR의 기본 클라이언트를 구성하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-164">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="cb34a-165"><xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>를 사용하여 원본 간 [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 요청에 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-165">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessagHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="cb34a-166"><xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 옵션에 <xref:System.Net.Http.HttpMessageHandler>를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-166">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var client = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="cb34a-167">자세한 내용은 <xref:signalr/configuration#configure-additional-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb34a-167">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="cb34a-168"> 서버</span><span class="sxs-lookup"><span data-stu-id="cb34a-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="cb34a-169">UI에 연결 상태 반영</span><span class="sxs-lookup"><span data-stu-id="cb34a-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="cb34a-170">클라이언트에서 연결이 끊어진 것을 감지하면, 클라이언트가 다시 연결하는 동안 기본 UI가 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="cb34a-171">다시 연결하지 못한 경우 사용자에게 다시 시도 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="cb34a-172">UI를 사용자 지정하려면 *_Host.cshtml* Razor 페이지의 `<body>`에서 `components-reconnect-modal`의 `id`를 사용하여 요소를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="cb34a-173">다음 표에서는 `components-reconnect-modal` 요소에 적용된 CSS 클래스에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="cb34a-174">CSS 클래스</span><span class="sxs-lookup"><span data-stu-id="cb34a-174">CSS class</span></span>                       | <span data-ttu-id="cb34a-175">표시&hellip;</span><span class="sxs-lookup"><span data-stu-id="cb34a-175">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="cb34a-176">연결이 끊어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-176">A lost connection.</span></span> <span data-ttu-id="cb34a-177">클라이언트가 다시 연결하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-177">The client is attempting to reconnect.</span></span> <span data-ttu-id="cb34a-178">모달을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-178">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="cb34a-179">서버에 대해 활성 연결이 다시 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-179">An active connection is re-established to the server.</span></span> <span data-ttu-id="cb34a-180">모달을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-180">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="cb34a-181">네트워크 오류로 인해 다시 연결하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-181">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="cb34a-182">다시 연결하려면 `window.Blazor.reconnect()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-182">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="cb34a-183">다시 연결이 거부되었습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-183">Reconnection rejected.</span></span> <span data-ttu-id="cb34a-184">서버에 접근했지만 연결이 거부되었으며, 서버의 사용자 상태가 손실되었습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-184">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="cb34a-185">앱을 다시 로드하려면 `location.reload()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-185">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="cb34a-186">이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-186">This connection state may result when:</span></span><ul><li><span data-ttu-id="cb34a-187">서버 쪽 회로에서 크래시가 발생한 경우</span><span class="sxs-lookup"><span data-stu-id="cb34a-187">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="cb34a-188">서버에서 사용자 상태를 삭제하기에 충분한 기간에 클라이언트 연결이 끊긴 경우.</span><span class="sxs-lookup"><span data-stu-id="cb34a-188">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="cb34a-189">사용자가 조작 중인 구성 요소 인스턴스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-189">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="cb34a-190">서버가 다시 시작되었거나, 앱의 작업자 프로세스가 재활용된 경우</span><span class="sxs-lookup"><span data-stu-id="cb34a-190">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="cb34a-191">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="cb34a-191">Render mode</span></span>

Blazor<span data-ttu-id="cb34a-192"> 서버 앱은 기본적으로 클라이언트가 서버에 연결되기 전에 서버에서 UI를 미리 렌더링하도록 설정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-192"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="cb34a-193">이 옵션은 *_Host.cshtml* Razor 페이지에서 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-193">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="cb34a-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>는 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="cb34a-195">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="cb34a-195">Is prerendered into the page.</span></span>
* <span data-ttu-id="cb34a-196">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="cb34a-196">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="cb34a-197">설명</span><span class="sxs-lookup"><span data-stu-id="cb34a-197">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="cb34a-198">구성 요소를 정적 HTML에 렌더링하고 Blazor 서버 앱의 표식을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-198">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="cb34a-199">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-199">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="cb34a-200">Blazor 서버 앱의 표식을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-200">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="cb34a-201">구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-201">Output from the component isn't included.</span></span> <span data-ttu-id="cb34a-202">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-202">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="cb34a-203">구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-203">Renders the component into static HTML.</span></span> |

<span data-ttu-id="cb34a-204">정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-204">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="cb34a-205">Blazor 서버 앱에 적합하게 SignalR 클라이언트 구성</span><span class="sxs-lookup"><span data-stu-id="cb34a-205">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="cb34a-206">Blazor 서버 앱에서 사용하는 SignalR 클라이언트를 구성해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-206">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="cb34a-207">예를 들어 연결 문제를 진단하기 위해 SignalR 클라이언트에서 로깅을 구성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="cb34a-208">*Pages/_Host.cshtml* 파일에서 SignalR 클라이언트를 구성하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-208">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="cb34a-209">`blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-209">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="cb34a-210">`Blazor.start`를 호출하고 SignalR 작성기를 지정하는 구성 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="cb34a-210">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="cb34a-211">로깅</span><span class="sxs-lookup"><span data-stu-id="cb34a-211">Logging</span></span>

<span data-ttu-id="cb34a-212">Blazor 서버 로깅 지원에 대한 자세한 내용은 <xref:fundamentals/logging/index#create-logs-in-blazor>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cb34a-212">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
