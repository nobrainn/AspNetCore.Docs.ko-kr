---
title: ASP.NET Core에서 여러 환경 사용
author: rick-anderson
description: ASP.NET Core 앱의 여러 환경에서 앱 동작을 제어하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/environments
ms.openlocfilehash: 977d222ed61fa914bd4ffb70e192ef19d4da5c33
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2020
ms.locfileid: "87330646"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>ASP.NET Core에서 여러 환경 사용

::: moniker range=">= aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Kirk Larkin](https://twitter.com/serpent5)

ASP.NET Core는 환경 변수를 사용하여 런타임 환경에 따라 앱 동작을 구성합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="environments"></a>환경

런타임 환경을 확인하려면 다음 환경 변수에서 ASP.NET Core를 읽습니다.

1. [DOTNET_ENVIRONMENT](xref:fundamentals/configuration/index#default-host-configuration)
1. <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>를 호출하는 경우 `ASPNETCORE_ENVIRONMENT`를 확인합니다. 기본 ASP.NET Core 웹앱 템플릿은 `ConfigureWebHostDefaults`를 호출합니다. `ASPNETCORE_ENVIRONMENT` 값은 `DOTNET_ENVIRONMENT`를 재정의합니다.

`IHostEnvironment.EnvironmentName`을 임의의 값으로 설정할 수 있지만 다음 값은 프레임워크에서 제공됩니다.

* <xref:Microsoft.Extensions.Hosting.Environments.Development> : [launchSettings. json](#lsj) 파일은 로컬 컴퓨터에서 `ASPNETCORE_ENVIRONMENT`를 `Development`로 설정합니다.
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production> : `DOTNET_ENVIRONMENT` 및 `ASPNETCORE_ENVIRONMENT`가 설정되지 않은 경우 기본값입니다.

코드는 다음과 같습니다.

* `ASPNETCORE_ENVIRONMENT`이 `Development`로 설정된 경우 [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)를 호출합니다.
* `ASPNETCORE_ENVIRONMENT`의 값이 `Staging`, `Production` 또는 `Staging_2`로 설정된 경우 [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)를 호출합니다.
* `Startup.Configure`에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 삽입합니다. 이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경의 `Startup.Configure`만 조정해야 하는 경우에 유용합니다.
* ASP.NET Core 템플릿에서 생성된 코드와 유사합니다.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

[Environment 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)는 [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName)의 값을 사용하여 요소에 태그를 포함하거나 제외합니다.

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)의 [정보 페이지](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml)에는 이전 태그가 포함되며 `IWebHostEnvironment.EnvironmentName`의 값이 표시됩니다.

Windows 및 macOS에서 환경 변수 및 값은 대/소문자를 구분하지 않습니다. Linux 환경 변수 및 값은 기본적으로 **대/소문자를 구분**합니다.

### <a name="create-environmentssample"></a>EnvironmentsSample 만들기

이 문서에서 사용되는 [샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)는 *EnvironmentsSample*이라는 Razor Pages 프로젝트를 기반으로 합니다.

다음 코드는 *EnvironmentsSample*이라는 웹앱을 만들고 실행합니다.

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

앱이 실행되면 다음과 같은 출력이 표시됩니다.

```bash
Using launch settings from c:\tmp\EnvironmentsSample\Properties\launchSettings.json
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: c:\tmp\EnvironmentsSample
```

<a name="lsj"></a>

### <a name="development-and-launchsettingsjson"></a>개발 및 launchSettings.json

개발 환경은 프로덕션에서 노출해서는 안 되는 기능을 활성화할 수 있습니다. 예를 들어 ASP.NET Core 템플릿은 개발 환경에서 [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)를 활성화합니다.

로컬 컴퓨터 개발을 위한 환경은 프로젝트의 *Properties\launchSettings.json* 파일에서 설정할 수 있습니다. *launchSettings.json*의 환경 값은 시스템 환경에 설정된 값을 재정의합니다.

*launchSettings.json* 파일은 다음과 같습니다.

* 로컬 개발 머신에서만 사용됩니다.
* 배포되지 않습니다.
* 프로필 설정을 포함합니다.

다음 JSON은 Visual Studio 또는 `dotnet new`를 사용하여 만든 *EnvironmentsSample*이라는 이름의 예정된 ASP.NET Core 웹에 대한 *launchSettings.json* 파일을 보여 줍니다.

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

위의 태그는 다음과 같은 두 개의 프로필을 포함합니다.

* `IIS Express`: Visual Studio에서 앱을 시작할 때 사용되는 기본 프로필입니다. `"commandName"` 키에 `"IISExpress"` 값이 있으므로 [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) 웹 서버입니다. 시작 프로필을 프로젝트 또는 포함된 다른 프로필로 설정할 수 있습니다. 예를 들어 아래 이미지에서 프로젝트 이름을 선택하면 [Kestrel 웹 서버](xref:fundamentals/servers/kestrel)가 시작됩니다.

  ![메뉴에서 IIS Express 시작](environments/_static/iisx2.png)
* `EnvironmentsSample`: 프로필 이름은 프로젝트 이름입니다. `dotnet run`를 사용하여 앱을 시작할 때 기본적으로 이 프로필이 사용됩니다.  `"commandName"` 키에 `"Project"` 값이 있으므로 [Kestrel 웹 서버](xref:fundamentals/servers/kestrel)가 시작됩니다.

`commandName`의 값은 시작할 웹 서버를 지정할 수 있습니다. `commandName`은 다음 중 하나일 수 있습니다.

* `IISExpress` : IIS Express를 시작합니다.
* `IIS` : 웹 서버가 시작되지 않았습니다. IIS를 사용할 수 있어야 합니다.
* `Project` : Kestrel을 시작합니다.

Visual Studio 프로젝트 속성 **Debug** 탭은 *launchSettings.json* 파일을 편집할 수 있는 GUI를 제공합니다. 웹 서버가 다시 시작되기 전에는 프로젝트 프로필의 변경 내용이 적용되지 않을 수 있습니다. 해당 환경에 대한 변경 내용을 감지하려면 Kestrel을 다시 시작해야 합니다.

![프로젝트 속성 설정 환경 변수](environments/_static/project-properties-debug.png)

다음 *launchSettings. json* 파일에는 여러 프로필이 포함되어 있습니다.

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

프로필은 다음과 같이 선택할 수 있습니다.

* Visual Studio UI에서 선택
* 프로필 이름으로 설정된 `--launch-profile` 옵션이 있는 명령 셸에서 [`dotnet run`](/dotnet/core/tools/dotnet-run) 명령을 사용하여 선택 이 방법은 Kestrel 프로필만 지원합니다.

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> *launchSettings.json*은 암호를 저장하지 않아야 합니다. [암호 관리자 도구](xref:security/app-secrets)를 사용하여 로컬 개발에 사용되는 암호를 저장할 수 있습니다.

[Visual Studio Code](https://code.visualstudio.com/)를 사용하는 경우 환경 변수는 *.vscode/launch.json* 파일에서 설정할 수 있습니다. 다음 예제에서는 여러 [호스트 구성 값 환경 변수](xref:fundamentals/host/web-host#host-configuration-values)를 다음과 같이 설정합니다.

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

*.vscode/launch.json* 파일은 Visual Studio Code에서만 사용됩니다.

### <a name="production"></a>프로덕션

프로덕션 환경은 보안, [성능](xref:performance/performance-best-practices) 및 애플리케이션 견고성을 최대화하도록 구성되어야 합니다. 개발과 다른 몇 가지 일반적인 설정은 다음과 같습니다.

* [캐싱](xref:performance/caching/memory).
* 클라이언트 쪽 리소스가 번들로 제공되고, 축소되며, 잠재적으로 CDN에서 처리됩니다.
* 진단 오류 페이지를 사용하지 않습니다.
* 친숙한 오류 페이지를 사용하도록 설정합니다.
* 프로덕션 [로깅](xref:fundamentals/logging/index) 및 모니터링을 사용합니다. 예를 들어 [Application Insights](/azure/application-insights/app-insights-asp-net-core)를 사용합니다.

## <a name="set-the-environment"></a>환경 설정

환경 변수 또는 플랫폼 설정을 사용하여 테스트하기 위해 특정 환경을 설정하면 유용한 경우가 많습니다. 환경을 설정하지 않으면 대부분의 디버깅 기능을 사용하지 않는 `Production`으로 기본값이 지정됩니다. 환경을 설정하는 방법은 운영 체제에 따라 다릅니다.

호스트를 빌드할 때 앱에서 읽은 마지막 환경 설정에 따라 앱의 환경이 결정됩니다. 앱을 실행하는 동안에는 앱 환경을 변경할 수 없습니다.

[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)의 [정보 페이지](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml)에는 `IWebHostEnvironment.EnvironmentName`의 값이 표시됩니다.

### <a name="azure-app-service"></a>Azure App Service

[Azure App Service](https://azure.microsoft.com/services/app-service/)에서 환경을 설정하려면 다음 단계를 수행합니다.

1. **App Services** 블레이드에서 앱을 선택합니다.
1. **설정** 그룹에서 **구성** 블레이드를 선택합니다.
1. **애플리케이션 설정** 탭에서 **새 애플리케이션 설정**를 선택합니다.
1. **애플리케이션 설정 추가/편집** 창에서 **이름**에 대한 `ASPNETCORE_ENVIRONMENT`를 제공합니다. **값**에는 환경을 입력합니다(예: `Staging`).
1. 배포 슬롯을 교환할 때 환경 설정을 현재 슬롯으로 유지하려면 **배포 슬롯 설정** 확인란을 선택합니다. 자세한 내용은 Azure 설명서의 [Azure App Service에서 스테이징 환경 설정](/azure/app-service/web-sites-staged-publishing)을 참조하세요.
1. **확인**을 선택하여 **애플리케이션 설정 추가/편집** 창을 닫습니다.
1. **구성** 블레이드의 맨 위에 있는 **저장**을 선택합니다.

Azure App Service는 Azure Portal에서 앱 설정이 추가, 변경 또는 삭제된 후 앱을 자동으로 다시 시작합니다.

### <a name="windows"></a>Windows

*launchSettings.json*의 환경 값은 시스템 환경에 설정된 값을 재정의합니다.

앱이 [dotnet run](/dotnet/core/tools/dotnet-run)을 사용하여 시작할 때 현재 세션에 대한 `ASPNETCORE_ENVIRONMENT`를 설정하려면 다음 명령이 사용됩니다.

**명령 프롬프트**

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

이전 명령은 해당 명령 창에서 시작된 프로세스에 대해서만 `ASPNETCORE_ENVIRONMENT`를 설정합니다.

Windows에서 전역적으로 값을 설정하려면 다음 방법 중 하나를 사용합니다.

* **제어판** > **시스템** > **고급 시스템 설정**을 열고 `ASPNETCORE_ENVIRONMENT` 값을 추가하거나 편집합니다.

  ![시스템 고급 속성](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 환경 변수](environments/_static/windows_aspnetcore_environment.png)

* 관리자 권한의 명령 프롬프트를 열고 `setx` 명령을 사용하거나 관리자 권한의 PowerShell 명령 프롬프트를 열고 `[Environment]::SetEnvironmentVariable`을 사용합니다.

  **명령 프롬프트**

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  `/M` 스위치는 시스템 수준에서 환경 변수를 설정함을 나타냅니다. `/M` 스위치를 사용하지 않으면 환경 변수가 사용자 계정에 대해 설정됩니다.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  `Machine` 옵션 값은 시스템 수준에서 환경 변수를 설정함을 나타냅니다. 옵션 값이 `User`로 변경되면 환경 변수가 사용자 계정에 대해 설정됩니다.

`ASPNETCORE_ENVIRONMENT` 환경 변수를 전역적으로 설정하면 값이 설정된 후 열리는 모든 명령 창에서 `dotnet run`에 대해 적용됩니다. *launchSettings.json*의 환경 값은 시스템 환경에 설정된 값을 재정의합니다.

**web.config**

*web.config*를 사용하여 `ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하려면 <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>의 *환경 변수 설정* 섹션을 참조하세요.

**프로젝트 파일 또는 게시 프로필**

**Windows IIS 배포의 경우:** `<EnvironmentName>` 속성을 [게시 프로필(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 또는 프로젝트 파일에 포함합니다. 이 방법은 프로젝트가 게시될 때 *web.config*에 환경을 설정합니다.

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**IIS 응용 프로그램 풀 별**

격리된 애플리케이션 풀에서 실행되는(IIS 10.0 이상에서 지원됨) 앱에 대한 `ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하려면, [ 환경 변수 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 *AppCmd.exe 명령* 섹션을 참조하세요. `ASPNETCORE_ENVIRONMENT` 환경 변수를 앱 풀에 대해 설정하면 해당 값이 시스템 수준의 설정을 재정의합니다.

IIS에서 앱을 호스팅하고 `ASPNETCORE_ENVIRONMENT` 환경 변수를 추가 또는 변경할 때 다음 방법 중 하나를 사용하여 앱에서 선택한 새 값을 가져옵니다.

* 명령 프롬프트에서 `net stop was /y` 다음에 `net start w3svc`를 실행합니다.
* 서버를 다시 시작합니다.

#### <a name="macos"></a>macOS

macOS에 대한 현재 환경 설정은 앱을 실행할 때 인라인으로 수행할 수 있습니다.

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

또는 앱을 실행하기 전에 `export`를 사용하여 환경을 설정합니다.

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

컴퓨터 수준 환경 변수는 *.bashrc* 또는 *.bash_profile* 파일에서 설정됩니다. 임의의 텍스트 편집기를 사용하여 파일을 편집합니다. 다음 명령문을 추가합니다.

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a>Linux

Linux 배포의 경우 세션 기반 변수 설정을 위해 명령 프롬프트에서 `export` 명령을 사용하거나 컴퓨터 수준 환경 설정을 위해 *bash_profile* 파일을 사용합니다.

### <a name="set-the-environment-in-code"></a>코드에서 환경 설정

호스트를 빌드할 때 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*>를 호출합니다. <xref:fundamentals/host/generic-host#environmentname>을 참조하세요.

### <a name="configuration-by-environment"></a>환경별 구성

환경별 구성을 로드하려면 <xref:fundamentals/configuration/index#json-configuration-provider>를 확인하세요.

## <a name="environment-based-startup-class-and-methods"></a>환경에 따른 시작 클래스 및 메서드

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>IWebHostEnvironment를 Startup 클래스에 삽입

`Startup` 생성자에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 삽입합니다. 이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경만 `Startup`을 구성해야 하는 경우에 유용합니다.

다음 예제에서,

* 환경은 `_env` 필드에 유지됩니다.
* `_env`는 `ConfigureServices` 및 `Configure`에서 사용되어 앱의 환경에 따라 시작 구성을 적용합니다.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a>시작 클래스 규칙

ASP.NET Core 앱이 시작되면 [Startup 클래스](xref:fundamentals/startup)가 앱을 부트스트랩합니다. 앱은 다양한 환경에 대한 여러 `Startup` 클래스를 정의할 수 있습니다. 런타임에 적절한 `Startup` 클래스가 선택됩니다. 이름 접미사가 현재 환경과 일치하는 클래스에 우선 순위가 부여됩니다. 일치하는 `Startup{EnvironmentName}` 클래스를 찾을 수 없으면 `Startup` 클래스가 사용됩니다. 이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경의 시작을 구성해야 하는 경우에 유용합니다. 일반적인 앱에는 이 방법이 필요하지 않습니다.

환경 기반 `Startup` 클래스를 구현하려면 `Startup{EnvironmentName}` 클래스와 대체 `Startup` 클래스를 만듭니다.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

어셈블리 이름을 허용하는 [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) 오버로드를 사용합니다.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a>시작 메서드 규칙

[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) 및 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices)는 `Configure<EnvironmentName>` 및 `Configure<EnvironmentName>Services` 양식의 환경 특정 버전을 지원합니다. 이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경의 시작을 구성해야 하는 경우에 유용합니다.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core는 환경 변수를 사용하여 런타임 환경에 따라 앱 동작을 구성합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="environments"></a>환경

ASP.NET Core는 앱 시작 시 환경 변수 `ASPNETCORE_ENVIRONMENT`를 읽고 [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName)에 그 값을 저장합니다. `ASPNETCORE_ENVIRONMENT`를 임의의 값으로 설정할 수 있지만 세 개의 값은 프레임워크에서 제공됩니다.

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>(기본값)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

위의 코드는

* `ASPNETCORE_ENVIRONMENT`이 `Development`로 설정된 경우 [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)를 호출합니다.
* `ASPNETCORE_ENVIRONMENT`의 값이 다음 중 하나로 설정된 경우 [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)를 호출합니다.

  * `Staging`
  * `Production`
  * `Staging_2`

[Environment 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)는 `IHostingEnvironment.EnvironmentName`의 값을 사용하여 요소에 태그를 포함하거나 제외합니다.

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

Windows 및 macOS에서 환경 변수 및 값은 대/소문자를 구분하지 않습니다. Linux 환경 변수 및 값은 기본적으로 대/소문자를 구분합니다.

### <a name="development"></a>개발

개발 환경은 프로덕션에서 노출해서는 안 되는 기능을 활성화할 수 있습니다. 예를 들어 ASP.NET Core 템플릿은 개발 환경에서 [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)를 활성화합니다.

로컬 컴퓨터 개발을 위한 환경은 프로젝트의 *Properties\launchSettings.json* 파일에서 설정할 수 있습니다. *launchSettings.json*의 환경 값은 시스템 환경에 설정된 값을 재정의합니다.

다음 JSON은 *launchSettings.json* 파일의 세 가지 프로필을 보여줍니다.

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> *launchSettings.json*의 `applicationUrl` 속성은 서버 URL의 목록을 지정할 수 있습니다. 목록의 URL 사이에 세미콜론을 사용합니다.
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

[dotnet run](/dotnet/core/tools/dotnet-run)을 사용하여 앱을 시작하면 `"commandName": "Project"`를 포함한 첫 번째 프로필이 사용됩니다. `commandName`의 값은 시작할 웹 서버를 지정합니다. `commandName`은 다음 중 하나일 수 있습니다.

* `IISExpress`
* `IIS`
* `Project`(Kestrel을 시작합니다.)

[dotnet run](/dotnet/core/tools/dotnet-run)으로 앱을 시작하는 경우:

* 가능한 경우 *launchSettings.json*을 읽습니다. *launchSettings.json*의 `environmentVariables` 설정이 환경 변수를 재정의합니다.
* 호스팅 환경이 표시됩니다.

다음 출력은 [dotnet run](/dotnet/core/tools/dotnet-run)으로 시작된 앱을 보여 줍니다.

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Visual Studio 프로젝트 속성 **Debug** 탭은 *launchSettings.json* 파일을 편집할 수 있는 GUI를 제공합니다.

![프로젝트 속성 설정 환경 변수](environments/_static/project-properties-debug.png)

웹 서버가 다시 시작되기 전에는 프로젝트 프로필의 변경 내용이 적용되지 않을 수 있습니다. 해당 환경에 대한 변경 내용을 감지하려면 Kestrel을 다시 시작해야 합니다.

> [!WARNING]
> *launchSettings.json*은 암호를 저장하지 않아야 합니다. [암호 관리자 도구](xref:security/app-secrets)를 사용하여 로컬 개발에 사용되는 암호를 저장할 수 있습니다.

[Visual Studio Code](https://code.visualstudio.com/)를 사용하는 경우 환경 변수는 *.vscode/launch.json* 파일에서 설정할 수 있습니다. 다음 예제는 환경을 `Development`로 설정합니다.

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

*Properties/launchSettings.json*과 같은 방식으로 `dotnet run`을 사용하여 앱을 시작할 때 프로젝트의 *.vscode/launch.json* 파일은 읽히지 않습니다. *launchSettings.json* 파일이 없는 개발 환경에서 앱을 시작할 때는 `dotnet run` 명령에 대한 환경 변수 또는 명령줄 인수로 환경을 설정합니다.

### <a name="production"></a>프로덕션

프로덕션 환경은 보안, 성능 및 앱 견고성을 최대화하도록 구성되어야 합니다. 개발과 다른 몇 가지 일반적인 설정은 다음과 같습니다.

* 캐싱.
* 클라이언트 쪽 리소스가 번들로 제공되고, 축소되며, 잠재적으로 CDN에서 처리됩니다.
* 진단 오류 페이지를 사용하지 않습니다.
* 친숙한 오류 페이지를 사용하도록 설정합니다.
* 프로덕션 로깅 및 모니터링을 사용합니다. 예: [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>환경 설정

환경 변수 또는 플랫폼 설정을 사용하여 테스트하기 위해 특정 환경을 설정하면 유용한 경우가 많습니다. 환경을 설정하지 않으면 대부분의 디버깅 기능을 사용하지 않는 `Production`으로 기본값이 지정됩니다. 환경을 설정하는 방법은 운영 체제에 따라 다릅니다.

호스트를 빌드할 때 앱에서 읽은 마지막 환경 설정에 따라 앱의 환경이 결정됩니다. 앱을 실행하는 동안에는 앱 환경을 변경할 수 없습니다.

### <a name="environment-variable-or-platform-setting"></a>환경 변수 또는 플랫폼 설정

#### <a name="azure-app-service"></a>Azure App Service

[Azure App Service](https://azure.microsoft.com/services/app-service/)에서 환경을 설정하려면 다음 단계를 수행합니다.

1. **App Services** 블레이드에서 앱을 선택합니다.
1. **설정** 그룹에서 **구성** 블레이드를 선택합니다.
1. **애플리케이션 설정** 탭에서 **새 애플리케이션 설정**를 선택합니다.
1. **애플리케이션 설정 추가/편집** 창에서 **이름**에 대한 `ASPNETCORE_ENVIRONMENT`를 제공합니다. **값**에는 환경을 입력합니다(예: `Staging`).
1. 배포 슬롯을 교환할 때 환경 설정을 현재 슬롯으로 유지하려면 **배포 슬롯 설정** 확인란을 선택합니다. 자세한 내용은 Azure 설명서의 [Azure App Service에서 스테이징 환경 설정](/azure/app-service/web-sites-staged-publishing)을 참조하세요.
1. **확인**을 선택하여 **애플리케이션 설정 추가/편집** 창을 닫습니다.
1. **구성** 블레이드의 맨 위에 있는 **저장**을 선택합니다.

Azure App Service는 Azure 포털에서 앱 설정(환경 변수)이 추가, 변경 또는 삭제된 후 앱을 자동으로 다시 시작합니다.

#### <a name="windows"></a>Windows

앱이 [dotnet run](/dotnet/core/tools/dotnet-run)을 사용하여 시작할 때 현재 세션에 대한 `ASPNETCORE_ENVIRONMENT`를 설정하려면 다음 명령이 사용됩니다.

**명령 프롬프트**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

이 명령들은 현재 창에만 적용됩니다. 창이 닫히면 `ASPNETCORE_ENVIRONMENT` 설정이 기본 설정 또는 컴퓨터 값으로 되돌아갑니다.

Windows에서 전역적으로 값을 설정하려면 다음 방법 중 하나를 사용합니다.

* **제어판** > **시스템** > **고급 시스템 설정**을 열고 `ASPNETCORE_ENVIRONMENT` 값을 추가하거나 편집합니다.

  ![시스템 고급 속성](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 환경 변수](environments/_static/windows_aspnetcore_environment.png)

* 관리자 권한의 명령 프롬프트를 열고 `setx` 명령을 사용하거나 관리자 권한의 PowerShell 명령 프롬프트를 열고 `[Environment]::SetEnvironmentVariable`을 사용합니다.

  **명령 프롬프트**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  `/M` 스위치는 시스템 수준에서 환경 변수를 설정함을 나타냅니다. `/M` 스위치를 사용하지 않으면 환경 변수가 사용자 계정에 대해 설정됩니다.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  `Machine` 옵션 값은 시스템 수준에서 환경 변수를 설정함을 나타냅니다. 옵션 값이 `User`로 변경되면 환경 변수가 사용자 계정에 대해 설정됩니다.

`ASPNETCORE_ENVIRONMENT` 환경 변수를 전역적으로 설정하면 값이 설정된 후 열리는 모든 명령 창에서 `dotnet run`에 대해 적용됩니다.

**web.config**

*web.config*를 사용하여 `ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하려면 <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>의 *환경 변수 설정* 섹션을 참조하세요.

**프로젝트 파일 또는 게시 프로필**

**Windows IIS 배포의 경우:** `<EnvironmentName>` 속성을 [게시 프로필(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 또는 프로젝트 파일에 포함합니다. 이 방법은 프로젝트가 게시될 때 *web.config*에 환경을 설정합니다.

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**IIS 응용 프로그램 풀 별**

격리된 애플리케이션 풀에서 실행되는(IIS 10.0 이상에서 지원됨) 앱에 대한 `ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하려면, [ 환경 변수 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 *AppCmd.exe 명령* 섹션을 참조하세요. `ASPNETCORE_ENVIRONMENT` 환경 변수를 앱 풀에 대해 설정하면 해당 값이 시스템 수준의 설정을 재정의합니다.

> [!IMPORTANT]
> IIS에서 앱을 호스팅하고 `ASPNETCORE_ENVIRONMENT` 환경 변수를 추가 또는 변경할 때 다음 방법 중 하나를 사용하여 앱에서 선택한 새 값을 가져옵니다.
>
> * 명령 프롬프트에서 `net stop was /y` 다음에 `net start w3svc`를 실행합니다.
> * 서버를 다시 시작합니다.

#### <a name="macos"></a>macOS

macOS에 대한 현재 환경 설정은 앱을 실행할 때 인라인으로 수행할 수 있습니다.

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

또는 앱을 실행하기 전에 `export`를 사용하여 환경을 설정합니다.

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

컴퓨터 수준 환경 변수는 *.bashrc* 또는 *.bash_profile* 파일에서 설정됩니다. 임의의 텍스트 편집기를 사용하여 파일을 편집합니다. 다음 명령문을 추가합니다.

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

Linux 배포의 경우 세션 기반 변수 설정을 위해 명령 프롬프트에서 `export` 명령을 사용하거나 컴퓨터 수준 환경 설정을 위해 *bash_profile* 파일을 사용합니다.

### <a name="set-the-environment-in-code"></a>코드에서 환경 설정

호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*>를 호출합니다. <xref:fundamentals/host/web-host#environment>을 참조하세요.

### <a name="configuration-by-environment"></a>환경별 구성

환경별 구성을 로드하려면 다음을 권장합니다.

* *appsettings* 파일(*appsettings.{Environment}.json*). <xref:fundamentals/configuration/index#json-configuration-provider>을 참조하세요.
* 환경 변수(앱이 호스팅되는 각 시스템에서 설정). <xref:fundamentals/host/web-host#environment> 및 <xref:security/app-secrets#environment-variables>를 확인합니다.
* 비밀 관리자(개발 환경에만 해당). <xref:security/app-secrets>을 참조하세요.

## <a name="environment-based-startup-class-and-methods"></a>환경에 따른 시작 클래스 및 메서드

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>IHostingEnvironment를 Startup.Configure에 삽입

`Startup.Configure`에 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>를 삽입합니다. 이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경만 `Startup.Configure`를 구성해야 하는 경우에 유용합니다.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a>IHostingEnvironment를 Startup 클래스에 삽입

`Startup` 생성자에 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>를 삽입하고 `Startup` 클래스 전체에서 하기 위해 서비스를 필드에 할당합니다. 이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경만 시작을 구성해야 하는 경우에 유용합니다.

다음 예제에서,

* 환경은 `_env` 필드에 유지됩니다.
* `_env`는 `ConfigureServices` 및 `Configure`에서 사용되어 앱의 환경에 따라 시작 구성을 적용합니다.

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a>시작 클래스 규칙

ASP.NET Core 앱이 시작되면 [Startup 클래스](xref:fundamentals/startup)가 앱을 부트스트랩합니다. 앱은 다양한 환경에 대한 별도의 `Startup` 클래스를 정의할 수 있습니다(예: `StartupDevelopment`). 런타임에 적절한 `Startup` 클래스가 선택됩니다. 이름 접미사가 현재 환경과 일치하는 클래스에 우선 순위가 부여됩니다. 일치하는 `Startup{EnvironmentName}` 클래스를 찾을 수 없으면 `Startup` 클래스가 사용됩니다. 이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경의 시작을 구성해야 하는 경우에 유용합니다.

환경 기반 `Startup` 클래스를 구현하려면 사용 중인 각 환경에 대한 `Startup{EnvironmentName}` 클래스와 폴백 `Startup` 클래스를 만듭니다.

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

어셈블리 이름을 허용하는 [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) 오버로드를 사용합니다.

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a>시작 메서드 규칙

[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) 및 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices)는 `Configure<EnvironmentName>` 및 `Configure<EnvironmentName>Services` 양식의 환경 특정 버전을 지원합니다. 이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경의 시작을 구성해야 하는 경우에 유용합니다.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
