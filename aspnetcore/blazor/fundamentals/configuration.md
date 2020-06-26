---
title: ASP.NET Core Blazor 구성
author: guardrex
description: 앱 설정, 인증, 로깅 구성을 포함하여 Blazor 앱의 구성에 대해 알아봅니다.
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 0e36b81d771b07e85158724c02210ee50a3ab118
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242682"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="4f48b-103">ASP.NET Core Blazor 구성</span><span class="sxs-lookup"><span data-stu-id="4f48b-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="4f48b-104">이 항목은 Blazor WebAssembly에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="4f48b-105">ASP.NET Core 앱 구성에 대한 일반적인 지침은 <xref:fundamentals/configuration/index>의 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4f48b-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor<span data-ttu-id="4f48b-106"> WebAssembly는 다음에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-106"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="4f48b-107">기본적인 앱 설정 파일:</span><span class="sxs-lookup"><span data-stu-id="4f48b-107">App settings files by default:</span></span>
  * `wwwroot/appsettings.json`
  * `wwwroot/appsettings.{ENVIRONMENT}.json`
* <span data-ttu-id="4f48b-108">앱에 등록된 다른 [구성 공급자](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="4f48b-108">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="4f48b-109">일부 공급자는 Blazor WebAssembly 앱에 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-109">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="4f48b-110">Blazor WebAssembly를 지원하는 공급자에 관한 설명은 [Blazor WASM의 구성 공급자 설명(dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134)에서 추적됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-110">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="4f48b-111">Blazor WebAssembly 앱의 구성은 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-111">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="4f48b-112">**구성에 앱 비밀이나 자격 증명을 저장하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="4f48b-112">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="4f48b-113">구성 공급자에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4f48b-113">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="4f48b-114">앱 설정 구성</span><span class="sxs-lookup"><span data-stu-id="4f48b-114">App settings configuration</span></span>

<span data-ttu-id="4f48b-115">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="4f48b-115">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="4f48b-116"><xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-116">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="4f48b-117">공급자 구성</span><span class="sxs-lookup"><span data-stu-id="4f48b-117">Provider configuration</span></span>

<span data-ttu-id="4f48b-118">다음 예제에서는 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>를 사용하여 추가 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-118">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="4f48b-119">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4f48b-119">`Program.Main`:</span></span>

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

<span data-ttu-id="4f48b-120"><xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-120">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="4f48b-121">`wwwroot` 폴더에서 구성으로 다른 구성 파일을 읽으려면 <xref:System.Net.Http.HttpClient>를 사용하여 파일 콘텐츠를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-121">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="4f48b-122">이 방법을 사용하는 경우 다음 예제와 같이 기존 <xref:System.Net.Http.HttpClient> 서비스 등록은 생성된 로컬 클라이언트를 사용하여 파일을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-122">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="4f48b-123">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="4f48b-123">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="4f48b-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4f48b-124">`Program.Main`:</span></span>

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

## <a name="authentication-configuration"></a><span data-ttu-id="4f48b-125">인증 구성</span><span class="sxs-lookup"><span data-stu-id="4f48b-125">Authentication configuration</span></span>

<span data-ttu-id="4f48b-126">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="4f48b-126">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="4f48b-127">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4f48b-127">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="4f48b-128">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="4f48b-128">Logging configuration</span></span>

<span data-ttu-id="4f48b-129">[`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/)에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-129">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="4f48b-130">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="4f48b-130">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="4f48b-131">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4f48b-131">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="4f48b-132">호스트 빌더 구성</span><span class="sxs-lookup"><span data-stu-id="4f48b-132">Host builder configuration</span></span>

<span data-ttu-id="4f48b-133">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="4f48b-133">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="4f48b-134">캐시된 구성</span><span class="sxs-lookup"><span data-stu-id="4f48b-134">Cached configuration</span></span>

<span data-ttu-id="4f48b-135">구성 파일은 오프라인으로 사용할 수 있도록 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-135">Configuration files are cached for offline use.</span></span> <span data-ttu-id="4f48b-136">[PWA(프로그레시브 웹 애플리케이션)](xref:blazor/progressive-web-app)의 경우 새 배포를 만들 때만 구성 파일을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-136">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="4f48b-137">여러 배포 간에 구성 파일을 편집하는 것은 다음과 같은 이유로 인해 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-137">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="4f48b-138">사용자에게 계속해서 사용할 수 있는 캐시된 버전의 파일이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-138">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="4f48b-139">사용자가 다음번에 온라인으로 방문할 때 앱이 다시 배포되었음을 앱에 알려 주는 PWA의 `service-worker.js` 및 `service-worker-assets.js` 파일은 컴파일 시 다시 빌드되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4f48b-139">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="4f48b-140">PWA가 백그라운드 업데이트를 처리하는 방식에 대한 자세한 내용은 <xref:blazor/progressive-web-app#background-updates> 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4f48b-140">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
