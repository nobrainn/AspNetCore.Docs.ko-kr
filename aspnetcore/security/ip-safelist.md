---
title: ASP.NET Core에 대 한 클라이언트 IP safelist
author: damienbod
description: 승인 된 IP 주소 목록에 대해 원격 IP 주소의 유효성을 검사 하는 미들웨어 또는 작업 필터를 작성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/ip-safelist
ms.openlocfilehash: 7923a81e72124cfb0e11e3c1ac327c1e32194b21
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776502"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="964f2-103">ASP.NET Core에 대 한 클라이언트 IP safelist</span><span class="sxs-lookup"><span data-stu-id="964f2-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="964f2-104">[Damien Bowden](https://twitter.com/damien_bod) 및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="964f2-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="964f2-105">이 문서에서는 ASP.NET Core 앱에서 IP 주소 safelist (허용 목록이 라고도 함)를 구현 하는 세 가지 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="964f2-106">함께 제공 되는 샘플 앱은 세 가지 방법을 모두 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="964f2-107">다음을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-107">You can use:</span></span>

* <span data-ttu-id="964f2-108">미들웨어를 통해 모든 요청의 원격 IP 주소를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="964f2-109">MVC 작업 필터를 통해 특정 컨트롤러 또는 작업 메서드에 대 한 요청의 원격 IP 주소를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* Razor<span data-ttu-id="964f2-110">페이지 필터는 페이지에 대 한 Razor 요청의 원격 IP 주소를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-110"> Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="964f2-111">각각의 경우 승인 된 클라이언트 IP 주소를 포함 하는 문자열은 앱 설정에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="964f2-112">미들웨어 또는 필터:</span><span class="sxs-lookup"><span data-stu-id="964f2-112">The middleware or filter:</span></span>

* <span data-ttu-id="964f2-113">문자열을 배열로 구문 분석 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="964f2-114">원격 IP 주소가 배열에 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="964f2-115">배열에 IP 주소가 포함 되어 있으면 액세스가 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="964f2-116">그렇지 않으면 HTTP 403 사용할 수 없음 상태 코드가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="964f2-117">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="964f2-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="964f2-118">IP 주소 safelist</span><span class="sxs-lookup"><span data-stu-id="964f2-118">IP address safelist</span></span>

<span data-ttu-id="964f2-119">샘플 앱에서 IP 주소 safelist는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="964f2-120">Appsettings 파일의 `AdminSafeList` 속성으로 정의 *appsettings.json* 됩니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="964f2-121">[IPv4 (인터넷 프로토콜 버전 4)](https://wikipedia.org/wiki/IPv4) 및 [IPv6 (인터넷 프로토콜 버전 6)](https://wikipedia.org/wiki/IPv6) 주소를 둘 다 포함할 수 있는 세미콜론으로 구분 된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="964f2-122">`127.0.0.1` 앞의 예제에서 및 `192.168.1.5` 의 IPv4 주소와의 `::1` IPv6 루프백 주소 (압축 형식 `0:0:0:0:0:0:0:1`)가 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="964f2-123">미들웨어</span><span class="sxs-lookup"><span data-stu-id="964f2-123">Middleware</span></span>

<span data-ttu-id="964f2-124">메서드 `Startup.Configure` 는 사용자 지정 `AdminSafeListMiddleware` 미들웨어 형식을 응용 프로그램의 요청 파이프라인에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="964f2-125">Safelist는 .NET Core 구성 공급자를 사용 하 여 검색 되 고 생성자 매개 변수로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="964f2-126">미들웨어는 문자열을 배열로 구문 분석 하 고 배열에서 원격 IP 주소를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="964f2-127">원격 IP 주소를 찾을 수 없는 경우 미들웨어는 HTTP 403을 사용할 수 없음으로 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="964f2-128">이 유효성 검사 프로세스는 HTTP GET 요청에 대해 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="964f2-129">작업 필터</span><span class="sxs-lookup"><span data-stu-id="964f2-129">Action filter</span></span>

<span data-ttu-id="964f2-130">특정 MVC 컨트롤러나 작업 메서드에 대 한 safelist 기반 액세스 제어를 원하는 경우 작업 필터를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="964f2-131">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="964f2-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="964f2-132">에서 `Startup.ConfigureServices`MVC 필터 컬렉션에 작업 필터를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="964f2-133">다음 예에서는 `ClientIpCheckActionFilter` 작업 필터를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="964f2-134">Safelist 및 콘솔로 거 인스턴스는 생성자 매개 변수로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="964f2-135">그런 다음 작업 필터를 컨트롤러 또는 작업 메서드에 [[servicefilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) 특성으로 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="964f2-136">샘플 앱에서 작업 필터는 컨트롤러의 `Get` 동작 메서드에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="964f2-137">다음을 전송 하 여 앱을 테스트 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="964f2-137">When you test the app by sending:</span></span>

* <span data-ttu-id="964f2-138">HTTP GET 요청에서 특성은 `[ServiceFilter]` 클라이언트 IP 주소의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="964f2-139">`Get` 작업 메서드에 대 한 액세스가 허용 되는 경우 작업 필터 및 작업 메서드에서 다음 콘솔 출력의 변형이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="964f2-140">GET이 아닌 HTTP 요청 동사로 미들웨어는 `AdminSafeListMiddleware` 클라이언트 IP 주소의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="razor-pages-filter"></a>Razor<span data-ttu-id="964f2-141">페이지 필터</span><span class="sxs-lookup"><span data-stu-id="964f2-141"> Pages filter</span></span>

<span data-ttu-id="964f2-142">Razor 페이지 앱에 대 한 safelist 기반 access control을 사용 하려면 Razor 페이지 필터를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="964f2-143">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="964f2-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="964f2-144">에서 `Startup.ConfigureServices` Razor 페이지 필터를 MVC filters 컬렉션에 추가 하 여 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="964f2-145">다음 예제 `ClientIpCheckPageFilter` Razor 에서는 페이지 필터를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="964f2-146">Safelist 및 콘솔로 거 인스턴스는 생성자 매개 변수로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="964f2-147">샘플 앱의 *인덱스* Razor 페이지가 요청 되 면 페이지 필터는 Razor 클라이언트 IP 주소의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="964f2-148">필터는 다음 콘솔 출력의 변형을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="964f2-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="964f2-149">추가 자료</span><span class="sxs-lookup"><span data-stu-id="964f2-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="964f2-150">작업 필터</span><span class="sxs-lookup"><span data-stu-id="964f2-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
