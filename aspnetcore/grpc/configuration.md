---
<span data-ttu-id="3fc7e-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-102">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-103">'Identity'</span></span>
- <span data-ttu-id="3fc7e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-105">'Razor'</span></span>
- <span data-ttu-id="3fc7e-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-106">'SignalR' uid:</span></span> 

---
# <a name="grpc-for-net-configuration"></a><span data-ttu-id="3fc7e-107">.NET용 gRPC 구성</span><span class="sxs-lookup"><span data-stu-id="3fc7e-107">gRPC for .NET configuration</span></span>

## <a name="configure-services-options"></a><span data-ttu-id="3fc7e-108">서비스 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="3fc7e-108">Configure services options</span></span>

<span data-ttu-id="3fc7e-109">gRPC 서비스는 *Startup.cs*에서 `AddGrpc`를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-109">gRPC services are configured with `AddGrpc` in *Startup.cs*.</span></span> <span data-ttu-id="3fc7e-110">다음 표에서는 gRPC 서비스를 구성하기 위한 옵션을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-110">The following table describes options for configuring gRPC services:</span></span>

| <span data-ttu-id="3fc7e-111">옵션</span><span class="sxs-lookup"><span data-stu-id="3fc7e-111">Option</span></span> | <span data-ttu-id="3fc7e-112">기본값</span><span class="sxs-lookup"><span data-stu-id="3fc7e-112">Default Value</span></span> | <span data-ttu-id="3fc7e-113">설명</span><span class="sxs-lookup"><span data-stu-id="3fc7e-113">Description</span></span> |
| ---
<span data-ttu-id="3fc7e-114">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-114">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-115">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-115">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-116">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-116">'Identity'</span></span>
- <span data-ttu-id="3fc7e-117">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-117">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-118">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-118">'Razor'</span></span>
- <span data-ttu-id="3fc7e-119">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-119">'SignalR' uid:</span></span> 

<span data-ttu-id="3fc7e-120">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-120">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-121">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-121">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-122">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-122">'Identity'</span></span>
- <span data-ttu-id="3fc7e-123">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-123">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-124">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-124">'Razor'</span></span>
- <span data-ttu-id="3fc7e-125">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-125">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3fc7e-126">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-126">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-127">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-127">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-128">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-128">'Identity'</span></span>
- <span data-ttu-id="3fc7e-129">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-129">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-130">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-130">'Razor'</span></span>
- <span data-ttu-id="3fc7e-131">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-131">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3fc7e-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-133">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-134">'Identity'</span></span>
- <span data-ttu-id="3fc7e-135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-135">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-136">'Razor'</span></span>
- <span data-ttu-id="3fc7e-137">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3fc7e-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-139">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-140">'Identity'</span></span>
- <span data-ttu-id="3fc7e-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-142">'Razor'</span></span>
- <span data-ttu-id="3fc7e-143">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-143">'SignalR' uid:</span></span> 

<span data-ttu-id="3fc7e-144">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-144">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-145">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-146">'Identity'</span></span>
- <span data-ttu-id="3fc7e-147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-147">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-148">'Razor'</span></span>
- <span data-ttu-id="3fc7e-149">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3fc7e-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-151">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-152">'Identity'</span></span>
- <span data-ttu-id="3fc7e-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-154">'Razor'</span></span>
- <span data-ttu-id="3fc7e-155">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3fc7e-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-157">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-158">'Identity'</span></span>
- <span data-ttu-id="3fc7e-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-160">'Razor'</span></span>
- <span data-ttu-id="3fc7e-161">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-161">'SignalR' uid:</span></span> 

