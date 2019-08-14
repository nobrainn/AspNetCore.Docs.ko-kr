---
title: ASP.NET Core Razor 구성 요소 클래스 라이브러리
author: guardrex
description: 외부 구성 요소 라이브러리의 Blazor apps에 구성 요소를 포함 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/class-libraries
ms.openlocfilehash: 402b7b072554f63f85e7cf5e55336104d235a071
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68948443"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="4d66a-103">ASP.NET Core Razor 구성 요소 클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="4d66a-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="4d66a-104">[Simon](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="4d66a-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="4d66a-105">구성 요소는 프로젝트 간에 [RCL (Razor 클래스 라이브러리)](xref:razor-pages/ui-class) 에서 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="4d66a-106">*Razor 구성 요소 클래스 라이브러리* 는 다음에서 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="4d66a-107">솔루션의 다른 프로젝트</span><span class="sxs-lookup"><span data-stu-id="4d66a-107">Another project in the solution.</span></span>
* <span data-ttu-id="4d66a-108">NuGet 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-108">A NuGet package.</span></span>
* <span data-ttu-id="4d66a-109">참조 된 .NET 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-109">A referenced .NET library.</span></span>

<span data-ttu-id="4d66a-110">구성 요소가 일반적인 .NET 형식인 것 처럼 RCL에서 제공 하는 구성 요소는 일반적인 .NET 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="4d66a-111">RCL 만들기</span><span class="sxs-lookup"><span data-stu-id="4d66a-111">Create an RCL</span></span>

<span data-ttu-id="4d66a-112">Blazor에 대 한 환경을 <xref:blazor/get-started> 구성 하려면 문서의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="4d66a-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4d66a-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d66a-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="4d66a-114">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-114">Create a new project.</span></span>
1. <span data-ttu-id="4d66a-115">**새 ASP.NET Core 웹 응용 프로그램**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-115">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="4d66a-116">**다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-116">Select **Next**.</span></span>
1. <span data-ttu-id="4d66a-117">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="4d66a-118">이 항목의 예제에서는 프로젝트 이름을 `MyComponentLib1`사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-118">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="4d66a-119">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-119">Select **Create**.</span></span>
1. <span data-ttu-id="4d66a-120">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.0**이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-120">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="4d66a-121">**Razor 클래스 라이브러리** 템플릿을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-121">Select the **Razor Class Library** template.</span></span> <span data-ttu-id="4d66a-122">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-122">Select **Create**.</span></span>
1. <span data-ttu-id="4d66a-123">솔루션에 RCL을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-123">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="4d66a-124">솔루션을 마우스 오른쪽 단추로 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-124">Right-click the solution.</span></span> <span data-ttu-id="4d66a-125">**기존 프로젝트** **추가** > 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-125">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="4d66a-126">RCL의 프로젝트 파일로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-126">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="4d66a-127">RCL의 프로젝트 파일 ( *.csproj*)을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-127">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="4d66a-128">앱에서 RCL 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-128">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="4d66a-129">앱 프로젝트를 마우스 오른쪽 단추로 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-129">Right-click the app project.</span></span> <span data-ttu-id="4d66a-130">**참조** **추가** > 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-130">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="4d66a-131">RCL 프로젝트를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-131">Select the RCL project.</span></span> <span data-ttu-id="4d66a-132">          **확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-132">Select **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="4d66a-133">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4d66a-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="4d66a-134">명령 셸에서 [dotnet new](/dotnet/core/tools/dotnet-new) 명령과 함께 **Razor 클래스 라이브러리** 템플릿 (`razorclasslib`)을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="4d66a-135">다음 예에서는 라는 `MyComponentLib1`rcl을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="4d66a-136">명령이 실행 될 때 `MyComponentLib1` 가 포함 된 폴더가 자동으로 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. <span data-ttu-id="4d66a-137">기존 프로젝트에 라이브러리를 추가 하려면 명령 셸에서 [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) 명령을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-137">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="4d66a-138">다음 예제에서는 RCL이 앱에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-138">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="4d66a-139">라이브러리에 대 한 경로를 사용 하 여 앱의 프로젝트 폴더에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-139">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="rcls-not-supported-for-client-side-apps"></a><span data-ttu-id="4d66a-140">RCLs는 클라이언트 쪽 앱에 대해 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-140">RCLs not supported for client-side apps</span></span>

<span data-ttu-id="4d66a-141">현재 ASP.NET Core 3.0 미리 보기에서 Razor 클래스 라이브러리는 Blazor 클라이언트 쪽 앱과 호환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-141">In the current ASP.NET Core 3.0 Preview, Razor class libraries aren't compatible with Blazor client-side apps.</span></span> <span data-ttu-id="4d66a-142">Blazor 클라이언트 쪽 응용 프로그램의 경우 명령 셸에서 `blazorlib` 템플릿에서 만든 Blazor 구성 요소 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-142">For Blazor client-side apps, use a Blazor component library created by the `blazorlib` template in a command shell:</span></span>

```console
dotnet new blazorlib -o MyComponentLib1
```

<span data-ttu-id="4d66a-143">템플릿을 사용 하는 `blazorlib` 구성 요소 라이브러리는 이미지, JavaScript 및 스타일 시트와 같은 정적 파일을 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-143">Component libraries using the `blazorlib` template can include static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="4d66a-144">빌드 시 정적 파일은 작성 된 어셈블리 파일 ( *.dll*)에 포함 되며,이를 통해 해당 리소스를 포함 하는 방법에 대해 걱정할 필요 없이 구성 요소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-144">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="4d66a-145">`content` 디렉터리에 포함 된 모든 파일은 포함 리소스로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-145">Any files included in the `content` directory are marked as an embedded resource.</span></span>

## <a name="consume-a-library-component"></a><span data-ttu-id="4d66a-146">라이브러리 구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="4d66a-146">Consume a library component</span></span>

<span data-ttu-id="4d66a-147">다른 프로젝트의 라이브러리에 정의 된 구성 요소를 사용 하려면 다음 방법 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-147">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="4d66a-148">전체 형식 이름에 네임 스페이스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-148">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="4d66a-149">Razor의 [ \@using](xref:mvc/views/razor#using) 지시문을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-149">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="4d66a-150">개별 구성 요소는 이름으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-150">Individual components can be added by name.</span></span>

<span data-ttu-id="4d66a-151">다음 예제 `MyComponentLib1` 에서은 `SalesReport` 구성 요소를 포함 하는 구성 요소 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-151">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="4d66a-152">네임 `SalesReport` 스페이스의 전체 형식 이름을 사용 하 여 구성 요소를 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-152">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="4d66a-153">`@using` 지시문을 사용 하 여 라이브러리를 범위로 가져오는 경우 구성 요소를 참조할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-153">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="4d66a-154">최상위 수준 *가져오기. razor* 파일에 지시문을포함하여전체프로젝트에서라이브러리의구성요소를사용할수있도록합니다.`@using MyComponentLib1`</span><span class="sxs-lookup"><span data-stu-id="4d66a-154">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="4d66a-155">모든 수준에서 *_Import. razor* 파일에 지시문을 추가 하 여 폴더 내의 단일 페이지 또는 페이지 집합에 네임 스페이스를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-155">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="4d66a-156">빌드, 팩 및 NuGet에 제공</span><span class="sxs-lookup"><span data-stu-id="4d66a-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="4d66a-157">구성 요소 라이브러리는 표준 .NET 라이브러리 이기 때문에 NuGet으로 패키징 및 전달 하는 것은 nuget에 라이브러리를 패키징 및 전달 하는 것과 다르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="4d66a-158">패키지는 명령 셸에서 [dotnet pack](/dotnet/core/tools/dotnet-pack) 명령을 사용 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```console
dotnet pack
```

<span data-ttu-id="4d66a-159">명령 셸에서 [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) 명령을 사용 하 여 NuGet에 패키지를 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-159">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command in a command shell:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="4d66a-160">`blazorlib` 템플릿을 사용할 때 정적 리소스는 NuGet 패키지에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-160">When using the `blazorlib` template, static resources are included in the NuGet package.</span></span> <span data-ttu-id="4d66a-161">라이브러리 소비자는 스크립트 및 스타일 시트를 자동으로 받기 때문에 소비자는 리소스를 수동으로 설치할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-161">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="4d66a-162">정적 자산을 사용 하 여 Razor 구성 요소 클래스 라이브러리 만들기</span><span class="sxs-lookup"><span data-stu-id="4d66a-162">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="4d66a-163">RCL에는 정적 자산이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-163">An RCL can include static assets.</span></span> <span data-ttu-id="4d66a-164">정적 자산은 라이브러리를 사용 하는 모든 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4d66a-164">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="4d66a-165">자세한 내용은 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4d66a-165">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d66a-166">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4d66a-166">Additional resources</span></span>

* <xref:razor-pages/ui-class>