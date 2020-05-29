---
<span data-ttu-id="bbff1-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bbff1-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="bbff1-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bbff1-102">'Blazor'</span></span>
- <span data-ttu-id="bbff1-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bbff1-103">'Identity'</span></span>
- <span data-ttu-id="bbff1-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bbff1-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="bbff1-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bbff1-105">'Razor'</span></span>
- <span data-ttu-id="bbff1-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="bbff1-106">'SignalR' uid:</span></span> 

---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="bbff1-107">브라우저 앱에서 gRPC 사용</span><span class="sxs-lookup"><span data-stu-id="bbff1-107">Use gRPC in browser apps</span></span>

<span data-ttu-id="bbff1-108">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="bbff1-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="bbff1-109">**.NET의 gRPC-Web 지원은 실험적입니다.**</span><span class="sxs-lookup"><span data-stu-id="bbff1-109">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="bbff1-110">.NET용 gRPC-Web은 커밋된 제품이 아니라 실험적 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-110">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="bbff1-111">다음 작업을 수행하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-111">We want to:</span></span>
>
> * <span data-ttu-id="bbff1-112">gRPC-Web 구현 방법이 유효한지 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-112">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="bbff1-113">프록시를 통해 gRPC-Web을 설정하는 기존 방법에 비해 이 방법이 .NET 개발자에게 유용한지 아닌지에 대한 피드백을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-113">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="bbff1-114">개발자가 좋아하고 생산성을 높일 수 있는 제품을 빌드할 수 있도록 [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet)에서 피드백을 남겨 주시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-114">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="bbff1-115">브라우저 기반 앱에서 HTTP/2 gRPC 서비스를 호출할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-115">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="bbff1-116">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md)은 브라우저 JavaScript 및 Blazor 앱에서 gRPC 서비스를 호출할 수 있게 해주는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-116">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="bbff1-117">이 문서에서는 .NET Core에서 gRPC-Web을 사용하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-117">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="bbff1-118">ASP.NET Core의 gRPC-Web 및 Envoy</span><span class="sxs-lookup"><span data-stu-id="bbff1-118">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="bbff1-119">ASP.NET Core 앱에 gRPC-Web을 추가하는 방법은 두 가지입니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-119">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="bbff1-120">ASP.NET Core에서 gRPC HTTP/2와 함께 gRPC-Web을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-120">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="bbff1-121">이 옵션은 `Grpc.AspNetCore.Web` 패키지에서 제공하는 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-121">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="bbff1-122">[Envoy 프록시의](https://www.envoyproxy.io/) gRPC-Web 지원을 사용하여 gRPC-Web을 gRPC HTTP/2로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-122">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="bbff1-123">그러면 변환된 호출이 ASP.NET Core 앱으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-123">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="bbff1-124">각 접근 방식에는 장/단점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-124">There are pros and cons to each approach.</span></span> <span data-ttu-id="bbff1-125">앱 환경에서 Envoy를 프록시로 이미 사용하고 있는 경우 이를 사용하여 gRPC-Web 지원을 제공하는 것이 적합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-125">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="bbff1-126">ASP.NET Core만 필요한 gRPC-Web을 위한 단순한 솔루션을 원하는 경우 `Grpc.AspNetCore.Web`을 선택하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-126">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="bbff1-127">ASP.NET Core에서 gRPC-Web 구성</span><span class="sxs-lookup"><span data-stu-id="bbff1-127">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="bbff1-128">HTTP/2 gRPC와 함께 gRPC-Web을 지원하도록 ASP.NET Core에서 호스트된 gRPC 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-128">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="bbff1-129">gRPC-Web을 사용하기 위해 서비스를 변경할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-129">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="bbff1-130">시작 구성만 수정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-130">The only modification is startup configuration.</span></span>

