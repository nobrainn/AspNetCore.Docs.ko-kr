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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: f78803a3954feb98a39f26874b9de0aa08dc6327
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445218"
---
# <a name="aspnet-core-blazor-configuration"></a>ASP.NET Core Blazor 구성

> [!NOTE]
> 이 항목은 Blazor WebAssembly에 적용됩니다. ASP.NET Core 앱 구성에 대한 일반적인 지침은 <xref:fundamentals/configuration/index>의 내용을 참조하세요.

Blazor WebAssembly는 다음에서 구성을 로드합니다.

* 기본적인 앱 설정 파일:
  * `wwwroot/appsettings.json`
  * `wwwroot/appsettings.{ENVIRONMENT}.json`
* 앱에 등록된 다른 [구성 공급자](xref:fundamentals/configuration/index). 일부 공급자는 Blazor WebAssembly 앱에 적합하지 않습니다. Blazor WebAssembly를 지원하는 공급자에 관한 설명은 [Blazor WASM의 구성 공급자 설명(dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134)에서 추적됩니다.

> [!WARNING]
> Blazor WebAssembly 앱의 구성은 사용자에게 표시됩니다. **구성에 앱 비밀이나 자격 증명을 저장하지 마세요.**

구성 공급자에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.

## <a name="app-settings-configuration"></a>앱 설정 구성

`wwwroot/appsettings.json`:

```json
{
  "message": "Hello from config!"
}
```

<xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a>공급자 구성

다음 예제에서는 <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>를 사용하여 추가 구성을 제공합니다.

`Program.Main`:

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

<xref:Microsoft.Extensions.Configuration.IConfiguration> 인스턴스를 구성 요소에 삽입하여 구성 데이터에 액세스합니다.

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
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

`wwwroot` 폴더에서 구성으로 다른 구성 파일을 읽으려면 <xref:System.Net.Http.HttpClient>를 사용하여 파일 콘텐츠를 가져옵니다. 이 방법을 사용하는 경우 다음 예제와 같이 기존 <xref:System.Net.Http.HttpClient> 서비스 등록은 생성된 로컬 클라이언트를 사용하여 파일을 읽을 수 있습니다.

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a>인증 구성

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>로깅 구성

[`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/)에 대한 패키지 참조를 추가합니다.

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

`wwwroot/appsettings.json`:

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

`Program.Main`:

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>호스트 빌더 구성

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>캐시된 구성

구성 파일은 오프라인으로 사용할 수 있도록 캐시됩니다. [PWA(프로그레시브 웹 애플리케이션)](xref:blazor/progressive-web-app)의 경우 새 배포를 만들 때만 구성 파일을 업데이트할 수 있습니다. 여러 배포 간에 구성 파일을 편집하는 것은 다음과 같은 이유로 인해 적용되지 않습니다.

* 사용자에게 계속해서 사용할 수 있는 캐시된 버전의 파일이 있습니다.
* 사용자가 다음번에 온라인으로 방문할 때 앱이 다시 배포되었음을 앱에 알려 주는 PWA의 `service-worker.js` 및 `service-worker-assets.js` 파일은 컴파일 시 다시 빌드되어야 합니다.

PWA가 백그라운드 업데이트를 처리하는 방식에 대한 자세한 내용은 <xref:blazor/progressive-web-app#background-updates> 문서를 참조하세요.
