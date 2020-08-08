---
title: ASP.NET Core SignalR 구성
author: bradygaster
description: ASP.NET Core 앱을 구성 하는 방법을 알아봅니다 SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: d451e8807d761ab11509d33951009a98845f7e5e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021863"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="752e7-103">ASP.NET Core SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="752e7-104">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="752e7-105">ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="752e7-106">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="752e7-107">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="752e7-108">`AddJsonProtocol`[추가 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="752e7-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="752e7-109">`AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` .</span><span class="sxs-lookup"><span data-stu-id="752e7-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="752e7-110">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 인수와 반환 값의 serialization을 구성 하는 데 사용할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="752e7-111">자세한 내용은 [설명서의System.Text.Js](/dotnet/api/system.text.json)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="752e7-112">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="752e7-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="752e7-113">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="752e7-114">`Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="752e7-115">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="752e7-116">Newtonsoft.Js로 전환</span><span class="sxs-lookup"><span data-stu-id="752e7-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="752e7-117">`Newtonsoft.Json`에서 지원 되지 않는 기능이 필요한 경우 `System.Text.Json` [Newtonsoft.Js켜기로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="752e7-118">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-118">MessagePack serialization options</span></span>

<span data-ttu-id="752e7-119">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="752e7-120">자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="752e7-121">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="752e7-122">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-122">Configure server options</span></span>

<span data-ttu-id="752e7-123">다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="752e7-124">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-124">Option</span></span> | <span data-ttu-id="752e7-125">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-125">Default Value</span></span> | <span data-ttu-id="752e7-126">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="752e7-127">30초</span><span class="sxs-lookup"><span data-stu-id="752e7-127">30 seconds</span></span> | <span data-ttu-id="752e7-128">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="752e7-129">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="752e7-130">권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="752e7-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="752e7-131">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-131">15 seconds</span></span> | <span data-ttu-id="752e7-132">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="752e7-133">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="752e7-134">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="752e7-135">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-135">15 seconds</span></span> | <span data-ttu-id="752e7-136">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="752e7-137">변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="752e7-138">권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="752e7-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="752e7-139">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="752e7-139">All installed protocols</span></span> | <span data-ttu-id="752e7-140">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-140">Protocols supported by this hub.</span></span> <span data-ttu-id="752e7-141">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="752e7-142">이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="752e7-143">기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="752e7-144">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="752e7-145">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="752e7-146">32KB</span><span class="sxs-lookup"><span data-stu-id="752e7-146">32 KB</span></span> | <span data-ttu-id="752e7-147">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="752e7-148">에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="752e7-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="752e7-149">단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .</span><span class="sxs-lookup"><span data-stu-id="752e7-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="752e7-150">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="752e7-151">`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="752e7-152">이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="752e7-153">다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="752e7-154">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-154">Option</span></span> | <span data-ttu-id="752e7-155">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-155">Default Value</span></span> | <span data-ttu-id="752e7-156">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="752e7-157">32KB</span><span class="sxs-lookup"><span data-stu-id="752e7-157">32 KB</span></span> | <span data-ttu-id="752e7-158">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="752e7-159">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="752e7-160">허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="752e7-161">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="752e7-162">32KB</span><span class="sxs-lookup"><span data-stu-id="752e7-162">32 KB</span></span> | <span data-ttu-id="752e7-163">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="752e7-164">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="752e7-165">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-165">All Transports are enabled.</span></span> | <span data-ttu-id="752e7-166">`HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="752e7-167">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-167">See below.</span></span> | <span data-ttu-id="752e7-168">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="752e7-169">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-169">See below.</span></span> | <span data-ttu-id="752e7-170">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="752e7-171">0</span><span class="sxs-lookup"><span data-stu-id="752e7-171">0</span></span> | <span data-ttu-id="752e7-172">Negotiate 프로토콜의 최소 버전을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="752e7-173">클라이언트를 최신 버전으로 제한 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="752e7-174">긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .</span><span class="sxs-lookup"><span data-stu-id="752e7-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="752e7-175">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-175">Option</span></span> | <span data-ttu-id="752e7-176">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-176">Default Value</span></span> | <span data-ttu-id="752e7-177">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="752e7-178">90 초</span><span class="sxs-lookup"><span data-stu-id="752e7-178">90 seconds</span></span> | <span data-ttu-id="752e7-179">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="752e7-180">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="752e7-181">WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .</span><span class="sxs-lookup"><span data-stu-id="752e7-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="752e7-182">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-182">Option</span></span> | <span data-ttu-id="752e7-183">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-183">Default Value</span></span> | <span data-ttu-id="752e7-184">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="752e7-185">5초</span><span class="sxs-lookup"><span data-stu-id="752e7-185">5 seconds</span></span> | <span data-ttu-id="752e7-186">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="752e7-187">`Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="752e7-188">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="752e7-189">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-189">Configure client options</span></span>

<span data-ttu-id="752e7-190">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="752e7-191">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="752e7-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="752e7-192">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-192">Configure logging</span></span>

<span data-ttu-id="752e7-193">로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="752e7-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="752e7-194">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="752e7-195">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="752e7-196">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="752e7-197">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="752e7-198">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="752e7-199">확장 메서드를 호출 합니다 `AddConsole` .</span><span class="sxs-lookup"><span data-stu-id="752e7-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="752e7-200">JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="752e7-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="752e7-201">`LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="752e7-202">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="752e7-203">`LogLevel`값 대신 `string` 로그 수준 이름을 나타내는 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="752e7-204">이는 SignalR 상수에 대 한 액세스 권한이 없는 환경에서 로깅을 구성할 때 유용 `LogLevel` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="752e7-205">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-205">The following table lists the available log levels.</span></span> <span data-ttu-id="752e7-206">사용자가 지정 하는 값은 `configureLogging` 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="752e7-207">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="752e7-208">String</span><span class="sxs-lookup"><span data-stu-id="752e7-208">String</span></span>                      | <span data-ttu-id="752e7-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="752e7-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="752e7-210">`info` **또는** `information`</span><span class="sxs-lookup"><span data-stu-id="752e7-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="752e7-211">`warn` **또는** `warning`</span><span class="sxs-lookup"><span data-stu-id="752e7-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="752e7-212">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="752e7-213">로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="752e7-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="752e7-214">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="752e7-215">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="752e7-216">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="752e7-217">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="752e7-218">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="752e7-219">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-219">Configure allowed transports</span></span>

<span data-ttu-id="752e7-220">에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="752e7-221">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="752e7-222">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-222">All transports are enabled by default.</span></span>

