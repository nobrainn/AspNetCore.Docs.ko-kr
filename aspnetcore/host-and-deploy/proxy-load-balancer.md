---
title: 프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성
author: rick-anderson
description: 중요한 요청 정보를 종종 숨기는 프록시 서버 및 부하 분산 장치 뒤에 호스트되는 앱의 구성에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: e329b8604b820818167a563b3a21f01f2ab2a6ca
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776385"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="b43d3-103">프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성</span><span class="sxs-lookup"><span data-stu-id="b43d3-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="b43d3-104">작성자: [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="b43d3-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b43d3-105">ASP.NET Core의 권장 구성에서 앱은 IIS/ASP.NET Core 모듈, Nginx 또는 Apache를 사용하여 호스트됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="b43d3-106">프록시 서버, 부하 분산 장치 및 기타 네트워크 어플라이언스는 종종 앱에 도달하기 전에 요청에 대한 정보를 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="b43d3-107">HTTPS 요청이 HTTP를 통해 프록시된 경우 원래 스키마(HTTPS)가 손실되므로 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="b43d3-108">앱은 프록시에서 요청을 수신하고 인터넷 또는 회사 네트워크의 실제 소스를 수신하는 것이 아니므로 원래 클라이언트 IP 주소도 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="b43d3-109">이 정보는 요청 처리 시 중요할 수 있습니다(예: 리디렉션, 인증, 링크 생성, 정책 평가 및 클라이언트 지리적 위치).</span><span class="sxs-lookup"><span data-stu-id="b43d3-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="b43d3-110">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="b43d3-110">Forwarded headers</span></span>

<span data-ttu-id="b43d3-111">규칙에 따라 프록시는 HTTP 헤더에서 정보를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="b43d3-112">헤더</span><span class="sxs-lookup"><span data-stu-id="b43d3-112">Header</span></span> | <span data-ttu-id="b43d3-113">설명</span><span class="sxs-lookup"><span data-stu-id="b43d3-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="b43d3-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="b43d3-114">X-Forwarded-For</span></span> | <span data-ttu-id="b43d3-115">요청 및 프록시 체인의 후속 프록시를 시작한 클라이언트에 대한 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="b43d3-116">이 매개 변수에는 IP 주소(및 선택적으로 포트 번호)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="b43d3-117">프록시 서버의 체인에서 첫 번째 매개 변수는 요청이 처음 만들어진 클라이언트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="b43d3-118">그 뒤에 후속 프록시 식별자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="b43d3-119">체인의 마지막 프록시는 매개 변수 목록에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="b43d3-120">마지막 프록시의 IP 주소 및 선택적으로 포트 번호는 전송 계층에서 원격 IP 주소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="b43d3-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="b43d3-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="b43d3-122">원래 체계(HTTP/HTTPS)의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="b43d3-123">요청이 여러 프록시를 트래버스한 경우에는 값이 체계 목록일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="b43d3-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="b43d3-124">X-Forwarded-Host</span></span> | <span data-ttu-id="b43d3-125">호스트 헤더 필드의 원래 값입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-125">The original value of the Host header field.</span></span> <span data-ttu-id="b43d3-126">일반적으로 프록시는 호스트 헤더를 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="b43d3-127">프록시가 호스트 헤더를 유효성 검사하거나 알려진 정상 값으로 제한하지 않는 시스템에 영향을 미치는 권한 상승 취약성에 대한 자세한 내용은 [Microsoft 보안 공지 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="b43d3-128">[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 패키지의 전달된 헤더 미들웨어는 헤더를 읽고 <xref:Microsoft.AspNetCore.Http.HttpContext>의 관련 필드를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="b43d3-129">미들웨어가 다음을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-129">The middleware updates:</span></span>

* <span data-ttu-id="b43d3-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash;`X-Forwarded-For` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="b43d3-131">추가 설정은 미들웨어가 `RemoteIpAddress`를 설정하는 방법에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="b43d3-132">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="b43d3-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash;`X-Forwarded-Proto` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="b43d3-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash;`X-Forwarded-Host` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="b43d3-135">전달된 헤더 미들웨어 [기본 설정](#forwarded-headers-middleware-options)을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="b43d3-136">기본 설정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-136">The default settings are:</span></span>

* <span data-ttu-id="b43d3-137">앱과 요청 소스 사이에는 ‘하나의 프록시’만 있습니다. </span><span class="sxs-lookup"><span data-stu-id="b43d3-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="b43d3-138">알려진 프록시 및 알려진 네트워크의 경우 루프백 주소만 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="b43d3-139">전달된 헤더의 이름은 `X-Forwarded-For` 및 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="b43d3-140">일부 네트워크 어플라이언스는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 추가하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="b43d3-141">프록시된 요청이 앱에 도달할 때 이러한 헤더를 포함하지 않는 경우에는 어플라이언스 제조업체의 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="b43d3-142">어플라이언스가 `X-Forwarded-For` 및 `X-Forwarded-Proto`와는 다른 헤더 이름을 사용하는 경우 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 어플라이언스에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="b43d3-143">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 및 [다른 헤더 이름을 사용하는 프록시 구성](#configuration-for-a-proxy-that-uses-different-header-names)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="b43d3-144">IIS/IIS Express 및 ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="b43d3-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="b43d3-145">앱이 IIS 및 ASP.NET Core 모듈 뒤에서 호스트되는 [Out of Process](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)인 경우 전달된 헤더 미들웨어가 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#out-of-process-hosting-model)에 의해 기본적으로 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="b43d3-146">전달된 헤더 미들웨어는 전달된 헤더 관련 신뢰 문제(예: [IP 스푸핑](https://www.iplocation.net/ip-spoofing))로 인해 ASP.NET Core 모듈에 특정한 제한된 구성을 사용하여 미들웨어 파이프라인에서 첫 번째로 실행될 수 있도록 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="b43d3-147">미들웨어는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 구성되고 단일 localhost 프록시로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="b43d3-148">추가 구성이 필요한 경우 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b43d3-149">기타 프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="b43d3-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b43d3-150">[Out of Process](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)를 호스트할 때 [IIS 통합](xref:host-and-deploy/iis/index#out-of-process-hosting-model) 사용 외에는 전달된 헤더 미들웨어가 기본적으로 사용하도록 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="b43d3-151">앱에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 전달된 헤더를 처리하려면 전달된 헤더 미들웨어를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="b43d3-152">미들웨어를 사용하도록 설정한 후 미들웨어에 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 지정하지 않은 경우 기본 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="b43d3-153"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 사용하여 `X-Forwarded-For`의 `X-Forwarded-Proto` 및 `Startup.ConfigureServices` 헤더를 전달하도록 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b43d3-154">다른 미들웨어를 호출하기 전에 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>에서 `Startup.Configure` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="b43d3-155"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>에서 `Startup.ConfigureServices`를 지정하지 않거나 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 확장 메서드에 직접 지정하지 않은 경우 전달할 기본 헤더는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="b43d3-156">전달할 헤더를 사용하여 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="b43d3-157">Nginx 구성</span><span class="sxs-lookup"><span data-stu-id="b43d3-157">Nginx configuration</span></span>

<span data-ttu-id="b43d3-158">`X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하려면 <xref:host-and-deploy/linux-nginx#configure-nginx>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="b43d3-159">자세한 내용은 [NGINX: 전달된 헤더 사용](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="b43d3-160">Apache 구성</span><span class="sxs-lookup"><span data-stu-id="b43d3-160">Apache configuration</span></span>

<span data-ttu-id="b43d3-161">`X-Forwarded-For`가 자동으로 추가됩니다( [Apache 모듈 mod_proxy: 역방향 프록시 요청 헤더](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) 참조).</span><span class="sxs-lookup"><span data-stu-id="b43d3-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="b43d3-162">`X-Forwarded-Proto` 헤더를 전달하는 방법에 대한 내용은 <xref:host-and-deploy/linux-apache#configure-apache>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="b43d3-163">전달된 헤더 미들웨어 옵션</span><span class="sxs-lookup"><span data-stu-id="b43d3-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="b43d3-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>는 전달된 헤더 미들웨어의 동작을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="b43d3-165">다음 예제에서는 기본값을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-165">The following example changes the default values:</span></span>

* <span data-ttu-id="b43d3-166">전달된 헤더의 항목 수를 `2`로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="b43d3-167">`127.0.10.1`의 알려진 프록시 주소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="b43d3-168">전달된 헤더 이름을 기본값 `X-Forwarded-For`에서 `X-Forwarded-For-My-Custom-Header-Name`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="b43d3-169">옵션</span><span class="sxs-lookup"><span data-stu-id="b43d3-169">Option</span></span> | <span data-ttu-id="b43d3-170">설명</span><span class="sxs-lookup"><span data-stu-id="b43d3-170">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="b43d3-171">`X-Forwarded-Host` 헤더에 의한 호스트를 제공된 값으로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-171">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="b43d3-172">값은 ordinal-ignore-case를 사용하여 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-172">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="b43d3-173">포트 번호는 제외해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-173">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="b43d3-174">목록이 비어 있으면 모든 호스트가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-174">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="b43d3-175">최상위 수준 와일드카드 `*`는 비어 있지 않은 모든 호스트를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-175">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="b43d3-176">하위 도메인 와일드카드는 허용되지만 루트 도메인과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-176">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="b43d3-177">예를 들어 `*.contoso.com`은 하위 도메인 `foo.contoso.com`과 일치하지만 루트 도메인 `contoso.com`과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-177">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="b43d3-178">유니코드 호스트 이름은 허용되지만 일치시킬 [Punycode](https://tools.ietf.org/html/rfc3492)로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-178">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="b43d3-179">[IPv6 주소](https://tools.ietf.org/html/rfc4291)는 경계 대괄호를 포함하고 [기존 형식](https://tools.ietf.org/html/rfc4291#section-2.2)이어야 합니다(예: `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="b43d3-179">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="b43d3-180">IPv6 주소는 서로 다른 형식 간에 논리적 같음을 확인하기 위해 특별히 처리되지 않으며 정규화가 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-180">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="b43d3-181">허용된 호스트를 제한하지 못하면 공격자가 서비스에서 생성된 링크를 스푸핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-181">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="b43d3-182">기본값은 빈 `IList<string>`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-182">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="b43d3-183">[ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-183">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="b43d3-184">이 옵션은 프록시/전달자가 `X-Forwarded-For` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-184">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="b43d3-185">기본값은 `X-Forwarded-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-185">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="b43d3-186">처리해야 할 전달자를 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-186">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="b43d3-187">적용되는 필드 목록은 [ForwardedHeaders 열거형](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-187">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="b43d3-188">이 속성에 할당된 일반적인 값은 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-188">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="b43d3-189">기본값은 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-189">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="b43d3-190">[ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-190">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="b43d3-191">이 옵션은 프록시/전달자가 `X-Forwarded-Host` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-191">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="b43d3-192">기본값은 `X-Forwarded-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-192">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="b43d3-193">[ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-193">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="b43d3-194">이 옵션은 프록시/전달자가 `X-Forwarded-Proto` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-194">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="b43d3-195">기본값은 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-195">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="b43d3-196">처리되는 헤더의 항목 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-196">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="b43d3-197">제한을 사용하지 않도록 `null`로 설정하지만, `KnownProxies` 또는 `KnownNetworks`가 구성된 경우에만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-197">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="b43d3-198">비-`null` 값을 설정하면 잘못 구성된 프록시 및 네트워크에서 측면 채널에서 오는 악성 요청을 예방하지만 보증하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-198">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="b43d3-199">전달된 헤더 미들웨어는 헤더를 역순으로 오른쪽에서 왼쪽으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-199">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="b43d3-200">기본값(`1`)만 사용된다면 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-200">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="b43d3-201">기본값은 `1`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-201">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="b43d3-202">전달된 헤더를 허용하기 위한 알려진 네트워크의 주소 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-202">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="b43d3-203">CIDR(Classless Interdomain Routing) 표기법을 사용하여 IP 범위를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-203">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="b43d3-204">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-204">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="b43d3-205">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="b43d3-206">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-206">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="b43d3-207">자세한 내용은 [IPv6 주소로 표시되는 IPv4 주소 구성](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-207">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="b43d3-208">기본값은 `IList`에 대한 단일 항목을 포함하는 \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>`IPAddress.Loopback`>입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-208">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="b43d3-209">전달된 헤더를 허용하기 위한 알려진 프록시의 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-209">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="b43d3-210">`KnownProxies`를 사용하여 정확한 IP 주소 일치 항목을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-210">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="b43d3-211">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-211">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="b43d3-212">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="b43d3-213">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-213">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="b43d3-214">자세한 내용은 [IPv6 주소로 표시되는 IPv4 주소 구성](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-214">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="b43d3-215">기본값은 `IList`에 대한 단일 항목을 포함하는 \<<xref:System.Net.IPAddress>`IPAddress.IPv6Loopback`>입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-215">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="b43d3-216">[ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-216">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="b43d3-217">기본값은 `X-Original-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-217">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="b43d3-218">[ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="b43d3-219">기본값은 `X-Original-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-219">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="b43d3-220">[ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="b43d3-221">기본값은 `X-Original-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-221">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="b43d3-222">처리 중인 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 간에 동기화할 헤더 값 수가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-222">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="b43d3-223">ASP.NET Core 1.x의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-223">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="b43d3-224">ASP.NET Core 2.0 이상의 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-224">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="b43d3-225">시나리오 및 사용 사례</span><span class="sxs-lookup"><span data-stu-id="b43d3-225">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="b43d3-226">전달된 헤더를 추가할 수 없고 모든 요청이 안전한 경우</span><span class="sxs-lookup"><span data-stu-id="b43d3-226">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="b43d3-227">일부 경우에는 앱으로 프록시된 요청에 전달된 헤더를 추가할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-227">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="b43d3-228">모든 공용 외부 요청이 HTTPS가 되도록 프록시가 적용되는 경우 미들웨어를 사용하기 전에 `Startup.Configure`에서 체계를 수동으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-228">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="b43d3-229">이 코드는 개발 또는 스테이징 환경에서 환경 변수 또는 기타 구성 설정을 통해 사용하지 않도록 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-229">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="b43d3-230">요청 경로를 변경하는 프록시 및 경로 기준을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-230">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="b43d3-231">일부 프록시는 경로를 그대로 전달하지만 라우팅이 제대로 작동하도록 제거해야 하는 앱 기본 경로를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-231">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="b43d3-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 미들웨어는 경로를 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)로 분할하고 앱 기본 경로를 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="b43d3-233">`/foo`가 `/foo/api/1`로 전달되는 프록시 경로의 앱 기본 경로인 경우 미들웨어는 다음 명령을 사용하여 `Request.PathBase`를 `/foo`로 설정하고 `Request.Path`를 `/api/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-233">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="b43d3-234">미들웨어가 역방향으로 다시 호출되면 원래 경로 및 경로 기준이 다시 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-234">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="b43d3-235">미들웨어 순서 처리에 대한 자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-235">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="b43d3-236">프록시가 경로를 자르는 경우(예: `/foo/api/1`을 `/api/1`에 전달) 요청의 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 속성을 설정하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-236">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="b43d3-237">프록시가 경로 데이터를 추가하는 경우 경로의 일부를 삭제한 다음, <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*>를 사용하고 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 속성에 할당하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-237">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="b43d3-238">다른 헤더 이름을 사용하는 프록시에 대한 구성</span><span class="sxs-lookup"><span data-stu-id="b43d3-238">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="b43d3-239">프록시가 `X-Forwarded-For` 및 `X-Forwarded-Proto`라는 헤더를 사용하여 프록시 주소/포트 및 원래 체계 정보를 전달하지 않는 경우, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 프록시에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-239">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="b43d3-240">IPv6 주소로 표시되는 IPv4 주소 구성</span><span class="sxs-lookup"><span data-stu-id="b43d3-240">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="b43d3-241">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1` 또는 `::ffff:a00:1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-241">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="b43d3-242">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="b43d3-243">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-243">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="b43d3-244">다음 예제에서는 전달된 헤더를 제공하는 네트워크 주소가 IPv6 형식의 `KnownNetworks` 목록에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-244">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="b43d3-245">IPv4 주소: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="b43d3-245">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="b43d3-246">변환된 IPv6 주소: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="b43d3-246">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="b43d3-247">변환된 접두사 길이: 104</span><span class="sxs-lookup"><span data-stu-id="b43d3-247">Converted prefix length: 104</span></span>

<span data-ttu-id="b43d3-248">16진수 형식으로 주소를 제공할 수도 있습니다(IPv6에서 표시된 `10.11.12.1`을 `::ffff:0a0b:0c01`로 표시).</span><span class="sxs-lookup"><span data-stu-id="b43d3-248">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="b43d3-249">IPv4 주소를 IPv6로 변환할 때 CIDR 접두사 길이(예제에서는 `8`)에 96을 추가하여 추가 `::ffff:` IPv6 접두사를 처리합니다(8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="b43d3-249">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="b43d3-250">Linux용 스키마 및 비 IIS 역방향 프록시 전달</span><span class="sxs-lookup"><span data-stu-id="b43d3-250">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="b43d3-251"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 및 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>을(를) 호출하는 앱은 Azure Linux App Service, Azure Linux VM(가상 머신) 또는 IIS 이외의 다른 역방향 프록시 뒤에 배포된 경우 사이트를 무한 루프에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-251">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="b43d3-252">TLS는 역방향 프록시에 의해 종료되며, Kestrel은 올바른 요청 체계를 인식하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="b43d3-253">OAuth 및 OIDC도 잘못된 리디렉션을 생성하므로 이 구성에서 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="b43d3-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>은 IIS 뒤에서 실행되는 경우 전달된 헤더 미들웨어를 추가하고 구성하지만 Linux에 대한 일치하는 자동 구성이 없습니다(Apache 또는 Nginx 통합).</span><span class="sxs-lookup"><span data-stu-id="b43d3-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="b43d3-255">비 IIS 시나리오에서 프록시의 스키마를 전달하려면 전달된 헤더 미들웨어를 추가하고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="b43d3-256">`Startup.ConfigureServices`에서 다음 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-256">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="b43d3-257">인증서 전달</span><span class="sxs-lookup"><span data-stu-id="b43d3-257">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="b43d3-258">Azure</span><span class="sxs-lookup"><span data-stu-id="b43d3-258">Azure</span></span>

<span data-ttu-id="b43d3-259">인증서 전달에 대한 Azure App Service를 구성하려면 [Azure App Service에 대한 TLS 상호 인증 구성](/azure/app-service/app-service-web-configure-tls-mutual-auth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-259">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="b43d3-260">다음 지침은 ASP.NET Core 앱 구성과 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-260">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="b43d3-261">`Startup.Configure`에서 `app.UseAuthentication();` 호출 앞에 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-261">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="b43d3-262">인증서 전달 미들웨어를 구성하여 Azure를 사용하는 헤더 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-262">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="b43d3-263">`Startup.ConfigureServices`에서 다음 코드를 추가하여 미들웨어가 인증서를 빌드하는 헤더를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-263">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="b43d3-264">다른 웹 프록시</span><span class="sxs-lookup"><span data-stu-id="b43d3-264">Other web proxies</span></span>

<span data-ttu-id="b43d3-265">IIS 또는 Azure App Service의 ARR(애플리케이션 요청 라우팅)이 아닌 프록시를 사용하는 경우 HTTP 헤더에서 받은 인증서를 전달하도록 프록시를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-265">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="b43d3-266">`Startup.Configure`에서 `app.UseAuthentication();` 호출 앞에 다음 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-266">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="b43d3-267">인증서 전달 미들웨어를 구성하여 헤더 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-267">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="b43d3-268">`Startup.ConfigureServices`에서 다음 코드를 추가하여 미들웨어가 인증서를 빌드하는 헤더를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-268">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="b43d3-269">프록시가 Nginx에서와 같이 인증서를 base64 인코딩하지 않는 경우 `HeaderConverter` 옵션을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-269">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="b43d3-270">`Startup.ConfigureServices`에서 다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-270">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="b43d3-271">문제 해결</span><span class="sxs-lookup"><span data-stu-id="b43d3-271">Troubleshoot</span></span>

<span data-ttu-id="b43d3-272">헤더가 예상대로 전달되지 않으면 [로깅](xref:fundamentals/logging/index)을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-272">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b43d3-273">로그가 문제를 해결하기에 충분한 정보를 제공하지 않으면 서버가 수신하는 요청 헤더를 열거합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-273">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="b43d3-274">인라인 미들웨어를 사용하여 앱 응답에 요청 헤더를 쓰거나 헤더를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-274">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="b43d3-275">앱 응답에 헤더를 기록하려면 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>에서 `Startup.Configure` 호출 바로 뒤에 다음 터미널 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-275">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="b43d3-276">응답 본문 대신 로그에 기록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-276">You can write to logs instead of the response body.</span></span> <span data-ttu-id="b43d3-277">로그에 기록하면 디버깅 중 사이트가 정상적으로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-277">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="b43d3-278">응답 본문이 아닌 로그를 기록하려면:</span><span class="sxs-lookup"><span data-stu-id="b43d3-278">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="b43d3-279">`ILogger<Startup>`시작 시 로그 만들기`Startup`에 설명된 대로 [을 ](xref:fundamentals/logging/index#create-logs-in-startup) 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-279">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="b43d3-280"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>에서 `Startup.Configure` 호출 바로 뒤에 다음 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-280">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="b43d3-281">처리 시 `X-Forwarded-{For|Proto|Host}` 값이 `X-Original-{For|Proto|Host}`로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-281">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="b43d3-282">제공된 헤더에 여러 값이 있는 경우 전달된 헤더 미들웨어는 오른쪽에서 왼쪽으로 역순으로 헤더를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-282">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="b43d3-283">기본 `ForwardLimit`는 `1`(일)이므로 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-283">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="b43d3-284">요청의 원래 원격 IP는 전달된 헤더가 처리되기 전에 `KnownProxies` 또는 `KnownNetworks` 목록의 항목과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-284">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="b43d3-285">이렇게 하면 신뢰할 수 없는 프록시에서 전달자가 허용되지 않아 헤더 스푸핑이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-285">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="b43d3-286">알 수 없는 프록시가 검색되면 로그에 프록시 주소가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-286">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="b43d3-287">앞의 예제에서 10.0.0.100은 프록시 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-287">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="b43d3-288">서버가 신뢰할 수 있는 프록시인 경우 서버의 IP 주소를 `KnownProxies`의 `KnownNetworks`에 추가합니다(또는 신뢰할 수 있는 네트워크를 `Startup.ConfigureServices`에 추가).</span><span class="sxs-lookup"><span data-stu-id="b43d3-288">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b43d3-289">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-289">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="b43d3-290">신뢰할 수 있는 프록시 및 네트워크만 헤더를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-290">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="b43d3-291">그렇지 않을 경우 [IP 스푸핑](https://www.iplocation.net/ip-spoofing) 공격이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-291">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b43d3-292">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b43d3-292">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="b43d3-293">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)(Microsoft 보안 공지 CVE-2018-0787: ASP.NET Core 권한 상승 취약성)</span><span class="sxs-lookup"><span data-stu-id="b43d3-293">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b43d3-294">ASP.NET Core의 권장 구성에서 앱은 IIS/ASP.NET Core 모듈, Nginx 또는 Apache를 사용하여 호스트됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-294">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="b43d3-295">프록시 서버, 부하 분산 장치 및 기타 네트워크 어플라이언스는 종종 앱에 도달하기 전에 요청에 대한 정보를 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-295">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="b43d3-296">HTTPS 요청이 HTTP를 통해 프록시된 경우 원래 스키마(HTTPS)가 손실되므로 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-296">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="b43d3-297">앱은 프록시에서 요청을 수신하고 인터넷 또는 회사 네트워크의 실제 소스를 수신하는 것이 아니므로 원래 클라이언트 IP 주소도 헤더에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-297">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="b43d3-298">이 정보는 요청 처리 시 중요할 수 있습니다(예: 리디렉션, 인증, 링크 생성, 정책 평가 및 클라이언트 지리적 위치).</span><span class="sxs-lookup"><span data-stu-id="b43d3-298">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="b43d3-299">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="b43d3-299">Forwarded headers</span></span>

<span data-ttu-id="b43d3-300">규칙에 따라 프록시는 HTTP 헤더에서 정보를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-300">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="b43d3-301">헤더</span><span class="sxs-lookup"><span data-stu-id="b43d3-301">Header</span></span> | <span data-ttu-id="b43d3-302">설명</span><span class="sxs-lookup"><span data-stu-id="b43d3-302">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="b43d3-303">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="b43d3-303">X-Forwarded-For</span></span> | <span data-ttu-id="b43d3-304">요청 및 프록시 체인의 후속 프록시를 시작한 클라이언트에 대한 정보를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-304">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="b43d3-305">이 매개 변수에는 IP 주소(및 선택적으로 포트 번호)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-305">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="b43d3-306">프록시 서버의 체인에서 첫 번째 매개 변수는 요청이 처음 만들어진 클라이언트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-306">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="b43d3-307">그 뒤에 후속 프록시 식별자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-307">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="b43d3-308">체인의 마지막 프록시는 매개 변수 목록에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-308">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="b43d3-309">마지막 프록시의 IP 주소 및 선택적으로 포트 번호는 전송 계층에서 원격 IP 주소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-309">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="b43d3-310">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="b43d3-310">X-Forwarded-Proto</span></span> | <span data-ttu-id="b43d3-311">원래 체계(HTTP/HTTPS)의 값입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-311">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="b43d3-312">요청이 여러 프록시를 트래버스한 경우에는 값이 체계 목록일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-312">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="b43d3-313">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="b43d3-313">X-Forwarded-Host</span></span> | <span data-ttu-id="b43d3-314">호스트 헤더 필드의 원래 값입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-314">The original value of the Host header field.</span></span> <span data-ttu-id="b43d3-315">일반적으로 프록시는 호스트 헤더를 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-315">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="b43d3-316">프록시가 호스트 헤더를 유효성 검사하거나 알려진 정상 값으로 제한하지 않는 시스템에 영향을 미치는 권한 상승 취약성에 대한 자세한 내용은 [Microsoft 보안 공지 CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-316">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="b43d3-317">[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) 패키지의 전달된 헤더 미들웨어는 헤더를 읽고 <xref:Microsoft.AspNetCore.Http.HttpContext>의 관련 필드를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-317">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="b43d3-318">미들웨어가 다음을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-318">The middleware updates:</span></span>

* <span data-ttu-id="b43d3-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash;`X-Forwarded-For` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="b43d3-320">추가 설정은 미들웨어가 `RemoteIpAddress`를 설정하는 방법에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-320">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="b43d3-321">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-321">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="b43d3-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash;`X-Forwarded-Proto` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="b43d3-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash;`X-Forwarded-Host` 헤더 값을 사용하여 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="b43d3-324">전달된 헤더 미들웨어 [기본 설정](#forwarded-headers-middleware-options)을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-324">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="b43d3-325">기본 설정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-325">The default settings are:</span></span>

* <span data-ttu-id="b43d3-326">앱과 요청 소스 사이에는 ‘하나의 프록시’만 있습니다. </span><span class="sxs-lookup"><span data-stu-id="b43d3-326">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="b43d3-327">알려진 프록시 및 알려진 네트워크의 경우 루프백 주소만 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-327">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="b43d3-328">전달된 헤더의 이름은 `X-Forwarded-For` 및 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-328">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="b43d3-329">일부 네트워크 어플라이언스는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 추가하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-329">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="b43d3-330">프록시된 요청이 앱에 도달할 때 이러한 헤더를 포함하지 않는 경우에는 어플라이언스 제조업체의 지침을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-330">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="b43d3-331">어플라이언스가 `X-Forwarded-For` 및 `X-Forwarded-Proto`와는 다른 헤더 이름을 사용하는 경우 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 어플라이언스에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-331">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="b43d3-332">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 및 [다른 헤더 이름을 사용하는 프록시 구성](#configuration-for-a-proxy-that-uses-different-header-names)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-332">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="b43d3-333">IIS/IIS Express 및 ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="b43d3-333">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="b43d3-334">앱이 IIS 및 ASP.NET Core 모듈 뒤에서 호스트되는 [Out of Process](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)인 경우 전달된 헤더 미들웨어가 [IIS 통합 미들웨어](xref:host-and-deploy/iis/index#out-of-process-hosting-model)에 의해 기본적으로 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-334">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="b43d3-335">전달된 헤더 미들웨어는 전달된 헤더 관련 신뢰 문제(예: [IP 스푸핑](https://www.iplocation.net/ip-spoofing))로 인해 ASP.NET Core 모듈에 특정한 제한된 구성을 사용하여 미들웨어 파이프라인에서 첫 번째로 실행될 수 있도록 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-335">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="b43d3-336">미들웨어는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 구성되고 단일 localhost 프록시로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-336">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="b43d3-337">추가 구성이 필요한 경우 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-337">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b43d3-338">기타 프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="b43d3-338">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b43d3-339">[Out of Process](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)를 호스트할 때 [IIS 통합](xref:host-and-deploy/iis/index#out-of-process-hosting-model) 사용 외에는 전달된 헤더 미들웨어가 기본적으로 사용하도록 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-339">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="b43d3-340">앱에서 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 전달된 헤더를 처리하려면 전달된 헤더 미들웨어를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-340">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="b43d3-341">미들웨어를 사용하도록 설정한 후 미들웨어에 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 지정하지 않은 경우 기본 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-341">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="b43d3-342"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>를 사용하여 `X-Forwarded-For`의 `X-Forwarded-Proto` 및 `Startup.ConfigureServices` 헤더를 전달하도록 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-342">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b43d3-343">다른 미들웨어를 호출하기 전에 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>에서 `Startup.Configure` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-343">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="b43d3-344"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>에서 `Startup.ConfigureServices`를 지정하지 않거나 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>를 사용하여 확장 메서드에 직접 지정하지 않은 경우 전달할 기본 헤더는 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-344">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="b43d3-345">전달할 헤더를 사용하여 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> 속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-345">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="b43d3-346">Nginx 구성</span><span class="sxs-lookup"><span data-stu-id="b43d3-346">Nginx configuration</span></span>

<span data-ttu-id="b43d3-347">`X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하려면 <xref:host-and-deploy/linux-nginx#configure-nginx>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-347">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="b43d3-348">자세한 내용은 [NGINX: 전달된 헤더 사용](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-348">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="b43d3-349">Apache 구성</span><span class="sxs-lookup"><span data-stu-id="b43d3-349">Apache configuration</span></span>

<span data-ttu-id="b43d3-350">`X-Forwarded-For`가 자동으로 추가됩니다( [Apache 모듈 mod_proxy: 역방향 프록시 요청 헤더](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers) 참조).</span><span class="sxs-lookup"><span data-stu-id="b43d3-350">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="b43d3-351">`X-Forwarded-Proto` 헤더를 전달하는 방법에 대한 내용은 <xref:host-and-deploy/linux-apache#configure-apache>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-351">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="b43d3-352">전달된 헤더 미들웨어 옵션</span><span class="sxs-lookup"><span data-stu-id="b43d3-352">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="b43d3-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>는 전달된 헤더 미들웨어의 동작을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="b43d3-354">다음 예제에서는 기본값을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-354">The following example changes the default values:</span></span>

* <span data-ttu-id="b43d3-355">전달된 헤더의 항목 수를 `2`로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-355">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="b43d3-356">`127.0.10.1`의 알려진 프록시 주소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-356">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="b43d3-357">전달된 헤더 이름을 기본값 `X-Forwarded-For`에서 `X-Forwarded-For-My-Custom-Header-Name`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-357">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="b43d3-358">옵션</span><span class="sxs-lookup"><span data-stu-id="b43d3-358">Option</span></span> | <span data-ttu-id="b43d3-359">설명</span><span class="sxs-lookup"><span data-stu-id="b43d3-359">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="b43d3-360">`X-Forwarded-Host` 헤더에 의한 호스트를 제공된 값으로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-360">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="b43d3-361">값은 ordinal-ignore-case를 사용하여 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-361">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="b43d3-362">포트 번호는 제외해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-362">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="b43d3-363">목록이 비어 있으면 모든 호스트가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-363">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="b43d3-364">최상위 수준 와일드카드 `*`는 비어 있지 않은 모든 호스트를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-364">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="b43d3-365">하위 도메인 와일드카드는 허용되지만 루트 도메인과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-365">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="b43d3-366">예를 들어 `*.contoso.com`은 하위 도메인 `foo.contoso.com`과 일치하지만 루트 도메인 `contoso.com`과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-366">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="b43d3-367">유니코드 호스트 이름은 허용되지만 일치시킬 [Punycode](https://tools.ietf.org/html/rfc3492)로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-367">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="b43d3-368">[IPv6 주소](https://tools.ietf.org/html/rfc4291)는 경계 대괄호를 포함하고 [기존 형식](https://tools.ietf.org/html/rfc4291#section-2.2)이어야 합니다(예: `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="b43d3-368">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="b43d3-369">IPv6 주소는 서로 다른 형식 간에 논리적 같음을 확인하기 위해 특별히 처리되지 않으며 정규화가 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-369">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="b43d3-370">허용된 호스트를 제한하지 못하면 공격자가 서비스에서 생성된 링크를 스푸핑할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-370">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="b43d3-371">기본값은 빈 `IList<string>`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-371">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="b43d3-372">[ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="b43d3-373">이 옵션은 프록시/전달자가 `X-Forwarded-For` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="b43d3-374">기본값은 `X-Forwarded-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-374">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="b43d3-375">처리해야 할 전달자를 알려줍니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-375">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="b43d3-376">적용되는 필드 목록은 [ForwardedHeaders 열거형](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-376">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="b43d3-377">이 속성에 할당된 일반적인 값은 `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-377">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="b43d3-378">기본값은 [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-378">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="b43d3-379">[ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-379">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="b43d3-380">이 옵션은 프록시/전달자가 `X-Forwarded-Host` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-380">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="b43d3-381">기본값은 `X-Forwarded-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-381">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="b43d3-382">[ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-382">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="b43d3-383">이 옵션은 프록시/전달자가 `X-Forwarded-Proto` 헤더를 사용하지 않고 일부 다른 헤더를 사용하여 정보를 전달할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-383">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="b43d3-384">기본값은 `X-Forwarded-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-384">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="b43d3-385">처리되는 헤더의 항목 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-385">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="b43d3-386">제한을 사용하지 않도록 `null`로 설정하지만, `KnownProxies` 또는 `KnownNetworks`가 구성된 경우에만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-386">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="b43d3-387">비-`null` 값을 설정하면 잘못 구성된 프록시 및 네트워크에서 측면 채널에서 오는 악성 요청을 예방하지만 보증하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-387">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="b43d3-388">전달된 헤더 미들웨어는 헤더를 역순으로 오른쪽에서 왼쪽으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-388">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="b43d3-389">기본값(`1`)만 사용된다면 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-389">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="b43d3-390">기본값은 `1`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-390">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="b43d3-391">전달된 헤더를 허용하기 위한 알려진 네트워크의 주소 범위입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-391">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="b43d3-392">CIDR(Classless Interdomain Routing) 표기법을 사용하여 IP 범위를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-392">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="b43d3-393">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="b43d3-394">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="b43d3-395">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="b43d3-396">자세한 내용은 [IPv6 주소로 표시되는 IPv4 주소 구성](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="b43d3-397">기본값은 `IList`에 대한 단일 항목을 포함하는 \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>`IPAddress.Loopback`>입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-397">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="b43d3-398">전달된 헤더를 허용하기 위한 알려진 프록시의 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-398">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="b43d3-399">`KnownProxies`를 사용하여 정확한 IP 주소 일치 항목을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-399">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="b43d3-400">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-400">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="b43d3-401">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-401">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="b43d3-402">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-402">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="b43d3-403">자세한 내용은 [IPv6 주소로 표시되는 IPv4 주소 구성](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-403">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="b43d3-404">기본값은 `IList`에 대한 단일 항목을 포함하는 \<<xref:System.Net.IPAddress>`IPAddress.IPv6Loopback`>입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-404">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="b43d3-405">[ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-405">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="b43d3-406">기본값은 `X-Original-For`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-406">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="b43d3-407">[ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-407">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="b43d3-408">기본값은 `X-Original-Host`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-408">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="b43d3-409">[ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)에서 지정된 헤더 대신 이 속성에서 지정된 헤더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="b43d3-410">기본값은 `X-Original-Proto`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-410">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="b43d3-411">처리 중인 [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) 간에 동기화할 헤더 값 수가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-411">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="b43d3-412">ASP.NET Core 1.x의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-412">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="b43d3-413">ASP.NET Core 2.0 이상의 기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-413">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="b43d3-414">시나리오 및 사용 사례</span><span class="sxs-lookup"><span data-stu-id="b43d3-414">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="b43d3-415">전달된 헤더를 추가할 수 없고 모든 요청이 안전한 경우</span><span class="sxs-lookup"><span data-stu-id="b43d3-415">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="b43d3-416">일부 경우에는 앱으로 프록시된 요청에 전달된 헤더를 추가할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-416">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="b43d3-417">모든 공용 외부 요청이 HTTPS가 되도록 프록시가 적용되는 경우 미들웨어를 사용하기 전에 `Startup.Configure`에서 체계를 수동으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-417">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="b43d3-418">이 코드는 개발 또는 스테이징 환경에서 환경 변수 또는 기타 구성 설정을 통해 사용하지 않도록 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-418">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="b43d3-419">요청 경로를 변경하는 프록시 및 경로 기준을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-419">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="b43d3-420">일부 프록시는 경로를 그대로 전달하지만 라우팅이 제대로 작동하도록 제거해야 하는 앱 기본 경로를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-420">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="b43d3-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) 미들웨어는 경로를 [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path)로 분할하고 앱 기본 경로를 [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="b43d3-422">`/foo`가 `/foo/api/1`로 전달되는 프록시 경로의 앱 기본 경로인 경우 미들웨어는 다음 명령을 사용하여 `Request.PathBase`를 `/foo`로 설정하고 `Request.Path`를 `/api/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-422">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="b43d3-423">미들웨어가 역방향으로 다시 호출되면 원래 경로 및 경로 기준이 다시 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-423">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="b43d3-424">미들웨어 순서 처리에 대한 자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-424">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="b43d3-425">프록시가 경로를 자르는 경우(예: `/foo/api/1`을 `/api/1`에 전달) 요청의 [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 속성을 설정하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-425">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="b43d3-426">프록시가 경로 데이터를 추가하는 경우 경로의 일부를 삭제한 다음, <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*>를 사용하고 <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> 속성에 할당하여 리디렉션 및 링크를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-426">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="b43d3-427">다른 헤더 이름을 사용하는 프록시에 대한 구성</span><span class="sxs-lookup"><span data-stu-id="b43d3-427">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="b43d3-428">프록시가 `X-Forwarded-For` 및 `X-Forwarded-Proto`라는 헤더를 사용하여 프록시 주소/포트 및 원래 체계 정보를 전달하지 않는 경우, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> 및 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> 옵션을 프록시에서 사용하는 헤더 이름과 일치하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-428">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="b43d3-429">IPv6 주소로 표시되는 IPv4 주소 구성</span><span class="sxs-lookup"><span data-stu-id="b43d3-429">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="b43d3-430">서버에서 이중 모드 소켓을 사용하는 경우 IPv4 주소가 IPv6 형식(예: IPv4에서 `10.0.0.1`은 IPv6에서 `::ffff:10.0.0.1` 또는 `::ffff:a00:1`로 표시됨)으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-430">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="b43d3-431">[IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-431">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="b43d3-432">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)를 참조하여 이 형식이 필요한지 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-432">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="b43d3-433">다음 예제에서는 전달된 헤더를 제공하는 네트워크 주소가 IPv6 형식의 `KnownNetworks` 목록에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-433">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="b43d3-434">IPv4 주소: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="b43d3-434">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="b43d3-435">변환된 IPv6 주소: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="b43d3-435">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="b43d3-436">변환된 접두사 길이: 104</span><span class="sxs-lookup"><span data-stu-id="b43d3-436">Converted prefix length: 104</span></span>

<span data-ttu-id="b43d3-437">16진수 형식으로 주소를 제공할 수도 있습니다(IPv6에서 표시된 `10.11.12.1`을 `::ffff:0a0b:0c01`로 표시).</span><span class="sxs-lookup"><span data-stu-id="b43d3-437">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="b43d3-438">IPv4 주소를 IPv6로 변환할 때 CIDR 접두사 길이(예제에서는 `8`)에 96을 추가하여 추가 `::ffff:` IPv6 접두사를 처리합니다(8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="b43d3-438">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="b43d3-439">Linux용 스키마 및 비 IIS 역방향 프록시 전달</span><span class="sxs-lookup"><span data-stu-id="b43d3-439">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="b43d3-440"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 및 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>을(를) 호출하는 앱은 Azure Linux App Service, Azure Linux VM(가상 머신) 또는 IIS 이외의 다른 역방향 프록시 뒤에 배포된 경우 사이트를 무한 루프에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-440">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="b43d3-441">TLS는 역방향 프록시에 의해 종료되며, Kestrel은 올바른 요청 체계를 인식하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-441">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="b43d3-442">OAuth 및 OIDC도 잘못된 리디렉션을 생성하므로 이 구성에서 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-442">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="b43d3-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>은 IIS 뒤에서 실행되는 경우 전달된 헤더 미들웨어를 추가하고 구성하지만 Linux에 대한 일치하는 자동 구성이 없습니다(Apache 또는 Nginx 통합).</span><span class="sxs-lookup"><span data-stu-id="b43d3-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="b43d3-444">비 IIS 시나리오에서 프록시의 스키마를 전달하려면 전달된 헤더 미들웨어를 추가하고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-444">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="b43d3-445">`Startup.ConfigureServices`에서 다음 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-445">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="b43d3-446">문제 해결</span><span class="sxs-lookup"><span data-stu-id="b43d3-446">Troubleshoot</span></span>

<span data-ttu-id="b43d3-447">헤더가 예상대로 전달되지 않으면 [로깅](xref:fundamentals/logging/index)을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-447">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b43d3-448">로그가 문제를 해결하기에 충분한 정보를 제공하지 않으면 서버가 수신하는 요청 헤더를 열거합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-448">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="b43d3-449">인라인 미들웨어를 사용하여 앱 응답에 요청 헤더를 쓰거나 헤더를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-449">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="b43d3-450">앱 응답에 헤더를 기록하려면 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>에서 `Startup.Configure` 호출 바로 뒤에 다음 터미널 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-450">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="b43d3-451">응답 본문 대신 로그에 기록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-451">You can write to logs instead of the response body.</span></span> <span data-ttu-id="b43d3-452">로그에 기록하면 디버깅 중 사이트가 정상적으로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-452">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="b43d3-453">응답 본문이 아닌 로그를 기록하려면:</span><span class="sxs-lookup"><span data-stu-id="b43d3-453">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="b43d3-454">`ILogger<Startup>`시작 시 로그 만들기`Startup`에 설명된 대로 [을 ](xref:fundamentals/logging/index#create-logs-in-startup) 클래스에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-454">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="b43d3-455"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>에서 `Startup.Configure` 호출 바로 뒤에 다음 인라인 미들웨어를 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-455">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="b43d3-456">처리 시 `X-Forwarded-{For|Proto|Host}` 값이 `X-Original-{For|Proto|Host}`로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-456">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="b43d3-457">제공된 헤더에 여러 값이 있는 경우 전달된 헤더 미들웨어는 오른쪽에서 왼쪽으로 역순으로 헤더를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-457">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="b43d3-458">기본 `ForwardLimit`는 `1`(일)이므로 `ForwardLimit`의 값을 늘리지 않는 한, 헤더에서 맨 오른쪽 값만 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-458">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="b43d3-459">요청의 원래 원격 IP는 전달된 헤더가 처리되기 전에 `KnownProxies` 또는 `KnownNetworks` 목록의 항목과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-459">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="b43d3-460">이렇게 하면 신뢰할 수 없는 프록시에서 전달자가 허용되지 않아 헤더 스푸핑이 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-460">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="b43d3-461">알 수 없는 프록시가 검색되면 로그에 프록시 주소가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-461">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="b43d3-462">앞의 예제에서 10.0.0.100은 프록시 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-462">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="b43d3-463">서버가 신뢰할 수 있는 프록시인 경우 서버의 IP 주소를 `KnownProxies`의 `KnownNetworks`에 추가합니다(또는 신뢰할 수 있는 네트워크를 `Startup.ConfigureServices`에 추가).</span><span class="sxs-lookup"><span data-stu-id="b43d3-463">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b43d3-464">자세한 내용은 [전달된 헤더 미들웨어 옵션](#forwarded-headers-middleware-options) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b43d3-464">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="b43d3-465">신뢰할 수 있는 프록시 및 네트워크만 헤더를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-465">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="b43d3-466">그렇지 않을 경우 [IP 스푸핑](https://www.iplocation.net/ip-spoofing) 공격이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="b43d3-466">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b43d3-467">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b43d3-467">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="b43d3-468">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)(Microsoft 보안 공지 CVE-2018-0787: ASP.NET Core 권한 상승 취약성)</span><span class="sxs-lookup"><span data-stu-id="b43d3-468">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end
