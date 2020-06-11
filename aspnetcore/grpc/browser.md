---
title: 브라우저 앱에서 gRPC 사용
author: jamesnk
description: gRPC-Web을 사용하여 브라우저 앱에서 호출할 수 있도록 gRPC 서비스를 ASP.NET Core에서 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: 6f66a94b41e6e13550396e2e19fdf48f9dc63d46
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106600"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="0d4c0-103">브라우저 앱에서 gRPC 사용</span><span class="sxs-lookup"><span data-stu-id="0d4c0-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="0d4c0-104">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="0d4c0-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="0d4c0-105">브라우저 기반 앱에서 HTTP/2 gRPC 서비스를 호출할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-105">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="0d4c0-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md)은 브라우저 JavaScript 및 Blazor 앱에서 gRPC 서비스를 호출할 수 있게 해주는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="0d4c0-107">이 문서에서는 .NET Core에서 gRPC-Web을 사용하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-107">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="0d4c0-108">ASP.NET Core의 gRPC-Web 및 Envoy</span><span class="sxs-lookup"><span data-stu-id="0d4c0-108">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="0d4c0-109">ASP.NET Core 앱에 gRPC-Web을 추가하는 방법은 두 가지입니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-109">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="0d4c0-110">ASP.NET Core에서 gRPC HTTP/2와 함께 gRPC-Web을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-110">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="0d4c0-111">이 옵션은 `Grpc.AspNetCore.Web` 패키지에서 제공하는 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-111">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="0d4c0-112">[Envoy 프록시의](https://www.envoyproxy.io/) gRPC-Web 지원을 사용하여 gRPC-Web을 gRPC HTTP/2로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-112">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="0d4c0-113">그러면 변환된 호출이 ASP.NET Core 앱으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-113">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="0d4c0-114">각 접근 방식에는 장/단점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-114">There are pros and cons to each approach.</span></span> <span data-ttu-id="0d4c0-115">앱 환경에서 Envoy를 프록시로 이미 사용하고 있는 경우 이를 사용하여 gRPC-Web 지원을 제공하는 것이 적합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-115">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="0d4c0-116">ASP.NET Core만 필요한 gRPC-Web을 위한 단순한 솔루션을 원하는 경우 `Grpc.AspNetCore.Web`을 선택하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-116">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="0d4c0-117">ASP.NET Core에서 gRPC-Web 구성</span><span class="sxs-lookup"><span data-stu-id="0d4c0-117">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="0d4c0-118">HTTP/2 gRPC와 함께 gRPC-Web을 지원하도록 ASP.NET Core에서 호스트된 gRPC 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-118">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="0d4c0-119">gRPC-Web을 사용하기 위해 서비스를 변경할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-119">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="0d4c0-120">시작 구성만 수정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-120">The only modification is startup configuration.</span></span>

<span data-ttu-id="0d4c0-121">ASP.NET Core gRPC 서비스에서 gRPC-Web을 사용하도록 설정하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-121">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="0d4c0-122">[Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) 패키지의 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-122">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="0d4c0-123">*Startup.cs*에 `UseGrpcWeb` 및 `EnableGrpcWeb`을 추가하여 gRPC-Web을 사용하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-123">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="0d4c0-124">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="0d4c0-124">The preceding code:</span></span>

* <span data-ttu-id="0d4c0-125">라우팅 이후, 엔드포인트 이전에 gRPC-Web 미들웨어인 `UseGrpcWeb`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-125">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="0d4c0-126">`EnableGrpcWeb`을 사용하여 `endpoints.MapGrpcService<GreeterService>()` 메서드에서 gRPC-Web을 지원하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-126">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="0d4c0-127">또는 모든 서비스가 기본적으로 gRPC-Web을 지원하고 `EnableGrpcWeb`이 필요하지 않도록 gRPC-Web 미들웨어를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-127">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="0d4c0-128">미들웨어를 추가할 때 `new GrpcWebOptions { DefaultEnabled = true }`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-128">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="0d4c0-129">.NET Core 3.x에서 [Http.sys로 호스트](xref:fundamentals/servers/httpsys)될 때 gRPC-Web이 실패하는 알려진 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-129">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="0d4c0-130">gRPC-Web이 Http.sys에서 작동하도록 하는 해결 방법은 [여기](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-130">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="0d4c0-131">gRPC-Web 및 CORS</span><span class="sxs-lookup"><span data-stu-id="0d4c0-131">gRPC-Web and CORS</span></span>

<span data-ttu-id="0d4c0-132">브라우저 보안 때문에 웹 페이지에서 해당 웹 페이지를 제공한 도메인이 아닌 다른 도메인에 요청을 수행할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-132">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="0d4c0-133">이 제한은 브라우저 앱에서 gRPC-Web 호출을 수행하는 데에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-133">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="0d4c0-134">예를 들어 `https://www.contoso.com`에서 제공하는 브라우저 앱은 `https://services.contoso.com`에서 호스트된 gRPC-Web 서비스를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-134">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="0d4c0-135">CORS(원본 간 리소스 공유)를 사용하여 이 제한을 완화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-135">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="0d4c0-136">브라우저 앱에서 원본 간 gRPC-Web 호출을 수행할 수 있게 하려면 [ASP.NET Core에서 CORS](xref:security/cors)를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-136">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="0d4c0-137">기본 제공 CORS 지원을 사용하고, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>를 통해 gRPC 특정 헤더를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-137">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="0d4c0-138">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="0d4c0-138">The preceding code:</span></span>