<span data-ttu-id="752e7-223">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="752e7-224">JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="752e7-225">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="752e7-226">Java 클라이언트에서의 메서드를 사용 하 여 전송이 선택 됩니다 `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="752e7-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="752e7-227">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="752e7-228">SignalRJava 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="752e7-229">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-229">Configure bearer authentication</span></span>

<span data-ttu-id="752e7-230">요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="752e7-231">.NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="752e7-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="752e7-232">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="752e7-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="752e7-233">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .</span><span class="sxs-lookup"><span data-stu-id="752e7-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="752e7-234">.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="752e7-235">JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="752e7-236">SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="752e7-237">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="752e7-238">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="752e7-239">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="752e7-240">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="752e7-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="752e7-241">.NET</span><span class="sxs-lookup"><span data-stu-id="752e7-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="752e7-242">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-242">Option</span></span> | <span data-ttu-id="752e7-243">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-243">Default value</span></span> | <span data-ttu-id="752e7-244">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="752e7-245">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="752e7-246">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-246">Timeout for server activity.</span></span> <span data-ttu-id="752e7-247">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="752e7-248">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="752e7-249">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="752e7-250">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-250">15 seconds</span></span> | <span data-ttu-id="752e7-251">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="752e7-252">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="752e7-253">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="752e7-254">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="752e7-255">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-255">15 seconds</span></span> | <span data-ttu-id="752e7-256">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="752e7-257">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="752e7-258">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="752e7-259">.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="752e7-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="752e7-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="752e7-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="752e7-261">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-261">Option</span></span> | <span data-ttu-id="752e7-262">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-262">Default value</span></span> | <span data-ttu-id="752e7-263">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="752e7-264">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="752e7-265">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-265">Timeout for server activity.</span></span> <span data-ttu-id="752e7-266">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="752e7-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="752e7-267">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="752e7-268">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="752e7-269">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="752e7-270">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="752e7-271">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="752e7-272">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="752e7-273">Java</span><span class="sxs-lookup"><span data-stu-id="752e7-273">Java</span></span>](#tab/java)

| <span data-ttu-id="752e7-274">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-274">Option</span></span> | <span data-ttu-id="752e7-275">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-275">Default value</span></span> | <span data-ttu-id="752e7-276">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="752e7-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="752e7-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="752e7-278">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="752e7-279">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-279">Timeout for server activity.</span></span> <span data-ttu-id="752e7-280">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="752e7-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="752e7-281">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="752e7-282">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="752e7-283">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-283">15 seconds</span></span> | <span data-ttu-id="752e7-284">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="752e7-285">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="752e7-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="752e7-286">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="752e7-287">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="752e7-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="752e7-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="752e7-289">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="752e7-290">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="752e7-291">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="752e7-292">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="752e7-293">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-293">Configure additional options</span></span>

<span data-ttu-id="752e7-294">`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="752e7-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="752e7-295">.NET</span><span class="sxs-lookup"><span data-stu-id="752e7-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="752e7-296">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-296">.NET Option</span></span> |  <span data-ttu-id="752e7-297">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-297">Default value</span></span> | <span data-ttu-id="752e7-298">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="752e7-299">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="752e7-300">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="752e7-301">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="752e7-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="752e7-302">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="752e7-303">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-303">Empty</span></span> | <span data-ttu-id="752e7-304">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="752e7-305">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-305">Empty</span></span> | <span data-ttu-id="752e7-306">cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="752e7-307">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-307">Empty</span></span> | <span data-ttu-id="752e7-308">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="752e7-309">5초</span><span class="sxs-lookup"><span data-stu-id="752e7-309">5 seconds</span></span> | <span data-ttu-id="752e7-310">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-310">WebSockets only.</span></span> <span data-ttu-id="752e7-311">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="752e7-312">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="752e7-313">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-313">Empty</span></span> | <span data-ttu-id="752e7-314">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="752e7-315">HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="752e7-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="752e7-316">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="752e7-317">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="752e7-318">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="752e7-319">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="752e7-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="752e7-320">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="752e7-321">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="752e7-322">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="752e7-323">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="752e7-324">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="752e7-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="752e7-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="752e7-326">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-326">JavaScript Option</span></span> | <span data-ttu-id="752e7-327">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-327">Default Value</span></span> | <span data-ttu-id="752e7-328">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="752e7-329">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="752e7-330">모든 HTTP 요청과 함께 전송 되는 헤더의 사전입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="752e7-331">브라우저에서 헤더 보내기가 Websocket 또는 스트림에 대해 작동 하지 않습니다 <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> .</span><span class="sxs-lookup"><span data-stu-id="752e7-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="752e7-332">`true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="752e7-333">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="752e7-334">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="752e7-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="752e7-335">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="752e7-336">CORS 요청과 함께 자격 증명을 보낼지 여부를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="752e7-337">Azure App Service는 cookie 고정 세션에 s를 사용 하 고 올바르게 작동 하려면이 옵션을 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="752e7-338">CORS에 대 한 자세한 SignalR 내용은을 참조 하십시오 <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="752e7-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="752e7-339">Java</span><span class="sxs-lookup"><span data-stu-id="752e7-339">Java</span></span>](#tab/java)

