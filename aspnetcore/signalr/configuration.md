---
title: ASP.NET Core SignalR 구성
author: bradygaster
description: ASP.NET Core SignalR 앱을 구성 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 7e0cd952fd152ff6adb6e0a7c56214d70d3c7b86
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559002"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="830b7-103">ASP.NET Core SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="830b7-104">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="830b7-105">ASP.NET Core SignalR는 메시지 인코딩에 사용할 두 가지 프로토콜인 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="830b7-106">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="830b7-107">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="830b7-108">`AddJsonProtocol`[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 뒤에 `Startup.ConfigureServices`추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="830b7-109">메서드 `AddJsonProtocol` 는 `options` 개체를 받는 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="830b7-110">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 인수와 반환 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 값의 serialization을 구성 하는 데 사용할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="830b7-111">자세한 내용은 [system.object 설명서](/dotnet/api/system.text.json)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="830b7-112">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면에서 `Startup.ConfigureServices`다음 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="830b7-113">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="830b7-114">확장 `Microsoft.Extensions.DependencyInjection` 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="830b7-115">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="830b7-116">Newtonsoft.json로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="830b7-117">에서 `Newtonsoft.Json` `System.Text.Json`지원 되지 않는 기능이 필요한 경우 [newtonsoft.json으로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="830b7-118">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-118">MessagePack serialization options</span></span>

<span data-ttu-id="830b7-119">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="830b7-120">자세한 내용은 [MessagePack In SignalR](xref:signalr/messagepackhubprotocol) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="830b7-121">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="830b7-122">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-122">Configure server options</span></span>

<span data-ttu-id="830b7-123">다음 표에서는 SignalR hubs를 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="830b7-124">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-124">Option</span></span> | <span data-ttu-id="830b7-125">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-125">Default Value</span></span> | <span data-ttu-id="830b7-126">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="830b7-127">30초</span><span class="sxs-lookup"><span data-stu-id="830b7-127">30 seconds</span></span> | <span data-ttu-id="830b7-128">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="830b7-129">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="830b7-130">권장 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="830b7-131">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-131">15 seconds</span></span> | <span data-ttu-id="830b7-132">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="830b7-133">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="830b7-134">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="830b7-135">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-135">15 seconds</span></span> | <span data-ttu-id="830b7-136">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="830b7-137">변경 `KeepAliveInterval`하는 경우 클라이언트 `ServerTimeout` / `serverTimeoutInMilliseconds` 에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="830b7-138">`ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="830b7-139">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="830b7-139">All installed protocols</span></span> | <span data-ttu-id="830b7-140">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-140">Protocols supported by this hub.</span></span> <span data-ttu-id="830b7-141">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="830b7-142">이면 `true`허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="830b7-143">기본값은입니다 `false`. 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="830b7-144">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="830b7-145">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="830b7-146">32KB</span><span class="sxs-lookup"><span data-stu-id="830b7-146">32 KB</span></span> | <span data-ttu-id="830b7-147">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="830b7-148">에서 `AddSignalR` `Startup.ConfigureServices`호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="830b7-149">단일 허브에 대 한 옵션은에서 `AddSignalR` 제공 되는 전역 옵션을 재정의 하 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>고 다음을 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="830b7-150">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="830b7-151">전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="830b7-152">이러한 옵션은의 `Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="830b7-153">다음 표에서는 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="830b7-154">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-154">Option</span></span> | <span data-ttu-id="830b7-155">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-155">Default Value</span></span> | <span data-ttu-id="830b7-156">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="830b7-157">32KB</span><span class="sxs-lookup"><span data-stu-id="830b7-157">32 KB</span></span> | <span data-ttu-id="830b7-158">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="830b7-159">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="830b7-160">허브 클래스에 적용 된 `Authorize` 특성에서 자동으로 수집 된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="830b7-161">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="830b7-162">32KB</span><span class="sxs-lookup"><span data-stu-id="830b7-162">32 KB</span></span> | <span data-ttu-id="830b7-163">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="830b7-164">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="830b7-165">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-165">All Transports are enabled.</span></span> | <span data-ttu-id="830b7-166">클라이언트에서 연결 하는 `HttpTransportType` 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="830b7-167">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="830b7-167">See below.</span></span> | <span data-ttu-id="830b7-168">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="830b7-169">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="830b7-169">See below.</span></span> | <span data-ttu-id="830b7-170">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="830b7-171">0</span><span class="sxs-lookup"><span data-stu-id="830b7-171">0</span></span> | <span data-ttu-id="830b7-172">Negotiate 프로토콜의 최소 버전을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="830b7-173">클라이언트를 최신 버전으로 제한 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="830b7-174">긴 폴링 전송에는 속성을 `LongPolling` 사용 하 여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="830b7-175">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-175">Option</span></span> | <span data-ttu-id="830b7-176">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-176">Default Value</span></span> | <span data-ttu-id="830b7-177">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="830b7-178">90 초</span><span class="sxs-lookup"><span data-stu-id="830b7-178">90 seconds</span></span> | <span data-ttu-id="830b7-179">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="830b7-180">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="830b7-181">WebSocket 전송에는 속성을 `WebSockets` 사용 하 여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="830b7-182">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-182">Option</span></span> | <span data-ttu-id="830b7-183">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-183">Default Value</span></span> | <span data-ttu-id="830b7-184">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="830b7-185">5초</span><span class="sxs-lookup"><span data-stu-id="830b7-185">5 seconds</span></span> | <span data-ttu-id="830b7-186">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="830b7-187">`Sec-WebSocket-Protocol` 헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="830b7-188">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="830b7-189">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-189">Configure client options</span></span>

<span data-ttu-id="830b7-190">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.Net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="830b7-191">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 `HubConnection` 자체도 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="830b7-192">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-192">Configure logging</span></span>

<span data-ttu-id="830b7-193">로깅은 .NET 클라이언트에서 메서드를 `ConfigureLogging` 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="830b7-194">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="830b7-195">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="830b7-196">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="830b7-197">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="830b7-198">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="830b7-199">확장 메서드 `AddConsole` 를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="830b7-200">JavaScript 클라이언트에는 유사한 `configureLogging` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="830b7-201">생성할 로그 `LogLevel` 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="830b7-202">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="830b7-203">`LogLevel` 값 대신 로그 수준 이름을 나타내는 값을 `string` 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="830b7-204">이는 `LogLevel` 상수에 대 한 액세스 권한이 없는 환경에서 SignalR 로깅을 구성할 때 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="830b7-205">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-205">The following table lists the available log levels.</span></span> <span data-ttu-id="830b7-206">사용자가 지정 하는 `configureLogging` 값은 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="830b7-207">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="830b7-208">String</span><span class="sxs-lookup"><span data-stu-id="830b7-208">String</span></span>                      | <span data-ttu-id="830b7-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="830b7-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="830b7-210">`info`**또는**`information`</span><span class="sxs-lookup"><span data-stu-id="830b7-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="830b7-211">`warn`**또는**`warning`</span><span class="sxs-lookup"><span data-stu-id="830b7-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="830b7-212">로깅을 완전히 사용 하지 않도록 설정 `signalR.LogLevel.None` 하려면 `configureLogging` 메서드에를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="830b7-213">로깅에 대 한 자세한 내용은 [SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="830b7-214">SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="830b7-215">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="830b7-216">다음 코드 조각에서는 SignalR Java 클라이언트와 `java.util.logging` 함께를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="830b7-217">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="830b7-218">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="830b7-219">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-219">Configure allowed transports</span></span>

<span data-ttu-id="830b7-220">SignalR에서 사용 하는 전송은 JavaScript `WithUrl` `withUrl` 의 호출에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="830b7-221">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="830b7-222">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-222">All transports are enabled by default.</span></span>

<span data-ttu-id="830b7-223">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="830b7-224">JavaScript 클라이언트에서 `withUrl`다음과 같이 제공 된 options 개체의 필드 `transport` 를 설정 하 여 전송을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="830b7-225">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="830b7-226">Java 클라이언트에서의 `withTransport` 메서드를 사용 하 여 전송이 선택 됩니다. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="830b7-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="830b7-227">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="830b7-228">SignalR Java 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="830b7-229">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-229">Configure bearer authentication</span></span>