<span data-ttu-id="bbff1-131">ASP.NET Core gRPC 서비스에서 gRPC-Web을 사용하도록 설정하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-131">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="bbff1-132">[Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) 패키지의 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-132">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="bbff1-133">*Startup.cs*에 `UseGrpcWeb` 및 `EnableGrpcWeb`을 추가하여 gRPC-Web을 사용하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-133">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="bbff1-134">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="bbff1-134">The preceding code:</span></span>

* <span data-ttu-id="bbff1-135">라우팅 이후, 엔드포인트 이전에 gRPC-Web 미들웨어인 `UseGrpcWeb`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-135">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="bbff1-136">`EnableGrpcWeb`을 사용하여 `endpoints.MapGrpcService<GreeterService>()` 메서드에서 gRPC-Web을 지원하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-136">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="bbff1-137">또는 모든 서비스가 기본적으로 gRPC-Web을 지원하고 `EnableGrpcWeb`이 필요하지 않도록 gRPC-Web 미들웨어를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-137">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="bbff1-138">미들웨어를 추가할 때 `new GrpcWebOptions { DefaultEnabled = true }`를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-138">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="bbff1-139">.NET Core 3.x에서 [Http.sys로 호스트](xref:fundamentals/servers/httpsys)될 때 gRPC-Web이 실패하는 알려진 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-139">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="bbff1-140">gRPC-Web이 Http.sys에서 작동하도록 하는 해결 방법은 [여기](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-140">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="bbff1-141">gRPC-Web 및 CORS</span><span class="sxs-lookup"><span data-stu-id="bbff1-141">gRPC-Web and CORS</span></span>

<span data-ttu-id="bbff1-142">브라우저 보안 때문에 웹 페이지에서 해당 웹 페이지를 제공한 도메인이 아닌 다른 도메인에 요청을 수행할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-142">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="bbff1-143">이 제한은 브라우저 앱에서 gRPC-Web 호출을 수행하는 데에도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-143">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="bbff1-144">예를 들어 `https://www.contoso.com`에서 제공하는 브라우저 앱은 `https://services.contoso.com`에서 호스트된 gRPC-Web 서비스를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-144">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="bbff1-145">CORS(원본 간 리소스 공유)를 사용하여 이 제한을 완화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-145">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="bbff1-146">브라우저 앱에서 원본 간 gRPC-Web 호출을 수행할 수 있게 하려면 [ASP.NET Core에서 CORS](xref:security/cors)를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-146">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="bbff1-147">기본 제공 CORS 지원을 사용하고, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>를 통해 gRPC 특정 헤더를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-147">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="bbff1-148">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="bbff1-148">The preceding code:</span></span>

