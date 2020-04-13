---
title: ASP.NET Core의 Razor 파일 컴파일
author: rick-anderson
description: Razor 파일의 컴파일이 ASP.NET Core 앱에서 발생하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 0afd39fdb5a6f570e0e78ad54f6c436460bad3a6
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223961"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="2eed9-103">ASP.NET Core의 Razor 파일 컴파일</span><span class="sxs-lookup"><span data-stu-id="2eed9-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="2eed9-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2eed9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2eed9-105">확장명이 *cshtml*인 Razor 파일은 [Razor SDK](xref:razor-pages/sdk)를 사용하여 빌드 및 게시 시 모두에 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="2eed9-106">애플리케이션을 구성하여 런타임 컴파일을 선택적으로 활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-106">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="2eed9-107">Razor 컴파일</span><span class="sxs-lookup"><span data-stu-id="2eed9-107">Razor compilation</span></span>

<span data-ttu-id="2eed9-108">Razor 파일의 빌드 및 게시 시점 컴파일은 Razor SDK에서 기본적으로 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-108">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="2eed9-109">런타임 컴파일이 활성화되면 Razor 파일이 편집될 경우 해당 파일이 갱신될 수 있도록 빌드 시 컴파일을 보완합니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="2eed9-110">런타임 컴파일</span><span class="sxs-lookup"><span data-stu-id="2eed9-110">Runtime compilation</span></span>

<span data-ttu-id="2eed9-111">모든 환경 및 구성 모드에 대해 런타임 컴파일을 사용하도록 설정하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-111">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="2eed9-112">[Microsoft.AspNetCore.Mvc.Razor.Runtime컴파일](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-112">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="2eed9-113"><xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-113">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="2eed9-114">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-114">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="2eed9-115">조건부로 런타임 컴파일을 사용하도록 설정</span><span class="sxs-lookup"><span data-stu-id="2eed9-115">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="2eed9-116">런타임 컴파일을 로컬 개발에만 사용하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-116">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="2eed9-117">이러한 방식으로 조건부로 사용하도록 설정하면 게시된 출력은 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-117">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="2eed9-118">컴파일된 보기를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-118">Uses compiled views.</span></span>
* <span data-ttu-id="2eed9-119">크기가 더 작습니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-119">Is smaller in size.</span></span>
* <span data-ttu-id="2eed9-120">프로덕션에서 파일 감시자를 사용하도록 설정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-120">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="2eed9-121">환경 및 구성 모드를 기반으로 런타임 컴파일을 사용하도록 설정하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-121">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="2eed9-122">활성 `Configuration` 값에 따라 조건부로 [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-122">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="2eed9-123">`AddRazorRuntimeCompilation`에 대한 호출을 포함하도록 프로젝트의 `Startup.ConfigureServices` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-123">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="2eed9-124">`ASPNETCORE_ENVIRONMENT` 변수가 `Development`로 설정된 경우에만 디버그 모드에서 실행되도록 조건부로 `AddRazorRuntimeCompilation`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-124">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

  [!code-csharp[](~/mvc/views/view-compilation/sample/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="2eed9-125">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2eed9-125">Additional resources</span></span>

* <span data-ttu-id="2eed9-126">[Razor컴파일온빌드 및 Razor컴파일온퍼퍼퍼표](xref:razor-pages/sdk#properties) 속성.</span><span class="sxs-lookup"><span data-stu-id="2eed9-126">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="2eed9-127">프로젝트 전체에서 런타임 컴파일 작업을 수행한다는 샘플은 [GitHub의 런타임 컴파일 샘플을](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="2eed9-127">See the [runtimecompilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2eed9-128">Razor 파일은 런타임 시에 관련 Razor Page 또는 MVC 보기가 호출될 때 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-128">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="2eed9-129">Razor 파일은 [Razor SDK](xref:razor-pages/sdk)를 사용하여 빌드 및 게시 시 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-129">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="2eed9-130">Razor 컴파일</span><span class="sxs-lookup"><span data-stu-id="2eed9-130">Razor compilation</span></span>

<span data-ttu-id="2eed9-131">Razor 파일의 빌드 및 게시 시점 컴파일은 Razor SDK에서 기본적으로 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-131">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="2eed9-132">업데이트된 후에 Razor 파일을 편집하는 작업은 빌드 시 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-132">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="2eed9-133">기본적으로 Razor 파일을 컴파일하는 데 필요한 컴파일된 *Views.dll*(*.cshtml* 파일 없음) 또는 참조 어셈블리만 앱과 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-133">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2eed9-134">사전 컴파일 도구는 더 이상 사용되지 않으며 ASP.NET Core 3.0에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-134">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="2eed9-135">[Razor Sdk](xref:razor-pages/sdk)로 마이그레이션하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-135">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="2eed9-136">Razor SDK는 미리 컴파일 특정 속성이 프로젝트 파일에서 설정되지 않은 경우에만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-136">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="2eed9-137">예를 들어 *.csproj* 파일의 `MvcRazorCompileOnPublish` 속성을 `true`로 설정하면 Razor SDK가 비활성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-137">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="2eed9-138">런타임 컴파일</span><span class="sxs-lookup"><span data-stu-id="2eed9-138">Runtime compilation</span></span>

<span data-ttu-id="2eed9-139">빌드 시 컴파일은 Razor 파일의 런타임 컴파일에 의해 보완됩니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-139">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="2eed9-140">*.cshtml* 파일의 내용이 변경되면 ASP.NET Core MVC는 Razor 파일을 다시 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="2eed9-140">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2eed9-141">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2eed9-141">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