* <span data-ttu-id="0d4c0-139">`AddCors`를 호출하여 CORS 서비스를 추가하고 gRPC 특정 헤더를 공개하는 CORS 정책을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-139">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="0d4c0-140">`UseCors`를 호출하여 라우팅 이후, 엔드포인트 이전에 CORS 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-140">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="0d4c0-141">`RequiresCors`를 사용하여 `endpoints.MapGrpcService<GreeterService>()` 메서드에서 CORS를 지원하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-141">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="0d4c0-142">브라우저에서 gRPC-Web 호출</span><span class="sxs-lookup"><span data-stu-id="0d4c0-142">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="0d4c0-143">브라우저 앱은 gRPC-Web을 사용하여 gRPC 서비스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-143">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="0d4c0-144">브라우저에서 gRPC-Web을 사용하여 gRPC 서비스를 호출하는 경우 다음과 같은 몇 가지 요구 사항과 제한 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-144">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="0d4c0-145">서버가 gRPC-Web을 지원하도록 구성되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-145">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="0d4c0-146">클라이언트 스트리밍 및 양방향 스트리밍 호출이 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-146">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="0d4c0-147">서버 스트리밍이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-147">Server streaming is supported.</span></span>
* <span data-ttu-id="0d4c0-148">다른 도메인에 있는 gRPC 서비스를 호출하려면 서버에서 [CORS](xref:security/cors)를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-148">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="0d4c0-149">JavaScript gRPC-Web 클라이언트</span><span class="sxs-lookup"><span data-stu-id="0d4c0-149">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="0d4c0-150">JavaScript gRPC-Web 클라이언트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-150">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="0d4c0-151">JavaScript에서 gRPC-Web을 사용하는 방법에 대한 자세한 내용은 [gRPC-Web을 사용하여 JavaScript 클라이언트 코드 작성](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-151">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="0d4c0-152">.NET gRPC 클라이언트를 사용하여 gRPC-Web 구성</span><span class="sxs-lookup"><span data-stu-id="0d4c0-152">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="0d4c0-153">gRPC-Web 호출을 수행하도록 .NET gRPC 클라이언트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-153">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="0d4c0-154">이 기능은 브라우저에서 호스트되고 JavaScript 코드와 동일한 HTTP 제한 사항이 있는 [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) 앱에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-154">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="0d4c0-155">.NET 클라이언트를 사용하여 gRPC-Web을 호출하는 것은 [HTTP/2 gRPC와 동일](xref:grpc/client)합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-155">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="0d4c0-156">채널을 만드는 방법만 수정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-156">The only modification is how the channel is created.</span></span>

<span data-ttu-id="0d4c0-157">gRPC-Web을 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-157">To use gRPC-Web:</span></span>

* <span data-ttu-id="0d4c0-158">[Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) 패키지의 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-158">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="0d4c0-159">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 패키지의 참조가 2.29.0 이상인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-159">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="0d4c0-160">`GrpcWebHandler`를 사용하도록 채널을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-160">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="0d4c0-161">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="0d4c0-161">The preceding code:</span></span>

* <span data-ttu-id="0d4c0-162">gRPC-Web을 사용하도록 채널을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-162">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="0d4c0-163">클라이언트를 만들고 채널을 사용하여 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-163">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="0d4c0-164">`GrpcWebHandler`에는 다음과 같은 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-164">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="0d4c0-165">**InnerHandler**: gRPC HTTP 요청을 수행하는 기본 <xref:System.Net.Http.HttpMessageHandler>입니다(예: `HttpClientHandler`).</span><span class="sxs-lookup"><span data-stu-id="0d4c0-165">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="0d4c0-166">**GrpcWebMode**: gRPC HTTP 요청 `Content-Type`이 `application/grpc-web` 또는 `application/grpc-web-text`인지를 지정하는 열거형 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-166">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="0d4c0-167">`GrpcWebMode.GrpcWeb`은 인코딩 없이 전송되도록 콘텐츠를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-167">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="0d4c0-168">기본값.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-168">Default value.</span></span>
    * <span data-ttu-id="0d4c0-169">`GrpcWebMode.GrpcWebText`는 base64로 인코딩되도록 콘텐츠를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-169">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="0d4c0-170">브라우저에서 서버 스트리밍 호출을 수행하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-170">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="0d4c0-171">**HttpVersion**: 기본 gRPC HTTP 요청에 [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version)을 설정하는 데 사용되는 HTTP 프로토콜 `Version`입니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-171">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="0d4c0-172">gRPC-Web은 특정 버전이 필요하지 않으며, 지정하지 않을 경우 기본값을 재정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-172">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0d4c0-173">생성된 gRPC 클라이언트에는 단항 메서드를 호출하기 위한 동기 및 비동기 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-173">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="0d4c0-174">예를 들어 `SayHello`는 동기이고, `SayHelloAsync`는 비동기입니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-174">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="0d4c0-175">Blazor WebAssembly 앱에서 동기 메서드를 호출하면 앱이 응답하지 않게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-175">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="0d4c0-176">비동기 메서드는 항상 Blazor WebAssembly에서 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0d4c0-176">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d4c0-177">추가 자료</span><span class="sxs-lookup"><span data-stu-id="0d4c0-177">Additional resources</span></span>

* [<span data-ttu-id="0d4c0-178">웹 클라이언트용 gRPC GitHub 프로젝트</span><span class="sxs-lookup"><span data-stu-id="0d4c0-178">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
