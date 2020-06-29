---
title: ASP.NET Core의 컨트롤러에 종속성 주입
author: ardalis
description: ASP.NET Core MVC 컨트롤러가 ASP.NET Core의 종속성 주입을 사용하여 해당 생성자를 통해 명시적으로 해당 종속성을 요청하는 방법을 알아봅니다.
ms.author: riande
ms.date: 02/24/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: bae31e38c3b4146ec5e4b7a398a2e0fa290fd34c
ms.sourcegitcommit: 99c784a873b62fbd97a73c5c07f4fe7a7f5db638
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2020
ms.locfileid: "85503541"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="65eaa-103">ASP.NET Core의 컨트롤러에 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="65eaa-103">Dependency injection into controllers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65eaa-104">작성자: [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="65eaa-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="65eaa-105">ASP.NET Core MVC 컨트롤러는 생성자를 통해 명시적으로 종속성을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="65eaa-106">ASP.NET Core는 기본적으로 [DI(종속성 주입 )](xref:fundamentals/dependency-injection)를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="65eaa-107">DI를 사용하면 앱의 테스트와 유지 관리가 쉬워집니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="65eaa-108">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="65eaa-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="65eaa-109">생성자 주입</span><span class="sxs-lookup"><span data-stu-id="65eaa-109">Constructor Injection</span></span>

<span data-ttu-id="65eaa-110">서비스는 생성자 매개 변수로 추가되며, 런타임은 서비스 컨테이너에서 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="65eaa-111">서비스는 일반적으로 인터페이스를 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="65eaa-112">예를 들어, 현재 시간이 필요한 앱을 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="65eaa-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="65eaa-113">다음 인터페이스는 `IDateTime` 서비스를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="65eaa-114">다음 코드는 `IDateTime` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="65eaa-115">서비스를 서비스 컨테이너에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="65eaa-116"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>에 대한 자세한 내용은 [DI 서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65eaa-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="65eaa-117">다음 코드는 현재 시간을 기준으로 사용자에게 인사말을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="65eaa-118">앱을 실행하면 시간을 기준으로 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="65eaa-119">FromServices를 이용한 작업 주입</span><span class="sxs-lookup"><span data-stu-id="65eaa-119">Action injection with FromServices</span></span>

<span data-ttu-id="65eaa-120"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute>를 사용하면 생성자 주입을 사용하지 않고 작업 메서드에 직접 서비스를 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="65eaa-121">컨트롤러에서 설정 액세스</span><span class="sxs-lookup"><span data-stu-id="65eaa-121">Access settings from a controller</span></span>

<span data-ttu-id="65eaa-122">컨트롤러 내에서 앱 또는 구성 설정에 액세스하는 것은 일반적인 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="65eaa-123"><xref:fundamentals/configuration/options>에 설명된 *옵션 패턴*은 설정을 관리하기 위해 선호되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="65eaa-124">일반적으로 컨트롤러에 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 직접 주입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="65eaa-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="65eaa-125">옵션을 나타내는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-125">Create a class that represents the options.</span></span> <span data-ttu-id="65eaa-126">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-126">For example:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="65eaa-127">서비스 컬렉션에 구성 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="65eaa-128">JSON 형식 파일에서 설정을 읽도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="65eaa-129">다음 코드는 서비스 컨테이너에서 `IOptions<SampleWebSettings>` 설정을 요청하고 이를 `Index` 메서드에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="65eaa-130">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="65eaa-130">Additional resources</span></span>

* <span data-ttu-id="65eaa-131">컨트롤러에서 종속성을 명시적으로 요청하여 코드를 더 쉽게 테스트할 수 있는 방법을 알아보려면 <xref:mvc/controllers/testing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65eaa-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="65eaa-132">[기본 종속성 주입 컨테이너를 타사 구현으로 바꿉니다](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="65eaa-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="65eaa-133">작성자: [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="65eaa-133">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="65eaa-134">ASP.NET Core MVC 컨트롤러는 생성자를 통해 명시적으로 종속성을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-134">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="65eaa-135">ASP.NET Core는 기본적으로 [DI(종속성 주입 )](xref:fundamentals/dependency-injection)를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-135">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="65eaa-136">DI를 사용하면 앱의 테스트와 유지 관리가 쉬워집니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-136">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="65eaa-137">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="65eaa-137">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="65eaa-138">생성자 주입</span><span class="sxs-lookup"><span data-stu-id="65eaa-138">Constructor Injection</span></span>

<span data-ttu-id="65eaa-139">서비스는 생성자 매개 변수로 추가되며, 런타임은 서비스 컨테이너에서 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-139">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="65eaa-140">서비스는 일반적으로 인터페이스를 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-140">Services are typically defined using interfaces.</span></span> <span data-ttu-id="65eaa-141">예를 들어, 현재 시간이 필요한 앱을 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="65eaa-141">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="65eaa-142">다음 인터페이스는 `IDateTime` 서비스를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-142">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="65eaa-143">다음 코드는 `IDateTime` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-143">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="65eaa-144">서비스를 서비스 컨테이너에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-144">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="65eaa-145"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>에 대한 자세한 내용은 [DI 서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65eaa-145">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="65eaa-146">다음 코드는 현재 시간을 기준으로 사용자에게 인사말을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-146">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="65eaa-147">앱을 실행하면 시간을 기준으로 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-147">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="65eaa-148">FromServices를 이용한 작업 주입</span><span class="sxs-lookup"><span data-stu-id="65eaa-148">Action injection with FromServices</span></span>

<span data-ttu-id="65eaa-149"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute>를 사용하면 생성자 주입을 사용하지 않고 작업 메서드에 직접 서비스를 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-149">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="65eaa-150">컨트롤러에서 설정 액세스</span><span class="sxs-lookup"><span data-stu-id="65eaa-150">Access settings from a controller</span></span>

<span data-ttu-id="65eaa-151">컨트롤러 내에서 앱 또는 구성 설정에 액세스하는 것은 일반적인 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-151">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="65eaa-152"><xref:fundamentals/configuration/options>에 설명된 *옵션 패턴*은 설정을 관리하기 위해 선호되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-152">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="65eaa-153">일반적으로 컨트롤러에 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 직접 주입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="65eaa-153">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="65eaa-154">옵션을 나타내는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-154">Create a class that represents the options.</span></span> <span data-ttu-id="65eaa-155">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-155">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="65eaa-156">서비스 컬렉션에 구성 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-156">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="65eaa-157">JSON 형식 파일에서 설정을 읽도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-157">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="65eaa-158">다음 코드는 서비스 컨테이너에서 `IOptions<SampleWebSettings>` 설정을 요청하고 이를 `Index` 메서드에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="65eaa-158">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="65eaa-159">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="65eaa-159">Additional resources</span></span>

* <span data-ttu-id="65eaa-160">컨트롤러에서 종속성을 명시적으로 요청하여 코드를 더 쉽게 테스트할 수 있는 방법을 알아보려면 <xref:mvc/controllers/testing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65eaa-160">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="65eaa-161">[기본 종속성 주입 컨테이너를 타사 구현으로 바꿉니다](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="65eaa-161">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end