| <span data-ttu-id="752e7-340">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-340">Java Option</span></span> | <span data-ttu-id="752e7-341">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-341">Default Value</span></span> | <span data-ttu-id="752e7-342">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="752e7-343">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="752e7-344">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="752e7-345">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="752e7-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="752e7-346">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="752e7-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="752e7-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="752e7-348">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-348">Empty</span></span> | <span data-ttu-id="752e7-349">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="752e7-350">.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="752e7-351">JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="752e7-352">Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="752e7-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="752e7-353">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="752e7-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="752e7-354">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="752e7-355">ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="752e7-356">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="752e7-357">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="752e7-358">`AddJsonProtocol`[추가 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="752e7-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="752e7-359">`AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` .</span><span class="sxs-lookup"><span data-stu-id="752e7-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="752e7-360">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 인수와 반환 값의 serialization을 구성 하는 데 사용할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="752e7-361">자세한 내용은 [설명서의System.Text.Js](/dotnet/api/system.text.json)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="752e7-362">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="752e7-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="752e7-363">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="752e7-364">`Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="752e7-365">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="752e7-366">Newtonsoft.Js로 전환</span><span class="sxs-lookup"><span data-stu-id="752e7-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="752e7-367">`Newtonsoft.Json`에서 지원 되지 않는 기능이 필요한 경우 `System.Text.Json` [Newtonsoft.Js켜기로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="752e7-368">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-368">MessagePack serialization options</span></span>

<span data-ttu-id="752e7-369">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="752e7-370">자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="752e7-371">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="752e7-372">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-372">Configure server options</span></span>

<span data-ttu-id="752e7-373">다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="752e7-374">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-374">Option</span></span> | <span data-ttu-id="752e7-375">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-375">Default Value</span></span> | <span data-ttu-id="752e7-376">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="752e7-377">30초</span><span class="sxs-lookup"><span data-stu-id="752e7-377">30 seconds</span></span> | <span data-ttu-id="752e7-378">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="752e7-379">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="752e7-380">권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="752e7-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="752e7-381">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-381">15 seconds</span></span> | <span data-ttu-id="752e7-382">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="752e7-383">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="752e7-384">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="752e7-385">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-385">15 seconds</span></span> | <span data-ttu-id="752e7-386">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="752e7-387">변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="752e7-388">권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="752e7-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="752e7-389">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="752e7-389">All installed protocols</span></span> | <span data-ttu-id="752e7-390">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-390">Protocols supported by this hub.</span></span> <span data-ttu-id="752e7-391">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="752e7-392">이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="752e7-393">기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="752e7-394">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="752e7-395">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="752e7-396">32KB</span><span class="sxs-lookup"><span data-stu-id="752e7-396">32 KB</span></span> | <span data-ttu-id="752e7-397">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="752e7-398">에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="752e7-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="752e7-399">단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .</span><span class="sxs-lookup"><span data-stu-id="752e7-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="752e7-400">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="752e7-401">`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="752e7-402">이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="752e7-403">다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="752e7-404">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-404">Option</span></span> | <span data-ttu-id="752e7-405">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-405">Default Value</span></span> | <span data-ttu-id="752e7-406">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="752e7-407">32KB</span><span class="sxs-lookup"><span data-stu-id="752e7-407">32 KB</span></span> | <span data-ttu-id="752e7-408">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="752e7-409">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="752e7-410">허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="752e7-411">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="752e7-412">32KB</span><span class="sxs-lookup"><span data-stu-id="752e7-412">32 KB</span></span> | <span data-ttu-id="752e7-413">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="752e7-414">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="752e7-415">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-415">All Transports are enabled.</span></span> | <span data-ttu-id="752e7-416">`HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="752e7-417">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-417">See below.</span></span> | <span data-ttu-id="752e7-418">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="752e7-419">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-419">See below.</span></span> | <span data-ttu-id="752e7-420">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="752e7-421">0</span><span class="sxs-lookup"><span data-stu-id="752e7-421">0</span></span> | <span data-ttu-id="752e7-422">Negotiate 프로토콜의 최소 버전을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="752e7-423">클라이언트를 최신 버전으로 제한 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="752e7-424">긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .</span><span class="sxs-lookup"><span data-stu-id="752e7-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="752e7-425">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-425">Option</span></span> | <span data-ttu-id="752e7-426">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-426">Default Value</span></span> | <span data-ttu-id="752e7-427">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="752e7-428">90 초</span><span class="sxs-lookup"><span data-stu-id="752e7-428">90 seconds</span></span> | <span data-ttu-id="752e7-429">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="752e7-430">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="752e7-431">WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .</span><span class="sxs-lookup"><span data-stu-id="752e7-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="752e7-432">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-432">Option</span></span> | <span data-ttu-id="752e7-433">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-433">Default Value</span></span> | <span data-ttu-id="752e7-434">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="752e7-435">5초</span><span class="sxs-lookup"><span data-stu-id="752e7-435">5 seconds</span></span> | <span data-ttu-id="752e7-436">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="752e7-437">`Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="752e7-438">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="752e7-439">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-439">Configure client options</span></span>

<span data-ttu-id="752e7-440">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="752e7-441">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="752e7-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="752e7-442">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-442">Configure logging</span></span>

<span data-ttu-id="752e7-443">로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="752e7-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="752e7-444">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="752e7-445">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="752e7-446">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="752e7-447">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="752e7-448">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="752e7-449">확장 메서드를 호출 합니다 `AddConsole` .</span><span class="sxs-lookup"><span data-stu-id="752e7-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="752e7-450">JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="752e7-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="752e7-451">`LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="752e7-452">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="752e7-453">`LogLevel`값 대신 `string` 로그 수준 이름을 나타내는 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="752e7-454">이는 SignalR 상수에 대 한 액세스 권한이 없는 환경에서 로깅을 구성할 때 유용 `LogLevel` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="752e7-455">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-455">The following table lists the available log levels.</span></span> <span data-ttu-id="752e7-456">사용자가 지정 하는 값은 `configureLogging` 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="752e7-457">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="752e7-458">String</span><span class="sxs-lookup"><span data-stu-id="752e7-458">String</span></span>                      | <span data-ttu-id="752e7-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="752e7-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="752e7-460">`info` **또는** `information`</span><span class="sxs-lookup"><span data-stu-id="752e7-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="752e7-461">`warn` **또는** `warning`</span><span class="sxs-lookup"><span data-stu-id="752e7-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="752e7-462">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="752e7-463">로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="752e7-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="752e7-464">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="752e7-465">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="752e7-466">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="752e7-467">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="752e7-468">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="752e7-469">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-469">Configure allowed transports</span></span>

<span data-ttu-id="752e7-470">에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="752e7-471">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="752e7-472">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-472">All transports are enabled by default.</span></span>

<span data-ttu-id="752e7-473">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="752e7-474">JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="752e7-475">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="752e7-476">Java 클라이언트에서의 메서드를 사용 하 여 전송이 선택 됩니다 `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="752e7-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="752e7-477">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="752e7-478">SignalRJava 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="752e7-479">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-479">Configure bearer authentication</span></span>

<span data-ttu-id="752e7-480">요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="752e7-481">.NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="752e7-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="752e7-482">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="752e7-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="752e7-483">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .</span><span class="sxs-lookup"><span data-stu-id="752e7-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="752e7-484">.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="752e7-485">JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="752e7-486">SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="752e7-487">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="752e7-488">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="752e7-489">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="752e7-490">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="752e7-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="752e7-491">.NET</span><span class="sxs-lookup"><span data-stu-id="752e7-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="752e7-492">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-492">Option</span></span> | <span data-ttu-id="752e7-493">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-493">Default value</span></span> | <span data-ttu-id="752e7-494">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="752e7-495">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="752e7-496">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-496">Timeout for server activity.</span></span> <span data-ttu-id="752e7-497">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="752e7-498">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="752e7-499">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="752e7-500">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-500">15 seconds</span></span> | <span data-ttu-id="752e7-501">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="752e7-502">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="752e7-503">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="752e7-504">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="752e7-505">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-505">15 seconds</span></span> | <span data-ttu-id="752e7-506">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="752e7-507">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="752e7-508">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="752e7-509">.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="752e7-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="752e7-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="752e7-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="752e7-511">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-511">Option</span></span> | <span data-ttu-id="752e7-512">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-512">Default value</span></span> | <span data-ttu-id="752e7-513">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="752e7-514">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="752e7-515">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-515">Timeout for server activity.</span></span> <span data-ttu-id="752e7-516">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="752e7-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="752e7-517">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="752e7-518">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="752e7-519">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="752e7-520">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="752e7-521">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="752e7-522">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="752e7-523">Java</span><span class="sxs-lookup"><span data-stu-id="752e7-523">Java</span></span>](#tab/java)

| <span data-ttu-id="752e7-524">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-524">Option</span></span> | <span data-ttu-id="752e7-525">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-525">Default value</span></span> | <span data-ttu-id="752e7-526">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="752e7-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="752e7-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="752e7-528">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="752e7-529">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-529">Timeout for server activity.</span></span> <span data-ttu-id="752e7-530">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="752e7-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="752e7-531">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="752e7-532">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="752e7-533">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-533">15 seconds</span></span> | <span data-ttu-id="752e7-534">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="752e7-535">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="752e7-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="752e7-536">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="752e7-537">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="752e7-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="752e7-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="752e7-539">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="752e7-540">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="752e7-541">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="752e7-542">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="752e7-543">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-543">Configure additional options</span></span>

<span data-ttu-id="752e7-544">`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="752e7-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="752e7-545">.NET</span><span class="sxs-lookup"><span data-stu-id="752e7-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="752e7-546">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-546">.NET Option</span></span> |  <span data-ttu-id="752e7-547">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-547">Default value</span></span> | <span data-ttu-id="752e7-548">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="752e7-549">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="752e7-550">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="752e7-551">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="752e7-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="752e7-552">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="752e7-553">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-553">Empty</span></span> | <span data-ttu-id="752e7-554">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="752e7-555">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-555">Empty</span></span> | <span data-ttu-id="752e7-556">cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="752e7-557">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-557">Empty</span></span> | <span data-ttu-id="752e7-558">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="752e7-559">5초</span><span class="sxs-lookup"><span data-stu-id="752e7-559">5 seconds</span></span> | <span data-ttu-id="752e7-560">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-560">WebSockets only.</span></span> <span data-ttu-id="752e7-561">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="752e7-562">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="752e7-563">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-563">Empty</span></span> | <span data-ttu-id="752e7-564">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="752e7-565">HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="752e7-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="752e7-566">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="752e7-567">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="752e7-568">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="752e7-569">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="752e7-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="752e7-570">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="752e7-571">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="752e7-572">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="752e7-573">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="752e7-574">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="752e7-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="752e7-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="752e7-576">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-576">JavaScript Option</span></span> | <span data-ttu-id="752e7-577">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-577">Default Value</span></span> | <span data-ttu-id="752e7-578">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="752e7-579">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="752e7-580">`true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="752e7-581">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="752e7-582">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="752e7-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="752e7-583">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="752e7-584">Java</span><span class="sxs-lookup"><span data-stu-id="752e7-584">Java</span></span>](#tab/java)

| <span data-ttu-id="752e7-585">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-585">Java Option</span></span> | <span data-ttu-id="752e7-586">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-586">Default Value</span></span> | <span data-ttu-id="752e7-587">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="752e7-588">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="752e7-589">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="752e7-590">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="752e7-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="752e7-591">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="752e7-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="752e7-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="752e7-593">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-593">Empty</span></span> | <span data-ttu-id="752e7-594">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="752e7-595">.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="752e7-596">JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="752e7-597">Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="752e7-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="752e7-598">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="752e7-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="752e7-599">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="752e7-600">ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="752e7-601">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="752e7-602">JSON serialization은 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="752e7-603">`AddJsonProtocol`[추가 SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="752e7-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="752e7-604">`AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` .</span><span class="sxs-lookup"><span data-stu-id="752e7-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="752e7-605">해당 개체의 [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) 속성은 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 인수와 반환 값의 serialization을 구성 하는 데 사용할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="752e7-606">자세한 내용은 [설명서의System.Text.Js](/dotnet/api/system.text.json)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="752e7-607">예를 들어 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경 하지 않도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="752e7-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="752e7-608">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="752e7-609">`Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="752e7-610">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="752e7-611">Newtonsoft.Js로 전환</span><span class="sxs-lookup"><span data-stu-id="752e7-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="752e7-612">`Newtonsoft.Json`에서 지원 되지 않는 기능이 필요한 경우 `System.Text.Json` [Newtonsoft.Js켜기로 전환](xref:migration/22-to-30#switch-to-newtonsoftjson)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="752e7-613">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-613">MessagePack serialization options</span></span>

<span data-ttu-id="752e7-614">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="752e7-615">자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="752e7-616">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="752e7-617">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-617">Configure server options</span></span>

<span data-ttu-id="752e7-618">다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="752e7-619">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-619">Option</span></span> | <span data-ttu-id="752e7-620">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-620">Default Value</span></span> | <span data-ttu-id="752e7-621">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="752e7-622">30초</span><span class="sxs-lookup"><span data-stu-id="752e7-622">30 seconds</span></span> | <span data-ttu-id="752e7-623">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="752e7-624">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="752e7-625">권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="752e7-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="752e7-626">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-626">15 seconds</span></span> | <span data-ttu-id="752e7-627">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="752e7-628">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="752e7-629">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="752e7-630">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-630">15 seconds</span></span> | <span data-ttu-id="752e7-631">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="752e7-632">변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="752e7-633">권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="752e7-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="752e7-634">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="752e7-634">All installed protocols</span></span> | <span data-ttu-id="752e7-635">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-635">Protocols supported by this hub.</span></span> <span data-ttu-id="752e7-636">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="752e7-637">이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="752e7-638">기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="752e7-639">클라이언트 업로드 스트림에 대해 버퍼링 할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="752e7-640">이 제한에 도달 하면 서버에서 스트림 항목을 처리할 때까지 호출 처리가 차단 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="752e7-641">32KB</span><span class="sxs-lookup"><span data-stu-id="752e7-641">32 KB</span></span> | <span data-ttu-id="752e7-642">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="752e7-643">에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="752e7-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="752e7-644">단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .</span><span class="sxs-lookup"><span data-stu-id="752e7-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="752e7-645">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="752e7-646">`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="752e7-647">이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="752e7-648">다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="752e7-649">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-649">Option</span></span> | <span data-ttu-id="752e7-650">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-650">Default Value</span></span> | <span data-ttu-id="752e7-651">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="752e7-652">32KB</span><span class="sxs-lookup"><span data-stu-id="752e7-652">32 KB</span></span> | <span data-ttu-id="752e7-653">클라이언트에서 수신 된 백 압력을 적용 하기 전에 버퍼링 되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="752e7-654">이 값을 늘리면 서버는 백 압력을 적용 하지 않고 더 큰 메시지를 더 빨리 수신할 수 있지만 메모리 사용을 높일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="752e7-655">허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="752e7-656">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="752e7-657">32KB</span><span class="sxs-lookup"><span data-stu-id="752e7-657">32 KB</span></span> | <span data-ttu-id="752e7-658">서버에서 백 압력을 관찰 하기 전에 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="752e7-659">이 값을 늘리면 서버에서 백 압력을 대기 하지 않고 더 큰 메시지를 더 빨리 버퍼링 할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="752e7-660">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-660">All Transports are enabled.</span></span> | <span data-ttu-id="752e7-661">`HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="752e7-662">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-662">See below.</span></span> | <span data-ttu-id="752e7-663">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="752e7-664">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-664">See below.</span></span> | <span data-ttu-id="752e7-665">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="752e7-666">긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .</span><span class="sxs-lookup"><span data-stu-id="752e7-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="752e7-667">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-667">Option</span></span> | <span data-ttu-id="752e7-668">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-668">Default Value</span></span> | <span data-ttu-id="752e7-669">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="752e7-670">90 초</span><span class="sxs-lookup"><span data-stu-id="752e7-670">90 seconds</span></span> | <span data-ttu-id="752e7-671">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="752e7-672">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="752e7-673">WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .</span><span class="sxs-lookup"><span data-stu-id="752e7-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="752e7-674">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-674">Option</span></span> | <span data-ttu-id="752e7-675">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-675">Default Value</span></span> | <span data-ttu-id="752e7-676">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="752e7-677">5초</span><span class="sxs-lookup"><span data-stu-id="752e7-677">5 seconds</span></span> | <span data-ttu-id="752e7-678">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="752e7-679">`Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="752e7-680">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="752e7-681">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-681">Configure client options</span></span>

<span data-ttu-id="752e7-682">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="752e7-683">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="752e7-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="752e7-684">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-684">Configure logging</span></span>

<span data-ttu-id="752e7-685">로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="752e7-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="752e7-686">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="752e7-687">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="752e7-688">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="752e7-689">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="752e7-690">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="752e7-691">확장 메서드를 호출 합니다 `AddConsole` .</span><span class="sxs-lookup"><span data-stu-id="752e7-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="752e7-692">JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="752e7-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="752e7-693">`LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="752e7-694">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="752e7-695">`LogLevel`값 대신 `string` 로그 수준 이름을 나타내는 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="752e7-696">이는 SignalR 상수에 대 한 액세스 권한이 없는 환경에서 로깅을 구성할 때 유용 `LogLevel` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="752e7-697">다음 표에서는 사용 가능한 로그 수준을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-697">The following table lists the available log levels.</span></span> <span data-ttu-id="752e7-698">사용자가 지정 하는 값은 `configureLogging` 로깅할 **최소** 로그 수준을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="752e7-699">이 수준에서 기록 되 **는 메시지 또는 테이블의 이후 나열 된 수준**에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="752e7-700">String</span><span class="sxs-lookup"><span data-stu-id="752e7-700">String</span></span>                      | <span data-ttu-id="752e7-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="752e7-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="752e7-702">`info` **또는** `information`</span><span class="sxs-lookup"><span data-stu-id="752e7-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="752e7-703">`warn` **또는** `warning`</span><span class="sxs-lookup"><span data-stu-id="752e7-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="752e7-704">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="752e7-705">로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="752e7-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="752e7-706">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="752e7-707">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="752e7-708">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="752e7-709">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="752e7-710">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="752e7-711">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-711">Configure allowed transports</span></span>

<span data-ttu-id="752e7-712">에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="752e7-713">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="752e7-714">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-714">All transports are enabled by default.</span></span>

<span data-ttu-id="752e7-715">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="752e7-716">JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="752e7-717">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="752e7-718">Java 클라이언트에서의 메서드를 사용 하 여 전송이 선택 됩니다 `withTransport` `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="752e7-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="752e7-719">Java 클라이언트는 기본적으로 Websocket 전송을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="752e7-720">SignalRJava 클라이언트는 아직 전송 대체를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="752e7-721">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-721">Configure bearer authentication</span></span>

<span data-ttu-id="752e7-722">요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="752e7-723">.NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="752e7-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="752e7-724">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="752e7-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="752e7-725">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .</span><span class="sxs-lookup"><span data-stu-id="752e7-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="752e7-726">.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="752e7-727">JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="752e7-728">SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="752e7-729">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="752e7-730">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="752e7-731">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="752e7-732">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="752e7-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="752e7-733">.NET</span><span class="sxs-lookup"><span data-stu-id="752e7-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="752e7-734">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-734">Option</span></span> | <span data-ttu-id="752e7-735">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-735">Default value</span></span> | <span data-ttu-id="752e7-736">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="752e7-737">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="752e7-738">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-738">Timeout for server activity.</span></span> <span data-ttu-id="752e7-739">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="752e7-740">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="752e7-741">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="752e7-742">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-742">15 seconds</span></span> | <span data-ttu-id="752e7-743">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="752e7-744">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="752e7-745">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="752e7-746">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="752e7-747">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-747">15 seconds</span></span> | <span data-ttu-id="752e7-748">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="752e7-749">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="752e7-750">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="752e7-751">.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="752e7-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="752e7-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="752e7-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="752e7-753">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-753">Option</span></span> | <span data-ttu-id="752e7-754">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-754">Default value</span></span> | <span data-ttu-id="752e7-755">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="752e7-756">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="752e7-757">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-757">Timeout for server activity.</span></span> <span data-ttu-id="752e7-758">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="752e7-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="752e7-759">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="752e7-760">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="752e7-761">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="752e7-762">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="752e7-763">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="752e7-764">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="752e7-765">Java</span><span class="sxs-lookup"><span data-stu-id="752e7-765">Java</span></span>](#tab/java)

| <span data-ttu-id="752e7-766">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-766">Option</span></span> | <span data-ttu-id="752e7-767">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-767">Default value</span></span> | <span data-ttu-id="752e7-768">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="752e7-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="752e7-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="752e7-770">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="752e7-771">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-771">Timeout for server activity.</span></span> <span data-ttu-id="752e7-772">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="752e7-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="752e7-773">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="752e7-774">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="752e7-775">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-775">15 seconds</span></span> | <span data-ttu-id="752e7-776">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="752e7-777">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="752e7-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="752e7-778">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="752e7-779">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="752e7-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="752e7-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="752e7-781">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="752e7-782">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="752e7-783">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="752e7-784">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="752e7-785">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-785">Configure additional options</span></span>

<span data-ttu-id="752e7-786">`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="752e7-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="752e7-787">.NET</span><span class="sxs-lookup"><span data-stu-id="752e7-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="752e7-788">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-788">.NET Option</span></span> |  <span data-ttu-id="752e7-789">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-789">Default value</span></span> | <span data-ttu-id="752e7-790">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="752e7-791">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="752e7-792">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="752e7-793">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="752e7-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="752e7-794">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="752e7-795">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-795">Empty</span></span> | <span data-ttu-id="752e7-796">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="752e7-797">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-797">Empty</span></span> | <span data-ttu-id="752e7-798">cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="752e7-799">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-799">Empty</span></span> | <span data-ttu-id="752e7-800">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="752e7-801">5초</span><span class="sxs-lookup"><span data-stu-id="752e7-801">5 seconds</span></span> | <span data-ttu-id="752e7-802">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-802">WebSockets only.</span></span> <span data-ttu-id="752e7-803">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="752e7-804">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="752e7-805">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-805">Empty</span></span> | <span data-ttu-id="752e7-806">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="752e7-807">HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="752e7-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="752e7-808">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="752e7-809">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="752e7-810">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="752e7-811">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="752e7-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="752e7-812">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="752e7-813">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="752e7-814">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="752e7-815">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="752e7-816">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="752e7-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="752e7-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="752e7-818">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-818">JavaScript Option</span></span> | <span data-ttu-id="752e7-819">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-819">Default Value</span></span> | <span data-ttu-id="752e7-820">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="752e7-821">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="752e7-822">`true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="752e7-823">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="752e7-824">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="752e7-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="752e7-825">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="752e7-826">Java</span><span class="sxs-lookup"><span data-stu-id="752e7-826">Java</span></span>](#tab/java)

| <span data-ttu-id="752e7-827">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-827">Java Option</span></span> | <span data-ttu-id="752e7-828">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-828">Default Value</span></span> | <span data-ttu-id="752e7-829">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="752e7-830">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="752e7-831">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="752e7-832">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="752e7-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="752e7-833">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="752e7-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="752e7-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="752e7-835">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-835">Empty</span></span> | <span data-ttu-id="752e7-836">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="752e7-837">.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="752e7-838">JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="752e7-839">Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="752e7-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="752e7-840">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="752e7-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="752e7-841">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="752e7-842">ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="752e7-843">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="752e7-844">[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 JSON serialization을 구성할 수 있습니다 .이 메서드는 [메서드에 SignalR 추가](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="752e7-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="752e7-845">`AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` .</span><span class="sxs-lookup"><span data-stu-id="752e7-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="752e7-846">해당 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 `JsonSerializerSettings` 인수 및 반환 값의 serialization을 구성 하는 데 사용할 수 있는 JSON.NET 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="752e7-847">자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="752e7-848">예를 들어 기본 "camelCase" 이름 대신 "대/소문자 구분" 속성 이름을 사용 하도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="752e7-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="752e7-849">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="752e7-850">`Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="752e7-851">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="752e7-852">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-852">MessagePack serialization options</span></span>

<span data-ttu-id="752e7-853">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="752e7-854">자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="752e7-855">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="752e7-856">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-856">Configure server options</span></span>

<span data-ttu-id="752e7-857">다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="752e7-858">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-858">Option</span></span> | <span data-ttu-id="752e7-859">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-859">Default Value</span></span> | <span data-ttu-id="752e7-860">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="752e7-861">30초</span><span class="sxs-lookup"><span data-stu-id="752e7-861">30 seconds</span></span> | <span data-ttu-id="752e7-862">서버는이 간격 내에 메시지를 받지 못한 경우 (keep-alive 포함) 클라이언트의 연결을 해제 하는 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="752e7-863">이를 구현 하는 방법으로 인해 클라이언트에서 실제로 연결이 끊어진 것으로 표시 되는 시간 제한 간격 보다 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="752e7-864">권장 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="752e7-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="752e7-865">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-865">15 seconds</span></span> | <span data-ttu-id="752e7-866">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="752e7-867">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="752e7-868">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="752e7-869">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-869">15 seconds</span></span> | <span data-ttu-id="752e7-870">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="752e7-871">변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="752e7-872">권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="752e7-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="752e7-873">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="752e7-873">All installed protocols</span></span> | <span data-ttu-id="752e7-874">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-874">Protocols supported by this hub.</span></span> <span data-ttu-id="752e7-875">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="752e7-876">이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="752e7-877">기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="752e7-878">에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="752e7-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="752e7-879">단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .</span><span class="sxs-lookup"><span data-stu-id="752e7-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="752e7-880">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="752e7-881">`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="752e7-882">이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="752e7-883">다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="752e7-884">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-884">Option</span></span> | <span data-ttu-id="752e7-885">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-885">Default Value</span></span> | <span data-ttu-id="752e7-886">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="752e7-887">32KB</span><span class="sxs-lookup"><span data-stu-id="752e7-887">32 KB</span></span> | <span data-ttu-id="752e7-888">서버에서 버퍼링 하는 클라이언트로부터 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="752e7-889">이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="752e7-890">허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="752e7-891">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="752e7-892">32KB</span><span class="sxs-lookup"><span data-stu-id="752e7-892">32 KB</span></span> | <span data-ttu-id="752e7-893">서버가 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="752e7-894">이 값을 늘리면 서버에서 더 큰 메시지를 보낼 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="752e7-895">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-895">All Transports are enabled.</span></span> | <span data-ttu-id="752e7-896">`HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="752e7-897">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-897">See below.</span></span> | <span data-ttu-id="752e7-898">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="752e7-899">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-899">See below.</span></span> | <span data-ttu-id="752e7-900">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="752e7-901">긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .</span><span class="sxs-lookup"><span data-stu-id="752e7-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="752e7-902">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-902">Option</span></span> | <span data-ttu-id="752e7-903">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-903">Default Value</span></span> | <span data-ttu-id="752e7-904">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="752e7-905">90 초</span><span class="sxs-lookup"><span data-stu-id="752e7-905">90 seconds</span></span> | <span data-ttu-id="752e7-906">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="752e7-907">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="752e7-908">WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .</span><span class="sxs-lookup"><span data-stu-id="752e7-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="752e7-909">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-909">Option</span></span> | <span data-ttu-id="752e7-910">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-910">Default Value</span></span> | <span data-ttu-id="752e7-911">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="752e7-912">5초</span><span class="sxs-lookup"><span data-stu-id="752e7-912">5 seconds</span></span> | <span data-ttu-id="752e7-913">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="752e7-914">`Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="752e7-915">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="752e7-916">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-916">Configure client options</span></span>

<span data-ttu-id="752e7-917">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="752e7-918">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="752e7-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="752e7-919">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-919">Configure logging</span></span>

<span data-ttu-id="752e7-920">로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="752e7-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="752e7-921">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="752e7-922">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="752e7-923">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="752e7-924">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="752e7-925">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="752e7-926">확장 메서드를 호출 합니다 `AddConsole` .</span><span class="sxs-lookup"><span data-stu-id="752e7-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="752e7-927">JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="752e7-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="752e7-928">`LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="752e7-929">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="752e7-930">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="752e7-931">로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="752e7-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="752e7-932">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="752e7-933">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="752e7-934">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="752e7-935">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="752e7-936">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="752e7-937">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-937">Configure allowed transports</span></span>

<span data-ttu-id="752e7-938">에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="752e7-939">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="752e7-940">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-940">All transports are enabled by default.</span></span>

<span data-ttu-id="752e7-941">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="752e7-942">JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="752e7-943">이 버전의 Java 클라이언트 websocket은 유일 하 게 사용할 수 있는 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="752e7-944">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-944">Configure bearer authentication</span></span>

<span data-ttu-id="752e7-945">요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="752e7-946">.NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="752e7-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="752e7-947">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="752e7-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="752e7-948">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .</span><span class="sxs-lookup"><span data-stu-id="752e7-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="752e7-949">.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="752e7-950">JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="752e7-951">SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="752e7-952">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="752e7-953">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="752e7-954">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="752e7-955">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="752e7-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="752e7-956">.NET</span><span class="sxs-lookup"><span data-stu-id="752e7-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="752e7-957">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-957">Option</span></span> | <span data-ttu-id="752e7-958">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-958">Default value</span></span> | <span data-ttu-id="752e7-959">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="752e7-960">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="752e7-961">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-961">Timeout for server activity.</span></span> <span data-ttu-id="752e7-962">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="752e7-963">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="752e7-964">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="752e7-965">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-965">15 seconds</span></span> | <span data-ttu-id="752e7-966">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="752e7-967">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="752e7-968">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="752e7-969">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="752e7-970">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-970">15 seconds</span></span> | <span data-ttu-id="752e7-971">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="752e7-972">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="752e7-973">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="752e7-974">.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="752e7-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="752e7-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="752e7-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="752e7-976">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-976">Option</span></span> | <span data-ttu-id="752e7-977">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-977">Default value</span></span> | <span data-ttu-id="752e7-978">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="752e7-979">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="752e7-980">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-980">Timeout for server activity.</span></span> <span data-ttu-id="752e7-981">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="752e7-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="752e7-982">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="752e7-983">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="752e7-984">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="752e7-985">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="752e7-986">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="752e7-987">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="752e7-988">Java</span><span class="sxs-lookup"><span data-stu-id="752e7-988">Java</span></span>](#tab/java)

| <span data-ttu-id="752e7-989">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-989">Option</span></span> | <span data-ttu-id="752e7-990">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-990">Default value</span></span> | <span data-ttu-id="752e7-991">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="752e7-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="752e7-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="752e7-993">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="752e7-994">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-994">Timeout for server activity.</span></span> <span data-ttu-id="752e7-995">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="752e7-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="752e7-996">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="752e7-997">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="752e7-998">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-998">15 seconds</span></span> | <span data-ttu-id="752e7-999">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="752e7-1000">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="752e7-1001">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="752e7-1002">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="752e7-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="752e7-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="752e7-1004">15 초 (15000 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="752e7-1005">클라이언트에서 ping 메시지를 보내는 간격을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="752e7-1006">클라이언트에서 메시지를 보내면 타이머를 간격의 시작으로 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="752e7-1007">클라이언트에서 서버의 집합에 있는 메시지를 보내지 않은 경우 `ClientTimeoutInterval` 서버는 클라이언트의 연결을 끊은 것으로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="752e7-1008">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1008">Configure additional options</span></span>

<span data-ttu-id="752e7-1009">`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="752e7-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="752e7-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="752e7-1011">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1011">.NET Option</span></span> |  <span data-ttu-id="752e7-1012">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-1012">Default value</span></span> | <span data-ttu-id="752e7-1013">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="752e7-1014">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="752e7-1015">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="752e7-1016">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="752e7-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="752e7-1017">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="752e7-1018">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-1018">Empty</span></span> | <span data-ttu-id="752e7-1019">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="752e7-1020">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-1020">Empty</span></span> | <span data-ttu-id="752e7-1021">cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="752e7-1022">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-1022">Empty</span></span> | <span data-ttu-id="752e7-1023">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="752e7-1024">5초</span><span class="sxs-lookup"><span data-stu-id="752e7-1024">5 seconds</span></span> | <span data-ttu-id="752e7-1025">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1025">WebSockets only.</span></span> <span data-ttu-id="752e7-1026">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="752e7-1027">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="752e7-1028">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-1028">Empty</span></span> | <span data-ttu-id="752e7-1029">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="752e7-1030">HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="752e7-1031">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="752e7-1032">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="752e7-1033">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="752e7-1034">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="752e7-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="752e7-1035">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="752e7-1036">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="752e7-1037">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="752e7-1038">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="752e7-1039">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="752e7-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="752e7-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="752e7-1041">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1041">JavaScript Option</span></span> | <span data-ttu-id="752e7-1042">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-1042">Default Value</span></span> | <span data-ttu-id="752e7-1043">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="752e7-1044">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="752e7-1045">`true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="752e7-1046">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="752e7-1047">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="752e7-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="752e7-1048">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="752e7-1049">Java</span><span class="sxs-lookup"><span data-stu-id="752e7-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="752e7-1050">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1050">Java Option</span></span> | <span data-ttu-id="752e7-1051">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-1051">Default Value</span></span> | <span data-ttu-id="752e7-1052">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="752e7-1053">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="752e7-1054">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="752e7-1055">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="752e7-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="752e7-1056">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="752e7-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="752e7-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="752e7-1058">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-1058">Empty</span></span> | <span data-ttu-id="752e7-1059">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="752e7-1060">.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="752e7-1061">JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="752e7-1062">Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="752e7-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="752e7-1063">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="752e7-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="752e7-1064">JSON/MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="752e7-1065">ASP.NET Core SignalR 는 메시지 인코딩에 [JSON](https://www.json.org/) 및 [MessagePack](https://msgpack.org/index.html)의 두 가지 프로토콜을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="752e7-1066">각 프로토콜에는 serialization 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="752e7-1067">[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 JSON serialization을 구성할 수 있습니다 .이 메서드는 [메서드에 SignalR 추가](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 된 후에 추가할 수 있습니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="752e7-1068">`AddJsonProtocol`메서드는 개체를 받는 대리자를 사용 합니다 `options` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="752e7-1069">해당 개체의 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 `JsonSerializerSettings` 인수 및 반환 값의 serialization을 구성 하는 데 사용할 수 있는 JSON.NET 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="752e7-1070">자세한 내용은 [JSON.NET 설명서](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="752e7-1071">예를 들어 기본 "camelCase" 이름 대신 "대/소문자 구분" 속성 이름을 사용 하도록 serializer를 구성 하려면에서 다음 코드를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="752e7-1072">.NET 클라이언트에서는 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="752e7-1073">`Microsoft.Extensions.DependencyInjection`확장 메서드를 확인 하려면 네임 스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="752e7-1074">지금은 JavaScript 클라이언트에서 JSON serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="752e7-1075">MessagePack serialization 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1075">MessagePack serialization options</span></span>

<span data-ttu-id="752e7-1076">MessagePack serialization은 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대리자를 제공 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="752e7-1077">자세한 내용은 [MessagePack SignalR 를](xref:signalr/messagepackhubprotocol) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="752e7-1078">이 시점에서는 JavaScript 클라이언트에서 MessagePack serialization을 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="752e7-1079">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-1079">Configure server options</span></span>

<span data-ttu-id="752e7-1080">다음 표에서는 허브를 구성 하는 옵션을 설명 합니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="752e7-1081">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1081">Option</span></span> | <span data-ttu-id="752e7-1082">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-1082">Default Value</span></span> | <span data-ttu-id="752e7-1083">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="752e7-1084">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-1084">15 seconds</span></span> | <span data-ttu-id="752e7-1085">클라이언트에서이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="752e7-1086">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="752e7-1087">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="752e7-1088">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-1088">15 seconds</span></span> | <span data-ttu-id="752e7-1089">서버에서이 간격 내에 메시지를 보내지 않은 경우 ping 메시지가 자동으로 전송 되어 연결이 계속 열려 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="752e7-1090">변경 하는 경우 `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` 클라이언트에서 설정을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="752e7-1091">권장 `ServerTimeout` / `serverTimeoutInMilliseconds` 값은 값 두 배가 됩니다 `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="752e7-1092">설치 된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="752e7-1092">All installed protocols</span></span> | <span data-ttu-id="752e7-1093">이 허브에서 지원 되는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="752e7-1094">기본적으로 서버에 등록 된 모든 프로토콜이 허용 되지만이 목록에서 프로토콜을 제거 하 여 개별 허브에 대 한 특정 프로토콜을 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="752e7-1095">이면 `true` 허브 메서드에서 예외가 throw 될 때 자세한 예외 메시지가 클라이언트에 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="752e7-1096">기본값은입니다 `false` . 이러한 예외 메시지에는 중요 한 정보가 포함 될 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="752e7-1097">에서 호출에 대 한 옵션 대리자를 제공 하 여 모든 허브에 대 한 옵션을 구성할 수 있습니다 `AddSignalR` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="752e7-1098">단일 허브에 대 한 옵션은에서 제공 되는 전역 옵션을 재정의 `AddSignalR` 하 고 다음을 사용 하 여 구성할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> .</span><span class="sxs-lookup"><span data-stu-id="752e7-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="752e7-1099">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="752e7-1100">`HttpConnectionDispatcherOptions`전송 및 메모리 버퍼 관리와 관련 된 고급 설정을 구성 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="752e7-1101">이러한 옵션은의 [Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 에 대리자를 전달 하 여 구성 `Startup.Configure` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="752e7-1102">다음 표에서는 ASP.NET Core SignalR 의 고급 HTTP 옵션을 구성 하는 옵션을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="752e7-1103">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1103">Option</span></span> | <span data-ttu-id="752e7-1104">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-1104">Default Value</span></span> | <span data-ttu-id="752e7-1105">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="752e7-1106">32KB</span><span class="sxs-lookup"><span data-stu-id="752e7-1106">32 KB</span></span> | <span data-ttu-id="752e7-1107">서버에서 버퍼링 하는 클라이언트로부터 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="752e7-1108">이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="752e7-1109">허브 클래스에 적용 된 특성에서 자동으로 수집 된 데이터 `Authorize` 입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="752e7-1110">클라이언트에 허브에 연결할 수 있는 권한이 있는지 여부를 확인 하는 데 사용 되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="752e7-1111">32KB</span><span class="sxs-lookup"><span data-stu-id="752e7-1111">32 KB</span></span> | <span data-ttu-id="752e7-1112">서버가 버퍼링 하는 앱에서 보낸 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="752e7-1113">이 값을 늘리면 서버에서 더 큰 메시지를 보낼 수 있지만 메모리 사용에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="752e7-1114">모든 전송을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1114">All Transports are enabled.</span></span> | <span data-ttu-id="752e7-1115">`HttpTransportType`클라이언트에서 연결 하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 비트 플래그 열거형입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="752e7-1116">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-1116">See below.</span></span> | <span data-ttu-id="752e7-1117">긴 폴링 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="752e7-1118">아래 내용을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-1118">See below.</span></span> | <span data-ttu-id="752e7-1119">Websocket 전송과 관련 된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="752e7-1120">긴 폴링 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `LongPolling` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="752e7-1121">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1121">Option</span></span> | <span data-ttu-id="752e7-1122">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-1122">Default Value</span></span> | <span data-ttu-id="752e7-1123">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="752e7-1124">90 초</span><span class="sxs-lookup"><span data-stu-id="752e7-1124">90 seconds</span></span> | <span data-ttu-id="752e7-1125">단일 폴링 요청을 종료 하기 전에 서버가 클라이언트에 보낼 메시지를 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="752e7-1126">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="752e7-1127">WebSocket 전송에는 속성을 사용 하 여 구성할 수 있는 추가 옵션이 있습니다 `WebSockets` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="752e7-1128">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1128">Option</span></span> | <span data-ttu-id="752e7-1129">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-1129">Default Value</span></span> | <span data-ttu-id="752e7-1130">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="752e7-1131">5초</span><span class="sxs-lookup"><span data-stu-id="752e7-1131">5 seconds</span></span> | <span data-ttu-id="752e7-1132">서버를 닫은 후 클라이언트를이 시간 간격 내에 닫지 못한 경우 연결이 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="752e7-1133">`Sec-WebSocket-Protocol`헤더를 사용자 지정 값으로 설정 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="752e7-1134">대리자는 클라이언트에서 입력으로 요청 된 값을 받고 원하는 값을 반환할 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="752e7-1135">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-1135">Configure client options</span></span>

<span data-ttu-id="752e7-1136">클라이언트 옵션은 `HubConnectionBuilder` 형식 (.net 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="752e7-1137">Java 클라이언트 에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 작성기 구성 옵션 뿐만 아니라 자체도 포함 됩니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="752e7-1138">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-1138">Configure logging</span></span>

<span data-ttu-id="752e7-1139">로깅은 .NET 클라이언트에서 메서드를 사용 하 여 구성 됩니다 `ConfigureLogging` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="752e7-1140">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="752e7-1141">자세한 내용은 [ASP.NET Core의 로깅](xref:fundamentals/logging/index) 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="752e7-1142">로깅 공급자를 등록 하려면 필요한 패키지를 설치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="752e7-1143">전체 목록은 문서에서 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="752e7-1144">예를 들어 콘솔 로깅을 사용 하도록 설정 하려면 `Microsoft.Extensions.Logging.Console` NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="752e7-1145">확장 메서드를 호출 합니다 `AddConsole` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="752e7-1146">JavaScript 클라이언트에는 유사한 메서드가 있습니다 `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="752e7-1147">`LogLevel`생성할 로그 메시지의 최소 수준을 나타내는 값을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="752e7-1148">로그는 브라우저 콘솔 창에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="752e7-1149">로깅을 완전히 사용 하지 않도록 설정 하려면 메서드에를 지정 `signalR.LogLevel.None` `configureLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="752e7-1150">로깅에 대 한 자세한 내용은 [ SignalR 진단 설명서](xref:signalr/diagnostics)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="752e7-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="752e7-1151">SignalRJava 클라이언트는 로깅에 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="752e7-1152">이는 라이브러리의 사용자가 특정 로깅 종속성을 가져와 자신의 특정 로깅 구현을 선택할 수 있게 해 주는 상위 수준 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="752e7-1153">다음 코드 조각에서는 `java.util.logging` Java 클라이언트와 함께를 사용 하는 방법을 보여 줍니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="752e7-1154">종속성에서 로깅을 구성 하지 않으면 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="752e7-1155">이는 무시 해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="752e7-1156">허용 된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-1156">Configure allowed transports</span></span>

<span data-ttu-id="752e7-1157">에서 사용 하는 전송은 SignalR `WithUrl` JavaScript의 호출에서 구성할 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="752e7-1158">값의 비트 or을 사용 하 여 `HttpTransportType` 지정 된 전송만 사용 하도록 클라이언트를 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="752e7-1159">모든 전송은 기본적으로 사용 하도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="752e7-1160">예를 들어 서버에서 보낸 이벤트 전송을 사용 하지 않도록 설정 하 고 Websocket 및 긴 폴링 연결을 허용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="752e7-1161">JavaScript 클라이언트에서 `transport` 다음과 같이 제공 된 options 개체의 필드를 설정 하 여 전송을 구성 합니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="752e7-1162">전달자 인증 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-1162">Configure bearer authentication</span></span>

<span data-ttu-id="752e7-1163">요청과 함께 인증 데이터를 제공 하려면 SignalR `AccessTokenProvider` ( `accessTokenFactory` JavaScript에서) 옵션을 사용 하 여 원하는 액세스 토큰을 반환 하는 함수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="752e7-1164">.NET 클라이언트에서이 액세스 토큰은 `Authorization` 의 형식과 함께 헤더를 사용 하 여 HTTP "전달자 인증" 토큰으로 전달 됩니다 `Bearer` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="752e7-1165">JavaScript 클라이언트에서 액세스 토큰은 브라우저 Api가 헤더를 적용 하는 기능을 제한 하는 경우를 **제외** 하 고 전달자 토큰으로 사용 됩니다 (특히 서버에서 보낸 이벤트와 websocket 요청에서).</span><span class="sxs-lookup"><span data-stu-id="752e7-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="752e7-1166">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 제공 됩니다 `access_token` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="752e7-1167">.NET 클라이언트에서 `AccessTokenProvider` 옵션은의 옵션 대리자를 사용 하 여 지정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="752e7-1168">JavaScript 클라이언트에서 액세스 토큰은 `accessTokenFactory` 의 options 개체에 있는 필드를 설정 하 여 구성 됩니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="752e7-1169">SignalRJava 클라이언트에서 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)에 액세스 토큰 팩터리를 제공 하 여 인증에 사용할 전달자 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="752e7-1170">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 를 사용 하 여 [rxjava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="752e7-1171">[단일. 지연](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)에 대 한 호출을 사용 하 여 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="752e7-1172">제한 시간 및 연결 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="752e7-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="752e7-1173">시간 제한 및 연결 유지 동작을 구성 하기 위한 추가 옵션은 개체 자체에서 사용할 수 있습니다 `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="752e7-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="752e7-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="752e7-1175">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1175">Option</span></span> | <span data-ttu-id="752e7-1176">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-1176">Default value</span></span> | <span data-ttu-id="752e7-1177">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="752e7-1178">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="752e7-1179">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1179">Timeout for server activity.</span></span> <span data-ttu-id="752e7-1180">서버에서이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버를 연결 되지 않은 것으로 간주 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="752e7-1181">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="752e7-1182">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="752e7-1183">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-1183">15 seconds</span></span> | <span data-ttu-id="752e7-1184">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="752e7-1185">서버에서이 간격으로 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 `Closed` 이벤트 ( `onclose` JavaScript)를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="752e7-1186">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="752e7-1187">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="752e7-1188">.NET 클라이언트에서 시간 제한 값은 값으로 지정 됩니다 `TimeSpan` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="752e7-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="752e7-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="752e7-1190">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1190">Option</span></span> | <span data-ttu-id="752e7-1191">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-1191">Default value</span></span> | <span data-ttu-id="752e7-1192">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="752e7-1193">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="752e7-1194">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1194">Timeout for server activity.</span></span> <span data-ttu-id="752e7-1195">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onclose` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="752e7-1196">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="752e7-1197">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 허용 하는 서버 값을 두 번 이상 사용 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="752e7-1198">Java</span><span class="sxs-lookup"><span data-stu-id="752e7-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="752e7-1199">옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1199">Option</span></span> | <span data-ttu-id="752e7-1200">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-1200">Default value</span></span> | <span data-ttu-id="752e7-1201">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="752e7-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="752e7-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="752e7-1203">30 초 (3만 밀리초)</span><span class="sxs-lookup"><span data-stu-id="752e7-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="752e7-1204">서버 작업에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1204">Timeout for server activity.</span></span> <span data-ttu-id="752e7-1205">서버가이 간격 내에 메시지를 보내지 않은 경우 클라이언트는 서버 연결을 끊고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="752e7-1206">이 값은 ping 메시지 **를 서버에서 보내고 시간** 제한 간격 내에 클라이언트에서 받을 수 있도록 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="752e7-1207">권장 값은 `KeepAliveInterval` ping이 도착할 때까지 시간을 허용 하는 서버 값을 두 번 이상 표시 하는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="752e7-1208">15초</span><span class="sxs-lookup"><span data-stu-id="752e7-1208">15 seconds</span></span> | <span data-ttu-id="752e7-1209">초기 서버 핸드셰이크에 대 한 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="752e7-1210">서버가이 간격 내에 핸드셰이크 응답을 보내지 않는 경우 클라이언트는 핸드셰이크를 취소 하 고 이벤트를 트리거합니다 `onClose` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="752e7-1211">심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 초과 오류가 발생 하는 경우에만 수정 해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="752e7-1212">핸드셰이크 프로세스에 대 한 자세한 내용은 [ SignalR 허브 프로토콜 사양](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="752e7-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="752e7-1213">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1213">Configure additional options</span></span>

<span data-ttu-id="752e7-1214">`WithUrl` `withUrl` `HubConnectionBuilder` Java 클라이언트의에 있는 다양 한 구성 api에서 또는의 (JavaScript) 메서드에서 추가 옵션을 구성할 수 있습니다 `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="752e7-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="752e7-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="752e7-1216">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1216">.NET Option</span></span> |  <span data-ttu-id="752e7-1217">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-1217">Default value</span></span> | <span data-ttu-id="752e7-1218">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="752e7-1219">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="752e7-1220">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="752e7-1221">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="752e7-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="752e7-1222">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="752e7-1223">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-1223">Empty</span></span> | <span data-ttu-id="752e7-1224">인증 요청에 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="752e7-1225">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-1225">Empty</span></span> | <span data-ttu-id="752e7-1226">cookie모든 http 요청과 함께 보낼 http s의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="752e7-1227">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-1227">Empty</span></span> | <span data-ttu-id="752e7-1228">모든 HTTP 요청과 함께 전송할 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="752e7-1229">5초</span><span class="sxs-lookup"><span data-stu-id="752e7-1229">5 seconds</span></span> | <span data-ttu-id="752e7-1230">Websocket 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1230">WebSockets only.</span></span> <span data-ttu-id="752e7-1231">서버가 닫기 요청을 승인할 때까지 대기한 후 클라이언트에서 대기 하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="752e7-1232">이 시간 내에 서버에서 닫기를 승인 하지 않으면 클라이언트 연결이 끊어집니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="752e7-1233">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-1233">Empty</span></span> | <span data-ttu-id="752e7-1234">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="752e7-1235">HTTP 요청을 보내는 데 사용 되는을 구성 하거나 바꾸는 데 사용할 수 있는 대리자입니다 `HttpMessageHandler` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="752e7-1236">WebSocket 연결에는 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="752e7-1237">이 대리자는 null이 아닌 값을 반환 해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="752e7-1238">해당 기본값에 대 한 설정을 수정 하 고 반환 하거나 새 인스턴스를 반환 `HttpMessageHandler` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="752e7-1239">**처리기를 대체할 때 제공 된 처리기에서 유지할 설정을 복사 해야 합니다. 그렇지 않으면 구성 된 옵션 (예: Cookie s 및 헤더)이 새 처리기에 적용 되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="752e7-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="752e7-1240">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="752e7-1241">HTTP 및 Websocket 요청에 대 한 기본 자격 증명을 보내려면이 부울 값을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="752e7-1242">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="752e7-1243">추가 WebSocket 옵션을 구성 하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="752e7-1244">옵션을 구성 하는 데 사용할 수 있는 [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) 의 인스턴스를 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="752e7-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="752e7-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="752e7-1246">JavaScript 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1246">JavaScript Option</span></span> | <span data-ttu-id="752e7-1247">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-1247">Default Value</span></span> | <span data-ttu-id="752e7-1248">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="752e7-1249">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="752e7-1250">`true`클라이언트에서 보내고 받은 메시지의 바이트/문자를 기록 하려면로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="752e7-1251">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="752e7-1252">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="752e7-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="752e7-1253">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="752e7-1254">Java</span><span class="sxs-lookup"><span data-stu-id="752e7-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="752e7-1255">Java 옵션</span><span class="sxs-lookup"><span data-stu-id="752e7-1255">Java Option</span></span> | <span data-ttu-id="752e7-1256">기본값</span><span class="sxs-lookup"><span data-stu-id="752e7-1256">Default Value</span></span> | <span data-ttu-id="752e7-1257">설명</span><span class="sxs-lookup"><span data-stu-id="752e7-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="752e7-1258">HTTP 요청에서 전달자 인증 토큰으로 제공 되는 문자열을 반환 하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="752e7-1259">협상 단계를 건너뛰려면이를로 설정 `true` 합니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="752e7-1260">**Websocket 전송이 유일 하 게 설정 된 전송 인 경우에만 지원 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="752e7-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="752e7-1261">Azure 서비스를 사용 하는 경우이 설정을 사용 하도록 설정할 수 없습니다 SignalR .</span><span class="sxs-lookup"><span data-stu-id="752e7-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="752e7-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="752e7-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="752e7-1263">Empty</span><span class="sxs-lookup"><span data-stu-id="752e7-1263">Empty</span></span> | <span data-ttu-id="752e7-1264">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="752e7-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="752e7-1265">.NET 클라이언트에서 이러한 옵션은에 제공 된 옵션 대리자로 수정할 수 있습니다 `WithUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="752e7-1266">JavaScript 클라이언트에서 이러한 옵션은 다음에 제공 된 JavaScript 개체에 제공 될 수 있습니다 `withUrl` .</span><span class="sxs-lookup"><span data-stu-id="752e7-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="752e7-1267">Java 클라이언트에서 다음에서 반환 된의 메서드를 사용 하 여 이러한 옵션을 구성할 수 있습니다. `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="752e7-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="752e7-1268">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="752e7-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