<span data-ttu-id="830b7-230">SignalR 요청과 함께 인증 데이터를 제공 하려면 ( `AccessTokenProvider` `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="830b7-231">.NET 클라이언트에서이 액세스 토큰은의 `Authorization` `Bearer`형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="830b7-232">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="830b7-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="830b7-233">이러한 경우 액세스 토큰은 쿼리 문자열 값 `access_token`으로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="830b7-234">.NET 클라이언트에서 옵션은 `AccessTokenProvider` 의 `WithUrl`옵션 대리자를 사용 하 여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="830b7-235">JavaScript 클라이언트에서 액세스 토큰은의 `accessTokenFactory` `withUrl`options 개체에 있는 필드를 설정 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="830b7-236">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="830b7-237">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [단일\<문자열>](https://reactivex.io/documentation/single.html)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="830b7-238">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="830b7-239">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="830b7-240">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="830b7-241">.NET</span><span class="sxs-lookup"><span data-stu-id="830b7-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="830b7-242">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-242">Option</span></span> | <span data-ttu-id="830b7-243">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-243">Default value</span></span> | <span data-ttu-id="830b7-244">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="830b7-245">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="830b7-246">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-246">Timeout for server activity.</span></span> <span data-ttu-id="830b7-247">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 `Closed` 간주 하`onclose` 고 이벤트 (JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="830b7-248">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="830b7-249">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="830b7-250">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-250">15 seconds</span></span> | <span data-ttu-id="830b7-251">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="830b7-252">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 (`onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="830b7-253">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="830b7-254">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="830b7-255">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-255">15 seconds</span></span> | <span data-ttu-id="830b7-256">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="830b7-257">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="830b7-258">클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="830b7-259">.NET 클라이언트에서 시간 제한 값은 값으로 `TimeSpan` 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="830b7-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="830b7-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="830b7-261">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-261">Option</span></span> | <span data-ttu-id="830b7-262">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-262">Default value</span></span> | <span data-ttu-id="830b7-263">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="830b7-264">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="830b7-265">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-265">Timeout for server activity.</span></span> <span data-ttu-id="830b7-266">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onclose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="830b7-267">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="830b7-268">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="830b7-269">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="830b7-270">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="830b7-271">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="830b7-272">클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="830b7-273">Java</span><span class="sxs-lookup"><span data-stu-id="830b7-273">Java</span></span>](#tab/java)

| <span data-ttu-id="830b7-274">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-274">Option</span></span> | <span data-ttu-id="830b7-275">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-275">Default value</span></span> | <span data-ttu-id="830b7-276">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="830b7-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="830b7-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="830b7-278">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="830b7-279">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-279">Timeout for server activity.</span></span> <span data-ttu-id="830b7-280">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onClose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="830b7-281">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="830b7-282">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="830b7-283">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-283">15 seconds</span></span> | <span data-ttu-id="830b7-284">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="830b7-285">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 `onClose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="830b7-286">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="830b7-287">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="830b7-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="830b7-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="830b7-289">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="830b7-290">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="830b7-291">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="830b7-292">클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="830b7-293">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-293">Configure additional options</span></span>

<span data-ttu-id="830b7-294">Java 클라이언트 `WithUrl` `HttpHubConnectionBuilder` 의에 있는 다양 한 구성`withUrl` api에서 또는의 `HubConnectionBuilder` (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="830b7-295">.NET</span><span class="sxs-lookup"><span data-stu-id="830b7-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="830b7-296">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-296">.NET Option</span></span> |  <span data-ttu-id="830b7-297">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-297">Default value</span></span> | <span data-ttu-id="830b7-298">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="830b7-299">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="830b7-300">협상 단계를 `true` 건너뛰려면이를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="830b7-301">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="830b7-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="830b7-302">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="830b7-303">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-303">Empty</span></span> | <span data-ttu-id="830b7-304">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="830b7-305">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-305">Empty</span></span> | <span data-ttu-id="830b7-306">모든 HTTP 요청과 함께 보낼 HTTP 쿠키의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="830b7-307">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-307">Empty</span></span> | <span data-ttu-id="830b7-308">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="830b7-309">5초</span><span class="sxs-lookup"><span data-stu-id="830b7-309">5 seconds</span></span> | <span data-ttu-id="830b7-310">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-310">WebSockets only.</span></span> <span data-ttu-id="830b7-311">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="830b7-312">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="830b7-313">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-313">Empty</span></span> | <span data-ttu-id="830b7-314">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="830b7-315">HTTP 요청을 보내는 데 `HttpMessageHandler` 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="830b7-316">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="830b7-317">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="830b7-318">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 `HttpMessageHandler` 인스턴스를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="830b7-319">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: 쿠키 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="830b7-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="830b7-320">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="830b7-321">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="830b7-322">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="830b7-323">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="830b7-324">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="830b7-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="830b7-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="830b7-326">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-326">JavaScript Option</span></span> | <span data-ttu-id="830b7-327">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-327">Default Value</span></span> | <span data-ttu-id="830b7-328">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="830b7-329">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="830b7-330">협상 단계를 `true` 건너뛰려면이를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="830b7-331">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="830b7-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="830b7-332">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="830b7-333">CORS 요청과 함께 자격 증명을 보낼지 여부를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="830b7-334">Azure App Service는 고정 세션에 쿠키를 사용 하며이 옵션이 사용 하도록 설정 되어 있어야 제대로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="830b7-335">SignalR와 CORS에 대 한 자세한 내용은을 <xref:signalr/security#cross-origin-resource-sharing>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="830b7-336">Java</span><span class="sxs-lookup"><span data-stu-id="830b7-336">Java</span></span>](#tab/java)

| <span data-ttu-id="830b7-337">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-337">Java Option</span></span> | <span data-ttu-id="830b7-338">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-338">Default Value</span></span> | <span data-ttu-id="830b7-339">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="830b7-340">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="830b7-341">협상 단계를 `true` 건너뛰려면이를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="830b7-342">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="830b7-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="830b7-343">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="830b7-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="830b7-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="830b7-345">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-345">Empty</span></span> | <span data-ttu-id="830b7-346">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="830b7-347">.NET 클라이언트에서 이러한 옵션은에 `WithUrl`제공 된 옵션 대리자로 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="830b7-348">JavaScript 클라이언트에서 이러한 옵션은 다음에 `withUrl`제공 된 javascript 개체에 제공 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="830b7-349">Java 클라이언트에서 다음에서 `HttpHubConnectionBuilder` 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="830b7-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="830b7-350">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="830b7-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="830b7-351">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="830b7-352">ASP.NET Core SignalR는 메시지 인코딩에 사용할 두 가지 프로토콜인 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="830b7-353">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="830b7-354">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="830b7-355">`AddJsonProtocol`[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 뒤에 `Startup.ConfigureServices`추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="830b7-356">메서드 `AddJsonProtocol` 는 `options` 개체를 받는 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="830b7-357">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 인수와 반환 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 값의 serialization을 구성 하는 데 사용할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="830b7-358">자세한 내용은 [system.object 설명서](/dotnet/api/system.text.json)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="830b7-359">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면에서 `Startup.ConfigureServices`다음 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="830b7-360">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="830b7-361">확장 `Microsoft.Extensions.DependencyInjection` 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="830b7-362">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="830b7-363">Newtonsoft.json로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="830b7-364">에서 `Newtonsoft.Json` `System.Text.Json`지원 되지 않는 기능이 필요한 경우 [newtonsoft.json으로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="830b7-365">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-365">MessagePack serialization options</span></span>

<span data-ttu-id="830b7-366">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="830b7-367">자세한 내용은 [MessagePack In SignalR](xref:signalr/messagepackhubprotocol) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="830b7-368">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="830b7-369">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-369">Configure server options</span></span>

<span data-ttu-id="830b7-370">다음 표에서는 SignalR hubs를 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="830b7-371">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-371">Option</span></span> | <span data-ttu-id="830b7-372">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-372">Default Value</span></span> | <span data-ttu-id="830b7-373">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="830b7-374">30초</span><span class="sxs-lookup"><span data-stu-id="830b7-374">30 seconds</span></span> | <span data-ttu-id="830b7-375">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="830b7-376">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="830b7-377">권장 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="830b7-378">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-378">15 seconds</span></span> | <span data-ttu-id="830b7-379">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="830b7-380">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="830b7-381">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="830b7-382">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-382">15 seconds</span></span> | <span data-ttu-id="830b7-383">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="830b7-384">변경 `KeepAliveInterval`하는 경우 클라이언트 `ServerTimeout` / `serverTimeoutInMilliseconds` 에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="830b7-385">`ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="830b7-386">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="830b7-386">All installed protocols</span></span> | <span data-ttu-id="830b7-387">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-387">Protocols supported by this hub.</span></span> <span data-ttu-id="830b7-388">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="830b7-389">이면 `true`허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="830b7-390">기본값은입니다 `false`. 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="830b7-391">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="830b7-392">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="830b7-393">32KB</span><span class="sxs-lookup"><span data-stu-id="830b7-393">32 KB</span></span> | <span data-ttu-id="830b7-394">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="830b7-395">에서 `AddSignalR` `Startup.ConfigureServices`호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="830b7-396">단일 허브에 대 한 옵션은에서 `AddSignalR` 제공 되는 전역 옵션을 재정의 하 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>고 다음을 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="830b7-397">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="830b7-398">전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="830b7-399">이러한 옵션은의 `Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="830b7-400">다음 표에서는 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="830b7-401">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-401">Option</span></span> | <span data-ttu-id="830b7-402">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-402">Default Value</span></span> | <span data-ttu-id="830b7-403">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="830b7-404">32KB</span><span class="sxs-lookup"><span data-stu-id="830b7-404">32 KB</span></span> | <span data-ttu-id="830b7-405">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="830b7-406">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="830b7-407">허브 클래스에 적용 된 `Authorize` 특성에서 자동으로 수집 된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="830b7-408">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="830b7-409">32KB</span><span class="sxs-lookup"><span data-stu-id="830b7-409">32 KB</span></span> | <span data-ttu-id="830b7-410">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="830b7-411">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="830b7-412">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-412">All Transports are enabled.</span></span> | <span data-ttu-id="830b7-413">클라이언트에서 연결 하는 `HttpTransportType` 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="830b7-414">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="830b7-414">See below.</span></span> | <span data-ttu-id="830b7-415">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="830b7-416">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="830b7-416">See below.</span></span> | <span data-ttu-id="830b7-417">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="830b7-418">0</span><span class="sxs-lookup"><span data-stu-id="830b7-418">0</span></span> | <span data-ttu-id="830b7-419">Negotiate 프로토콜의 최소 버전을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="830b7-420">클라이언트를 최신 버전으로 제한 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="830b7-421">긴 폴링 전송에는 속성을 `LongPolling` 사용 하 여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="830b7-422">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-422">Option</span></span> | <span data-ttu-id="830b7-423">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-423">Default Value</span></span> | <span data-ttu-id="830b7-424">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="830b7-425">90 초</span><span class="sxs-lookup"><span data-stu-id="830b7-425">90 seconds</span></span> | <span data-ttu-id="830b7-426">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="830b7-427">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="830b7-428">WebSocket 전송에는 속성을 `WebSockets` 사용 하 여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="830b7-429">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-429">Option</span></span> | <span data-ttu-id="830b7-430">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-430">Default Value</span></span> | <span data-ttu-id="830b7-431">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="830b7-432">5초</span><span class="sxs-lookup"><span data-stu-id="830b7-432">5 seconds</span></span> | <span data-ttu-id="830b7-433">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="830b7-434">`Sec-WebSocket-Protocol` 헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="830b7-435">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="830b7-436">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-436">Configure client options</span></span>

<span data-ttu-id="830b7-437">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.Net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="830b7-438">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 `HubConnection` 자체도 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="830b7-439">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-439">Configure logging</span></span>

<span data-ttu-id="830b7-440">로깅은 .NET 클라이언트에서 메서드를 `ConfigureLogging` 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="830b7-441">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="830b7-442">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="830b7-443">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="830b7-444">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="830b7-445">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="830b7-446">확장 메서드 `AddConsole` 를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="830b7-447">JavaScript 클라이언트에는 유사한 `configureLogging` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="830b7-448">생성할 로그 `LogLevel` 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="830b7-449">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="830b7-450">`LogLevel` 값 대신 로그 수준 이름을 나타내는 값을 `string` 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="830b7-451">이는 `LogLevel` 상수에 대 한 액세스 권한이 없는 환경에서 SignalR 로깅을 구성할 때 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="830b7-452">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-452">The following table lists the available log levels.</span></span> <span data-ttu-id="830b7-453">사용자가 지정 하는 `configureLogging` 값은 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="830b7-454">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="830b7-455">String</span><span class="sxs-lookup"><span data-stu-id="830b7-455">String</span></span>                      | <span data-ttu-id="830b7-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="830b7-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="830b7-457">`info`**또는**`information`</span><span class="sxs-lookup"><span data-stu-id="830b7-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="830b7-458">`warn`**또는**`warning`</span><span class="sxs-lookup"><span data-stu-id="830b7-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="830b7-459">로깅을 완전히 사용 하지 않도록 설정 `signalR.LogLevel.None` 하려면 `configureLogging` 메서드에를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="830b7-460">로깅에 대 한 자세한 내용은 [SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="830b7-461">SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="830b7-462">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="830b7-463">다음 코드 조각에서는 SignalR Java 클라이언트와 `java.util.logging` 함께를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="830b7-464">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="830b7-465">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="830b7-466">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-466">Configure allowed transports</span></span>

<span data-ttu-id="830b7-467">SignalR에서 사용 하는 전송은 JavaScript `WithUrl` `withUrl` 의 호출에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="830b7-468">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="830b7-469">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-469">All transports are enabled by default.</span></span>

<span data-ttu-id="830b7-470">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="830b7-471">JavaScript 클라이언트에서 `withUrl`다음과 같이 제공 된 options 개체의 필드 `transport` 를 설정 하 여 전송을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="830b7-472">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="830b7-473">Java 클라이언트에서의 `withTransport` 메서드를 사용 하 여 전송이 선택 됩니다. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="830b7-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="830b7-474">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="830b7-475">SignalR Java 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="830b7-476">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-476">Configure bearer authentication</span></span>

<span data-ttu-id="830b7-477">SignalR 요청과 함께 인증 데이터를 제공 하려면 ( `AccessTokenProvider` `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="830b7-478">.NET 클라이언트에서이 액세스 토큰은의 `Authorization` `Bearer`형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="830b7-479">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="830b7-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="830b7-480">이러한 경우 액세스 토큰은 쿼리 문자열 값 `access_token`으로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="830b7-481">.NET 클라이언트에서 옵션은 `AccessTokenProvider` 의 `WithUrl`옵션 대리자를 사용 하 여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="830b7-482">JavaScript 클라이언트에서 액세스 토큰은의 `accessTokenFactory` `withUrl`options 개체에 있는 필드를 설정 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="830b7-483">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="830b7-484">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [단일\<문자열>](https://reactivex.io/documentation/single.html)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="830b7-485">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="830b7-486">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="830b7-487">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="830b7-488">.NET</span><span class="sxs-lookup"><span data-stu-id="830b7-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="830b7-489">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-489">Option</span></span> | <span data-ttu-id="830b7-490">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-490">Default value</span></span> | <span data-ttu-id="830b7-491">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="830b7-492">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="830b7-493">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-493">Timeout for server activity.</span></span> <span data-ttu-id="830b7-494">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 `Closed` 간주 하`onclose` 고 이벤트 (JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="830b7-495">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="830b7-496">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="830b7-497">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-497">15 seconds</span></span> | <span data-ttu-id="830b7-498">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="830b7-499">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 (`onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="830b7-500">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="830b7-501">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="830b7-502">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-502">15 seconds</span></span> | <span data-ttu-id="830b7-503">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="830b7-504">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="830b7-505">클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="830b7-506">.NET 클라이언트에서 시간 제한 값은 값으로 `TimeSpan` 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="830b7-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="830b7-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="830b7-508">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-508">Option</span></span> | <span data-ttu-id="830b7-509">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-509">Default value</span></span> | <span data-ttu-id="830b7-510">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="830b7-511">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="830b7-512">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-512">Timeout for server activity.</span></span> <span data-ttu-id="830b7-513">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onclose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="830b7-514">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="830b7-515">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="830b7-516">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="830b7-517">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="830b7-518">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="830b7-519">클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="830b7-520">Java</span><span class="sxs-lookup"><span data-stu-id="830b7-520">Java</span></span>](#tab/java)

| <span data-ttu-id="830b7-521">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-521">Option</span></span> | <span data-ttu-id="830b7-522">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-522">Default value</span></span> | <span data-ttu-id="830b7-523">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="830b7-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="830b7-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="830b7-525">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="830b7-526">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-526">Timeout for server activity.</span></span> <span data-ttu-id="830b7-527">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onClose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="830b7-528">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="830b7-529">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="830b7-530">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-530">15 seconds</span></span> | <span data-ttu-id="830b7-531">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="830b7-532">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 `onClose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="830b7-533">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="830b7-534">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="830b7-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="830b7-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="830b7-536">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="830b7-537">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="830b7-538">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="830b7-539">클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="830b7-540">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-540">Configure additional options</span></span>

<span data-ttu-id="830b7-541">Java 클라이언트 `WithUrl` `HttpHubConnectionBuilder` 의에 있는 다양 한 구성`withUrl` api에서 또는의 `HubConnectionBuilder` (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="830b7-542">.NET</span><span class="sxs-lookup"><span data-stu-id="830b7-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="830b7-543">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-543">.NET Option</span></span> |  <span data-ttu-id="830b7-544">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-544">Default value</span></span> | <span data-ttu-id="830b7-545">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="830b7-546">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="830b7-547">협상 단계를 `true` 건너뛰려면이를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="830b7-548">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="830b7-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="830b7-549">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="830b7-550">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-550">Empty</span></span> | <span data-ttu-id="830b7-551">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="830b7-552">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-552">Empty</span></span> | <span data-ttu-id="830b7-553">모든 HTTP 요청과 함께 보낼 HTTP 쿠키의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="830b7-554">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-554">Empty</span></span> | <span data-ttu-id="830b7-555">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="830b7-556">5초</span><span class="sxs-lookup"><span data-stu-id="830b7-556">5 seconds</span></span> | <span data-ttu-id="830b7-557">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-557">WebSockets only.</span></span> <span data-ttu-id="830b7-558">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="830b7-559">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="830b7-560">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-560">Empty</span></span> | <span data-ttu-id="830b7-561">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="830b7-562">HTTP 요청을 보내는 데 `HttpMessageHandler` 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="830b7-563">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="830b7-564">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="830b7-565">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 `HttpMessageHandler` 인스턴스를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="830b7-566">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: 쿠키 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="830b7-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="830b7-567">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="830b7-568">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="830b7-569">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="830b7-570">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="830b7-571">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="830b7-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="830b7-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="830b7-573">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-573">JavaScript Option</span></span> | <span data-ttu-id="830b7-574">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-574">Default Value</span></span> | <span data-ttu-id="830b7-575">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="830b7-576">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="830b7-577">협상 단계를 `true` 건너뛰려면이를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="830b7-578">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="830b7-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="830b7-579">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="830b7-580">Java</span><span class="sxs-lookup"><span data-stu-id="830b7-580">Java</span></span>](#tab/java)

| <span data-ttu-id="830b7-581">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-581">Java Option</span></span> | <span data-ttu-id="830b7-582">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-582">Default Value</span></span> | <span data-ttu-id="830b7-583">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="830b7-584">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="830b7-585">협상 단계를 `true` 건너뛰려면이를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="830b7-586">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="830b7-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="830b7-587">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="830b7-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="830b7-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="830b7-589">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-589">Empty</span></span> | <span data-ttu-id="830b7-590">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="830b7-591">.NET 클라이언트에서 이러한 옵션은에 `WithUrl`제공 된 옵션 대리자로 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="830b7-592">JavaScript 클라이언트에서 이러한 옵션은 다음에 `withUrl`제공 된 javascript 개체에 제공 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="830b7-593">Java 클라이언트에서 다음에서 `HttpHubConnectionBuilder` 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="830b7-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="830b7-594">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="830b7-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="830b7-595">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="830b7-596">ASP.NET Core SignalR는 메시지 인코딩에 사용할 두 가지 프로토콜인 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="830b7-597">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="830b7-598">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="830b7-599">`AddJsonProtocol`[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 뒤에 `Startup.ConfigureServices`추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="830b7-600">메서드 `AddJsonProtocol` 는 `options` 개체를 받는 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="830b7-601">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 인수와 반환 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 값의 serialization을 구성 하는 데 사용할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="830b7-602">자세한 내용은 [system.object 설명서](/dotnet/api/system.text.json)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="830b7-603">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면에서 `Startup.ConfigureServices`다음 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="830b7-604">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="830b7-605">확장 `Microsoft.Extensions.DependencyInjection` 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="830b7-606">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="830b7-607">Newtonsoft.json로 전환 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="830b7-608">에서 `Newtonsoft.Json` `System.Text.Json`지원 되지 않는 기능이 필요한 경우 [newtonsoft.json으로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="830b7-609">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-609">MessagePack serialization options</span></span>

<span data-ttu-id="830b7-610">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="830b7-611">자세한 내용은 [MessagePack In SignalR](xref:signalr/messagepackhubprotocol) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="830b7-612">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="830b7-613">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-613">Configure server options</span></span>

<span data-ttu-id="830b7-614">다음 표에서는 SignalR hubs를 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="830b7-615">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-615">Option</span></span> | <span data-ttu-id="830b7-616">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-616">Default Value</span></span> | <span data-ttu-id="830b7-617">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="830b7-618">30초</span><span class="sxs-lookup"><span data-stu-id="830b7-618">30 seconds</span></span> | <span data-ttu-id="830b7-619">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="830b7-620">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="830b7-621">권장 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="830b7-622">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-622">15 seconds</span></span> | <span data-ttu-id="830b7-623">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="830b7-624">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="830b7-625">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="830b7-626">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-626">15 seconds</span></span> | <span data-ttu-id="830b7-627">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="830b7-628">변경 `KeepAliveInterval`하는 경우 클라이언트 `ServerTimeout` / `serverTimeoutInMilliseconds` 에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="830b7-629">`ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="830b7-630">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="830b7-630">All installed protocols</span></span> | <span data-ttu-id="830b7-631">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-631">Protocols supported by this hub.</span></span> <span data-ttu-id="830b7-632">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="830b7-633">이면 `true`허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="830b7-634">기본값은입니다 `false`. 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="830b7-635">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="830b7-636">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="830b7-637">32KB</span><span class="sxs-lookup"><span data-stu-id="830b7-637">32 KB</span></span> | <span data-ttu-id="830b7-638">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="830b7-639">에서 `AddSignalR` `Startup.ConfigureServices`호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="830b7-640">단일 허브에 대 한 옵션은에서 `AddSignalR` 제공 되는 전역 옵션을 재정의 하 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>고 다음을 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="830b7-641">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="830b7-642">전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="830b7-643">이러한 옵션은의 `Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="830b7-644">다음 표에서는 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="830b7-645">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-645">Option</span></span> | <span data-ttu-id="830b7-646">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-646">Default Value</span></span> | <span data-ttu-id="830b7-647">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="830b7-648">32KB</span><span class="sxs-lookup"><span data-stu-id="830b7-648">32 KB</span></span> | <span data-ttu-id="830b7-649">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="830b7-650">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="830b7-651">허브 클래스에 적용 된 `Authorize` 특성에서 자동으로 수집 된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="830b7-652">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="830b7-653">32KB</span><span class="sxs-lookup"><span data-stu-id="830b7-653">32 KB</span></span> | <span data-ttu-id="830b7-654">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="830b7-655">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="830b7-656">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-656">All Transports are enabled.</span></span> | <span data-ttu-id="830b7-657">클라이언트에서 연결 하는 `HttpTransportType` 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="830b7-658">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="830b7-658">See below.</span></span> | <span data-ttu-id="830b7-659">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="830b7-660">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="830b7-660">See below.</span></span> | <span data-ttu-id="830b7-661">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="830b7-662">긴 폴링 전송에는 속성을 `LongPolling` 사용 하 여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="830b7-663">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-663">Option</span></span> | <span data-ttu-id="830b7-664">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-664">Default Value</span></span> | <span data-ttu-id="830b7-665">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="830b7-666">90 초</span><span class="sxs-lookup"><span data-stu-id="830b7-666">90 seconds</span></span> | <span data-ttu-id="830b7-667">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="830b7-668">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="830b7-669">WebSocket 전송에는 속성을 `WebSockets` 사용 하 여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="830b7-670">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-670">Option</span></span> | <span data-ttu-id="830b7-671">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-671">Default Value</span></span> | <span data-ttu-id="830b7-672">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="830b7-673">5초</span><span class="sxs-lookup"><span data-stu-id="830b7-673">5 seconds</span></span> | <span data-ttu-id="830b7-674">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="830b7-675">`Sec-WebSocket-Protocol` 헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="830b7-676">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="830b7-677">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-677">Configure client options</span></span>

<span data-ttu-id="830b7-678">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.Net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="830b7-679">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 `HubConnection` 자체도 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="830b7-680">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-680">Configure logging</span></span>

<span data-ttu-id="830b7-681">로깅은 .NET 클라이언트에서 메서드를 `ConfigureLogging` 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="830b7-682">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="830b7-683">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="830b7-684">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="830b7-685">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="830b7-686">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="830b7-687">확장 메서드 `AddConsole` 를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="830b7-688">JavaScript 클라이언트에는 유사한 `configureLogging` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="830b7-689">생성할 로그 `LogLevel` 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="830b7-690">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="830b7-691">`LogLevel` 값 대신 로그 수준 이름을 나타내는 값을 `string` 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="830b7-692">이는 `LogLevel` 상수에 대 한 액세스 권한이 없는 환경에서 SignalR 로깅을 구성할 때 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="830b7-693">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-693">The following table lists the available log levels.</span></span> <span data-ttu-id="830b7-694">사용자가 지정 하는 `configureLogging` 값은 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="830b7-695">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="830b7-696">String</span><span class="sxs-lookup"><span data-stu-id="830b7-696">String</span></span>                      | <span data-ttu-id="830b7-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="830b7-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="830b7-698">`info`**또는**`information`</span><span class="sxs-lookup"><span data-stu-id="830b7-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="830b7-699">`warn`**또는**`warning`</span><span class="sxs-lookup"><span data-stu-id="830b7-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="830b7-700">로깅을 완전히 사용 하지 않도록 설정 `signalR.LogLevel.None` 하려면 `configureLogging` 메서드에를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="830b7-701">로깅에 대 한 자세한 내용은 [SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="830b7-702">SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="830b7-703">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="830b7-704">다음 코드 조각에서는 SignalR Java 클라이언트와 `java.util.logging` 함께를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="830b7-705">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="830b7-706">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="830b7-707">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-707">Configure allowed transports</span></span>

<span data-ttu-id="830b7-708">SignalR에서 사용 하는 전송은 JavaScript `WithUrl` `withUrl` 의 호출에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="830b7-709">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="830b7-710">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-710">All transports are enabled by default.</span></span>

<span data-ttu-id="830b7-711">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="830b7-712">JavaScript 클라이언트에서 `withUrl`다음과 같이 제공 된 options 개체의 필드 `transport` 를 설정 하 여 전송을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="830b7-713">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="830b7-714">Java 클라이언트에서의 `withTransport` 메서드를 사용 하 여 전송이 선택 됩니다. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="830b7-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="830b7-715">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="830b7-716">SignalR Java 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="830b7-717">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-717">Configure bearer authentication</span></span>

<span data-ttu-id="830b7-718">SignalR 요청과 함께 인증 데이터를 제공 하려면 ( `AccessTokenProvider` `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="830b7-719">.NET 클라이언트에서이 액세스 토큰은의 `Authorization` `Bearer`형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="830b7-720">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="830b7-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="830b7-721">이러한 경우 액세스 토큰은 쿼리 문자열 값 `access_token`으로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="830b7-722">.NET 클라이언트에서 옵션은 `AccessTokenProvider` 의 `WithUrl`옵션 대리자를 사용 하 여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="830b7-723">JavaScript 클라이언트에서 액세스 토큰은의 `accessTokenFactory` `withUrl`options 개체에 있는 필드를 설정 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="830b7-724">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="830b7-725">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [단일\<문자열>](https://reactivex.io/documentation/single.html)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="830b7-726">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="830b7-727">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="830b7-728">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="830b7-729">.NET</span><span class="sxs-lookup"><span data-stu-id="830b7-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="830b7-730">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-730">Option</span></span> | <span data-ttu-id="830b7-731">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-731">Default value</span></span> | <span data-ttu-id="830b7-732">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="830b7-733">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="830b7-734">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-734">Timeout for server activity.</span></span> <span data-ttu-id="830b7-735">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 `Closed` 간주 하`onclose` 고 이벤트 (JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="830b7-736">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="830b7-737">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="830b7-738">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-738">15 seconds</span></span> | <span data-ttu-id="830b7-739">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="830b7-740">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 (`onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="830b7-741">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="830b7-742">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="830b7-743">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-743">15 seconds</span></span> | <span data-ttu-id="830b7-744">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="830b7-745">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="830b7-746">클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="830b7-747">.NET 클라이언트에서 시간 제한 값은 값으로 `TimeSpan` 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="830b7-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="830b7-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="830b7-749">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-749">Option</span></span> | <span data-ttu-id="830b7-750">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-750">Default value</span></span> | <span data-ttu-id="830b7-751">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="830b7-752">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="830b7-753">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-753">Timeout for server activity.</span></span> <span data-ttu-id="830b7-754">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onclose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="830b7-755">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="830b7-756">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="830b7-757">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="830b7-758">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="830b7-759">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="830b7-760">클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="830b7-761">Java</span><span class="sxs-lookup"><span data-stu-id="830b7-761">Java</span></span>](#tab/java)

| <span data-ttu-id="830b7-762">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-762">Option</span></span> | <span data-ttu-id="830b7-763">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-763">Default value</span></span> | <span data-ttu-id="830b7-764">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="830b7-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="830b7-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="830b7-766">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="830b7-767">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-767">Timeout for server activity.</span></span> <span data-ttu-id="830b7-768">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onClose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="830b7-769">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="830b7-770">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="830b7-771">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-771">15 seconds</span></span> | <span data-ttu-id="830b7-772">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="830b7-773">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 `onClose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="830b7-774">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="830b7-775">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="830b7-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="830b7-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="830b7-777">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="830b7-778">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="830b7-779">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="830b7-780">클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="830b7-781">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-781">Configure additional options</span></span>

<span data-ttu-id="830b7-782">Java 클라이언트 `WithUrl` `HttpHubConnectionBuilder` 의에 있는 다양 한 구성`withUrl` api에서 또는의 `HubConnectionBuilder` (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="830b7-783">.NET</span><span class="sxs-lookup"><span data-stu-id="830b7-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="830b7-784">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-784">.NET Option</span></span> |  <span data-ttu-id="830b7-785">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-785">Default value</span></span> | <span data-ttu-id="830b7-786">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="830b7-787">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="830b7-788">협상 단계를 `true` 건너뛰려면이를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="830b7-789">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="830b7-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="830b7-790">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="830b7-791">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-791">Empty</span></span> | <span data-ttu-id="830b7-792">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="830b7-793">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-793">Empty</span></span> | <span data-ttu-id="830b7-794">모든 HTTP 요청과 함께 보낼 HTTP 쿠키의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="830b7-795">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-795">Empty</span></span> | <span data-ttu-id="830b7-796">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="830b7-797">5초</span><span class="sxs-lookup"><span data-stu-id="830b7-797">5 seconds</span></span> | <span data-ttu-id="830b7-798">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-798">WebSockets only.</span></span> <span data-ttu-id="830b7-799">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="830b7-800">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="830b7-801">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-801">Empty</span></span> | <span data-ttu-id="830b7-802">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="830b7-803">HTTP 요청을 보내는 데 `HttpMessageHandler` 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="830b7-804">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="830b7-805">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="830b7-806">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 `HttpMessageHandler` 인스턴스를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="830b7-807">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: 쿠키 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="830b7-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="830b7-808">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="830b7-809">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="830b7-810">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="830b7-811">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="830b7-812">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="830b7-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="830b7-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="830b7-814">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-814">JavaScript Option</span></span> | <span data-ttu-id="830b7-815">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-815">Default Value</span></span> | <span data-ttu-id="830b7-816">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="830b7-817">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="830b7-818">협상 단계를 `true` 건너뛰려면이를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="830b7-819">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="830b7-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="830b7-820">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="830b7-821">Java</span><span class="sxs-lookup"><span data-stu-id="830b7-821">Java</span></span>](#tab/java)

| <span data-ttu-id="830b7-822">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-822">Java Option</span></span> | <span data-ttu-id="830b7-823">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-823">Default Value</span></span> | <span data-ttu-id="830b7-824">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="830b7-825">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="830b7-826">협상 단계를 `true` 건너뛰려면이를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="830b7-827">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="830b7-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="830b7-828">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="830b7-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="830b7-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="830b7-830">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-830">Empty</span></span> | <span data-ttu-id="830b7-831">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="830b7-832">.NET 클라이언트에서 이러한 옵션은에 `WithUrl`제공 된 옵션 대리자로 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="830b7-833">JavaScript 클라이언트에서 이러한 옵션은 다음에 `withUrl`제공 된 javascript 개체에 제공 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="830b7-834">Java 클라이언트에서 다음에서 `HttpHubConnectionBuilder` 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="830b7-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="830b7-835">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="830b7-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="830b7-836">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="830b7-837">ASP.NET Core SignalR는 메시지 인코딩에 사용할 두 가지 프로토콜인 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="830b7-838">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="830b7-839">[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 JSON serialization을 구성할 수 있습니다 .이 메서드는 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` 메서드에서 AddSignalR 뒤에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="830b7-840">메서드 `AddJsonProtocol` 는 `options` 개체를 받는 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="830b7-841">해당 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 인수 및 반환 `JsonSerializerSettings` 값의 serialization을 구성 하는 데 사용할 수 있는 JSON.NET 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="830b7-842">자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="830b7-843">예를 들어 기본 "camelCase" 이름 대신 "대/소문자 구분" 속성 이름을 사용 하도록 serializer를 구성 하려면에서 `Startup.ConfigureServices`다음 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="830b7-844">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="830b7-845">확장 `Microsoft.Extensions.DependencyInjection` 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="830b7-846">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="830b7-847">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-847">MessagePack serialization options</span></span>

<span data-ttu-id="830b7-848">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="830b7-849">자세한 내용은 [MessagePack In SignalR](xref:signalr/messagepackhubprotocol) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="830b7-850">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="830b7-851">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-851">Configure server options</span></span>

<span data-ttu-id="830b7-852">다음 표에서는 SignalR hubs를 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="830b7-853">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-853">Option</span></span> | <span data-ttu-id="830b7-854">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-854">Default Value</span></span> | <span data-ttu-id="830b7-855">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="830b7-856">30초</span><span class="sxs-lookup"><span data-stu-id="830b7-856">30 seconds</span></span> | <span data-ttu-id="830b7-857">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="830b7-858">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="830b7-859">권장 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="830b7-860">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-860">15 seconds</span></span> | <span data-ttu-id="830b7-861">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="830b7-862">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="830b7-863">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="830b7-864">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-864">15 seconds</span></span> | <span data-ttu-id="830b7-865">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="830b7-866">변경 `KeepAliveInterval`하는 경우 클라이언트 `ServerTimeout` / `serverTimeoutInMilliseconds` 에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="830b7-867">`ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="830b7-868">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="830b7-868">All installed protocols</span></span> | <span data-ttu-id="830b7-869">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-869">Protocols supported by this hub.</span></span> <span data-ttu-id="830b7-870">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="830b7-871">이면 `true`허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="830b7-872">기본값은입니다 `false`. 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="830b7-873">에서 `AddSignalR` `Startup.ConfigureServices`호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="830b7-874">단일 허브에 대 한 옵션은에서 `AddSignalR` 제공 되는 전역 옵션을 재정의 하 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>고 다음을 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="830b7-875">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="830b7-876">전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="830b7-877">이러한 옵션은의 `Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="830b7-878">다음 표에서는 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="830b7-879">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-879">Option</span></span> | <span data-ttu-id="830b7-880">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-880">Default Value</span></span> | <span data-ttu-id="830b7-881">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="830b7-882">32KB</span><span class="sxs-lookup"><span data-stu-id="830b7-882">32 KB</span></span> | <span data-ttu-id="830b7-883">서버에서 버퍼링 하는 클라이언트로부터 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="830b7-884">이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="830b7-885">허브 클래스에 적용 된 `Authorize` 특성에서 자동으로 수집 된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="830b7-886">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="830b7-887">32KB</span><span class="sxs-lookup"><span data-stu-id="830b7-887">32 KB</span></span> | <span data-ttu-id="830b7-888">서버가 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="830b7-889">이 값을 늘리면 서버에서 더 큰 메시지를 보낼 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="830b7-890">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-890">All Transports are enabled.</span></span> | <span data-ttu-id="830b7-891">클라이언트에서 연결 하는 `HttpTransportType` 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="830b7-892">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="830b7-892">See below.</span></span> | <span data-ttu-id="830b7-893">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="830b7-894">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="830b7-894">See below.</span></span> | <span data-ttu-id="830b7-895">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="830b7-896">긴 폴링 전송에는 속성을 `LongPolling` 사용 하 여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="830b7-897">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-897">Option</span></span> | <span data-ttu-id="830b7-898">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-898">Default Value</span></span> | <span data-ttu-id="830b7-899">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="830b7-900">90 초</span><span class="sxs-lookup"><span data-stu-id="830b7-900">90 seconds</span></span> | <span data-ttu-id="830b7-901">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="830b7-902">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="830b7-903">WebSocket 전송에는 속성을 `WebSockets` 사용 하 여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="830b7-904">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-904">Option</span></span> | <span data-ttu-id="830b7-905">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-905">Default Value</span></span> | <span data-ttu-id="830b7-906">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="830b7-907">5초</span><span class="sxs-lookup"><span data-stu-id="830b7-907">5 seconds</span></span> | <span data-ttu-id="830b7-908">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="830b7-909">`Sec-WebSocket-Protocol` 헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="830b7-910">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="830b7-911">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-911">Configure client options</span></span>

<span data-ttu-id="830b7-912">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.Net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="830b7-913">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 `HubConnection` 자체도 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="830b7-914">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-914">Configure logging</span></span>

<span data-ttu-id="830b7-915">로깅은 .NET 클라이언트에서 메서드를 `ConfigureLogging` 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="830b7-916">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="830b7-917">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="830b7-918">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="830b7-919">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="830b7-920">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="830b7-921">확장 메서드 `AddConsole` 를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="830b7-922">JavaScript 클라이언트에는 유사한 `configureLogging` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="830b7-923">생성할 로그 `LogLevel` 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="830b7-924">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="830b7-925">로깅을 완전히 사용 하지 않도록 설정 `signalR.LogLevel.None` 하려면 `configureLogging` 메서드에를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="830b7-926">로깅에 대 한 자세한 내용은 [SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="830b7-927">SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="830b7-928">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="830b7-929">다음 코드 조각에서는 SignalR Java 클라이언트와 `java.util.logging` 함께를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="830b7-930">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="830b7-931">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="830b7-932">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-932">Configure allowed transports</span></span>

<span data-ttu-id="830b7-933">SignalR에서 사용 하는 전송은 JavaScript `WithUrl` `withUrl` 의 호출에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="830b7-934">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="830b7-935">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-935">All transports are enabled by default.</span></span>

<span data-ttu-id="830b7-936">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="830b7-937">JavaScript 클라이언트에서 `withUrl`다음과 같이 제공 된 options 개체의 필드 `transport` 를 설정 하 여 전송을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="830b7-938">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="830b7-939">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-939">Configure bearer authentication</span></span>

<span data-ttu-id="830b7-940">SignalR 요청과 함께 인증 데이터를 제공 하려면 ( `AccessTokenProvider` `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="830b7-941">.NET 클라이언트에서이 액세스 토큰은의 `Authorization` `Bearer`형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="830b7-942">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="830b7-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="830b7-943">이러한 경우 액세스 토큰은 쿼리 문자열 값 `access_token`으로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="830b7-944">.NET 클라이언트에서 옵션은 `AccessTokenProvider` 의 `WithUrl`옵션 대리자를 사용 하 여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="830b7-945">JavaScript 클라이언트에서 액세스 토큰은의 `accessTokenFactory` `withUrl`options 개체에 있는 필드를 설정 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="830b7-946">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="830b7-947">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [단일\<문자열>](https://reactivex.io/documentation/single.html)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="830b7-948">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="830b7-949">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="830b7-950">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="830b7-951">.NET</span><span class="sxs-lookup"><span data-stu-id="830b7-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="830b7-952">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-952">Option</span></span> | <span data-ttu-id="830b7-953">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-953">Default value</span></span> | <span data-ttu-id="830b7-954">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="830b7-955">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="830b7-956">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-956">Timeout for server activity.</span></span> <span data-ttu-id="830b7-957">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 `Closed` 간주 하`onclose` 고 이벤트 (JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="830b7-958">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="830b7-959">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="830b7-960">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-960">15 seconds</span></span> | <span data-ttu-id="830b7-961">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="830b7-962">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 (`onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="830b7-963">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="830b7-964">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="830b7-965">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-965">15 seconds</span></span> | <span data-ttu-id="830b7-966">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="830b7-967">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="830b7-968">클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="830b7-969">.NET 클라이언트에서 시간 제한 값은 값으로 `TimeSpan` 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="830b7-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="830b7-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="830b7-971">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-971">Option</span></span> | <span data-ttu-id="830b7-972">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-972">Default value</span></span> | <span data-ttu-id="830b7-973">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="830b7-974">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="830b7-975">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-975">Timeout for server activity.</span></span> <span data-ttu-id="830b7-976">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onclose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="830b7-977">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="830b7-978">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="830b7-979">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="830b7-980">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="830b7-981">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="830b7-982">클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="830b7-983">Java</span><span class="sxs-lookup"><span data-stu-id="830b7-983">Java</span></span>](#tab/java)

| <span data-ttu-id="830b7-984">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-984">Option</span></span> | <span data-ttu-id="830b7-985">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-985">Default value</span></span> | <span data-ttu-id="830b7-986">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="830b7-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="830b7-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="830b7-988">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="830b7-989">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-989">Timeout for server activity.</span></span> <span data-ttu-id="830b7-990">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onClose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="830b7-991">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="830b7-992">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="830b7-993">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-993">15 seconds</span></span> | <span data-ttu-id="830b7-994">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="830b7-995">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 `onClose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="830b7-996">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="830b7-997">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="830b7-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="830b7-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="830b7-999">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="830b7-1000">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="830b7-1001">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="830b7-1002">클라이언트에서 서버의 `ClientTimeoutInterval` 집합에 있는 메시지를 보내지 않은 경우 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="830b7-1003">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1003">Configure additional options</span></span>

<span data-ttu-id="830b7-1004">Java 클라이언트 `WithUrl` `HttpHubConnectionBuilder` 의에 있는 다양 한 구성`withUrl` api에서 또는의 `HubConnectionBuilder` (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="830b7-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="830b7-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="830b7-1006">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1006">.NET Option</span></span> |  <span data-ttu-id="830b7-1007">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-1007">Default value</span></span> | <span data-ttu-id="830b7-1008">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="830b7-1009">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="830b7-1010">협상 단계를 `true` 건너뛰려면이를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="830b7-1011">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="830b7-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="830b7-1012">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="830b7-1013">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-1013">Empty</span></span> | <span data-ttu-id="830b7-1014">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="830b7-1015">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-1015">Empty</span></span> | <span data-ttu-id="830b7-1016">모든 HTTP 요청과 함께 보낼 HTTP 쿠키의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="830b7-1017">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-1017">Empty</span></span> | <span data-ttu-id="830b7-1018">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="830b7-1019">5초</span><span class="sxs-lookup"><span data-stu-id="830b7-1019">5 seconds</span></span> | <span data-ttu-id="830b7-1020">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1020">WebSockets only.</span></span> <span data-ttu-id="830b7-1021">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="830b7-1022">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="830b7-1023">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-1023">Empty</span></span> | <span data-ttu-id="830b7-1024">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="830b7-1025">HTTP 요청을 보내는 데 `HttpMessageHandler` 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="830b7-1026">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="830b7-1027">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="830b7-1028">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 `HttpMessageHandler` 인스턴스를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="830b7-1029">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: 쿠키 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="830b7-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="830b7-1030">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="830b7-1031">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="830b7-1032">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="830b7-1033">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="830b7-1034">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="830b7-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="830b7-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="830b7-1036">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1036">JavaScript Option</span></span> | <span data-ttu-id="830b7-1037">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-1037">Default Value</span></span> | <span data-ttu-id="830b7-1038">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="830b7-1039">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="830b7-1040">협상 단계를 `true` 건너뛰려면이를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="830b7-1041">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="830b7-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="830b7-1042">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="830b7-1043">Java</span><span class="sxs-lookup"><span data-stu-id="830b7-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="830b7-1044">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1044">Java Option</span></span> | <span data-ttu-id="830b7-1045">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-1045">Default Value</span></span> | <span data-ttu-id="830b7-1046">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="830b7-1047">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="830b7-1048">협상 단계를 `true` 건너뛰려면이를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="830b7-1049">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="830b7-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="830b7-1050">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="830b7-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="830b7-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="830b7-1052">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-1052">Empty</span></span> | <span data-ttu-id="830b7-1053">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="830b7-1054">.NET 클라이언트에서 이러한 옵션은에 `WithUrl`제공 된 옵션 대리자로 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="830b7-1055">JavaScript 클라이언트에서 이러한 옵션은 다음에 `withUrl`제공 된 javascript 개체에 제공 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="830b7-1056">Java 클라이언트에서 다음에서 `HttpHubConnectionBuilder` 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="830b7-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="830b7-1057">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="830b7-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="830b7-1058">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="830b7-1059">ASP.NET Core SignalR는 메시지 인코딩에 사용할 두 가지 프로토콜인 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="830b7-1060">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="830b7-1061">[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 JSON serialization을 구성할 수 있습니다 .이 메서드는 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices` 메서드에서 AddSignalR 뒤에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="830b7-1062">메서드 `AddJsonProtocol` 는 `options` 개체를 받는 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="830b7-1063">해당 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 인수 및 반환 `JsonSerializerSettings` 값의 serialization을 구성 하는 데 사용할 수 있는 JSON.NET 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="830b7-1064">자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="830b7-1065">예를 들어 기본 "camelCase" 이름 대신 "대/소문자 구분" 속성 이름을 사용 하도록 serializer를 구성 하려면에서 `Startup.ConfigureServices`다음 코드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="830b7-1066">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="830b7-1067">확장 `Microsoft.Extensions.DependencyInjection` 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="830b7-1068">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="830b7-1069">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1069">MessagePack serialization options</span></span>

<span data-ttu-id="830b7-1070">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="830b7-1071">자세한 내용은 [MessagePack In SignalR](xref:signalr/messagepackhubprotocol) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="830b7-1072">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="830b7-1073">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-1073">Configure server options</span></span>

<span data-ttu-id="830b7-1074">다음 표에서는 SignalR hubs를 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="830b7-1075">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1075">Option</span></span> | <span data-ttu-id="830b7-1076">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-1076">Default Value</span></span> | <span data-ttu-id="830b7-1077">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="830b7-1078">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-1078">15 seconds</span></span> | <span data-ttu-id="830b7-1079">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="830b7-1080">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="830b7-1081">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="830b7-1082">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-1082">15 seconds</span></span> | <span data-ttu-id="830b7-1083">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="830b7-1084">변경 `KeepAliveInterval`하는 경우 클라이언트 `ServerTimeout` / `serverTimeoutInMilliseconds` 에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="830b7-1085">`ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 `KeepAliveInterval` 값 두 배가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="830b7-1086">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="830b7-1086">All installed protocols</span></span> | <span data-ttu-id="830b7-1087">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="830b7-1088">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="830b7-1089">이면 `true`허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="830b7-1090">기본값은입니다 `false`. 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="830b7-1091">에서 `AddSignalR` `Startup.ConfigureServices`호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="830b7-1092">단일 허브에 대 한 옵션은에서 `AddSignalR` 제공 되는 전역 옵션을 재정의 하 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>고 다음을 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="830b7-1093">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="830b7-1094">전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="830b7-1095">이러한 옵션은의 `Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="830b7-1096">다음 표에서는 ASP.NET Core SignalR의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="830b7-1097">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1097">Option</span></span> | <span data-ttu-id="830b7-1098">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-1098">Default Value</span></span> | <span data-ttu-id="830b7-1099">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="830b7-1100">32KB</span><span class="sxs-lookup"><span data-stu-id="830b7-1100">32 KB</span></span> | <span data-ttu-id="830b7-1101">서버에서 버퍼링 하는 클라이언트로부터 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="830b7-1102">이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="830b7-1103">허브 클래스에 적용 된 `Authorize` 특성에서 자동으로 수집 된 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="830b7-1104">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="830b7-1105">32KB</span><span class="sxs-lookup"><span data-stu-id="830b7-1105">32 KB</span></span> | <span data-ttu-id="830b7-1106">서버가 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="830b7-1107">이 값을 늘리면 서버에서 더 큰 메시지를 보낼 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="830b7-1108">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1108">All Transports are enabled.</span></span> | <span data-ttu-id="830b7-1109">클라이언트에서 연결 하는 `HttpTransportType` 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="830b7-1110">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="830b7-1110">See below.</span></span> | <span data-ttu-id="830b7-1111">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="830b7-1112">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="830b7-1112">See below.</span></span> | <span data-ttu-id="830b7-1113">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="830b7-1114">긴 폴링 전송에는 속성을 `LongPolling` 사용 하 여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="830b7-1115">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1115">Option</span></span> | <span data-ttu-id="830b7-1116">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-1116">Default Value</span></span> | <span data-ttu-id="830b7-1117">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="830b7-1118">90 초</span><span class="sxs-lookup"><span data-stu-id="830b7-1118">90 seconds</span></span> | <span data-ttu-id="830b7-1119">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="830b7-1120">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="830b7-1121">WebSocket 전송에는 속성을 `WebSockets` 사용 하 여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="830b7-1122">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1122">Option</span></span> | <span data-ttu-id="830b7-1123">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-1123">Default Value</span></span> | <span data-ttu-id="830b7-1124">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="830b7-1125">5초</span><span class="sxs-lookup"><span data-stu-id="830b7-1125">5 seconds</span></span> | <span data-ttu-id="830b7-1126">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="830b7-1127">`Sec-WebSocket-Protocol` 헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="830b7-1128">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="830b7-1129">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-1129">Configure client options</span></span>

<span data-ttu-id="830b7-1130">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.Net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="830b7-1131">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 `HubConnection` 자체도 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="830b7-1132">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-1132">Configure logging</span></span>

<span data-ttu-id="830b7-1133">로깅은 .NET 클라이언트에서 메서드를 `ConfigureLogging` 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="830b7-1134">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="830b7-1135">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="830b7-1136">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="830b7-1137">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="830b7-1138">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="830b7-1139">확장 메서드 `AddConsole` 를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="830b7-1140">JavaScript 클라이언트에는 유사한 `configureLogging` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="830b7-1141">생성할 로그 `LogLevel` 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="830b7-1142">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="830b7-1143">로깅을 완전히 사용 하지 않도록 설정 `signalR.LogLevel.None` 하려면 `configureLogging` 메서드에를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="830b7-1144">로깅에 대 한 자세한 내용은 [SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="830b7-1145">SignalR Java 클라이언트는 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 하 여 로깅을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="830b7-1146">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="830b7-1147">다음 코드 조각에서는 SignalR Java 클라이언트와 `java.util.logging` 함께를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="830b7-1148">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="830b7-1149">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="830b7-1150">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-1150">Configure allowed transports</span></span>

<span data-ttu-id="830b7-1151">SignalR에서 사용 하는 전송은 JavaScript `WithUrl` `withUrl` 의 호출에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="830b7-1152">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="830b7-1153">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="830b7-1154">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="830b7-1155">JavaScript 클라이언트에서 `withUrl`다음과 같이 제공 된 options 개체의 필드 `transport` 를 설정 하 여 전송을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="830b7-1156">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-1156">Configure bearer authentication</span></span>

<span data-ttu-id="830b7-1157">SignalR 요청과 함께 인증 데이터를 제공 하려면 ( `AccessTokenProvider` `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="830b7-1158">.NET 클라이언트에서이 액세스 토큰은의 `Authorization` `Bearer`형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="830b7-1159">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="830b7-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="830b7-1160">이러한 경우 액세스 토큰은 쿼리 문자열 값 `access_token`으로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="830b7-1161">.NET 클라이언트에서 옵션은 `AccessTokenProvider` 의 `WithUrl`옵션 대리자를 사용 하 여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="830b7-1162">JavaScript 클라이언트에서 액세스 토큰은의 `accessTokenFactory` `withUrl`options 개체에 있는 필드를 설정 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="830b7-1163">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="830b7-1164">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [단일\<문자열>](https://reactivex.io/documentation/single.html)를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="830b7-1165">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="830b7-1166">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="830b7-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="830b7-1167">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 `HubConnection` 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="830b7-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="830b7-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="830b7-1169">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1169">Option</span></span> | <span data-ttu-id="830b7-1170">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-1170">Default value</span></span> | <span data-ttu-id="830b7-1171">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="830b7-1172">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="830b7-1173">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1173">Timeout for server activity.</span></span> <span data-ttu-id="830b7-1174">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 `Closed` 간주 하`onclose` 고 이벤트 (JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="830b7-1175">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="830b7-1176">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="830b7-1177">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-1177">15 seconds</span></span> | <span data-ttu-id="830b7-1178">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="830b7-1179">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 (`onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="830b7-1180">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="830b7-1181">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="830b7-1182">.NET 클라이언트에서 시간 제한 값은 값으로 `TimeSpan` 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="830b7-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="830b7-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="830b7-1184">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1184">Option</span></span> | <span data-ttu-id="830b7-1185">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-1185">Default value</span></span> | <span data-ttu-id="830b7-1186">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="830b7-1187">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="830b7-1188">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1188">Timeout for server activity.</span></span> <span data-ttu-id="830b7-1189">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onclose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="830b7-1190">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="830b7-1191">권장 값은 ping이 도착할 때까지 허용 하는 서버 `KeepAliveInterval` 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="830b7-1192">Java</span><span class="sxs-lookup"><span data-stu-id="830b7-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="830b7-1193">옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1193">Option</span></span> | <span data-ttu-id="830b7-1194">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-1194">Default value</span></span> | <span data-ttu-id="830b7-1195">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="830b7-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="830b7-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="830b7-1197">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="830b7-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="830b7-1198">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1198">Timeout for server activity.</span></span> <span data-ttu-id="830b7-1199">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 `onClose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="830b7-1200">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="830b7-1201">권장 값은 ping이 도착할 때까지 시간을 허용 하 `KeepAliveInterval` 는 서버 값을 두 번 이상 표시 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="830b7-1202">15초</span><span class="sxs-lookup"><span data-stu-id="830b7-1202">15 seconds</span></span> | <span data-ttu-id="830b7-1203">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="830b7-1204">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 `onClose` 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="830b7-1205">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="830b7-1206">핸드셰이크 프로세스에 대 한 자세한 내용은 [SignalR Hub 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="830b7-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="830b7-1207">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1207">Configure additional options</span></span>

<span data-ttu-id="830b7-1208">Java 클라이언트 `WithUrl` `HttpHubConnectionBuilder` 의에 있는 다양 한 구성`withUrl` api에서 또는의 `HubConnectionBuilder` (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="830b7-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="830b7-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="830b7-1210">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1210">.NET Option</span></span> |  <span data-ttu-id="830b7-1211">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-1211">Default value</span></span> | <span data-ttu-id="830b7-1212">설명</span><span class="sxs-lookup"><span data-stu-id="830b7-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="830b7-1213">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="830b7-1214">협상 단계를 `true` 건너뛰려면이를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="830b7-1215">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="830b7-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="830b7-1216">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="830b7-1217">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-1217">Empty</span></span> | <span data-ttu-id="830b7-1218">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="830b7-1219">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-1219">Empty</span></span> | <span data-ttu-id="830b7-1220">모든 HTTP 요청과 함께 보낼 HTTP 쿠키의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="830b7-1221">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-1221">Empty</span></span> | <span data-ttu-id="830b7-1222">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="830b7-1223">5초</span><span class="sxs-lookup"><span data-stu-id="830b7-1223">5 seconds</span></span> | <span data-ttu-id="830b7-1224">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1224">WebSockets only.</span></span> <span data-ttu-id="830b7-1225">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="830b7-1226">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="830b7-1227">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-1227">Empty</span></span> | <span data-ttu-id="830b7-1228">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="830b7-1229">HTTP 요청을 보내는 데 `HttpMessageHandler` 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="830b7-1230">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="830b7-1231">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="830b7-1232">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 `HttpMessageHandler` 인스턴스를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="830b7-1233">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: 쿠키 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="830b7-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="830b7-1234">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="830b7-1235">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="830b7-1236">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="830b7-1237">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="830b7-1238">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="830b7-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="830b7-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="830b7-1240">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1240">JavaScript Option</span></span> | <span data-ttu-id="830b7-1241">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-1241">Default Value</span></span> | <span data-ttu-id="830b7-1242">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="830b7-1243">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="830b7-1244">협상 단계를 `true` 건너뛰려면이를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="830b7-1245">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="830b7-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="830b7-1246">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="830b7-1247">Java</span><span class="sxs-lookup"><span data-stu-id="830b7-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="830b7-1248">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="830b7-1248">Java Option</span></span> | <span data-ttu-id="830b7-1249">기본값</span><span class="sxs-lookup"><span data-stu-id="830b7-1249">Default Value</span></span> | <span data-ttu-id="830b7-1250">Description</span><span class="sxs-lookup"><span data-stu-id="830b7-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="830b7-1251">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="830b7-1252">협상 단계를 `true` 건너뛰려면이를로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="830b7-1253">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="830b7-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="830b7-1254">Azure SignalR 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="830b7-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="830b7-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="830b7-1256">비어 있음</span><span class="sxs-lookup"><span data-stu-id="830b7-1256">Empty</span></span> | <span data-ttu-id="830b7-1257">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="830b7-1258">.NET 클라이언트에서 이러한 옵션은에 `WithUrl`제공 된 옵션 대리자로 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="830b7-1259">JavaScript 클라이언트에서 이러한 옵션은 다음에 `withUrl`제공 된 javascript 개체에 제공 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="830b7-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="830b7-1260">Java 클라이언트에서 다음에서 `HttpHubConnectionBuilder` 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="830b7-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="830b7-1261">추가 자료</span><span class="sxs-lookup"><span data-stu-id="830b7-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
