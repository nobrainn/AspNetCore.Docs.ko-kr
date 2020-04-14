---
title: ASP.NET SignalR 코어 구성
author: bradygaster
description: ASP.NET 핵심 SignalR 앱을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 9f98a9c6371ef7e38586b0d728c670b0eecb8f6e
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228142"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="0980a-103">ASP.NET Core SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="0980a-104">JSON/메시지팩 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="0980a-105">ASP.NET 코어 시그널R는 메시지 인코딩을 위한 두 가지 프로토콜을 지원합니다: [JSON](https://www.json.org/) 및 [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="0980a-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="0980a-106">각 프로토콜에는 직렬화 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="0980a-107">JSON [직렬화AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 방법을 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="0980a-108">`AddJsonProtocol`[에서 AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 후 `Startup.ConfigureServices`추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0980a-109">메서드는 `AddJsonProtocol` 개체를 받는 `options` 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="0980a-110">해당 개체의 [Payload SerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 속성은 인수의 직렬화를 구성하고 값을 반환하는 데 사용할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="0980a-111">자세한 내용은 [System.Text.Json 문서를](/dotnet/api/system.text.json)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="0980a-112">예를 들어, 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경하지 않도록 serializer를 구성하려면 다음 코드를 `Startup.ConfigureServices`사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="0980a-113">.NET 클라이언트에서 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="0980a-114">확장 `Microsoft.Extensions.DependencyInjection` 메서드를 해결하려면 네임스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="0980a-115">현재 는 JavaScript 클라이언트에서 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="0980a-116">뉴턴소프트.Json으로 전환</span><span class="sxs-lookup"><span data-stu-id="0980a-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="0980a-117">에서 `System.Text.Json`지원되지 `Newtonsoft.Json` 않는 기능이 필요한 경우 [[ 뉴턴소프트.Json](xref:migration/22-to-30#switch-to-newtonsoftjson)로 전환 보기]</span><span class="sxs-lookup"><span data-stu-id="0980a-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="0980a-118">메시지팩 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-118">MessagePack serialization options</span></span>

<span data-ttu-id="0980a-119">메시지팩 일련화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대한 대리자를 제공하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="0980a-120">자세한 내용은 [SignalR의 메시지 팩을](xref:signalr/messagepackhubprotocol) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="0980a-121">현재 는 JavaScript 클라이언트에서 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="0980a-122">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-122">Configure server options</span></span>

<span data-ttu-id="0980a-123">다음 표는 SignalR 허브를 구성하는 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="0980a-124">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-124">Option</span></span> | <span data-ttu-id="0980a-125">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-125">Default Value</span></span> | <span data-ttu-id="0980a-126">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="0980a-127">30초</span><span class="sxs-lookup"><span data-stu-id="0980a-127">30 seconds</span></span> | <span data-ttu-id="0980a-128">서버는 이 간격으로 클라이언트가 연결이 끊어진 것으로 간주합니다(유지-활성 포함)가 수신되지 않은 경우 클라이언트가 연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="0980a-129">클라이언트가 실제로 연결이 끊어진 것으로 표시되는 데 이 시간 초과 간격보다 더 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="0980a-130">권장 값은 값의 `KeepAliveInterval` 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="0980a-131">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-131">15 seconds</span></span> | <span data-ttu-id="0980a-132">클라이언트가 이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0980a-133">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0980a-134">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0980a-135">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-135">15 seconds</span></span> | <span data-ttu-id="0980a-136">서버가 이 간격 내에 메시지를 보내지 않은 경우 연결을 열어 두기 위해 ping 메시지가 자동으로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0980a-137">`KeepAliveInterval`변경하면 클라이언트의 `ServerTimeout` / `serverTimeoutInMilliseconds` 설정을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0980a-138">`ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 값의 `KeepAliveInterval` 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0980a-139">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="0980a-139">All installed protocols</span></span> | <span data-ttu-id="0980a-140">이 허브에서 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-140">Protocols supported by this hub.</span></span> <span data-ttu-id="0980a-141">기본적으로 서버에 등록된 모든 프로토콜은 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대한 특정 프로토콜을 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0980a-142">자세한 `true`예외 메시지가 Hub 메서드에서 예외를 throw하면 클라이언트에 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0980a-143">기본값은 `false`은 예외 메시지에 중요한 정보가 포함될 수 있으므로 입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="0980a-144">클라이언트 업로드 스트림에 대해 버퍼링할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="0980a-145">이 제한에 도달하면 서버가 스트림 항목을 처리할 때까지 호출 처리가 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="0980a-146">32KB</span><span class="sxs-lookup"><span data-stu-id="0980a-146">32 KB</span></span> | <span data-ttu-id="0980a-147">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="0980a-148">옵션에서 호출에 대한 옵션 대리자를 제공하여 모든 `AddSignalR` 허브에 대해 옵션을 구성할 수 있습니다. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="0980a-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="0980a-149">단일 허브에 대한 옵션은 제공된 전역 `AddSignalR` 옵션을 재정의하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>다음을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="0980a-150">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="0980a-151">전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하는 데 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="0980a-152">이러한 옵션은 [에서 MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 대리자를 전달하여 `Startup.Configure`구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="0980a-153">다음 표는 코어 SignalR의 고급 http 옵션을 구성하기 ASP.NET 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="0980a-154">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-154">Option</span></span> | <span data-ttu-id="0980a-155">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-155">Default Value</span></span> | <span data-ttu-id="0980a-156">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="0980a-157">32KB</span><span class="sxs-lookup"><span data-stu-id="0980a-157">32 KB</span></span> | <span data-ttu-id="0980a-158">배압을 적용하기 전에 서버가 버퍼링하는 클라이언트에서 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="0980a-159">이 값을 늘리면 서버가 역압을 적용하지 않고 더 큰 메시지를 더 빨리 받을 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="0980a-160">Hub 클래스에 `Authorize` 적용된 특성에서 데이터가 자동으로 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="0980a-161">클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="0980a-162">32KB</span><span class="sxs-lookup"><span data-stu-id="0980a-162">32 KB</span></span> | <span data-ttu-id="0980a-163">배압을 관찰하기 전에 서버가 버퍼링하는 앱에서 전송되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="0980a-164">이 값을 늘리면 서버가 배압을 기다리지 않고 더 큰 메시지를 더 빨리 버퍼링할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="0980a-165">모든 전송이 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-165">All Transports are enabled.</span></span> | <span data-ttu-id="0980a-166">비트는 클라이언트가 `HttpTransportType` 연결하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 열거형 플래그를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="0980a-167">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-167">See below.</span></span> | <span data-ttu-id="0980a-168">긴 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="0980a-169">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-169">See below.</span></span> | <span data-ttu-id="0980a-170">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="0980a-171">0</span><span class="sxs-lookup"><span data-stu-id="0980a-171">0</span></span> | <span data-ttu-id="0980a-172">협상 프로토콜의 최소 버전을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="0980a-173">클라이언트를 최신 버전으로 제한하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="0980a-174">Long Polling 전송에는 속성을 사용하여 구성할 `LongPolling` 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="0980a-175">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-175">Option</span></span> | <span data-ttu-id="0980a-176">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-176">Default Value</span></span> | <span data-ttu-id="0980a-177">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="0980a-178">90초</span><span class="sxs-lookup"><span data-stu-id="0980a-178">90 seconds</span></span> | <span data-ttu-id="0980a-179">단일 폴링 요청을 종료하기 전에 서버가 메시지가 클라이언트에 전송될 때까지 기다리는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="0980a-180">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="0980a-181">WebSocket 전송에는 `WebSockets` 속성을 사용하여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="0980a-182">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-182">Option</span></span> | <span data-ttu-id="0980a-183">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-183">Default Value</span></span> | <span data-ttu-id="0980a-184">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="0980a-185">5초</span><span class="sxs-lookup"><span data-stu-id="0980a-185">5 seconds</span></span> | <span data-ttu-id="0980a-186">서버가 닫히면 클라이언트가 이 시간 간격 내에 닫히지 않으면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="0980a-187">헤더를 사용자 지정 값으로 `Sec-WebSocket-Protocol` 설정하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="0980a-188">대리자는 클라이언트가 요청한 값을 입력으로 수신하고 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="0980a-189">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-189">Configure client options</span></span>

<span data-ttu-id="0980a-190">클라이언트 옵션은 `HubConnectionBuilder` 유형(.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="0980a-191">Java 클라이언트에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 빌더 구성 옵션과 그 자체가 `HubConnection` 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="0980a-192">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-192">Configure logging</span></span>

<span data-ttu-id="0980a-193">로깅은 `ConfigureLogging` 메서드를 사용하여 .NET 클라이언트에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="0980a-194">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="0980a-195">자세한 내용은 [ASP.NET 핵심 로그인](xref:fundamentals/logging/index) 설명서를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0980a-196">로깅 공급자를 등록하려면 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="0980a-197">전체 목록은 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="0980a-198">예를 들어 콘솔 로깅을 `Microsoft.Extensions.Logging.Console` 사용하려면 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="0980a-199">확장 `AddConsole` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="0980a-200">JavaScript 클라이언트에서 비슷한 `configureLogging` 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="0980a-201">생성할 `LogLevel` 로그 메시지의 최소 수준을 나타내는 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="0980a-202">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="0980a-203">`LogLevel` 값 대신 로그 수준 이름을 `string` 나타내는 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="0980a-204">이 기능은 상수에 액세스할 수 없는 환경에서 SignalR 로깅을 `LogLevel` 구성할 때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="0980a-205">다음 표에는 사용 가능한 로그 수준이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-205">The following table lists the available log levels.</span></span> <span data-ttu-id="0980a-206">기록할 최소 `configureLogging` 로그 **minimum** 수준을 설정하기 위해 제공하는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="0980a-207">이 수준에서 기록된 메시지 **또는 테이블에 나열된 수준이**기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="0980a-208">String</span><span class="sxs-lookup"><span data-stu-id="0980a-208">String</span></span>                      | <span data-ttu-id="0980a-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="0980a-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="0980a-210">`info`**또는**`information`</span><span class="sxs-lookup"><span data-stu-id="0980a-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="0980a-211">`warn`**또는**`warning`</span><span class="sxs-lookup"><span data-stu-id="0980a-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="0980a-212">로깅을 완전히 `signalR.LogLevel.None` 사용하지 `configureLogging` 않으려면 메서드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="0980a-213">로깅에 대한 자세한 내용은 [SignalR 진단 문서를](xref:signalr/diagnostics)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="0980a-214">SignalR Java 클라이언트는 로깅을 위해 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="0980a-215">라이브러리의 사용자가 특정 로깅 종속성을 가져와 고유한 특정 로깅 구현을 선택할 수 있는 고급 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="0980a-216">다음 코드 조각은 SignalR `java.util.logging` Java 클라이언트에서 사용하는 방법을 보여 주며 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="0980a-217">종속성에 로깅을 구성하지 않은 경우 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음 로거를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="0980a-218">이 무시 해도 안전 하 게 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="0980a-219">허용된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-219">Configure allowed transports</span></span>

<span data-ttu-id="0980a-220">SignalR에서 사용하는 전송은 `WithUrl` 호출(자바스크립트)에서`withUrl` 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="0980a-221">비트-OR값은 클라이언트가 `HttpTransportType` 지정된 전송만 사용하도록 제한하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="0980a-222">모든 전송은 기본적으로 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-222">All transports are enabled by default.</span></span>

<span data-ttu-id="0980a-223">예를 들어 서버 전송 이벤트 전송을 사용하지 않도록 설정하지만 WebSocket 및 긴 폴링 연결을 허용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="0980a-224">JavaScript 클라이언트에서 전송은 `transport` `withUrl`다음에 제공된 옵션 개체에 필드를 설정하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="0980a-225">이 버전의 Java 클라이언트 웹소켓은 사용 가능한 유일한 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="0980a-226">Java 클라이언트에서 전송은 `withTransport` `HttpHubConnectionBuilder`의 메서드를 사용하여 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="0980a-227">Java 클라이언트는 기본적으로 WebSockets 전송을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="0980a-228">SignalR Java 클라이언트는 아직 전송 대체를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="0980a-229">베어러 인증 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-229">Configure bearer authentication</span></span>

<span data-ttu-id="0980a-230">SignalR 요청과 함께 인증 데이터를 `AccessTokenProvider` 제공하려면`accessTokenFactory` 옵션(JavaScript)을 사용하여 원하는 액세스 토큰을 반환하는 함수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="0980a-231">.NET 클라이언트에서 이 액세스 토큰은 HTTP "보유자 인증" 토큰으로 `Authorization` 전달됩니다(유형이 `Bearer`있는 헤더 사용).</span><span class="sxs-lookup"><span data-stu-id="0980a-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="0980a-232">JavaScript 클라이언트에서 액세스 토큰은 브라우저 API가 헤더를 적용하는 기능을 제한하는 경우(특히 서버 전송 이벤트 및 WebSockets 요청)를 **제외하고** 베어러 토큰으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="0980a-233">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 `access_token`제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="0980a-234">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 다음 에서 `WithUrl`옵션 대리자를 사용하여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="0980a-235">JavaScript 클라이언트에서 액세스 토큰은 다음 의 `accessTokenFactory` `withUrl`옵션 개체에 필드를 설정하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="0980a-236">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder에](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)액세스 토큰 팩터리를 제공하여 인증에 사용할 베어러 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="0980a-237">[와 사용AccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) [RxJava](https://github.com/ReactiveX/RxJava) [단일\<문자열>. ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="0980a-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="0980a-238">[Single.defer를](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)호출하면 클라이언트에 대한 액세스 토큰을 생성하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="0980a-239">시간 시간 및 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="0980a-240">시간 시간 및 유지 동작을 구성하기 위한 추가 `HubConnection` 옵션은 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="0980a-241">.NET</span><span class="sxs-lookup"><span data-stu-id="0980a-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0980a-242">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-242">Option</span></span> | <span data-ttu-id="0980a-243">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-243">Default value</span></span> | <span data-ttu-id="0980a-244">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0980a-245">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0980a-246">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-246">Timeout for server activity.</span></span> <span data-ttu-id="0980a-247">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `Closed` 것으로`onclose` 간주하고 JavaScript에서 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0980a-248">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0980a-249">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0980a-250">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-250">15 seconds</span></span> | <span data-ttu-id="0980a-251">초기 서버 핸드셰이크에 대한 시간 시간 입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="0980a-252">서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` `onclose` JavaScript에서 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0980a-253">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0980a-254">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0980a-255">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-255">15 seconds</span></span> | <span data-ttu-id="0980a-256">클라이언트가 ping 메시지를 보내는 간격을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0980a-257">클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0980a-258">클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="0980a-259">.NET 클라이언트에서 시간 지정 값은 `TimeSpan` 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="0980a-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0980a-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0980a-261">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-261">Option</span></span> | <span data-ttu-id="0980a-262">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-262">Default value</span></span> | <span data-ttu-id="0980a-263">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0980a-264">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0980a-265">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-265">Timeout for server activity.</span></span> <span data-ttu-id="0980a-266">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onclose` 것으로 간주하고 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0980a-267">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0980a-268">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="0980a-269">15초(15,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0980a-270">클라이언트가 ping 메시지를 보내는 간격을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0980a-271">클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0980a-272">클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0980a-273">Java</span><span class="sxs-lookup"><span data-stu-id="0980a-273">Java</span></span>](#tab/java)

| <span data-ttu-id="0980a-274">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-274">Option</span></span> | <span data-ttu-id="0980a-275">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-275">Default value</span></span> | <span data-ttu-id="0980a-276">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0980a-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0980a-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0980a-278">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0980a-279">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-279">Timeout for server activity.</span></span> <span data-ttu-id="0980a-280">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onClose` 것으로 간주하고 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0980a-281">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0980a-282">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0980a-283">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-283">15 seconds</span></span> | <span data-ttu-id="0980a-284">초기 서버 핸드셰이크에 대한 시간 시간 입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="0980a-285">서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0980a-286">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0980a-287">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="0980a-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="0980a-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="0980a-289">15초(15,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0980a-290">클라이언트가 ping 메시지를 보내는 간격을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0980a-291">클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0980a-292">클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="0980a-293">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-293">Configure additional options</span></span>

<span data-ttu-id="0980a-294">`WithUrl` 추가 옵션은 Java 클라이언트의`withUrl` 다양한 구성 API에 `HubConnectionBuilder` 있는 (JavaScript) 메서드에서 구성할 `HttpHubConnectionBuilder` 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="0980a-295">.NET</span><span class="sxs-lookup"><span data-stu-id="0980a-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0980a-296">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-296">.NET Option</span></span> |  <span data-ttu-id="0980a-297">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-297">Default value</span></span> | <span data-ttu-id="0980a-298">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="0980a-299">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="0980a-300">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0980a-301">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0980a-302">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="0980a-303">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-303">Empty</span></span> | <span data-ttu-id="0980a-304">요청을 인증하기 위해 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="0980a-305">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-305">Empty</span></span> | <span data-ttu-id="0980a-306">모든 HTTP 요청과 함께 보낼 HTTP 쿠키 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="0980a-307">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-307">Empty</span></span> | <span data-ttu-id="0980a-308">모든 HTTP 요청과 함께 보낼 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="0980a-309">5초</span><span class="sxs-lookup"><span data-stu-id="0980a-309">5 seconds</span></span> | <span data-ttu-id="0980a-310">웹 소켓만.</span><span class="sxs-lookup"><span data-stu-id="0980a-310">WebSockets only.</span></span> <span data-ttu-id="0980a-311">서버가 닫기 요청을 승인할 때까지 클라이언트가 닫은 후 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="0980a-312">이 시간 내에 서버가 닫기를 승인하지 않으면 클라이언트가 연결 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="0980a-313">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-313">Empty</span></span> | <span data-ttu-id="0980a-314">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="0980a-315">HTTP 요청을 보내는 데 사용되는 대리자를 구성하거나 교체하는 데 사용할 수 있는 `HttpMessageHandler` 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="0980a-316">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="0980a-317">이 대리자는 null이 아닌 값을 반환해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="0980a-318">기본값의 설정을 수정하여 반환하거나 새 `HttpMessageHandler` 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="0980a-319">**처리기를 교체할 때 제공된 처리기에서 유지하려는 설정을 복사해야 하며, 그렇지 않으면 구성된 옵션(예: 쿠키 및 헤더)이 새 처리기에 적용되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="0980a-320">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="0980a-321">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 보내도록 이 부울을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="0980a-322">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="0980a-323">추가 WebSocket 옵션을 구성하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="0980a-324">옵션을 구성하는 데 사용할 수 있는 [ClientWebSocketOptions의](/dotnet/api/system.net.websockets.clientwebsocketoptions) 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="0980a-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0980a-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0980a-326">자바 스크립트 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-326">JavaScript Option</span></span> | <span data-ttu-id="0980a-327">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-327">Default Value</span></span> | <span data-ttu-id="0980a-328">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="0980a-329">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="0980a-330">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0980a-331">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0980a-332">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0980a-333">Java</span><span class="sxs-lookup"><span data-stu-id="0980a-333">Java</span></span>](#tab/java)

| <span data-ttu-id="0980a-334">자바 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-334">Java Option</span></span> | <span data-ttu-id="0980a-335">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-335">Default Value</span></span> | <span data-ttu-id="0980a-336">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-336">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="0980a-337">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-337">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="0980a-338">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-338">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0980a-339">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-339">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0980a-340">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-340">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="0980a-341">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="0980a-341">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="0980a-342">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-342">Empty</span></span> | <span data-ttu-id="0980a-343">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-343">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="0980a-344">.NET 클라이언트에서 이러한 옵션은 다음에 제공된 옵션 대리자에서 수정할 `WithUrl`수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-344">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="0980a-345">자바 스크립트 클라이언트에서 이러한 옵션은 `withUrl`다음에 제공되는 자바 스크립트 개체에서 제공 할 수 있습니다 .</span><span class="sxs-lookup"><span data-stu-id="0980a-345">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="0980a-346">Java 클라이언트에서 이러한 옵션은 `HttpHubConnectionBuilder` 반환된 메서드로 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="0980a-346">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="0980a-347">추가 자료</span><span class="sxs-lookup"><span data-stu-id="0980a-347">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="0980a-348">JSON/메시지팩 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-348">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="0980a-349">ASP.NET 코어 시그널R는 메시지 인코딩을 위한 두 가지 프로토콜을 지원합니다: [JSON](https://www.json.org/) 및 [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="0980a-349">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="0980a-350">각 프로토콜에는 직렬화 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-350">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="0980a-351">JSON [직렬화AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 방법을 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-351">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="0980a-352">`AddJsonProtocol`[에서 AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 후 `Startup.ConfigureServices`추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-352">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0980a-353">메서드는 `AddJsonProtocol` 개체를 받는 `options` 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-353">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="0980a-354">해당 개체의 [Payload SerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 속성은 인수의 직렬화를 구성하고 값을 반환하는 데 사용할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-354">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="0980a-355">자세한 내용은 [System.Text.Json 문서를](/dotnet/api/system.text.json)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-355">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="0980a-356">예를 들어, 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경하지 않도록 serializer를 구성하려면 다음 코드를 `Startup.ConfigureServices`사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-356">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="0980a-357">.NET 클라이언트에서 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-357">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="0980a-358">확장 `Microsoft.Extensions.DependencyInjection` 메서드를 해결하려면 네임스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-358">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="0980a-359">현재 는 JavaScript 클라이언트에서 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-359">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="0980a-360">뉴턴소프트.Json으로 전환</span><span class="sxs-lookup"><span data-stu-id="0980a-360">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="0980a-361">에서 `System.Text.Json`지원되지 `Newtonsoft.Json` 않는 기능이 필요한 경우 [[ 뉴턴소프트.Json](xref:migration/22-to-30#switch-to-newtonsoftjson)로 전환 보기]</span><span class="sxs-lookup"><span data-stu-id="0980a-361">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="0980a-362">메시지팩 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-362">MessagePack serialization options</span></span>

<span data-ttu-id="0980a-363">메시지팩 일련화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대한 대리자를 제공하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-363">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="0980a-364">자세한 내용은 [SignalR의 메시지 팩을](xref:signalr/messagepackhubprotocol) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-364">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="0980a-365">현재 는 JavaScript 클라이언트에서 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-365">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="0980a-366">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-366">Configure server options</span></span>

<span data-ttu-id="0980a-367">다음 표는 SignalR 허브를 구성하는 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-367">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="0980a-368">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-368">Option</span></span> | <span data-ttu-id="0980a-369">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-369">Default Value</span></span> | <span data-ttu-id="0980a-370">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="0980a-371">30초</span><span class="sxs-lookup"><span data-stu-id="0980a-371">30 seconds</span></span> | <span data-ttu-id="0980a-372">서버는 이 간격으로 클라이언트가 연결이 끊어진 것으로 간주합니다(유지-활성 포함)가 수신되지 않은 경우 클라이언트가 연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-372">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="0980a-373">클라이언트가 실제로 연결이 끊어진 것으로 표시되는 데 이 시간 초과 간격보다 더 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-373">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="0980a-374">권장 값은 값의 `KeepAliveInterval` 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-374">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="0980a-375">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-375">15 seconds</span></span> | <span data-ttu-id="0980a-376">클라이언트가 이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-376">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0980a-377">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0980a-378">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0980a-379">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-379">15 seconds</span></span> | <span data-ttu-id="0980a-380">서버가 이 간격 내에 메시지를 보내지 않은 경우 연결을 열어 두기 위해 ping 메시지가 자동으로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-380">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0980a-381">`KeepAliveInterval`변경하면 클라이언트의 `ServerTimeout` / `serverTimeoutInMilliseconds` 설정을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-381">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0980a-382">`ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 값의 `KeepAliveInterval` 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-382">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0980a-383">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="0980a-383">All installed protocols</span></span> | <span data-ttu-id="0980a-384">이 허브에서 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-384">Protocols supported by this hub.</span></span> <span data-ttu-id="0980a-385">기본적으로 서버에 등록된 모든 프로토콜은 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대한 특정 프로토콜을 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-385">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0980a-386">자세한 `true`예외 메시지가 Hub 메서드에서 예외를 throw하면 클라이언트에 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-386">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0980a-387">기본값은 `false`은 예외 메시지에 중요한 정보가 포함될 수 있으므로 입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-387">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="0980a-388">클라이언트 업로드 스트림에 대해 버퍼링할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-388">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="0980a-389">이 제한에 도달하면 서버가 스트림 항목을 처리할 때까지 호출 처리가 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-389">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="0980a-390">32KB</span><span class="sxs-lookup"><span data-stu-id="0980a-390">32 KB</span></span> | <span data-ttu-id="0980a-391">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-391">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="0980a-392">옵션에서 호출에 대한 옵션 대리자를 제공하여 모든 `AddSignalR` 허브에 대해 옵션을 구성할 수 있습니다. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="0980a-392">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="0980a-393">단일 허브에 대한 옵션은 제공된 전역 `AddSignalR` 옵션을 재정의하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>다음을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-393">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="0980a-394">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-394">Advanced HTTP configuration options</span></span>

<span data-ttu-id="0980a-395">전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하는 데 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-395">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="0980a-396">이러한 옵션은 [에서 MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 대리자를 전달하여 `Startup.Configure`구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-396">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="0980a-397">다음 표는 코어 SignalR의 고급 http 옵션을 구성하기 ASP.NET 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-397">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="0980a-398">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-398">Option</span></span> | <span data-ttu-id="0980a-399">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-399">Default Value</span></span> | <span data-ttu-id="0980a-400">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-400">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="0980a-401">32KB</span><span class="sxs-lookup"><span data-stu-id="0980a-401">32 KB</span></span> | <span data-ttu-id="0980a-402">배압을 적용하기 전에 서버가 버퍼링하는 클라이언트에서 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-402">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="0980a-403">이 값을 늘리면 서버가 역압을 적용하지 않고 더 큰 메시지를 더 빨리 받을 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-403">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="0980a-404">Hub 클래스에 `Authorize` 적용된 특성에서 데이터가 자동으로 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-404">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="0980a-405">클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-405">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="0980a-406">32KB</span><span class="sxs-lookup"><span data-stu-id="0980a-406">32 KB</span></span> | <span data-ttu-id="0980a-407">배압을 관찰하기 전에 서버가 버퍼링하는 앱에서 전송되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-407">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="0980a-408">이 값을 늘리면 서버가 배압을 기다리지 않고 더 큰 메시지를 더 빨리 버퍼링할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-408">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="0980a-409">모든 전송이 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-409">All Transports are enabled.</span></span> | <span data-ttu-id="0980a-410">비트는 클라이언트가 `HttpTransportType` 연결하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 열거형 플래그를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-410">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="0980a-411">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-411">See below.</span></span> | <span data-ttu-id="0980a-412">긴 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-412">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="0980a-413">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-413">See below.</span></span> | <span data-ttu-id="0980a-414">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-414">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="0980a-415">Long Polling 전송에는 속성을 사용하여 구성할 `LongPolling` 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-415">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="0980a-416">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-416">Option</span></span> | <span data-ttu-id="0980a-417">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-417">Default Value</span></span> | <span data-ttu-id="0980a-418">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-418">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="0980a-419">90초</span><span class="sxs-lookup"><span data-stu-id="0980a-419">90 seconds</span></span> | <span data-ttu-id="0980a-420">단일 폴링 요청을 종료하기 전에 서버가 메시지가 클라이언트에 전송될 때까지 기다리는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-420">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="0980a-421">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-421">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="0980a-422">WebSocket 전송에는 `WebSockets` 속성을 사용하여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-422">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="0980a-423">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-423">Option</span></span> | <span data-ttu-id="0980a-424">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-424">Default Value</span></span> | <span data-ttu-id="0980a-425">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-425">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="0980a-426">5초</span><span class="sxs-lookup"><span data-stu-id="0980a-426">5 seconds</span></span> | <span data-ttu-id="0980a-427">서버가 닫히면 클라이언트가 이 시간 간격 내에 닫히지 않으면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-427">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="0980a-428">헤더를 사용자 지정 값으로 `Sec-WebSocket-Protocol` 설정하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-428">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="0980a-429">대리자는 클라이언트가 요청한 값을 입력으로 수신하고 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-429">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="0980a-430">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-430">Configure client options</span></span>

<span data-ttu-id="0980a-431">클라이언트 옵션은 `HubConnectionBuilder` 유형(.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-431">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="0980a-432">Java 클라이언트에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 빌더 구성 옵션과 그 자체가 `HubConnection` 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-432">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="0980a-433">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-433">Configure logging</span></span>

<span data-ttu-id="0980a-434">로깅은 `ConfigureLogging` 메서드를 사용하여 .NET 클라이언트에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-434">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="0980a-435">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-435">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="0980a-436">자세한 내용은 [ASP.NET 핵심 로그인](xref:fundamentals/logging/index) 설명서를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-436">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0980a-437">로깅 공급자를 등록하려면 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-437">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="0980a-438">전체 목록은 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-438">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="0980a-439">예를 들어 콘솔 로깅을 `Microsoft.Extensions.Logging.Console` 사용하려면 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-439">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="0980a-440">확장 `AddConsole` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-440">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="0980a-441">JavaScript 클라이언트에서 비슷한 `configureLogging` 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-441">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="0980a-442">생성할 `LogLevel` 로그 메시지의 최소 수준을 나타내는 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-442">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="0980a-443">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-443">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="0980a-444">`LogLevel` 값 대신 로그 수준 이름을 `string` 나타내는 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-444">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="0980a-445">이 기능은 상수에 액세스할 수 없는 환경에서 SignalR 로깅을 `LogLevel` 구성할 때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-445">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="0980a-446">다음 표에는 사용 가능한 로그 수준이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-446">The following table lists the available log levels.</span></span> <span data-ttu-id="0980a-447">기록할 최소 `configureLogging` 로그 **minimum** 수준을 설정하기 위해 제공하는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-447">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="0980a-448">이 수준에서 기록된 메시지 **또는 테이블에 나열된 수준이**기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-448">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="0980a-449">String</span><span class="sxs-lookup"><span data-stu-id="0980a-449">String</span></span>                      | <span data-ttu-id="0980a-450">LogLevel</span><span class="sxs-lookup"><span data-stu-id="0980a-450">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="0980a-451">`info`**또는**`information`</span><span class="sxs-lookup"><span data-stu-id="0980a-451">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="0980a-452">`warn`**또는**`warning`</span><span class="sxs-lookup"><span data-stu-id="0980a-452">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="0980a-453">로깅을 완전히 `signalR.LogLevel.None` 사용하지 `configureLogging` 않으려면 메서드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-453">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="0980a-454">로깅에 대한 자세한 내용은 [SignalR 진단 문서를](xref:signalr/diagnostics)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-454">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="0980a-455">SignalR Java 클라이언트는 로깅을 위해 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-455">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="0980a-456">라이브러리의 사용자가 특정 로깅 종속성을 가져와 고유한 특정 로깅 구현을 선택할 수 있는 고급 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-456">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="0980a-457">다음 코드 조각은 SignalR `java.util.logging` Java 클라이언트에서 사용하는 방법을 보여 주며 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-457">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="0980a-458">종속성에 로깅을 구성하지 않은 경우 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음 로거를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-458">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="0980a-459">이 무시 해도 안전 하 게 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-459">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="0980a-460">허용된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-460">Configure allowed transports</span></span>

<span data-ttu-id="0980a-461">SignalR에서 사용하는 전송은 `WithUrl` 호출(자바스크립트)에서`withUrl` 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-461">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="0980a-462">비트-OR값은 클라이언트가 `HttpTransportType` 지정된 전송만 사용하도록 제한하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-462">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="0980a-463">모든 전송은 기본적으로 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-463">All transports are enabled by default.</span></span>

<span data-ttu-id="0980a-464">예를 들어 서버 전송 이벤트 전송을 사용하지 않도록 설정하지만 WebSocket 및 긴 폴링 연결을 허용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-464">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="0980a-465">JavaScript 클라이언트에서 전송은 `transport` `withUrl`다음에 제공된 옵션 개체에 필드를 설정하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-465">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="0980a-466">이 버전의 Java 클라이언트 웹소켓은 사용 가능한 유일한 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-466">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="0980a-467">Java 클라이언트에서 전송은 `withTransport` `HttpHubConnectionBuilder`의 메서드를 사용하여 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-467">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="0980a-468">Java 클라이언트는 기본적으로 WebSockets 전송을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-468">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="0980a-469">SignalR Java 클라이언트는 아직 전송 대체를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-469">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="0980a-470">베어러 인증 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-470">Configure bearer authentication</span></span>

<span data-ttu-id="0980a-471">SignalR 요청과 함께 인증 데이터를 `AccessTokenProvider` 제공하려면`accessTokenFactory` 옵션(JavaScript)을 사용하여 원하는 액세스 토큰을 반환하는 함수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-471">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="0980a-472">.NET 클라이언트에서 이 액세스 토큰은 HTTP "보유자 인증" 토큰으로 `Authorization` 전달됩니다(유형이 `Bearer`있는 헤더 사용).</span><span class="sxs-lookup"><span data-stu-id="0980a-472">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="0980a-473">JavaScript 클라이언트에서 액세스 토큰은 브라우저 API가 헤더를 적용하는 기능을 제한하는 경우(특히 서버 전송 이벤트 및 WebSockets 요청)를 **제외하고** 베어러 토큰으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-473">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="0980a-474">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 `access_token`제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-474">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="0980a-475">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 다음 에서 `WithUrl`옵션 대리자를 사용하여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-475">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="0980a-476">JavaScript 클라이언트에서 액세스 토큰은 다음 의 `accessTokenFactory` `withUrl`옵션 개체에 필드를 설정하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-476">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="0980a-477">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder에](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)액세스 토큰 팩터리를 제공하여 인증에 사용할 베어러 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-477">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="0980a-478">[와 사용AccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) [RxJava](https://github.com/ReactiveX/RxJava) [단일\<문자열>. ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="0980a-478">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="0980a-479">[Single.defer를](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)호출하면 클라이언트에 대한 액세스 토큰을 생성하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-479">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="0980a-480">시간 시간 및 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-480">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="0980a-481">시간 시간 및 유지 동작을 구성하기 위한 추가 `HubConnection` 옵션은 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-481">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="0980a-482">.NET</span><span class="sxs-lookup"><span data-stu-id="0980a-482">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0980a-483">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-483">Option</span></span> | <span data-ttu-id="0980a-484">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-484">Default value</span></span> | <span data-ttu-id="0980a-485">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-485">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0980a-486">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-486">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0980a-487">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-487">Timeout for server activity.</span></span> <span data-ttu-id="0980a-488">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `Closed` 것으로`onclose` 간주하고 JavaScript에서 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-488">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0980a-489">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-489">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0980a-490">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-490">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0980a-491">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-491">15 seconds</span></span> | <span data-ttu-id="0980a-492">초기 서버 핸드셰이크에 대한 시간 시간 입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-492">Timeout for initial server handshake.</span></span> <span data-ttu-id="0980a-493">서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` `onclose` JavaScript에서 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-493">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0980a-494">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-494">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0980a-495">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-495">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0980a-496">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-496">15 seconds</span></span> | <span data-ttu-id="0980a-497">클라이언트가 ping 메시지를 보내는 간격을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-497">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0980a-498">클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-498">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0980a-499">클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-499">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="0980a-500">.NET 클라이언트에서 시간 지정 값은 `TimeSpan` 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-500">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="0980a-501">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0980a-501">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0980a-502">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-502">Option</span></span> | <span data-ttu-id="0980a-503">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-503">Default value</span></span> | <span data-ttu-id="0980a-504">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-504">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0980a-505">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-505">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0980a-506">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-506">Timeout for server activity.</span></span> <span data-ttu-id="0980a-507">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onclose` 것으로 간주하고 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-507">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0980a-508">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-508">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0980a-509">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-509">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="0980a-510">15초(15,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-510">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0980a-511">클라이언트가 ping 메시지를 보내는 간격을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-511">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0980a-512">클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-512">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0980a-513">클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-513">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0980a-514">Java</span><span class="sxs-lookup"><span data-stu-id="0980a-514">Java</span></span>](#tab/java)

| <span data-ttu-id="0980a-515">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-515">Option</span></span> | <span data-ttu-id="0980a-516">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-516">Default value</span></span> | <span data-ttu-id="0980a-517">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-517">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0980a-518">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0980a-518">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0980a-519">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-519">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0980a-520">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-520">Timeout for server activity.</span></span> <span data-ttu-id="0980a-521">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onClose` 것으로 간주하고 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-521">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0980a-522">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-522">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0980a-523">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-523">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0980a-524">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-524">15 seconds</span></span> | <span data-ttu-id="0980a-525">초기 서버 핸드셰이크에 대한 시간 시간 입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-525">Timeout for initial server handshake.</span></span> <span data-ttu-id="0980a-526">서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-526">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0980a-527">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-527">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0980a-528">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-528">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="0980a-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="0980a-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="0980a-530">15초(15,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-530">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0980a-531">클라이언트가 ping 메시지를 보내는 간격을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-531">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0980a-532">클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-532">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0980a-533">클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-533">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="0980a-534">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-534">Configure additional options</span></span>

<span data-ttu-id="0980a-535">`WithUrl` 추가 옵션은 Java 클라이언트의`withUrl` 다양한 구성 API에 `HubConnectionBuilder` 있는 (JavaScript) 메서드에서 구성할 `HttpHubConnectionBuilder` 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-535">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="0980a-536">.NET</span><span class="sxs-lookup"><span data-stu-id="0980a-536">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0980a-537">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-537">.NET Option</span></span> |  <span data-ttu-id="0980a-538">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-538">Default value</span></span> | <span data-ttu-id="0980a-539">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-539">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="0980a-540">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-540">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="0980a-541">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-541">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0980a-542">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-542">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0980a-543">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-543">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="0980a-544">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-544">Empty</span></span> | <span data-ttu-id="0980a-545">요청을 인증하기 위해 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-545">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="0980a-546">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-546">Empty</span></span> | <span data-ttu-id="0980a-547">모든 HTTP 요청과 함께 보낼 HTTP 쿠키 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-547">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="0980a-548">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-548">Empty</span></span> | <span data-ttu-id="0980a-549">모든 HTTP 요청과 함께 보낼 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-549">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="0980a-550">5초</span><span class="sxs-lookup"><span data-stu-id="0980a-550">5 seconds</span></span> | <span data-ttu-id="0980a-551">웹 소켓만.</span><span class="sxs-lookup"><span data-stu-id="0980a-551">WebSockets only.</span></span> <span data-ttu-id="0980a-552">서버가 닫기 요청을 승인할 때까지 클라이언트가 닫은 후 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-552">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="0980a-553">이 시간 내에 서버가 닫기를 승인하지 않으면 클라이언트가 연결 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-553">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="0980a-554">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-554">Empty</span></span> | <span data-ttu-id="0980a-555">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-555">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="0980a-556">HTTP 요청을 보내는 데 사용되는 대리자를 구성하거나 교체하는 데 사용할 수 있는 `HttpMessageHandler` 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-556">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="0980a-557">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-557">Not used for WebSocket connections.</span></span> <span data-ttu-id="0980a-558">이 대리자는 null이 아닌 값을 반환해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-558">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="0980a-559">기본값의 설정을 수정하여 반환하거나 새 `HttpMessageHandler` 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-559">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="0980a-560">**처리기를 교체할 때 제공된 처리기에서 유지하려는 설정을 복사해야 하며, 그렇지 않으면 구성된 옵션(예: 쿠키 및 헤더)이 새 처리기에 적용되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-560">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="0980a-561">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-561">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="0980a-562">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 보내도록 이 부울을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-562">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="0980a-563">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-563">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="0980a-564">추가 WebSocket 옵션을 구성하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-564">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="0980a-565">옵션을 구성하는 데 사용할 수 있는 [ClientWebSocketOptions의](/dotnet/api/system.net.websockets.clientwebsocketoptions) 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-565">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="0980a-566">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0980a-566">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0980a-567">자바 스크립트 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-567">JavaScript Option</span></span> | <span data-ttu-id="0980a-568">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-568">Default Value</span></span> | <span data-ttu-id="0980a-569">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-569">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="0980a-570">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-570">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="0980a-571">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-571">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0980a-572">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-572">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0980a-573">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-573">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0980a-574">Java</span><span class="sxs-lookup"><span data-stu-id="0980a-574">Java</span></span>](#tab/java)

| <span data-ttu-id="0980a-575">자바 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-575">Java Option</span></span> | <span data-ttu-id="0980a-576">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-576">Default Value</span></span> | <span data-ttu-id="0980a-577">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-577">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="0980a-578">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-578">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="0980a-579">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-579">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0980a-580">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-580">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0980a-581">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-581">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="0980a-582">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="0980a-582">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="0980a-583">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-583">Empty</span></span> | <span data-ttu-id="0980a-584">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-584">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="0980a-585">.NET 클라이언트에서 이러한 옵션은 다음에 제공된 옵션 대리자에서 수정할 `WithUrl`수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-585">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="0980a-586">자바 스크립트 클라이언트에서 이러한 옵션은 `withUrl`다음에 제공되는 자바 스크립트 개체에서 제공 할 수 있습니다 .</span><span class="sxs-lookup"><span data-stu-id="0980a-586">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="0980a-587">Java 클라이언트에서 이러한 옵션은 `HttpHubConnectionBuilder` 반환된 메서드로 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="0980a-587">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="0980a-588">추가 자료</span><span class="sxs-lookup"><span data-stu-id="0980a-588">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="0980a-589">JSON/메시지팩 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-589">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="0980a-590">ASP.NET 코어 시그널R는 메시지 인코딩을 위한 두 가지 프로토콜을 지원합니다: [JSON](https://www.json.org/) 및 [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="0980a-590">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="0980a-591">각 프로토콜에는 직렬화 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-591">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="0980a-592">JSON 직렬화는 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 `Startup.ConfigureServices` 수 있습니다., 메서드에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 후 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-592">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0980a-593">메서드는 `AddJsonProtocol` 개체를 받는 `options` 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-593">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="0980a-594">해당 개체의 [페이로드 SerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 인수의 직렬화를 구성하고 값을 반환하는 데 사용할 수 있는 JSON.NET `JsonSerializerSettings` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-594">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="0980a-595">자세한 내용은 JSON.NET [문서를](https://www.newtonsoft.com/json/help/html/Introduction.htm)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-595">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="0980a-596">예를 들어, 기본 "camelCase" 이름 대신 "PascalCase" 속성 이름을 사용하도록 serializer를 구성하려면 `Startup.ConfigureServices`다음 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-596">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="0980a-597">.NET 클라이언트에서 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-597">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="0980a-598">확장 `Microsoft.Extensions.DependencyInjection` 메서드를 해결하려면 네임스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-598">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="0980a-599">현재 는 JavaScript 클라이언트에서 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-599">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="0980a-600">메시지팩 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-600">MessagePack serialization options</span></span>

<span data-ttu-id="0980a-601">메시지팩 일련화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대한 대리자를 제공하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-601">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="0980a-602">자세한 내용은 [SignalR의 메시지 팩을](xref:signalr/messagepackhubprotocol) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-602">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="0980a-603">현재 는 JavaScript 클라이언트에서 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-603">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="0980a-604">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-604">Configure server options</span></span>

<span data-ttu-id="0980a-605">다음 표는 SignalR 허브를 구성하는 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-605">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="0980a-606">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-606">Option</span></span> | <span data-ttu-id="0980a-607">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-607">Default Value</span></span> | <span data-ttu-id="0980a-608">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="0980a-609">30초</span><span class="sxs-lookup"><span data-stu-id="0980a-609">30 seconds</span></span> | <span data-ttu-id="0980a-610">서버는 이 간격으로 클라이언트가 연결이 끊어진 것으로 간주합니다(유지-활성 포함)가 수신되지 않은 경우 클라이언트가 연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-610">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="0980a-611">클라이언트가 실제로 연결이 끊어진 것으로 표시되는 데 이 시간 초과 간격보다 더 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-611">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="0980a-612">권장 값은 값의 `KeepAliveInterval` 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-612">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="0980a-613">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-613">15 seconds</span></span> | <span data-ttu-id="0980a-614">클라이언트가 이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-614">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0980a-615">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-615">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0980a-616">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-616">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0980a-617">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-617">15 seconds</span></span> | <span data-ttu-id="0980a-618">서버가 이 간격 내에 메시지를 보내지 않은 경우 연결을 열어 두기 위해 ping 메시지가 자동으로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-618">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0980a-619">`KeepAliveInterval`변경하면 클라이언트의 `ServerTimeout` / `serverTimeoutInMilliseconds` 설정을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-619">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0980a-620">`ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 값의 `KeepAliveInterval` 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-620">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0980a-621">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="0980a-621">All installed protocols</span></span> | <span data-ttu-id="0980a-622">이 허브에서 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-622">Protocols supported by this hub.</span></span> <span data-ttu-id="0980a-623">기본적으로 서버에 등록된 모든 프로토콜은 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대한 특정 프로토콜을 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-623">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0980a-624">자세한 `true`예외 메시지가 Hub 메서드에서 예외를 throw하면 클라이언트에 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-624">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0980a-625">기본값은 `false`은 예외 메시지에 중요한 정보가 포함될 수 있으므로 입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-625">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="0980a-626">옵션에서 호출에 대한 옵션 대리자를 제공하여 모든 `AddSignalR` 허브에 대해 옵션을 구성할 수 있습니다. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="0980a-626">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="0980a-627">단일 허브에 대한 옵션은 제공된 전역 `AddSignalR` 옵션을 재정의하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>다음을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-627">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="0980a-628">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-628">Advanced HTTP configuration options</span></span>

<span data-ttu-id="0980a-629">전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하는 데 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-629">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="0980a-630">이러한 옵션은 [에서 MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 대리자를 전달하여 `Startup.Configure`구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-630">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="0980a-631">다음 표는 코어 SignalR의 고급 http 옵션을 구성하기 ASP.NET 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-631">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="0980a-632">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-632">Option</span></span> | <span data-ttu-id="0980a-633">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-633">Default Value</span></span> | <span data-ttu-id="0980a-634">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-634">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="0980a-635">32KB</span><span class="sxs-lookup"><span data-stu-id="0980a-635">32 KB</span></span> | <span data-ttu-id="0980a-636">서버가 버퍼링하는 클라이언트에서 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-636">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="0980a-637">이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 소비에 부정적인 영향을 미칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-637">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="0980a-638">Hub 클래스에 `Authorize` 적용된 특성에서 데이터가 자동으로 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-638">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="0980a-639">클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-639">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="0980a-640">32KB</span><span class="sxs-lookup"><span data-stu-id="0980a-640">32 KB</span></span> | <span data-ttu-id="0980a-641">서버가 버퍼링하는 앱에서 보내는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-641">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="0980a-642">이 값을 늘리면 서버가 더 큰 메시지를 보낼 수 있지만 메모리 소비에 부정적인 영향을 미칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-642">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="0980a-643">모든 전송이 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-643">All Transports are enabled.</span></span> | <span data-ttu-id="0980a-644">비트는 클라이언트가 `HttpTransportType` 연결하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 열거형 플래그를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-644">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="0980a-645">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-645">See below.</span></span> | <span data-ttu-id="0980a-646">긴 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-646">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="0980a-647">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-647">See below.</span></span> | <span data-ttu-id="0980a-648">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-648">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="0980a-649">Long Polling 전송에는 속성을 사용하여 구성할 `LongPolling` 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-649">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="0980a-650">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-650">Option</span></span> | <span data-ttu-id="0980a-651">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-651">Default Value</span></span> | <span data-ttu-id="0980a-652">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-652">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="0980a-653">90초</span><span class="sxs-lookup"><span data-stu-id="0980a-653">90 seconds</span></span> | <span data-ttu-id="0980a-654">단일 폴링 요청을 종료하기 전에 서버가 메시지가 클라이언트에 전송될 때까지 기다리는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-654">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="0980a-655">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-655">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="0980a-656">WebSocket 전송에는 `WebSockets` 속성을 사용하여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-656">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="0980a-657">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-657">Option</span></span> | <span data-ttu-id="0980a-658">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-658">Default Value</span></span> | <span data-ttu-id="0980a-659">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-659">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="0980a-660">5초</span><span class="sxs-lookup"><span data-stu-id="0980a-660">5 seconds</span></span> | <span data-ttu-id="0980a-661">서버가 닫히면 클라이언트가 이 시간 간격 내에 닫히지 않으면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-661">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="0980a-662">헤더를 사용자 지정 값으로 `Sec-WebSocket-Protocol` 설정하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-662">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="0980a-663">대리자는 클라이언트가 요청한 값을 입력으로 수신하고 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-663">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="0980a-664">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-664">Configure client options</span></span>

<span data-ttu-id="0980a-665">클라이언트 옵션은 `HubConnectionBuilder` 유형(.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-665">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="0980a-666">Java 클라이언트에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 빌더 구성 옵션과 그 자체가 `HubConnection` 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-666">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="0980a-667">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-667">Configure logging</span></span>

<span data-ttu-id="0980a-668">로깅은 `ConfigureLogging` 메서드를 사용하여 .NET 클라이언트에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-668">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="0980a-669">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-669">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="0980a-670">자세한 내용은 [ASP.NET 핵심 로그인](xref:fundamentals/logging/index) 설명서를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-670">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0980a-671">로깅 공급자를 등록하려면 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-671">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="0980a-672">전체 목록은 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-672">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="0980a-673">예를 들어 콘솔 로깅을 `Microsoft.Extensions.Logging.Console` 사용하려면 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-673">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="0980a-674">확장 `AddConsole` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-674">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="0980a-675">JavaScript 클라이언트에서 비슷한 `configureLogging` 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-675">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="0980a-676">생성할 `LogLevel` 로그 메시지의 최소 수준을 나타내는 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-676">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="0980a-677">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-677">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="0980a-678">로깅을 완전히 `signalR.LogLevel.None` 사용하지 `configureLogging` 않으려면 메서드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-678">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="0980a-679">로깅에 대한 자세한 내용은 [SignalR 진단 문서를](xref:signalr/diagnostics)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-679">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="0980a-680">SignalR Java 클라이언트는 로깅을 위해 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-680">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="0980a-681">라이브러리의 사용자가 특정 로깅 종속성을 가져와 고유한 특정 로깅 구현을 선택할 수 있는 고급 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-681">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="0980a-682">다음 코드 조각은 SignalR `java.util.logging` Java 클라이언트에서 사용하는 방법을 보여 주며 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-682">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="0980a-683">종속성에 로깅을 구성하지 않은 경우 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음 로거를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-683">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="0980a-684">이 무시 해도 안전 하 게 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-684">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="0980a-685">허용된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-685">Configure allowed transports</span></span>

<span data-ttu-id="0980a-686">SignalR에서 사용하는 전송은 `WithUrl` 호출(자바스크립트)에서`withUrl` 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-686">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="0980a-687">비트-OR값은 클라이언트가 `HttpTransportType` 지정된 전송만 사용하도록 제한하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-687">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="0980a-688">모든 전송은 기본적으로 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-688">All transports are enabled by default.</span></span>

<span data-ttu-id="0980a-689">예를 들어 서버 전송 이벤트 전송을 사용하지 않도록 설정하지만 WebSocket 및 긴 폴링 연결을 허용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-689">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="0980a-690">JavaScript 클라이언트에서 전송은 `transport` `withUrl`다음에 제공된 옵션 개체에 필드를 설정하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-690">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="0980a-691">이 버전의 Java 클라이언트 웹소켓은 사용 가능한 유일한 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-691">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="0980a-692">베어러 인증 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-692">Configure bearer authentication</span></span>

<span data-ttu-id="0980a-693">SignalR 요청과 함께 인증 데이터를 `AccessTokenProvider` 제공하려면`accessTokenFactory` 옵션(JavaScript)을 사용하여 원하는 액세스 토큰을 반환하는 함수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-693">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="0980a-694">.NET 클라이언트에서 이 액세스 토큰은 HTTP "보유자 인증" 토큰으로 `Authorization` 전달됩니다(유형이 `Bearer`있는 헤더 사용).</span><span class="sxs-lookup"><span data-stu-id="0980a-694">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="0980a-695">JavaScript 클라이언트에서 액세스 토큰은 브라우저 API가 헤더를 적용하는 기능을 제한하는 경우(특히 서버 전송 이벤트 및 WebSockets 요청)를 **제외하고** 베어러 토큰으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-695">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="0980a-696">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 `access_token`제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-696">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="0980a-697">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 다음 에서 `WithUrl`옵션 대리자를 사용하여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-697">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="0980a-698">JavaScript 클라이언트에서 액세스 토큰은 다음 의 `accessTokenFactory` `withUrl`옵션 개체에 필드를 설정하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-698">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="0980a-699">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder에](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)액세스 토큰 팩터리를 제공하여 인증에 사용할 베어러 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-699">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="0980a-700">[와 사용AccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) [RxJava](https://github.com/ReactiveX/RxJava) [단일\<문자열>. ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="0980a-700">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="0980a-701">[Single.defer를](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)호출하면 클라이언트에 대한 액세스 토큰을 생성하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-701">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="0980a-702">시간 시간 및 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-702">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="0980a-703">시간 시간 및 유지 동작을 구성하기 위한 추가 `HubConnection` 옵션은 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-703">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="0980a-704">.NET</span><span class="sxs-lookup"><span data-stu-id="0980a-704">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0980a-705">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-705">Option</span></span> | <span data-ttu-id="0980a-706">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-706">Default value</span></span> | <span data-ttu-id="0980a-707">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-707">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0980a-708">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-708">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0980a-709">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-709">Timeout for server activity.</span></span> <span data-ttu-id="0980a-710">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `Closed` 것으로`onclose` 간주하고 JavaScript에서 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-710">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0980a-711">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-711">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0980a-712">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-712">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0980a-713">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-713">15 seconds</span></span> | <span data-ttu-id="0980a-714">초기 서버 핸드셰이크에 대한 시간 시간 입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-714">Timeout for initial server handshake.</span></span> <span data-ttu-id="0980a-715">서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` `onclose` JavaScript에서 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-715">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0980a-716">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-716">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0980a-717">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-717">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0980a-718">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-718">15 seconds</span></span> | <span data-ttu-id="0980a-719">클라이언트가 ping 메시지를 보내는 간격을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-719">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0980a-720">클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-720">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0980a-721">클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-721">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="0980a-722">.NET 클라이언트에서 시간 지정 값은 `TimeSpan` 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-722">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="0980a-723">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0980a-723">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0980a-724">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-724">Option</span></span> | <span data-ttu-id="0980a-725">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-725">Default value</span></span> | <span data-ttu-id="0980a-726">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-726">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0980a-727">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-727">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0980a-728">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-728">Timeout for server activity.</span></span> <span data-ttu-id="0980a-729">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onclose` 것으로 간주하고 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-729">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0980a-730">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-730">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0980a-731">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-731">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="0980a-732">15초(15,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-732">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0980a-733">클라이언트가 ping 메시지를 보내는 간격을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-733">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0980a-734">클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-734">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0980a-735">클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-735">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0980a-736">Java</span><span class="sxs-lookup"><span data-stu-id="0980a-736">Java</span></span>](#tab/java)

| <span data-ttu-id="0980a-737">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-737">Option</span></span> | <span data-ttu-id="0980a-738">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-738">Default value</span></span> | <span data-ttu-id="0980a-739">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-739">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0980a-740">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0980a-740">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0980a-741">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0980a-742">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-742">Timeout for server activity.</span></span> <span data-ttu-id="0980a-743">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onClose` 것으로 간주하고 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0980a-744">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0980a-745">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0980a-746">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-746">15 seconds</span></span> | <span data-ttu-id="0980a-747">초기 서버 핸드셰이크에 대한 시간 시간 입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="0980a-748">서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0980a-749">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0980a-750">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-750">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="0980a-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="0980a-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="0980a-752">15초(15,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-752">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0980a-753">클라이언트가 ping 메시지를 보내는 간격을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-753">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0980a-754">클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-754">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0980a-755">클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-755">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="0980a-756">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-756">Configure additional options</span></span>

<span data-ttu-id="0980a-757">`WithUrl` 추가 옵션은 Java 클라이언트의`withUrl` 다양한 구성 API에 `HubConnectionBuilder` 있는 (JavaScript) 메서드에서 구성할 `HttpHubConnectionBuilder` 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-757">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="0980a-758">.NET</span><span class="sxs-lookup"><span data-stu-id="0980a-758">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0980a-759">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-759">.NET Option</span></span> |  <span data-ttu-id="0980a-760">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-760">Default value</span></span> | <span data-ttu-id="0980a-761">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-761">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="0980a-762">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-762">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="0980a-763">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-763">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0980a-764">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-764">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0980a-765">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-765">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="0980a-766">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-766">Empty</span></span> | <span data-ttu-id="0980a-767">요청을 인증하기 위해 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-767">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="0980a-768">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-768">Empty</span></span> | <span data-ttu-id="0980a-769">모든 HTTP 요청과 함께 보낼 HTTP 쿠키 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-769">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="0980a-770">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-770">Empty</span></span> | <span data-ttu-id="0980a-771">모든 HTTP 요청과 함께 보낼 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-771">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="0980a-772">5초</span><span class="sxs-lookup"><span data-stu-id="0980a-772">5 seconds</span></span> | <span data-ttu-id="0980a-773">웹 소켓만.</span><span class="sxs-lookup"><span data-stu-id="0980a-773">WebSockets only.</span></span> <span data-ttu-id="0980a-774">서버가 닫기 요청을 승인할 때까지 클라이언트가 닫은 후 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-774">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="0980a-775">이 시간 내에 서버가 닫기를 승인하지 않으면 클라이언트가 연결 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-775">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="0980a-776">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-776">Empty</span></span> | <span data-ttu-id="0980a-777">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-777">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="0980a-778">HTTP 요청을 보내는 데 사용되는 대리자를 구성하거나 교체하는 데 사용할 수 있는 `HttpMessageHandler` 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-778">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="0980a-779">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-779">Not used for WebSocket connections.</span></span> <span data-ttu-id="0980a-780">이 대리자는 null이 아닌 값을 반환해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-780">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="0980a-781">기본값의 설정을 수정하여 반환하거나 새 `HttpMessageHandler` 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-781">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="0980a-782">**처리기를 교체할 때 제공된 처리기에서 유지하려는 설정을 복사해야 하며, 그렇지 않으면 구성된 옵션(예: 쿠키 및 헤더)이 새 처리기에 적용되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-782">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="0980a-783">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-783">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="0980a-784">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 보내도록 이 부울을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-784">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="0980a-785">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-785">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="0980a-786">추가 WebSocket 옵션을 구성하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-786">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="0980a-787">옵션을 구성하는 데 사용할 수 있는 [ClientWebSocketOptions의](/dotnet/api/system.net.websockets.clientwebsocketoptions) 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-787">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="0980a-788">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0980a-788">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0980a-789">자바 스크립트 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-789">JavaScript Option</span></span> | <span data-ttu-id="0980a-790">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-790">Default Value</span></span> | <span data-ttu-id="0980a-791">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-791">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="0980a-792">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-792">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="0980a-793">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-793">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0980a-794">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-794">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0980a-795">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-795">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0980a-796">Java</span><span class="sxs-lookup"><span data-stu-id="0980a-796">Java</span></span>](#tab/java)

| <span data-ttu-id="0980a-797">자바 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-797">Java Option</span></span> | <span data-ttu-id="0980a-798">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-798">Default Value</span></span> | <span data-ttu-id="0980a-799">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-799">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="0980a-800">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-800">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="0980a-801">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-801">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0980a-802">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-802">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0980a-803">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-803">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="0980a-804">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="0980a-804">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="0980a-805">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-805">Empty</span></span> | <span data-ttu-id="0980a-806">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="0980a-807">.NET 클라이언트에서 이러한 옵션은 다음에 제공된 옵션 대리자에서 수정할 `WithUrl`수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-807">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="0980a-808">자바 스크립트 클라이언트에서 이러한 옵션은 `withUrl`다음에 제공되는 자바 스크립트 개체에서 제공 할 수 있습니다 .</span><span class="sxs-lookup"><span data-stu-id="0980a-808">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="0980a-809">Java 클라이언트에서 이러한 옵션은 `HttpHubConnectionBuilder` 반환된 메서드로 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="0980a-809">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="0980a-810">추가 자료</span><span class="sxs-lookup"><span data-stu-id="0980a-810">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="0980a-811">JSON/메시지팩 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-811">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="0980a-812">ASP.NET 코어 시그널R는 메시지 인코딩을 위한 두 가지 프로토콜을 지원합니다: [JSON](https://www.json.org/) 및 [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="0980a-812">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="0980a-813">각 프로토콜에는 직렬화 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-813">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="0980a-814">JSON 직렬화는 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 `Startup.ConfigureServices` 수 있습니다., 메서드에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 후 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-814">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0980a-815">메서드는 `AddJsonProtocol` 개체를 받는 `options` 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-815">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="0980a-816">해당 개체의 [페이로드 SerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 인수의 직렬화를 구성하고 값을 반환하는 데 사용할 수 있는 JSON.NET `JsonSerializerSettings` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-816">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="0980a-817">자세한 내용은 JSON.NET [문서를](https://www.newtonsoft.com/json/help/html/Introduction.htm)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-817">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="0980a-818">예를 들어, 기본 "camelCase" 이름 대신 "PascalCase" 속성 이름을 사용하도록 serializer를 구성하려면 `Startup.ConfigureServices`다음 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-818">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="0980a-819">.NET 클라이언트에서 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-819">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="0980a-820">확장 `Microsoft.Extensions.DependencyInjection` 메서드를 해결하려면 네임스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-820">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="0980a-821">현재 는 JavaScript 클라이언트에서 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-821">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="0980a-822">메시지팩 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-822">MessagePack serialization options</span></span>

<span data-ttu-id="0980a-823">메시지팩 일련화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대한 대리자를 제공하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-823">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="0980a-824">자세한 내용은 [SignalR의 메시지 팩을](xref:signalr/messagepackhubprotocol) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-824">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="0980a-825">현재 는 JavaScript 클라이언트에서 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-825">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="0980a-826">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-826">Configure server options</span></span>

<span data-ttu-id="0980a-827">다음 표는 SignalR 허브를 구성하는 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-827">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="0980a-828">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-828">Option</span></span> | <span data-ttu-id="0980a-829">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-829">Default Value</span></span> | <span data-ttu-id="0980a-830">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-830">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="0980a-831">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-831">15 seconds</span></span> | <span data-ttu-id="0980a-832">클라이언트가 이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-832">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0980a-833">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-833">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0980a-834">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-834">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0980a-835">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-835">15 seconds</span></span> | <span data-ttu-id="0980a-836">서버가 이 간격 내에 메시지를 보내지 않은 경우 연결을 열어 두기 위해 ping 메시지가 자동으로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-836">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0980a-837">`KeepAliveInterval`변경하면 클라이언트의 `ServerTimeout` / `serverTimeoutInMilliseconds` 설정을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-837">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0980a-838">`ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 값의 `KeepAliveInterval` 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-838">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0980a-839">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="0980a-839">All installed protocols</span></span> | <span data-ttu-id="0980a-840">이 허브에서 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-840">Protocols supported by this hub.</span></span> <span data-ttu-id="0980a-841">기본적으로 서버에 등록된 모든 프로토콜은 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대한 특정 프로토콜을 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-841">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0980a-842">자세한 `true`예외 메시지가 Hub 메서드에서 예외를 throw하면 클라이언트에 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-842">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0980a-843">기본값은 `false`은 예외 메시지에 중요한 정보가 포함될 수 있으므로 입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-843">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="0980a-844">옵션에서 호출에 대한 옵션 대리자를 제공하여 모든 `AddSignalR` 허브에 대해 옵션을 구성할 수 있습니다. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="0980a-844">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="0980a-845">단일 허브에 대한 옵션은 제공된 전역 `AddSignalR` 옵션을 재정의하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>다음을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-845">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="0980a-846">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-846">Advanced HTTP configuration options</span></span>

<span data-ttu-id="0980a-847">전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하는 데 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-847">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="0980a-848">이러한 옵션은 [에서 MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 대리자를 전달하여 `Startup.Configure`구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-848">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="0980a-849">다음 표는 코어 SignalR의 고급 http 옵션을 구성하기 ASP.NET 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-849">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="0980a-850">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-850">Option</span></span> | <span data-ttu-id="0980a-851">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-851">Default Value</span></span> | <span data-ttu-id="0980a-852">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-852">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="0980a-853">32KB</span><span class="sxs-lookup"><span data-stu-id="0980a-853">32 KB</span></span> | <span data-ttu-id="0980a-854">서버가 버퍼링하는 클라이언트에서 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-854">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="0980a-855">이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 소비에 부정적인 영향을 미칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-855">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="0980a-856">Hub 클래스에 `Authorize` 적용된 특성에서 데이터가 자동으로 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-856">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="0980a-857">클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-857">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="0980a-858">32KB</span><span class="sxs-lookup"><span data-stu-id="0980a-858">32 KB</span></span> | <span data-ttu-id="0980a-859">서버가 버퍼링하는 앱에서 보내는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-859">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="0980a-860">이 값을 늘리면 서버가 더 큰 메시지를 보낼 수 있지만 메모리 소비에 부정적인 영향을 미칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-860">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="0980a-861">모든 전송이 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-861">All Transports are enabled.</span></span> | <span data-ttu-id="0980a-862">비트는 클라이언트가 `HttpTransportType` 연결하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 열거형 플래그를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-862">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="0980a-863">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-863">See below.</span></span> | <span data-ttu-id="0980a-864">긴 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-864">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="0980a-865">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-865">See below.</span></span> | <span data-ttu-id="0980a-866">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-866">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="0980a-867">Long Polling 전송에는 속성을 사용하여 구성할 `LongPolling` 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-867">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="0980a-868">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-868">Option</span></span> | <span data-ttu-id="0980a-869">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-869">Default Value</span></span> | <span data-ttu-id="0980a-870">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-870">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="0980a-871">90초</span><span class="sxs-lookup"><span data-stu-id="0980a-871">90 seconds</span></span> | <span data-ttu-id="0980a-872">단일 폴링 요청을 종료하기 전에 서버가 메시지가 클라이언트에 전송될 때까지 기다리는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-872">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="0980a-873">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-873">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="0980a-874">WebSocket 전송에는 `WebSockets` 속성을 사용하여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-874">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="0980a-875">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-875">Option</span></span> | <span data-ttu-id="0980a-876">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-876">Default Value</span></span> | <span data-ttu-id="0980a-877">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-877">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="0980a-878">5초</span><span class="sxs-lookup"><span data-stu-id="0980a-878">5 seconds</span></span> | <span data-ttu-id="0980a-879">서버가 닫히면 클라이언트가 이 시간 간격 내에 닫히지 않으면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-879">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="0980a-880">헤더를 사용자 지정 값으로 `Sec-WebSocket-Protocol` 설정하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-880">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="0980a-881">대리자는 클라이언트가 요청한 값을 입력으로 수신하고 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-881">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="0980a-882">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-882">Configure client options</span></span>

<span data-ttu-id="0980a-883">클라이언트 옵션은 `HubConnectionBuilder` 유형(.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-883">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="0980a-884">Java 클라이언트에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 빌더 구성 옵션과 그 자체가 `HubConnection` 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-884">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="0980a-885">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-885">Configure logging</span></span>

<span data-ttu-id="0980a-886">로깅은 `ConfigureLogging` 메서드를 사용하여 .NET 클라이언트에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-886">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="0980a-887">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-887">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="0980a-888">자세한 내용은 [ASP.NET 핵심 로그인](xref:fundamentals/logging/index) 설명서를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-888">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0980a-889">로깅 공급자를 등록하려면 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-889">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="0980a-890">전체 목록은 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-890">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="0980a-891">예를 들어 콘솔 로깅을 `Microsoft.Extensions.Logging.Console` 사용하려면 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-891">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="0980a-892">확장 `AddConsole` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-892">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="0980a-893">JavaScript 클라이언트에서 비슷한 `configureLogging` 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-893">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="0980a-894">생성할 `LogLevel` 로그 메시지의 최소 수준을 나타내는 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-894">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="0980a-895">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-895">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="0980a-896">로깅을 완전히 `signalR.LogLevel.None` 사용하지 `configureLogging` 않으려면 메서드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-896">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="0980a-897">로깅에 대한 자세한 내용은 [SignalR 진단 문서를](xref:signalr/diagnostics)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-897">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="0980a-898">SignalR Java 클라이언트는 로깅을 위해 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-898">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="0980a-899">라이브러리의 사용자가 특정 로깅 종속성을 가져와 고유한 특정 로깅 구현을 선택할 수 있는 고급 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-899">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="0980a-900">다음 코드 조각은 SignalR `java.util.logging` Java 클라이언트에서 사용하는 방법을 보여 주며 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-900">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="0980a-901">종속성에 로깅을 구성하지 않은 경우 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음 로거를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-901">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="0980a-902">이 무시 해도 안전 하 게 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-902">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="0980a-903">허용된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-903">Configure allowed transports</span></span>

<span data-ttu-id="0980a-904">SignalR에서 사용하는 전송은 `WithUrl` 호출(자바스크립트)에서`withUrl` 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-904">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="0980a-905">비트-OR값은 클라이언트가 `HttpTransportType` 지정된 전송만 사용하도록 제한하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-905">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="0980a-906">모든 전송은 기본적으로 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-906">All transports are enabled by default.</span></span>

<span data-ttu-id="0980a-907">예를 들어 서버 전송 이벤트 전송을 사용하지 않도록 설정하지만 WebSocket 및 긴 폴링 연결을 허용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-907">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="0980a-908">JavaScript 클라이언트에서 전송은 `transport` `withUrl`다음에 제공된 옵션 개체에 필드를 설정하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-908">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="0980a-909">베어러 인증 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-909">Configure bearer authentication</span></span>

<span data-ttu-id="0980a-910">SignalR 요청과 함께 인증 데이터를 `AccessTokenProvider` 제공하려면`accessTokenFactory` 옵션(JavaScript)을 사용하여 원하는 액세스 토큰을 반환하는 함수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-910">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="0980a-911">.NET 클라이언트에서 이 액세스 토큰은 HTTP "보유자 인증" 토큰으로 `Authorization` 전달됩니다(유형이 `Bearer`있는 헤더 사용).</span><span class="sxs-lookup"><span data-stu-id="0980a-911">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="0980a-912">JavaScript 클라이언트에서 액세스 토큰은 브라우저 API가 헤더를 적용하는 기능을 제한하는 경우(특히 서버 전송 이벤트 및 WebSockets 요청)를 **제외하고** 베어러 토큰으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-912">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="0980a-913">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 `access_token`제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-913">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="0980a-914">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 다음 에서 `WithUrl`옵션 대리자를 사용하여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-914">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="0980a-915">JavaScript 클라이언트에서 액세스 토큰은 다음 의 `accessTokenFactory` `withUrl`옵션 개체에 필드를 설정하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-915">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="0980a-916">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder에](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)액세스 토큰 팩터리를 제공하여 인증에 사용할 베어러 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-916">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="0980a-917">[와 사용AccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) [RxJava](https://github.com/ReactiveX/RxJava) [단일\<문자열>. ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="0980a-917">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="0980a-918">[Single.defer를](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)호출하면 클라이언트에 대한 액세스 토큰을 생성하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-918">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="0980a-919">시간 시간 및 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="0980a-919">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="0980a-920">시간 시간 및 유지 동작을 구성하기 위한 추가 `HubConnection` 옵션은 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-920">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="0980a-921">.NET</span><span class="sxs-lookup"><span data-stu-id="0980a-921">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0980a-922">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-922">Option</span></span> | <span data-ttu-id="0980a-923">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-923">Default value</span></span> | <span data-ttu-id="0980a-924">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-924">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0980a-925">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-925">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0980a-926">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-926">Timeout for server activity.</span></span> <span data-ttu-id="0980a-927">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `Closed` 것으로`onclose` 간주하고 JavaScript에서 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-927">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0980a-928">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-928">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0980a-929">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-929">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0980a-930">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-930">15 seconds</span></span> | <span data-ttu-id="0980a-931">초기 서버 핸드셰이크에 대한 시간 시간 입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-931">Timeout for initial server handshake.</span></span> <span data-ttu-id="0980a-932">서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` `onclose` JavaScript에서 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-932">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0980a-933">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-933">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0980a-934">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-934">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="0980a-935">.NET 클라이언트에서 시간 지정 값은 `TimeSpan` 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-935">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="0980a-936">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0980a-936">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0980a-937">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-937">Option</span></span> | <span data-ttu-id="0980a-938">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-938">Default value</span></span> | <span data-ttu-id="0980a-939">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-939">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0980a-940">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-940">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0980a-941">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-941">Timeout for server activity.</span></span> <span data-ttu-id="0980a-942">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onclose` 것으로 간주하고 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-942">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0980a-943">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-943">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0980a-944">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-944">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0980a-945">Java</span><span class="sxs-lookup"><span data-stu-id="0980a-945">Java</span></span>](#tab/java)

| <span data-ttu-id="0980a-946">옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-946">Option</span></span> | <span data-ttu-id="0980a-947">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-947">Default value</span></span> | <span data-ttu-id="0980a-948">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-948">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0980a-949">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0980a-949">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0980a-950">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="0980a-950">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0980a-951">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-951">Timeout for server activity.</span></span> <span data-ttu-id="0980a-952">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onClose` 것으로 간주하고 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-952">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0980a-953">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-953">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0980a-954">ping이 도착할 시간을 허용하기 위해 권장되는 값은 서버 값의 `KeepAliveInterval` 두 배 이상인 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-954">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0980a-955">15초</span><span class="sxs-lookup"><span data-stu-id="0980a-955">15 seconds</span></span> | <span data-ttu-id="0980a-956">초기 서버 핸드셰이크에 대한 시간 시간 입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-956">Timeout for initial server handshake.</span></span> <span data-ttu-id="0980a-957">서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-957">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0980a-958">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-958">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0980a-959">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="0980a-959">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="0980a-960">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-960">Configure additional options</span></span>

<span data-ttu-id="0980a-961">`WithUrl` 추가 옵션은 Java 클라이언트의`withUrl` 다양한 구성 API에 `HubConnectionBuilder` 있는 (JavaScript) 메서드에서 구성할 `HttpHubConnectionBuilder` 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-961">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="0980a-962">.NET</span><span class="sxs-lookup"><span data-stu-id="0980a-962">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0980a-963">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-963">.NET Option</span></span> |  <span data-ttu-id="0980a-964">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-964">Default value</span></span> | <span data-ttu-id="0980a-965">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-965">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="0980a-966">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-966">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="0980a-967">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-967">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0980a-968">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-968">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0980a-969">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-969">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="0980a-970">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-970">Empty</span></span> | <span data-ttu-id="0980a-971">요청을 인증하기 위해 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-971">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="0980a-972">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-972">Empty</span></span> | <span data-ttu-id="0980a-973">모든 HTTP 요청과 함께 보낼 HTTP 쿠키 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-973">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="0980a-974">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-974">Empty</span></span> | <span data-ttu-id="0980a-975">모든 HTTP 요청과 함께 보낼 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-975">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="0980a-976">5초</span><span class="sxs-lookup"><span data-stu-id="0980a-976">5 seconds</span></span> | <span data-ttu-id="0980a-977">웹 소켓만.</span><span class="sxs-lookup"><span data-stu-id="0980a-977">WebSockets only.</span></span> <span data-ttu-id="0980a-978">서버가 닫기 요청을 승인할 때까지 클라이언트가 닫은 후 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-978">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="0980a-979">이 시간 내에 서버가 닫기를 승인하지 않으면 클라이언트가 연결 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-979">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="0980a-980">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-980">Empty</span></span> | <span data-ttu-id="0980a-981">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-981">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="0980a-982">HTTP 요청을 보내는 데 사용되는 대리자를 구성하거나 교체하는 데 사용할 수 있는 `HttpMessageHandler` 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-982">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="0980a-983">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-983">Not used for WebSocket connections.</span></span> <span data-ttu-id="0980a-984">이 대리자는 null이 아닌 값을 반환해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-984">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="0980a-985">기본값의 설정을 수정하여 반환하거나 새 `HttpMessageHandler` 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-985">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="0980a-986">**처리기를 교체할 때 제공된 처리기에서 유지하려는 설정을 복사해야 하며, 그렇지 않으면 구성된 옵션(예: 쿠키 및 헤더)이 새 처리기에 적용되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-986">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="0980a-987">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-987">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="0980a-988">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 보내도록 이 부울을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-988">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="0980a-989">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-989">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="0980a-990">추가 WebSocket 옵션을 구성하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-990">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="0980a-991">옵션을 구성하는 데 사용할 수 있는 [ClientWebSocketOptions의](/dotnet/api/system.net.websockets.clientwebsocketoptions) 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-991">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="0980a-992">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0980a-992">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0980a-993">자바 스크립트 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-993">JavaScript Option</span></span> | <span data-ttu-id="0980a-994">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-994">Default Value</span></span> | <span data-ttu-id="0980a-995">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-995">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="0980a-996">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-996">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="0980a-997">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-997">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0980a-998">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-998">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0980a-999">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-999">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="0980a-1000">Java</span><span class="sxs-lookup"><span data-stu-id="0980a-1000">Java</span></span>](#tab/java)

| <span data-ttu-id="0980a-1001">자바 옵션</span><span class="sxs-lookup"><span data-stu-id="0980a-1001">Java Option</span></span> | <span data-ttu-id="0980a-1002">기본값</span><span class="sxs-lookup"><span data-stu-id="0980a-1002">Default Value</span></span> | <span data-ttu-id="0980a-1003">Description</span><span class="sxs-lookup"><span data-stu-id="0980a-1003">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="0980a-1004">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-1004">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="0980a-1005">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-1005">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0980a-1006">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="0980a-1006">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0980a-1007">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-1007">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="0980a-1008">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="0980a-1008">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="0980a-1009">Empty</span><span class="sxs-lookup"><span data-stu-id="0980a-1009">Empty</span></span> | <span data-ttu-id="0980a-1010">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-1010">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="0980a-1011">.NET 클라이언트에서 이러한 옵션은 다음에 제공된 옵션 대리자에서 수정할 `WithUrl`수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0980a-1011">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="0980a-1012">자바 스크립트 클라이언트에서 이러한 옵션은 `withUrl`다음에 제공되는 자바 스크립트 개체에서 제공 할 수 있습니다 .</span><span class="sxs-lookup"><span data-stu-id="0980a-1012">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="0980a-1013">Java 클라이언트에서 이러한 옵션은 `HttpHubConnectionBuilder` 반환된 메서드로 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="0980a-1013">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="0980a-1014">추가 자료</span><span class="sxs-lookup"><span data-stu-id="0980a-1014">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