* <span data-ttu-id="bbff1-149">`AddCors`를 호출하여 CORS 서비스를 추가하고 gRPC 특정 헤더를 공개하는 CORS 정책을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-149">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="bbff1-150">`UseCors`를 호출하여 라우팅 이후, 엔드포인트 이전에 CORS 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-150">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="bbff1-151">`RequiresCors`를 사용하여 `endpoints.MapGrpcService<GreeterService>()` 메서드에서 CORS를 지원하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-151">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="bbff1-152">브라우저에서 gRPC-Web 호출</span><span class="sxs-lookup"><span data-stu-id="bbff1-152">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="bbff1-153">브라우저 앱은 gRPC-Web을 사용하여 gRPC 서비스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-153">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="bbff1-154">브라우저에서 gRPC-Web을 사용하여 gRPC 서비스를 호출하는 경우 다음과 같은 몇 가지 요구 사항과 제한 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-154">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="bbff1-155">서버가 gRPC-Web을 지원하도록 구성되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-155">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="bbff1-156">클라이언트 스트리밍 및 양방향 스트리밍 호출이 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-156">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="bbff1-157">서버 스트리밍이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-157">Server streaming is supported.</span></span>
* <span data-ttu-id="bbff1-158">다른 도메인에 있는 gRPC 서비스를 호출하려면 서버에서 [CORS](xref:security/cors)를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-158">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="bbff1-159">JavaScript gRPC-Web 클라이언트</span><span class="sxs-lookup"><span data-stu-id="bbff1-159">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="bbff1-160">JavaScript gRPC-Web 클라이언트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-160">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="bbff1-161">JavaScript에서 gRPC-Web을 사용하는 방법에 대한 자세한 내용은 [gRPC-Web을 사용하여 JavaScript 클라이언트 코드 작성](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bbff1-161">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="bbff1-162">.NET gRPC 클라이언트를 사용하여 gRPC-Web 구성</span><span class="sxs-lookup"><span data-stu-id="bbff1-162">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="bbff1-163">gRPC-Web 호출을 수행하도록 .NET gRPC 클라이언트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-163">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="bbff1-164">이 기능은 브라우저에서 호스트되고 JavaScript 코드와 동일한 HTTP 제한 사항이 있는 [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) 앱에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-164">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="bbff1-165">.NET 클라이언트를 사용하여 gRPC-Web을 호출하는 것은 [HTTP/2 gRPC와 동일](xref:grpc/client)합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-165">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="bbff1-166">채널을 만드는 방법만 수정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-166">The only modification is how the channel is created.</span></span>

<span data-ttu-id="bbff1-167">gRPC-Web을 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-167">To use gRPC-Web:</span></span>

* <span data-ttu-id="bbff1-168">[Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) 패키지의 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-168">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="bbff1-169">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 패키지의 참조가 2.29.0 이상인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-169">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="bbff1-170">`GrpcWebHandler`를 사용하도록 채널을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-170">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="bbff1-171">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="bbff1-171">The preceding code:</span></span>

* <span data-ttu-id="bbff1-172">gRPC-Web을 사용하도록 채널을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-172">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="bbff1-173">클라이언트를 만들고 채널을 사용하여 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-173">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="bbff1-174">`GrpcWebHandler`에는 다음과 같은 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-174">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="bbff1-175">**InnerHandler**: gRPC HTTP 요청을 수행하는 기본 <xref:System.Net.Http.HttpMessageHandler>입니다(예: `HttpClientHandler`).</span><span class="sxs-lookup"><span data-stu-id="bbff1-175">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="bbff1-176">**GrpcWebMode**: gRPC HTTP 요청 `Content-Type`이 `application/grpc-web` 또는 `application/grpc-web-text`인지를 지정하는 열거형 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-176">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="bbff1-177">`GrpcWebMode.GrpcWeb`은 인코딩 없이 전송되도록 콘텐츠를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-177">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="bbff1-178">기본값.</span><span class="sxs-lookup"><span data-stu-id="bbff1-178">Default value.</span></span>
    * <span data-ttu-id="bbff1-179">`GrpcWebMode.GrpcWebText`는 base64로 인코딩되도록 콘텐츠를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-179">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="bbff1-180">브라우저에서 서버 스트리밍 호출을 수행하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-180">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="bbff1-181">**HttpVersion**: 기본 gRPC HTTP 요청에 [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version)을 설정하는 데 사용되는 HTTP 프로토콜 `Version`입니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-181">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="bbff1-182">gRPC-Web은 특정 버전이 필요하지 않으며, 지정하지 않을 경우 기본값을 재정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-182">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="bbff1-183">생성된 gRPC 클라이언트에는 단항 메서드를 호출하기 위한 동기 및 비동기 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-183">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="bbff1-184">예를 들어 `SayHello`는 동기이고, `SayHelloAsync`는 비동기입니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-184">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="bbff1-185">Blazor WebAssembly 앱에서 동기 메서드를 호출하면 앱이 응답하지 않게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-185">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="bbff1-186">비동기 메서드는 항상 Blazor WebAssembly에서 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bbff1-186">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bbff1-187">추가 자료</span><span class="sxs-lookup"><span data-stu-id="bbff1-187">Additional resources</span></span>

* [<span data-ttu-id="bbff1-188">웹 클라이언트용 gRPC GitHub 프로젝트</span><span class="sxs-lookup"><span data-stu-id="bbff1-188">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
