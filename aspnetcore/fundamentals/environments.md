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
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="61885-103">ASP.NET Core에서 여러 환경 사용</span><span class="sxs-lookup"><span data-stu-id="61885-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="61885-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="61885-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="61885-105">ASP.NET Core는 환경 변수를 사용하여 런타임 환경에 따라 앱 동작을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="61885-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="61885-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="61885-107">환경</span><span class="sxs-lookup"><span data-stu-id="61885-107">Environments</span></span>

<span data-ttu-id="61885-108">런타임 환경을 확인하려면 다음 환경 변수에서 ASP.NET Core를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-108">To determine the runtime environment, ASP.NET Core reads from the following environment variables:</span></span>

1. [<span data-ttu-id="61885-109">DOTNET_ENVIRONMENT</span><span class="sxs-lookup"><span data-stu-id="61885-109">DOTNET_ENVIRONMENT</span></span>](xref:fundamentals/configuration/index#default-host-configuration)
1. <span data-ttu-id="61885-110"><xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>를 호출하는 경우 `ASPNETCORE_ENVIRONMENT`를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-110">`ASPNETCORE_ENVIRONMENT` when <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> is called.</span></span> <span data-ttu-id="61885-111">기본 ASP.NET Core 웹앱 템플릿은 `ConfigureWebHostDefaults`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-111">The default ASP.NET Core web app templates call `ConfigureWebHostDefaults`.</span></span> <span data-ttu-id="61885-112">`ASPNETCORE_ENVIRONMENT` 값은 `DOTNET_ENVIRONMENT`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-112">The `ASPNETCORE_ENVIRONMENT` value overrides `DOTNET_ENVIRONMENT`.</span></span>

<span data-ttu-id="61885-113">`IHostEnvironment.EnvironmentName`을 임의의 값으로 설정할 수 있지만 다음 값은 프레임워크에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-113">`IHostEnvironment.EnvironmentName` can be set to any value, but the following values are provided by the framework:</span></span>

* <span data-ttu-id="61885-114"><xref:Microsoft.Extensions.Hosting.Environments.Development> : [launchSettings. json](#lsj) 파일은 로컬 컴퓨터에서 `ASPNETCORE_ENVIRONMENT`를 `Development`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-114"><xref:Microsoft.Extensions.Hosting.Environments.Development> : The [launchSettings.json](#lsj) file sets `ASPNETCORE_ENVIRONMENT` to `Development` on the local machine.</span></span>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="61885-115"><xref:Microsoft.Extensions.Hosting.Environments.Production> : `DOTNET_ENVIRONMENT` 및 `ASPNETCORE_ENVIRONMENT`가 설정되지 않은 경우 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="61885-115"><xref:Microsoft.Extensions.Hosting.Environments.Production> : The default if `DOTNET_ENVIRONMENT` and `ASPNETCORE_ENVIRONMENT` have not been set.</span></span>

<span data-ttu-id="61885-116">코드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-116">The following code:</span></span>

* <span data-ttu-id="61885-117">`ASPNETCORE_ENVIRONMENT`이 `Development`로 설정된 경우 [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-117">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="61885-118">`ASPNETCORE_ENVIRONMENT`의 값이 `Staging`, `Production` 또는 `Staging_2`로 설정된 경우 [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-118">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set to `Staging`, `Production`, or  `Staging_2`.</span></span>
* <span data-ttu-id="61885-119">`Startup.Configure`에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-119">Injects <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="61885-120">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경의 `Startup.Configure`만 조정해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-120">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>
* <span data-ttu-id="61885-121">ASP.NET Core 템플릿에서 생성된 코드와 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-121">Is similar to the code generated by the ASP.NET Core templates.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="61885-122">[Environment 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)는 [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName)의 값을 사용하여 요소에 태그를 포함하거나 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-122">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="61885-123">[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)의 [정보 페이지](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml)에는 이전 태그가 포함되며 `IWebHostEnvironment.EnvironmentName`의 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-123">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) includes the preceding markup and displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

<span data-ttu-id="61885-124">Windows 및 macOS에서 환경 변수 및 값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-124">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="61885-125">Linux 환경 변수 및 값은 기본적으로 **대/소문자를 구분**합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-125">Linux environment variables and values are **case-sensitive** by default.</span></span>

### <a name="create-environmentssample"></a><span data-ttu-id="61885-126">EnvironmentsSample 만들기</span><span class="sxs-lookup"><span data-stu-id="61885-126">Create EnvironmentsSample</span></span>

<span data-ttu-id="61885-127">이 문서에서 사용되는 [샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)는 *EnvironmentsSample*이라는 Razor Pages 프로젝트를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-127">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) used in this document is based on a Razor Pages project named *EnvironmentsSample*.</span></span>

<span data-ttu-id="61885-128">다음 코드는 *EnvironmentsSample*이라는 웹앱을 만들고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-128">The following code creates and runs a web app named *EnvironmentsSample*:</span></span>

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

<span data-ttu-id="61885-129">앱이 실행되면 다음과 같은 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-129">When the app runs, it displays some of the following output:</span></span>

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

### <a name="development-and-launchsettingsjson"></a><span data-ttu-id="61885-130">개발 및 launchSettings.json</span><span class="sxs-lookup"><span data-stu-id="61885-130">Development and launchSettings.json</span></span>

<span data-ttu-id="61885-131">개발 환경은 프로덕션에서 노출해서는 안 되는 기능을 활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-131">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="61885-132">예를 들어 ASP.NET Core 템플릿은 개발 환경에서 [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-132">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="61885-133">로컬 컴퓨터 개발을 위한 환경은 프로젝트의 *Properties\launchSettings.json* 파일에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-133">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="61885-134">*launchSettings.json*의 환경 값은 시스템 환경에 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-134">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="61885-135">*launchSettings.json* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-135">The *launchSettings.json* file:</span></span>

* <span data-ttu-id="61885-136">로컬 개발 머신에서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-136">Is only used on the local development machine.</span></span>
* <span data-ttu-id="61885-137">배포되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-137">Is not deployed.</span></span>
* <span data-ttu-id="61885-138">프로필 설정을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-138">contains profile settings.</span></span>

<span data-ttu-id="61885-139">다음 JSON은 Visual Studio 또는 `dotnet new`를 사용하여 만든 *EnvironmentsSample*이라는 이름의 예정된 ASP.NET Core 웹에 대한 *launchSettings.json* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="61885-139">The following JSON shows the *launchSettings.json* file for an ASP.NET Core web projected named *EnvironmentsSample* created with Visual Studio or `dotnet new`:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

<span data-ttu-id="61885-140">위의 태그는 다음과 같은 두 개의 프로필을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-140">The preceding markup contains two profiles:</span></span>

* <span data-ttu-id="61885-141">`IIS Express`: Visual Studio에서 앱을 시작할 때 사용되는 기본 프로필입니다.</span><span class="sxs-lookup"><span data-stu-id="61885-141">`IIS Express`: The default profile used when launching the app from Visual Studio.</span></span> <span data-ttu-id="61885-142">`"commandName"` 키에 `"IISExpress"` 값이 있으므로 [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="61885-142">The `"commandName"` key has the value `"IISExpress"`, therefore, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) is the web server.</span></span> <span data-ttu-id="61885-143">시작 프로필을 프로젝트 또는 포함된 다른 프로필로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-143">You can set the launch profile to the project or any other profile included.</span></span> <span data-ttu-id="61885-144">예를 들어 아래 이미지에서 프로젝트 이름을 선택하면 [Kestrel 웹 서버](xref:fundamentals/servers/kestrel)가 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-144">For example, in the image below, selecting the project name launches the [Kestrel web server](xref:fundamentals/servers/kestrel).</span></span>

  ![메뉴에서 IIS Express 시작](environments/_static/iisx2.png)
* <span data-ttu-id="61885-146">`EnvironmentsSample`: 프로필 이름은 프로젝트 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="61885-146">`EnvironmentsSample`: The profile name is the project name.</span></span> <span data-ttu-id="61885-147">`dotnet run`를 사용하여 앱을 시작할 때 기본적으로 이 프로필이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-147">This profile is used by default when launching the app with `dotnet run`.</span></span>  <span data-ttu-id="61885-148">`"commandName"` 키에 `"Project"` 값이 있으므로 [Kestrel 웹 서버](xref:fundamentals/servers/kestrel)가 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-148">The `"commandName"` key has the value `"Project"`, therefore, the [Kestrel web server](xref:fundamentals/servers/kestrel) is launched.</span></span>

<span data-ttu-id="61885-149">`commandName`의 값은 시작할 웹 서버를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-149">The value of `commandName` can specify the web server to launch.</span></span> <span data-ttu-id="61885-150">`commandName`은 다음 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-150">`commandName` can be any one of the following:</span></span>

* <span data-ttu-id="61885-151">`IISExpress` : IIS Express를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-151">`IISExpress` : Launches IIS Express.</span></span>
* <span data-ttu-id="61885-152">`IIS` : 웹 서버가 시작되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-152">`IIS` : No web server launched.</span></span> <span data-ttu-id="61885-153">IIS를 사용할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-153">IIS is expected to be available.</span></span>
* <span data-ttu-id="61885-154">`Project` : Kestrel을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-154">`Project` : Launches Kestrel.</span></span>

<span data-ttu-id="61885-155">Visual Studio 프로젝트 속성 **Debug** 탭은 *launchSettings.json* 파일을 편집할 수 있는 GUI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-155">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file.</span></span> <span data-ttu-id="61885-156">웹 서버가 다시 시작되기 전에는 프로젝트 프로필의 변경 내용이 적용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-156">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="61885-157">해당 환경에 대한 변경 내용을 감지하려면 Kestrel을 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-157">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

![프로젝트 속성 설정 환경 변수](environments/_static/project-properties-debug.png)

<span data-ttu-id="61885-159">다음 *launchSettings. json* 파일에는 여러 프로필이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-159">The following *launchSettings.json* file contains multiple profiles:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

<span data-ttu-id="61885-160">프로필은 다음과 같이 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-160">Profiles can be selected:</span></span>

* <span data-ttu-id="61885-161">Visual Studio UI에서 선택</span><span class="sxs-lookup"><span data-stu-id="61885-161">From the Visual Studio UI.</span></span>
* <span data-ttu-id="61885-162">프로필 이름으로 설정된 `--launch-profile` 옵션이 있는 명령 셸에서 [`dotnet run`](/dotnet/core/tools/dotnet-run) 명령을 사용하여 선택</span><span class="sxs-lookup"><span data-stu-id="61885-162">Using the [`dotnet run`](/dotnet/core/tools/dotnet-run) command in a command shell with the `--launch-profile` option set to the profile's name.</span></span> <span data-ttu-id="61885-163">이 방법은 Kestrel 프로필만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-163">*This approach only supports Kestrel profiles.*</span></span>

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> <span data-ttu-id="61885-164">*launchSettings.json*은 암호를 저장하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-164">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="61885-165">[암호 관리자 도구](xref:security/app-secrets)를 사용하여 로컬 개발에 사용되는 암호를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-165">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="61885-166">[Visual Studio Code](https://code.visualstudio.com/)를 사용하는 경우 환경 변수는 *.vscode/launch.json* 파일에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-166">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="61885-167">다음 예제에서는 여러 [호스트 구성 값 환경 변수](xref:fundamentals/host/web-host#host-configuration-values)를 다음과 같이 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-167">The following example sets several [Host configuration values environment variables](xref:fundamentals/host/web-host#host-configuration-values):</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

<span data-ttu-id="61885-168">*.vscode/launch.json* 파일은 Visual Studio Code에서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-168">The *.vscode/launch.json* file is only used by Visual Studio Code.</span></span>

### <a name="production"></a><span data-ttu-id="61885-169">프로덕션</span><span class="sxs-lookup"><span data-stu-id="61885-169">Production</span></span>

<span data-ttu-id="61885-170">프로덕션 환경은 보안, [성능](xref:performance/performance-best-practices) 및 애플리케이션 견고성을 최대화하도록 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-170">The production environment should be configured to maximize security, [performance](xref:performance/performance-best-practices), and application robustness.</span></span> <span data-ttu-id="61885-171">개발과 다른 몇 가지 일반적인 설정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-171">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="61885-172">[캐싱](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="61885-172">[Caching](xref:performance/caching/memory).</span></span>
* <span data-ttu-id="61885-173">클라이언트 쪽 리소스가 번들로 제공되고, 축소되며, 잠재적으로 CDN에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-173">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="61885-174">진단 오류 페이지를 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-174">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="61885-175">친숙한 오류 페이지를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-175">Friendly error pages enabled.</span></span>
* <span data-ttu-id="61885-176">프로덕션 [로깅](xref:fundamentals/logging/index) 및 모니터링을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-176">Production [logging](xref:fundamentals/logging/index) and monitoring enabled.</span></span> <span data-ttu-id="61885-177">예를 들어 [Application Insights](/azure/application-insights/app-insights-asp-net-core)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-177">For example, using [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="61885-178">환경 설정</span><span class="sxs-lookup"><span data-stu-id="61885-178">Set the environment</span></span>

<span data-ttu-id="61885-179">환경 변수 또는 플랫폼 설정을 사용하여 테스트하기 위해 특정 환경을 설정하면 유용한 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-179">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="61885-180">환경을 설정하지 않으면 대부분의 디버깅 기능을 사용하지 않는 `Production`으로 기본값이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-180">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="61885-181">환경을 설정하는 방법은 운영 체제에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="61885-181">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="61885-182">호스트를 빌드할 때 앱에서 읽은 마지막 환경 설정에 따라 앱의 환경이 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-182">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="61885-183">앱을 실행하는 동안에는 앱 환경을 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-183">The app's environment can't be changed while the app is running.</span></span>

<span data-ttu-id="61885-184">[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample)의 [정보 페이지](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml)에는 `IWebHostEnvironment.EnvironmentName`의 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-184">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="61885-185">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="61885-185">Azure App Service</span></span>

<span data-ttu-id="61885-186">[Azure App Service](https://azure.microsoft.com/services/app-service/)에서 환경을 설정하려면 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-186">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="61885-187">**App Services** 블레이드에서 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-187">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="61885-188">**설정** 그룹에서 **구성** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-188">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="61885-189">**애플리케이션 설정** 탭에서 **새 애플리케이션 설정**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-189">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="61885-190">**애플리케이션 설정 추가/편집** 창에서 **이름**에 대한 `ASPNETCORE_ENVIRONMENT`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-190">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="61885-191">**값**에는 환경을 입력합니다(예: `Staging`).</span><span class="sxs-lookup"><span data-stu-id="61885-191">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="61885-192">배포 슬롯을 교환할 때 환경 설정을 현재 슬롯으로 유지하려면 **배포 슬롯 설정** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-192">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="61885-193">자세한 내용은 Azure 설명서의 [Azure App Service에서 스테이징 환경 설정](/azure/app-service/web-sites-staged-publishing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="61885-193">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="61885-194">**확인**을 선택하여 **애플리케이션 설정 추가/편집** 창을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-194">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="61885-195">**구성** 블레이드의 맨 위에 있는 **저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-195">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="61885-196">Azure App Service는 Azure Portal에서 앱 설정이 추가, 변경 또는 삭제된 후 앱을 자동으로 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-196">Azure App Service automatically restarts the app after an app setting is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="61885-197">Windows</span><span class="sxs-lookup"><span data-stu-id="61885-197">Windows</span></span>

<span data-ttu-id="61885-198">*launchSettings.json*의 환경 값은 시스템 환경에 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-198">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="61885-199">앱이 [dotnet run](/dotnet/core/tools/dotnet-run)을 사용하여 시작할 때 현재 세션에 대한 `ASPNETCORE_ENVIRONMENT`를 설정하려면 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-199">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="61885-200">**명령 프롬프트**</span><span class="sxs-lookup"><span data-stu-id="61885-200">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

<span data-ttu-id="61885-201">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="61885-201">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

<span data-ttu-id="61885-202">이전 명령은 해당 명령 창에서 시작된 프로세스에 대해서만 `ASPNETCORE_ENVIRONMENT`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-202">The preceding command sets `ASPNETCORE_ENVIRONMENT` only for processes launched from that command window.</span></span>

<span data-ttu-id="61885-203">Windows에서 전역적으로 값을 설정하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-203">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="61885-204">**제어판** > **시스템** > **고급 시스템 설정**을 열고 `ASPNETCORE_ENVIRONMENT` 값을 추가하거나 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-204">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![시스템 고급 속성](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 환경 변수](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="61885-207">관리자 권한의 명령 프롬프트를 열고 `setx` 명령을 사용하거나 관리자 권한의 PowerShell 명령 프롬프트를 열고 `[Environment]::SetEnvironmentVariable`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-207">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="61885-208">**명령 프롬프트**</span><span class="sxs-lookup"><span data-stu-id="61885-208">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  <span data-ttu-id="61885-209">`/M` 스위치는 시스템 수준에서 환경 변수를 설정함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="61885-209">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="61885-210">`/M` 스위치를 사용하지 않으면 환경 변수가 사용자 계정에 대해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-210">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="61885-211">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="61885-211">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  <span data-ttu-id="61885-212">`Machine` 옵션 값은 시스템 수준에서 환경 변수를 설정함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="61885-212">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="61885-213">옵션 값이 `User`로 변경되면 환경 변수가 사용자 계정에 대해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-213">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="61885-214">`ASPNETCORE_ENVIRONMENT` 환경 변수를 전역적으로 설정하면 값이 설정된 후 열리는 모든 명령 창에서 `dotnet run`에 대해 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-214">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span> <span data-ttu-id="61885-215">*launchSettings.json*의 환경 값은 시스템 환경에 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-215">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="61885-216">**web.config**</span><span class="sxs-lookup"><span data-stu-id="61885-216">**web.config**</span></span>

<span data-ttu-id="61885-217">*web.config*를 사용하여 `ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하려면 <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>의 *환경 변수 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="61885-217">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="61885-218">**프로젝트 파일 또는 게시 프로필**</span><span class="sxs-lookup"><span data-stu-id="61885-218">**Project file or publish profile**</span></span>

<span data-ttu-id="61885-219">**Windows IIS 배포의 경우:** `<EnvironmentName>` 속성을 [게시 프로필(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 또는 프로젝트 파일에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-219">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="61885-220">이 방법은 프로젝트가 게시될 때 *web.config*에 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-220">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="61885-221">**IIS 응용 프로그램 풀 별**</span><span class="sxs-lookup"><span data-stu-id="61885-221">**Per IIS Application Pool**</span></span>

<span data-ttu-id="61885-222">격리된 애플리케이션 풀에서 실행되는(IIS 10.0 이상에서 지원됨) 앱에 대한 `ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하려면, [ 환경 변수 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 *AppCmd.exe 명령* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="61885-222">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="61885-223">`ASPNETCORE_ENVIRONMENT` 환경 변수를 앱 풀에 대해 설정하면 해당 값이 시스템 수준의 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-223">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

<span data-ttu-id="61885-224">IIS에서 앱을 호스팅하고 `ASPNETCORE_ENVIRONMENT` 환경 변수를 추가 또는 변경할 때 다음 방법 중 하나를 사용하여 앱에서 선택한 새 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="61885-224">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>

* <span data-ttu-id="61885-225">명령 프롬프트에서 `net stop was /y` 다음에 `net start w3svc`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-225">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
* <span data-ttu-id="61885-226">서버를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-226">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="61885-227">macOS</span><span class="sxs-lookup"><span data-stu-id="61885-227">macOS</span></span>

<span data-ttu-id="61885-228">macOS에 대한 현재 환경 설정은 앱을 실행할 때 인라인으로 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-228">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

<span data-ttu-id="61885-229">또는 앱을 실행하기 전에 `export`를 사용하여 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-229">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

<span data-ttu-id="61885-230">컴퓨터 수준 환경 변수는 *.bashrc* 또는 *.bash_profile* 파일에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-230">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="61885-231">임의의 텍스트 편집기를 사용하여 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-231">Edit the file using any text editor.</span></span> <span data-ttu-id="61885-232">다음 명령문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-232">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a><span data-ttu-id="61885-233">Linux</span><span class="sxs-lookup"><span data-stu-id="61885-233">Linux</span></span>

<span data-ttu-id="61885-234">Linux 배포의 경우 세션 기반 변수 설정을 위해 명령 프롬프트에서 `export` 명령을 사용하거나 컴퓨터 수준 환경 설정을 위해 *bash_profile* 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-234">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="61885-235">코드에서 환경 설정</span><span class="sxs-lookup"><span data-stu-id="61885-235">Set the environment in code</span></span>

<span data-ttu-id="61885-236">호스트를 빌드할 때 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-236">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="61885-237"><xref:fundamentals/host/generic-host#environmentname>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="61885-237">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="61885-238">환경별 구성</span><span class="sxs-lookup"><span data-stu-id="61885-238">Configuration by environment</span></span>

<span data-ttu-id="61885-239">환경별 구성을 로드하려면 <xref:fundamentals/configuration/index#json-configuration-provider>를 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="61885-239">To load configuration by environment, see <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="61885-240">환경에 따른 시작 클래스 및 메서드</span><span class="sxs-lookup"><span data-stu-id="61885-240">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="61885-241">IWebHostEnvironment를 Startup 클래스에 삽입</span><span class="sxs-lookup"><span data-stu-id="61885-241">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="61885-242">`Startup` 생성자에 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-242">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="61885-243">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경만 `Startup`을 구성해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-243">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="61885-244">다음 예제에서,</span><span class="sxs-lookup"><span data-stu-id="61885-244">In the following example:</span></span>

* <span data-ttu-id="61885-245">환경은 `_env` 필드에 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-245">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="61885-246">`_env`는 `ConfigureServices` 및 `Configure`에서 사용되어 앱의 환경에 따라 시작 구성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-246">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a><span data-ttu-id="61885-247">시작 클래스 규칙</span><span class="sxs-lookup"><span data-stu-id="61885-247">Startup class conventions</span></span>

<span data-ttu-id="61885-248">ASP.NET Core 앱이 시작되면 [Startup 클래스](xref:fundamentals/startup)가 앱을 부트스트랩합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-248">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="61885-249">앱은 다양한 환경에 대한 여러 `Startup` 클래스를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-249">The app can define multiple `Startup` classes for different environments.</span></span> <span data-ttu-id="61885-250">런타임에 적절한 `Startup` 클래스가 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-250">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="61885-251">이름 접미사가 현재 환경과 일치하는 클래스에 우선 순위가 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-251">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="61885-252">일치하는 `Startup{EnvironmentName}` 클래스를 찾을 수 없으면 `Startup` 클래스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-252">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="61885-253">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경의 시작을 구성해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-253">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span> <span data-ttu-id="61885-254">일반적인 앱에는 이 방법이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-254">Typical apps will not need this approach.</span></span>

<span data-ttu-id="61885-255">환경 기반 `Startup` 클래스를 구현하려면 `Startup{EnvironmentName}` 클래스와 대체 `Startup` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="61885-255">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` classes and a fallback `Startup` class:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

<span data-ttu-id="61885-256">어셈블리 이름을 허용하는 [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-256">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a><span data-ttu-id="61885-257">시작 메서드 규칙</span><span class="sxs-lookup"><span data-stu-id="61885-257">Startup method conventions</span></span>

<span data-ttu-id="61885-258">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) 및 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices)는 `Configure<EnvironmentName>` 및 `Configure<EnvironmentName>Services` 양식의 환경 특정 버전을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-258">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="61885-259">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경의 시작을 구성해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-259">This approach is useful when the app requires configuring startup for several environments with many code differences per environment:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="61885-260">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="61885-260">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="61885-261">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="61885-261">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="61885-262">ASP.NET Core는 환경 변수를 사용하여 런타임 환경에 따라 앱 동작을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-262">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="61885-263">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="61885-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="61885-264">환경</span><span class="sxs-lookup"><span data-stu-id="61885-264">Environments</span></span>

<span data-ttu-id="61885-265">ASP.NET Core는 앱 시작 시 환경 변수 `ASPNETCORE_ENVIRONMENT`를 읽고 [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName)에 그 값을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-265">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="61885-266">`ASPNETCORE_ENVIRONMENT`를 임의의 값으로 설정할 수 있지만 세 개의 값은 프레임워크에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-266">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="61885-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>(기본값)</span><span class="sxs-lookup"><span data-stu-id="61885-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="61885-268">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="61885-268">The preceding code:</span></span>

* <span data-ttu-id="61885-269">`ASPNETCORE_ENVIRONMENT`이 `Development`로 설정된 경우 [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-269">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="61885-270">`ASPNETCORE_ENVIRONMENT`의 값이 다음 중 하나로 설정된 경우 [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-270">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="61885-271">[Environment 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)는 `IHostingEnvironment.EnvironmentName`의 값을 사용하여 요소에 태그를 포함하거나 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-271">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="61885-272">Windows 및 macOS에서 환경 변수 및 값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-272">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="61885-273">Linux 환경 변수 및 값은 기본적으로 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-273">Linux environment variables and values are case-sensitive by default.</span></span>

### <a name="development"></a><span data-ttu-id="61885-274">개발</span><span class="sxs-lookup"><span data-stu-id="61885-274">Development</span></span>

<span data-ttu-id="61885-275">개발 환경은 프로덕션에서 노출해서는 안 되는 기능을 활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-275">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="61885-276">예를 들어 ASP.NET Core 템플릿은 개발 환경에서 [개발자 예외 페이지](xref:fundamentals/error-handling#developer-exception-page)를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-276">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="61885-277">로컬 컴퓨터 개발을 위한 환경은 프로젝트의 *Properties\launchSettings.json* 파일에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-277">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="61885-278">*launchSettings.json*의 환경 값은 시스템 환경에 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-278">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="61885-279">다음 JSON은 *launchSettings.json* 파일의 세 가지 프로필을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="61885-279">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="61885-280">*launchSettings.json*의 `applicationUrl` 속성은 서버 URL의 목록을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-280">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="61885-281">목록의 URL 사이에 세미콜론을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-281">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="61885-282">[dotnet run](/dotnet/core/tools/dotnet-run)을 사용하여 앱을 시작하면 `"commandName": "Project"`를 포함한 첫 번째 프로필이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-282">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="61885-283">`commandName`의 값은 시작할 웹 서버를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-283">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="61885-284">`commandName`은 다음 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-284">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="61885-285">`Project`(Kestrel을 시작합니다.)</span><span class="sxs-lookup"><span data-stu-id="61885-285">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="61885-286">[dotnet run](/dotnet/core/tools/dotnet-run)으로 앱을 시작하는 경우:</span><span class="sxs-lookup"><span data-stu-id="61885-286">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="61885-287">가능한 경우 *launchSettings.json*을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-287">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="61885-288">*launchSettings.json*의 `environmentVariables` 설정이 환경 변수를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-288">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="61885-289">호스팅 환경이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-289">The hosting environment is displayed.</span></span>

<span data-ttu-id="61885-290">다음 출력은 [dotnet run](/dotnet/core/tools/dotnet-run)으로 시작된 앱을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="61885-290">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="61885-291">Visual Studio 프로젝트 속성 **Debug** 탭은 *launchSettings.json* 파일을 편집할 수 있는 GUI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-291">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![프로젝트 속성 설정 환경 변수](environments/_static/project-properties-debug.png)

<span data-ttu-id="61885-293">웹 서버가 다시 시작되기 전에는 프로젝트 프로필의 변경 내용이 적용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-293">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="61885-294">해당 환경에 대한 변경 내용을 감지하려면 Kestrel을 다시 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-294">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="61885-295">*launchSettings.json*은 암호를 저장하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-295">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="61885-296">[암호 관리자 도구](xref:security/app-secrets)를 사용하여 로컬 개발에 사용되는 암호를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-296">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="61885-297">[Visual Studio Code](https://code.visualstudio.com/)를 사용하는 경우 환경 변수는 *.vscode/launch.json* 파일에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-297">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="61885-298">다음 예제는 환경을 `Development`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-298">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="61885-299">*Properties/launchSettings.json*과 같은 방식으로 `dotnet run`을 사용하여 앱을 시작할 때 프로젝트의 *.vscode/launch.json* 파일은 읽히지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-299">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="61885-300">*launchSettings.json* 파일이 없는 개발 환경에서 앱을 시작할 때는 `dotnet run` 명령에 대한 환경 변수 또는 명령줄 인수로 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-300">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="61885-301">프로덕션</span><span class="sxs-lookup"><span data-stu-id="61885-301">Production</span></span>

<span data-ttu-id="61885-302">프로덕션 환경은 보안, 성능 및 앱 견고성을 최대화하도록 구성되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-302">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="61885-303">개발과 다른 몇 가지 일반적인 설정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-303">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="61885-304">캐싱.</span><span class="sxs-lookup"><span data-stu-id="61885-304">Caching.</span></span>
* <span data-ttu-id="61885-305">클라이언트 쪽 리소스가 번들로 제공되고, 축소되며, 잠재적으로 CDN에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-305">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="61885-306">진단 오류 페이지를 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-306">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="61885-307">친숙한 오류 페이지를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-307">Friendly error pages enabled.</span></span>
* <span data-ttu-id="61885-308">프로덕션 로깅 및 모니터링을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-308">Production logging and monitoring enabled.</span></span> <span data-ttu-id="61885-309">예: [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="61885-309">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="61885-310">환경 설정</span><span class="sxs-lookup"><span data-stu-id="61885-310">Set the environment</span></span>

<span data-ttu-id="61885-311">환경 변수 또는 플랫폼 설정을 사용하여 테스트하기 위해 특정 환경을 설정하면 유용한 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-311">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="61885-312">환경을 설정하지 않으면 대부분의 디버깅 기능을 사용하지 않는 `Production`으로 기본값이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-312">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="61885-313">환경을 설정하는 방법은 운영 체제에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="61885-313">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="61885-314">호스트를 빌드할 때 앱에서 읽은 마지막 환경 설정에 따라 앱의 환경이 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-314">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="61885-315">앱을 실행하는 동안에는 앱 환경을 변경할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-315">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="61885-316">환경 변수 또는 플랫폼 설정</span><span class="sxs-lookup"><span data-stu-id="61885-316">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="61885-317">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="61885-317">Azure App Service</span></span>

<span data-ttu-id="61885-318">[Azure App Service](https://azure.microsoft.com/services/app-service/)에서 환경을 설정하려면 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-318">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="61885-319">**App Services** 블레이드에서 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-319">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="61885-320">**설정** 그룹에서 **구성** 블레이드를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-320">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="61885-321">**애플리케이션 설정** 탭에서 **새 애플리케이션 설정**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-321">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="61885-322">**애플리케이션 설정 추가/편집** 창에서 **이름**에 대한 `ASPNETCORE_ENVIRONMENT`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-322">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="61885-323">**값**에는 환경을 입력합니다(예: `Staging`).</span><span class="sxs-lookup"><span data-stu-id="61885-323">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="61885-324">배포 슬롯을 교환할 때 환경 설정을 현재 슬롯으로 유지하려면 **배포 슬롯 설정** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-324">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="61885-325">자세한 내용은 Azure 설명서의 [Azure App Service에서 스테이징 환경 설정](/azure/app-service/web-sites-staged-publishing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="61885-325">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="61885-326">**확인**을 선택하여 **애플리케이션 설정 추가/편집** 창을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-326">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="61885-327">**구성** 블레이드의 맨 위에 있는 **저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-327">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="61885-328">Azure App Service는 Azure 포털에서 앱 설정(환경 변수)이 추가, 변경 또는 삭제된 후 앱을 자동으로 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-328">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="61885-329">Windows</span><span class="sxs-lookup"><span data-stu-id="61885-329">Windows</span></span>

<span data-ttu-id="61885-330">앱이 [dotnet run](/dotnet/core/tools/dotnet-run)을 사용하여 시작할 때 현재 세션에 대한 `ASPNETCORE_ENVIRONMENT`를 설정하려면 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-330">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="61885-331">**명령 프롬프트**</span><span class="sxs-lookup"><span data-stu-id="61885-331">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="61885-332">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="61885-332">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="61885-333">이 명령들은 현재 창에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-333">These commands only take effect for the current window.</span></span> <span data-ttu-id="61885-334">창이 닫히면 `ASPNETCORE_ENVIRONMENT` 설정이 기본 설정 또는 컴퓨터 값으로 되돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="61885-334">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="61885-335">Windows에서 전역적으로 값을 설정하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-335">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="61885-336">**제어판** > **시스템** > **고급 시스템 설정**을 열고 `ASPNETCORE_ENVIRONMENT` 값을 추가하거나 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-336">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![시스템 고급 속성](environments/_static/systemsetting_environment.png)

  ![ASPNET Core 환경 변수](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="61885-339">관리자 권한의 명령 프롬프트를 열고 `setx` 명령을 사용하거나 관리자 권한의 PowerShell 명령 프롬프트를 열고 `[Environment]::SetEnvironmentVariable`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-339">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="61885-340">**명령 프롬프트**</span><span class="sxs-lookup"><span data-stu-id="61885-340">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="61885-341">`/M` 스위치는 시스템 수준에서 환경 변수를 설정함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="61885-341">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="61885-342">`/M` 스위치를 사용하지 않으면 환경 변수가 사용자 계정에 대해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-342">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="61885-343">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="61885-343">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="61885-344">`Machine` 옵션 값은 시스템 수준에서 환경 변수를 설정함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="61885-344">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="61885-345">옵션 값이 `User`로 변경되면 환경 변수가 사용자 계정에 대해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-345">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="61885-346">`ASPNETCORE_ENVIRONMENT` 환경 변수를 전역적으로 설정하면 값이 설정된 후 열리는 모든 명령 창에서 `dotnet run`에 대해 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-346">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="61885-347">**web.config**</span><span class="sxs-lookup"><span data-stu-id="61885-347">**web.config**</span></span>

<span data-ttu-id="61885-348">*web.config*를 사용하여 `ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하려면 <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>의 *환경 변수 설정* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="61885-348">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="61885-349">**프로젝트 파일 또는 게시 프로필**</span><span class="sxs-lookup"><span data-stu-id="61885-349">**Project file or publish profile**</span></span>

<span data-ttu-id="61885-350">**Windows IIS 배포의 경우:** `<EnvironmentName>` 속성을 [게시 프로필(.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) 또는 프로젝트 파일에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-350">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="61885-351">이 방법은 프로젝트가 게시될 때 *web.config*에 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-351">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="61885-352">**IIS 응용 프로그램 풀 별**</span><span class="sxs-lookup"><span data-stu-id="61885-352">**Per IIS Application Pool**</span></span>

<span data-ttu-id="61885-353">격리된 애플리케이션 풀에서 실행되는(IIS 10.0 이상에서 지원됨) 앱에 대한 `ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하려면, [ 환경 변수 &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 항목의 *AppCmd.exe 명령* 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="61885-353">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="61885-354">`ASPNETCORE_ENVIRONMENT` 환경 변수를 앱 풀에 대해 설정하면 해당 값이 시스템 수준의 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-354">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="61885-355">IIS에서 앱을 호스팅하고 `ASPNETCORE_ENVIRONMENT` 환경 변수를 추가 또는 변경할 때 다음 방법 중 하나를 사용하여 앱에서 선택한 새 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="61885-355">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="61885-356">명령 프롬프트에서 `net stop was /y` 다음에 `net start w3svc`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-356">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="61885-357">서버를 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-357">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="61885-358">macOS</span><span class="sxs-lookup"><span data-stu-id="61885-358">macOS</span></span>

<span data-ttu-id="61885-359">macOS에 대한 현재 환경 설정은 앱을 실행할 때 인라인으로 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="61885-359">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="61885-360">또는 앱을 실행하기 전에 `export`를 사용하여 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-360">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="61885-361">컴퓨터 수준 환경 변수는 *.bashrc* 또는 *.bash_profile* 파일에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-361">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="61885-362">임의의 텍스트 편집기를 사용하여 파일을 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-362">Edit the file using any text editor.</span></span> <span data-ttu-id="61885-363">다음 명령문을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-363">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="61885-364">Linux</span><span class="sxs-lookup"><span data-stu-id="61885-364">Linux</span></span>

<span data-ttu-id="61885-365">Linux 배포의 경우 세션 기반 변수 설정을 위해 명령 프롬프트에서 `export` 명령을 사용하거나 컴퓨터 수준 환경 설정을 위해 *bash_profile* 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-365">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="61885-366">코드에서 환경 설정</span><span class="sxs-lookup"><span data-stu-id="61885-366">Set the environment in code</span></span>

<span data-ttu-id="61885-367">호스트를 빌드할 때 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-367">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="61885-368"><xref:fundamentals/host/web-host#environment>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="61885-368">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="61885-369">환경별 구성</span><span class="sxs-lookup"><span data-stu-id="61885-369">Configuration by environment</span></span>

<span data-ttu-id="61885-370">환경별 구성을 로드하려면 다음을 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-370">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="61885-371">*appsettings* 파일(*appsettings.{Environment}.json*).</span><span class="sxs-lookup"><span data-stu-id="61885-371">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="61885-372"><xref:fundamentals/configuration/index#json-configuration-provider>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="61885-372">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="61885-373">환경 변수(앱이 호스팅되는 각 시스템에서 설정).</span><span class="sxs-lookup"><span data-stu-id="61885-373">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="61885-374"><xref:fundamentals/host/web-host#environment> 및 <xref:security/app-secrets#environment-variables>를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-374">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="61885-375">비밀 관리자(개발 환경에만 해당).</span><span class="sxs-lookup"><span data-stu-id="61885-375">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="61885-376"><xref:security/app-secrets>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="61885-376">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="61885-377">환경에 따른 시작 클래스 및 메서드</span><span class="sxs-lookup"><span data-stu-id="61885-377">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="61885-378">IHostingEnvironment를 Startup.Configure에 삽입</span><span class="sxs-lookup"><span data-stu-id="61885-378">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="61885-379">`Startup.Configure`에 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-379">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="61885-380">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경만 `Startup.Configure`를 구성해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-380">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="61885-381">IHostingEnvironment를 Startup 클래스에 삽입</span><span class="sxs-lookup"><span data-stu-id="61885-381">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="61885-382">`Startup` 생성자에 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>를 삽입하고 `Startup` 클래스 전체에서 하기 위해 서비스를 필드에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-382">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="61885-383">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경만 시작을 구성해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-383">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="61885-384">다음 예제에서,</span><span class="sxs-lookup"><span data-stu-id="61885-384">In the following example:</span></span>

* <span data-ttu-id="61885-385">환경은 `_env` 필드에 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-385">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="61885-386">`_env`는 `ConfigureServices` 및 `Configure`에서 사용되어 앱의 환경에 따라 시작 구성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-386">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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

### <a name="startup-class-conventions"></a><span data-ttu-id="61885-387">시작 클래스 규칙</span><span class="sxs-lookup"><span data-stu-id="61885-387">Startup class conventions</span></span>

<span data-ttu-id="61885-388">ASP.NET Core 앱이 시작되면 [Startup 클래스](xref:fundamentals/startup)가 앱을 부트스트랩합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-388">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="61885-389">앱은 다양한 환경에 대한 별도의 `Startup` 클래스를 정의할 수 있습니다(예: `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="61885-389">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="61885-390">런타임에 적절한 `Startup` 클래스가 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-390">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="61885-391">이름 접미사가 현재 환경과 일치하는 클래스에 우선 순위가 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-391">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="61885-392">일치하는 `Startup{EnvironmentName}` 클래스를 찾을 수 없으면 `Startup` 클래스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="61885-392">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="61885-393">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경의 시작을 구성해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-393">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="61885-394">환경 기반 `Startup` 클래스를 구현하려면 사용 중인 각 환경에 대한 `Startup{EnvironmentName}` 클래스와 폴백 `Startup` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="61885-394">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="61885-395">어셈블리 이름을 허용하는 [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-395">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="61885-396">시작 메서드 규칙</span><span class="sxs-lookup"><span data-stu-id="61885-396">Startup method conventions</span></span>

<span data-ttu-id="61885-397">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) 및 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices)는 `Configure<EnvironmentName>` 및 `Configure<EnvironmentName>Services` 양식의 환경 특정 버전을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-397">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="61885-398">이 접근 방식은 앱에서 환경별 코드 차이가 최소인 몇 가지 환경의 시작을 구성해야 하는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="61885-398">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="61885-399">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="61885-399">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