<span data-ttu-id="3fc7e-162">------ | | MaxSendMessageSize | `null` | 서버에서 보낼 수 있는 최대 메시지 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-162">------ | | MaxSendMessageSize | `null` | The maximum message size in bytes that can be sent from the server.</span></span> <span data-ttu-id="3fc7e-163">구성된 최대 메시지 크기를 초과하는 메시지를 전송하려고 하면 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-163">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> <span data-ttu-id="3fc7e-164">`null`로 설정하면 메시지 크기의 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-164">When set to `null`, the message size is unlimited.</span></span> <span data-ttu-id="3fc7e-165">| | MaxReceiveMessageSize | 4MB | 서버에서 받을 수 있는 최대 메시지 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-165">| | MaxReceiveMessageSize | 4 MB | The maximum message size in bytes that can be received by the server.</span></span> <span data-ttu-id="3fc7e-166">서버에서 이 한도를 초과하는 메시지를 수신하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-166">If the server receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="3fc7e-167">이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-167">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> <span data-ttu-id="3fc7e-168">`null`로 설정하면 메시지 크기의 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-168">When set to `null`, the message size is unlimited.</span></span> <span data-ttu-id="3fc7e-169">| | EnableDetailedErrors | `false` | `true`인 경우 서비스 메서드에서 예외가 throw되면 자세한 예외 메시지가 클라이언트에 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-169">| | EnableDetailedErrors | `false` | If `true`, detailed exception messages are returned to clients when an exception is thrown in a service method.</span></span> <span data-ttu-id="3fc7e-170">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-170">The default is `false`.</span></span> <span data-ttu-id="3fc7e-171">`EnableDetailedErrors`를 `true`로 설정하면 중요한 정보가 노출될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-171">Setting `EnableDetailedErrors` to `true` can leak sensitive information.</span></span> <span data-ttu-id="3fc7e-172">| | CompressionProviders | gzip | 메시지를 압축하고 압축을 푸는 데 사용되는 압축 공급자의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-172">| | CompressionProviders | gzip | A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="3fc7e-173">사용자 지정 압축 공급자를 만들어 컬렉션에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-173">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="3fc7e-174">구성된 기본 공급자는 **gzip** 압축을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-174">The default configured providers support **gzip** compression.</span></span> <span data-ttu-id="3fc7e-175">| | <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | 서버에서 보낸 메시지를 압축하는 데 사용되는 압축 알고리즘입니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-175">| | <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | The compression algorithm used to compress messages sent from the server.</span></span> <span data-ttu-id="3fc7e-176">이 알고리즘은 `CompressionProviders`의 압축 공급자와 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-176">The algorithm must match a compression provider in `CompressionProviders`.</span></span> <span data-ttu-id="3fc7e-177">이 알고리즘이 응답을 압축하려면 클라이언트는 **grpc-accept-encoding** 헤더에 전송하여 알고리즘을 지원한다는 사실을 표시해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-177">For the algorithm to compress a response, the client must indicate it supports the algorithm by sending it in the **grpc-accept-encoding** header.</span></span> <span data-ttu-id="3fc7e-178">| | ResponseCompressionLevel | `null` | 서버에서 보낸 메시지를 압축하는 데 사용되는 압축 수준입니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-178">| | ResponseCompressionLevel | `null` | The compress level used to compress messages sent from the server.</span></span> <span data-ttu-id="3fc7e-179">| | Interceptors | None | 각 gRPC 호출을 사용하여 실행되는 인터셉터의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-179">| | Interceptors | None | A collection of interceptors that are run with each gRPC call.</span></span> <span data-ttu-id="3fc7e-180">인터셉터는 등록된 순서대로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-180">Interceptors are run in the order they are registered.</span></span> <span data-ttu-id="3fc7e-181">전역적으로 구성된 인터셉터는 단일 서비스에 대해 구성된 인터셉터보다 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-181">Globally configured interceptors are run before interceptors configured for a single service.</span></span> <span data-ttu-id="3fc7e-182">gRPC 인터셉터에 대한 자세한 내용은 [gRPC 인터셉터 및 미들웨어](xref:grpc/migration#grpc-interceptors-vs-middleware)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-182">For more information about gRPC interceptors, see [gRPC Interceptors vs. Middleware](xref:grpc/migration#grpc-interceptors-vs-middleware).</span></span> <span data-ttu-id="3fc7e-183">| | IgnoreUnknownServices | `false` | `true`인 경우 알 수 없는 서비스 및 메서드에 대한 호출이 **UNIMPLEMENTED** 상태를 반환하지 않고 요청이 ASP.NET Core에서 등록된 다음 미들웨어에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-183">| | IgnoreUnknownServices | `false` | If `true`, calls to unknown services and methods don't return an **UNIMPLEMENTED** status, and the request passes to the next registered middleware in ASP.NET Core.</span></span> |

<span data-ttu-id="3fc7e-184">`Startup.ConfigureServices`에서 `AddGrpc` 호출에 옵션 대리자를 제공하여 모든 서비스에 대해 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-184">Options can be configured for all services by providing an options delegate to the `AddGrpc` call in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

<span data-ttu-id="3fc7e-185">단일 서비스에 대한 옵션은 `AddGrpc`에 제공된 전역 옵션을 재정의하며 `AddServiceOptions<TService>`를 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-185">Options for a single service override the global options provided in `AddGrpc` and can be configured using `AddServiceOptions<TService>`:</span></span>

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a><span data-ttu-id="3fc7e-186">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="3fc7e-186">Configure client options</span></span>

<span data-ttu-id="3fc7e-187">gRPC 클라이언트 구성은 `GrpcChannelOptions`에 설정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-187">gRPC client configuration is set on `GrpcChannelOptions`.</span></span> <span data-ttu-id="3fc7e-188">다음 표에서는 gRPC 채널을 구성하기 위한 옵션을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-188">The following table describes options for configuring gRPC channels:</span></span>

| <span data-ttu-id="3fc7e-189">옵션</span><span class="sxs-lookup"><span data-stu-id="3fc7e-189">Option</span></span> | <span data-ttu-id="3fc7e-190">기본값</span><span class="sxs-lookup"><span data-stu-id="3fc7e-190">Default Value</span></span> | <span data-ttu-id="3fc7e-191">설명</span><span class="sxs-lookup"><span data-stu-id="3fc7e-191">Description</span></span> |
| ---
<span data-ttu-id="3fc7e-192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-193">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-194">'Identity'</span></span>
- <span data-ttu-id="3fc7e-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-196">'Razor'</span></span>
- <span data-ttu-id="3fc7e-197">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-197">'SignalR' uid:</span></span> 

<span data-ttu-id="3fc7e-198">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-198">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-199">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-200">'Identity'</span></span>
- <span data-ttu-id="3fc7e-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-202">'Razor'</span></span>
- <span data-ttu-id="3fc7e-203">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3fc7e-204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-205">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-206">'Identity'</span></span>
- <span data-ttu-id="3fc7e-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-208">'Razor'</span></span>
- <span data-ttu-id="3fc7e-209">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3fc7e-210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-211">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-212">'Identity'</span></span>
- <span data-ttu-id="3fc7e-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-214">'Razor'</span></span>
- <span data-ttu-id="3fc7e-215">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3fc7e-216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-217">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-218">'Identity'</span></span>
- <span data-ttu-id="3fc7e-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-220">'Razor'</span></span>
- <span data-ttu-id="3fc7e-221">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-221">'SignalR' uid:</span></span> 

<span data-ttu-id="3fc7e-222">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-222">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-223">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-224">'Identity'</span></span>
- <span data-ttu-id="3fc7e-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-226">'Razor'</span></span>
- <span data-ttu-id="3fc7e-227">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3fc7e-228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-229">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-230">'Identity'</span></span>
- <span data-ttu-id="3fc7e-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-232">'Razor'</span></span>
- <span data-ttu-id="3fc7e-233">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3fc7e-234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3fc7e-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-235">'Blazor'</span></span>
- <span data-ttu-id="3fc7e-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-236">'Identity'</span></span>
- <span data-ttu-id="3fc7e-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="3fc7e-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3fc7e-238">'Razor'</span></span>
- <span data-ttu-id="3fc7e-239">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="3fc7e-239">'SignalR' uid:</span></span> 

<span data-ttu-id="3fc7e-240">------ | | HttpHandler | 새 인스턴스 | gRPC 호출을 수행하는 데 사용되는 `HttpMessageHandler`입니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-240">------ | | HttpHandler | New instance | The `HttpMessageHandler` used to make gRPC calls.</span></span> <span data-ttu-id="3fc7e-241">사용자 지정 `HttpClientHandler`를 구성하거나 gRPC 호출을 위해 HTTP 파이프라인에 추가 처리기를 추가하도록 클라이언트를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-241">A client can be set to configure a custom `HttpClientHandler` or add additional handlers to the HTTP pipeline for gRPC calls.</span></span> <span data-ttu-id="3fc7e-242">지정된 `HttpMessageHandler`가 없으면 채널에 새 `HttpClientHandler` 인스턴스가 만들어집니다(자동으로 삭제됨).</span><span class="sxs-lookup"><span data-stu-id="3fc7e-242">If no `HttpMessageHandler` is specified, a new `HttpClientHandler` instance is created for the channel with automatic disposal.</span></span> <span data-ttu-id="3fc7e-243">| | HttpClient | `null` | gRPC 호출을 수행하는 데 사용되는 `HttpClient`입니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-243">| | HttpClient | `null` | The `HttpClient` used to make gRPC calls.</span></span> <span data-ttu-id="3fc7e-244">이 설정은 `HttpHandler`의 대안입니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-244">This setting is an alternative to `HttpHandler`.</span></span> <span data-ttu-id="3fc7e-245">| | DisposeHttpClient | `false` | `true`로 설정되고 `HttpMessageHandler` 또는 `HttpClient`가 지정된 경우 `GrpcChannel`이 삭제될 때 `HttpHandler` 또는 `HttpClient`가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-245">| | DisposeHttpClient | `false` | If set to `true` and an `HttpMessageHandler` or `HttpClient` is specified, then either the `HttpHandler` or `HttpClient`, respectively, is disposed when the `GrpcChannel` is disposed.</span></span> <span data-ttu-id="3fc7e-246">| | LoggerFactory | `null` | 클라이언트에서 gRPC 호출에 대한 정보를 기록하는 데 사용되는 `LoggerFactory`입니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-246">| | LoggerFactory | `null` | The `LoggerFactory` used by the client to log information about gRPC calls.</span></span> <span data-ttu-id="3fc7e-247">`LoggerFactory` 인스턴스는 종속성 주입을 통해 확인되거나 `LoggerFactory.Create`를 사용하여 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-247">A `LoggerFactory` instance can be resolved from dependency injection or created using `LoggerFactory.Create`.</span></span> <span data-ttu-id="3fc7e-248">로깅 구성 예제는 <xref:grpc/diagnostics#grpc-client-logging>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-248">For examples of configuring logging, see <xref:grpc/diagnostics#grpc-client-logging>.</span></span> <span data-ttu-id="3fc7e-249">| | MaxSendMessageSize | `null` | 클라이언트에서 보낼 수 있는 최대 메시지 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-249">| | MaxSendMessageSize | `null` | The maximum message size in bytes that can be sent from the client.</span></span> <span data-ttu-id="3fc7e-250">구성된 최대 메시지 크기를 초과하는 메시지를 전송하려고 하면 예외가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-250">Attempting to send a message that exceeds the configured maximum message size results in an exception.</span></span> <span data-ttu-id="3fc7e-251">`null`로 설정하면 메시지 크기의 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-251">When set to `null`, the message size is unlimited.</span></span> <span data-ttu-id="3fc7e-252">| | <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4MB | 클라이언트에서 받을 수 있는 최대 메시지 크기(바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-252">| | <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | The maximum message size in bytes that can be received by the client.</span></span> <span data-ttu-id="3fc7e-253">클라이언트에서 이 한도를 초과하는 메시지를 수신하면 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-253">If the client receives a message that exceeds this limit, it throws an exception.</span></span> <span data-ttu-id="3fc7e-254">이 값을 늘리면 클라이언트가 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-254">Increasing this value allows the client to receive larger messages, but can negatively impact memory consumption.</span></span> <span data-ttu-id="3fc7e-255">`null`로 설정하면 메시지 크기의 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-255">When set to `null`, the message size is unlimited.</span></span> <span data-ttu-id="3fc7e-256">| | 자격 증명 | `null` | `ChannelCredentials` 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-256">| | Credentials | `null` | A `ChannelCredentials` instance.</span></span> <span data-ttu-id="3fc7e-257">자격 증명은 gRPC 호출에 인증 메타데이터를 추가하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-257">Credentials are used to add authentication metadata to gRPC calls.</span></span> <span data-ttu-id="3fc7e-258">| | CompressionProviders | gzip | 메시지를 압축하고 압축을 푸는 데 사용되는 압축 공급자의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-258">| | CompressionProviders | gzip | A collection of compression providers used to compress and decompress messages.</span></span> <span data-ttu-id="3fc7e-259">사용자 지정 압축 공급자를 만들어 컬렉션에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-259">Custom compression providers can be created and added to the collection.</span></span> <span data-ttu-id="3fc7e-260">구성된 기본 공급자는 **gzip** 압축을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-260">The default configured providers support **gzip** compression.</span></span> |

<span data-ttu-id="3fc7e-261">코드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-261">The following code:</span></span>

* <span data-ttu-id="3fc7e-262">채널의 최대 송신 및 수신 메시지 크기를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-262">Sets the maximum send and receive message size on the channel.</span></span>
* <span data-ttu-id="3fc7e-263">클라이언트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3fc7e-263">Creates a client.</span></span>

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="3fc7e-264">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3fc7e-264">Additional resources</span></span>

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
