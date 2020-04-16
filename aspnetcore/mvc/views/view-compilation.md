---
title: ASP.NET Core의 Razor 파일 컴파일
author: rick-anderson
description: Razor 파일의 컴파일이 ASP.NET Core 앱에서 발생하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 3d871ab960de28a565280d9e4cb2c597832e2455
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440937"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="77497-103">ASP.NET Core의 Razor 파일 컴파일</span><span class="sxs-lookup"><span data-stu-id="77497-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="77497-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="77497-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="77497-105">확장명이 *cshtml*인 Razor 파일은 [Razor SDK](xref:razor-pages/sdk)를 사용하여 빌드 및 게시 시 모두에 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="77497-106">런타임 컴파일은 프로젝트를 구성하여 선택적으로 활성화될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77497-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="77497-107">Razor 컴파일</span><span class="sxs-lookup"><span data-stu-id="77497-107">Razor compilation</span></span>

<span data-ttu-id="77497-108">Razor 파일의 빌드 시간 및 게시 시간 컴파일은 Razor SDK에 의해 기본적으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="77497-109">런타임 컴파일을 사용하도록 설정하면 빌드 시간 컴파일이 보완되므로 편집할 경우 Razor 파일을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77497-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="77497-110">프로젝트 생성 시 런타임 컴파일 사용</span><span class="sxs-lookup"><span data-stu-id="77497-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="77497-111">Razor 페이지 및 MVC 프로젝트 템플릿에는 프로젝트를 만들 때 런타임 컴파일을 활성화하는 옵션이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77497-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="77497-112">이 옵션은 ASP.NET 코어 3.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="77497-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77497-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="77497-114">새로운 **ASP.NET 코어 웹 응용 프로그램** 대화 상자 만들기에서:</span><span class="sxs-lookup"><span data-stu-id="77497-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="77497-115">**웹 응용 프로그램** 또는 웹 **응용 프로그램(모델-뷰-컨트롤러)** 프로젝트 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="77497-116">Razor **런타임 컴파일 사용** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="77497-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="77497-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="77497-118">`-rrc` 또는 `--razor-runtime-compilation` 템플릿 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="77497-119">예를 들어 다음 명령은 런타임 컴파일을 사용하도록 설정된 새 Razor Pages 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="77497-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="77497-120">기존 프로젝트에서 런타임 컴파일 사용</span><span class="sxs-lookup"><span data-stu-id="77497-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="77497-121">기존 프로젝트의 모든 환경에 대해 런타임 컴파일을 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="77497-122">[Microsoft.AspNetCore.Mvc.Razor.Runtime컴파일](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="77497-123"><xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="77497-124">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="77497-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="77497-125">기존 프로젝트에서 런타임 컴파일을 조건부로 활성화</span><span class="sxs-lookup"><span data-stu-id="77497-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="77497-126">런타임 컴파일을 로컬 개발에만 사용하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77497-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="77497-127">이러한 방식으로 조건부로 사용하도록 설정하면 게시된 출력은 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="77497-128">컴파일된 보기를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-128">Uses compiled views.</span></span>
* <span data-ttu-id="77497-129">프로덕션에서 파일 감시자를 사용하도록 설정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="77497-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="77497-130">개발 환경에서만 런타임 컴파일을 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="77497-131">[Microsoft.AspNetCore.Mvc.Razor.Runtime컴파일](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="77497-132">`environmentVariables` *launchSettings.json에서*시작 프로필 섹션을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="77497-133">확인이 `ASPNETCORE_ENVIRONMENT` `"Development"`로 설정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77497-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="77497-134">`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`를 `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="77497-135">다음 예제에서는 런타임 컴파일이 개발 환경에서 및 `IIS Express` `RazorPagesApp` 시작 프로필에 대해 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77497-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="77497-136">프로젝트 `Startup` 클래스에는 코드를 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="77497-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="77497-137">런타임시 ASP.NET Core는 에서 `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` [어셈블리 수준 HostingStartup 특성을](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="77497-138">속성은 `HostingStartup` 실행할 앱 시작 코드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="77497-139">이 시작 코드는 런타임 컴파일을 가능하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="77497-140">Razor 클래스 라이브러리에 대한 런타임 컴파일 사용</span><span class="sxs-lookup"><span data-stu-id="77497-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="77497-141">Razor 페이지 프로젝트가 *MyClassLib이라는* [Razor 클래스 라이브러리(RCL)를](xref:razor-pages/ui-class) 참조하는 시나리오를 생각해 보십시오.</span><span class="sxs-lookup"><span data-stu-id="77497-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="77497-142">RCL에는 팀의 모든 MVC 및 Razor 페이지 프로젝트가 사용하는 *_Layout.cshtml* 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77497-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="77497-143">해당 RCL에서 *_Layout.cshtml* 파일에 대한 런타임 컴파일을 사용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="77497-144">Razor 페이지 프로젝트에서 다음 을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="77497-145">런타임 컴파일을 [조건부로 사용하여 기존 프로젝트에서 런타임 컴파일을 사용하도록 설정합니다.](#conditionally-enable-runtime-compilation-in-an-existing-project)</span><span class="sxs-lookup"><span data-stu-id="77497-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="77497-146">런타임 컴파일 옵션을 다음과 `Startup.ConfigureServices`같은 방법으로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="77497-147">앞의 코드에서 *MyClassLib* RCL에 대한 절대 경로가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="77497-148">[PhysicalFileProvider API는](xref:fundamentals/file-providers#physicalfileprovider) 해당 절대 경로에서 디렉터리 및 파일을 찾는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="77497-149">마지막으로 RCL의 `PhysicalFileProvider` *.cshtml* 파일에 액세스할 수 있는 파일 공급자 컬렉션에 인스턴스가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77497-150">추가 자료</span><span class="sxs-lookup"><span data-stu-id="77497-150">Additional resources</span></span>

* <span data-ttu-id="77497-151">[Razor컴파일온빌드 및 Razor컴파일온퍼퍼퍼표](xref:razor-pages/sdk#properties) 속성.</span><span class="sxs-lookup"><span data-stu-id="77497-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="77497-152">확장명이 *cshtml*인 Razor 파일은 [Razor SDK](xref:razor-pages/sdk)를 사용하여 빌드 및 게시 시 모두에 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="77497-153">애플리케이션을 구성하여 런타임 컴파일을 선택적으로 활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77497-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="77497-154">Razor 컴파일</span><span class="sxs-lookup"><span data-stu-id="77497-154">Razor compilation</span></span>

<span data-ttu-id="77497-155">Razor 파일의 빌드 시간 및 게시 시간 컴파일은 Razor SDK에 의해 기본적으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="77497-156">런타임 컴파일을 사용하도록 설정하면 빌드 시간 컴파일이 보완되므로 편집할 경우 Razor 파일을 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77497-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="77497-157">런타임 컴파일</span><span class="sxs-lookup"><span data-stu-id="77497-157">Runtime compilation</span></span>

<span data-ttu-id="77497-158">모든 환경 및 구성 모드에 대해 런타임 컴파일을 사용하도록 설정하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="77497-159">[Microsoft.AspNetCore.Mvc.Razor.Runtime컴파일](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="77497-160"><xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="77497-161">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="77497-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="77497-162">조건부로 런타임 컴파일을 사용하도록 설정</span><span class="sxs-lookup"><span data-stu-id="77497-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="77497-163">런타임 컴파일을 로컬 개발에만 사용하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="77497-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="77497-164">이러한 방식으로 조건부로 사용하도록 설정하면 게시된 출력은 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="77497-165">컴파일된 보기를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-165">Uses compiled views.</span></span>
* <span data-ttu-id="77497-166">크기가 더 작습니다.</span><span class="sxs-lookup"><span data-stu-id="77497-166">Is smaller in size.</span></span>
* <span data-ttu-id="77497-167">프로덕션에서 파일 감시자를 사용하도록 설정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="77497-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="77497-168">환경 및 구성 모드를 기반으로 런타임 컴파일을 사용하도록 설정하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="77497-169">활성 `Configuration` 값에 따라 조건부로 [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="77497-170">`AddRazorRuntimeCompilation`에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="77497-171">`ASPNETCORE_ENVIRONMENT` 변수가 `Development`로 설정된 경우에만 디버그 모드에서 실행되도록 조건부로 `AddRazorRuntimeCompilation`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="77497-172">추가 자료</span><span class="sxs-lookup"><span data-stu-id="77497-172">Additional resources</span></span>

* <span data-ttu-id="77497-173">[Razor컴파일온빌드 및 Razor컴파일온퍼퍼퍼표](xref:razor-pages/sdk#properties) 속성.</span><span class="sxs-lookup"><span data-stu-id="77497-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="77497-174">프로젝트 전체에서 런타임 컴파일 작업을 수행한다는 샘플은 [GitHub의 런타임 컴파일 샘플을](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="77497-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="77497-175">Razor 파일은 런타임 시에 관련 Razor Page 또는 MVC 보기가 호출될 때 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="77497-176">Razor 파일은 [Razor SDK](xref:razor-pages/sdk)를 사용하여 빌드 및 게시 시 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="77497-177">Razor 컴파일</span><span class="sxs-lookup"><span data-stu-id="77497-177">Razor compilation</span></span>

<span data-ttu-id="77497-178">Razor 파일의 빌드 및 게시 시점 컴파일은 Razor SDK에서 기본적으로 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="77497-179">업데이트된 후에 Razor 파일을 편집하는 작업은 빌드 시 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="77497-180">기본적으로 Razor 파일을 컴파일하는 데 필요한 컴파일된 *Views.dll*(*.cshtml* 파일 없음) 또는 참조 어셈블리만 앱과 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="77497-181">사전 컴파일 도구는 더 이상 사용되지 않으며 ASP.NET Core 3.0에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="77497-182">[Razor Sdk](xref:razor-pages/sdk)로 마이그레이션하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="77497-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="77497-183">Razor SDK는 미리 컴파일 특정 속성이 프로젝트 파일에서 설정되지 않은 경우에만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="77497-184">예를 들어 *.csproj* 파일의 `MvcRazorCompileOnPublish` 속성을 `true`로 설정하면 Razor SDK가 비활성됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="77497-185">런타임 컴파일</span><span class="sxs-lookup"><span data-stu-id="77497-185">Runtime compilation</span></span>

<span data-ttu-id="77497-186">빌드 시 컴파일은 Razor 파일의 런타임 컴파일에 의해 보완됩니다.</span><span class="sxs-lookup"><span data-stu-id="77497-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="77497-187">*.cshtml* 파일의 내용이 변경되면 ASP.NET Core MVC는 Razor 파일을 다시 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="77497-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77497-188">추가 자료</span><span class="sxs-lookup"><span data-stu-id="77497-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
