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
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223987"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="a5910-103">ASP.NET Core SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a5910-104">JSON/메시지팩 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a5910-105">ASP.NET 코어 시그널R는 메시지 인코딩을 위한 두 가지 프로토콜을 지원합니다: [JSON](https://www.json.org/) 및 [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="a5910-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a5910-106">각 프로토콜에는 직렬화 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a5910-107">JSON [직렬화AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 방법을 사용 하 여 서버에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a5910-108">`AddJsonProtocol`[에서 AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 후 `Startup.ConfigureServices`추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a5910-109">메서드는 `AddJsonProtocol` 개체를 받는 `options` 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a5910-110">해당 개체의 [Payload SerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 속성은 인수의 직렬화를 구성하고 값을 반환하는 데 사용할 수 있는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a5910-111">자세한 내용은 [System.Text.Json 문서를](/dotnet/api/system.text.json)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a5910-112">예를 들어, 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경하지 않도록 serializer를 구성하려면 다음 코드를 `Startup.ConfigureServices`사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="a5910-113">.NET 클라이언트에서 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a5910-114">확장 `Microsoft.Extensions.DependencyInjection` 메서드를 해결하려면 네임스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a5910-115">현재 는 JavaScript 클라이언트에서 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a5910-116">뉴턴소프트.Json으로 전환</span><span class="sxs-lookup"><span data-stu-id="a5910-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a5910-117">에서 `System.Text.Json`지원되지 `Newtonsoft.Json` 않는 기능이 필요한 경우 [[ 뉴턴소프트.Json](xref:migration/22-to-30#switch-to-newtonsoftjson)로 전환 보기]</span><span class="sxs-lookup"><span data-stu-id="a5910-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a5910-118">메시지팩 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-118">MessagePack serialization options</span></span>

<span data-ttu-id="a5910-119">메시지팩 일련화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대한 대리자를 제공하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a5910-120">자세한 내용은 [SignalR의 메시지 팩을](xref:signalr/messagepackhubprotocol) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a5910-121">현재 는 JavaScript 클라이언트에서 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a5910-122">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-122">Configure server options</span></span>

<span data-ttu-id="a5910-123">다음 표는 SignalR 허브를 구성하는 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a5910-124">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-124">Option</span></span> | <span data-ttu-id="a5910-125">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-125">Default Value</span></span> | <span data-ttu-id="a5910-126">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a5910-127">30초</span><span class="sxs-lookup"><span data-stu-id="a5910-127">30 seconds</span></span> | <span data-ttu-id="a5910-128">서버는 이 간격으로 클라이언트가 연결이 끊어진 것으로 간주합니다(유지-활성 포함)가 수신되지 않은 경우 클라이언트가 연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a5910-129">클라이언트가 실제로 연결이 끊어진 것으로 표시되는 데 이 시간 초과 간격보다 더 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a5910-130">권장 값은 값의 `KeepAliveInterval` 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a5910-131">15초</span><span class="sxs-lookup"><span data-stu-id="a5910-131">15 seconds</span></span> | <span data-ttu-id="a5910-132">클라이언트가 이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a5910-133">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a5910-134">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a5910-135">15초</span><span class="sxs-lookup"><span data-stu-id="a5910-135">15 seconds</span></span> | <span data-ttu-id="a5910-136">서버가 이 간격 내에 메시지를 보내지 않은 경우 연결을 열어 두기 위해 ping 메시지가 자동으로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a5910-137">`KeepAliveInterval`변경하면 클라이언트의 `ServerTimeout` / `serverTimeoutInMilliseconds` 설정을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a5910-138">`ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 값의 `KeepAliveInterval` 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a5910-139">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="a5910-139">All installed protocols</span></span> | <span data-ttu-id="a5910-140">이 허브에서 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-140">Protocols supported by this hub.</span></span> <span data-ttu-id="a5910-141">기본적으로 서버에 등록된 모든 프로토콜은 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대한 특정 프로토콜을 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a5910-142">자세한 `true`예외 메시지가 Hub 메서드에서 예외를 throw하면 클라이언트에 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a5910-143">기본값은 `false`은 예외 메시지에 중요한 정보가 포함될 수 있으므로 입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a5910-144">클라이언트 업로드 스트림에 대해 버퍼링할 수 있는 최대 항목 수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a5910-145">이 제한에 도달하면 서버가 스트림 항목을 처리할 때까지 호출 처리가 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a5910-146">32KB</span><span class="sxs-lookup"><span data-stu-id="a5910-146">32 KB</span></span> | <span data-ttu-id="a5910-147">들어오는 단일 허브 메시지의 최대 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="a5910-148">옵션에서 호출에 대한 옵션 대리자를 제공하여 모든 `AddSignalR` 허브에 대해 옵션을 구성할 수 있습니다. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="a5910-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a5910-149">단일 허브에 대한 옵션은 제공된 전역 `AddSignalR` 옵션을 재정의하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>다음을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a5910-150">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a5910-151">전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하는 데 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a5910-152">이러한 옵션은 [에서 MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 대리자를 전달하여 `Startup.Configure`구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a5910-153">다음 표는 코어 SignalR의 고급 http 옵션을 구성하기 ASP.NET 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a5910-154">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-154">Option</span></span> | <span data-ttu-id="a5910-155">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-155">Default Value</span></span> | <span data-ttu-id="a5910-156">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a5910-157">32KB</span><span class="sxs-lookup"><span data-stu-id="a5910-157">32 KB</span></span> | <span data-ttu-id="a5910-158">배압을 적용하기 전에 서버가 버퍼링하는 클라이언트에서 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a5910-159">이 값을 늘리면 서버가 역압을 적용하지 않고 더 큰 메시지를 더 빨리 받을 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a5910-160">Hub 클래스에 `Authorize` 적용된 특성에서 데이터가 자동으로 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a5910-161">클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a5910-162">32KB</span><span class="sxs-lookup"><span data-stu-id="a5910-162">32 KB</span></span> | <span data-ttu-id="a5910-163">배압을 관찰하기 전에 서버가 버퍼링하는 앱에서 전송되는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a5910-164">이 값을 늘리면 서버가 배압을 기다리지 않고 더 큰 메시지를 더 빨리 버퍼링할 수 있지만 메모리 소비가 증가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a5910-165">모든 전송이 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-165">All Transports are enabled.</span></span> | <span data-ttu-id="a5910-166">비트는 클라이언트가 `HttpTransportType` 연결하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 열거형 플래그를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a5910-167">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-167">See below.</span></span> | <span data-ttu-id="a5910-168">긴 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a5910-169">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-169">See below.</span></span> | <span data-ttu-id="a5910-170">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a5910-171">Long Polling 전송에는 속성을 사용하여 구성할 `LongPolling` 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a5910-172">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-172">Option</span></span> | <span data-ttu-id="a5910-173">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-173">Default Value</span></span> | <span data-ttu-id="a5910-174">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a5910-175">90초</span><span class="sxs-lookup"><span data-stu-id="a5910-175">90 seconds</span></span> | <span data-ttu-id="a5910-176">단일 폴링 요청을 종료하기 전에 서버가 메시지가 클라이언트에 전송될 때까지 기다리는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a5910-177">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a5910-178">WebSocket 전송에는 `WebSockets` 속성을 사용하여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a5910-179">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-179">Option</span></span> | <span data-ttu-id="a5910-180">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-180">Default Value</span></span> | <span data-ttu-id="a5910-181">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a5910-182">5초</span><span class="sxs-lookup"><span data-stu-id="a5910-182">5 seconds</span></span> | <span data-ttu-id="a5910-183">서버가 닫히면 클라이언트가 이 시간 간격 내에 닫히지 않으면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a5910-184">헤더를 사용자 지정 값으로 `Sec-WebSocket-Protocol` 설정하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a5910-185">대리자는 클라이언트가 요청한 값을 입력으로 수신하고 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a5910-186">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-186">Configure client options</span></span>

<span data-ttu-id="a5910-187">클라이언트 옵션은 `HubConnectionBuilder` 유형(.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a5910-188">Java 클라이언트에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 빌더 구성 옵션과 그 자체가 `HubConnection` 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a5910-189">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-189">Configure logging</span></span>

<span data-ttu-id="a5910-190">로깅은 `ConfigureLogging` 메서드를 사용하여 .NET 클라이언트에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a5910-191">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a5910-192">자세한 내용은 [ASP.NET 핵심 로그인](xref:fundamentals/logging/index) 설명서를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a5910-193">로깅 공급자를 등록하려면 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a5910-194">전체 목록은 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a5910-195">예를 들어 콘솔 로깅을 `Microsoft.Extensions.Logging.Console` 사용하려면 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a5910-196">확장 `AddConsole` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a5910-197">JavaScript 클라이언트에서 비슷한 `configureLogging` 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a5910-198">생성할 `LogLevel` 로그 메시지의 최소 수준을 나타내는 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a5910-199">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="a5910-200">`LogLevel` 값 대신 로그 수준 이름을 `string` 나타내는 값을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a5910-201">이 기능은 상수에 액세스할 수 없는 환경에서 SignalR 로깅을 `LogLevel` 구성할 때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a5910-202">다음 표에는 사용 가능한 로그 수준이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-202">The following table lists the available log levels.</span></span> <span data-ttu-id="a5910-203">기록할 최소 `configureLogging` 로그 **minimum** 수준을 설정하기 위해 제공하는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a5910-204">이 수준에서 기록된 메시지 **또는 테이블에 나열된 수준이**기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a5910-205">String</span><span class="sxs-lookup"><span data-stu-id="a5910-205">String</span></span>                      | <span data-ttu-id="a5910-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a5910-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a5910-207">`info`**또는**`information`</span><span class="sxs-lookup"><span data-stu-id="a5910-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a5910-208">`warn`**또는**`warning`</span><span class="sxs-lookup"><span data-stu-id="a5910-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="a5910-209">로깅을 완전히 `signalR.LogLevel.None` 사용하지 `configureLogging` 않으려면 메서드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a5910-210">로깅에 대한 자세한 내용은 [SignalR 진단 문서를](xref:signalr/diagnostics)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a5910-211">SignalR Java 클라이언트는 로깅을 위해 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a5910-212">라이브러리의 사용자가 특정 로깅 종속성을 가져와 고유한 특정 로깅 구현을 선택할 수 있는 고급 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a5910-213">다음 코드 조각은 SignalR `java.util.logging` Java 클라이언트에서 사용하는 방법을 보여 주며 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a5910-214">종속성에 로깅을 구성하지 않은 경우 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음 로거를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a5910-215">이 무시 해도 안전 하 게 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a5910-216">허용된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-216">Configure allowed transports</span></span>

<span data-ttu-id="a5910-217">SignalR에서 사용하는 전송은 `WithUrl` 호출(자바스크립트)에서`withUrl` 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a5910-218">비트-OR값은 클라이언트가 `HttpTransportType` 지정된 전송만 사용하도록 제한하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a5910-219">모든 전송은 기본적으로 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-219">All transports are enabled by default.</span></span>

<span data-ttu-id="a5910-220">예를 들어 서버 전송 이벤트 전송을 사용하지 않도록 설정하지만 WebSocket 및 긴 폴링 연결을 허용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a5910-221">JavaScript 클라이언트에서 전송은 `transport` `withUrl`다음에 제공된 옵션 개체에 필드를 설정하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a5910-222">이 버전의 Java 클라이언트 웹소켓은 사용 가능한 유일한 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="a5910-223">Java 클라이언트에서 전송은 `withTransport` `HttpHubConnectionBuilder`의 메서드를 사용하여 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a5910-224">Java 클라이언트는 기본적으로 WebSockets 전송을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a5910-225">SignalR Java 클라이언트는 아직 전송 대체를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a5910-226">베어러 인증 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-226">Configure bearer authentication</span></span>

<span data-ttu-id="a5910-227">SignalR 요청과 함께 인증 데이터를 `AccessTokenProvider` 제공하려면`accessTokenFactory` 옵션(JavaScript)을 사용하여 원하는 액세스 토큰을 반환하는 함수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a5910-228">.NET 클라이언트에서 이 액세스 토큰은 HTTP "보유자 인증" 토큰으로 `Authorization` 전달됩니다(유형이 `Bearer`있는 헤더 사용).</span><span class="sxs-lookup"><span data-stu-id="a5910-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a5910-229">JavaScript 클라이언트에서 액세스 토큰은 브라우저 API가 헤더를 적용하는 기능을 제한하는 경우(특히 서버 전송 이벤트 및 WebSockets 요청)를 **제외하고** 베어러 토큰으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a5910-230">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 `access_token`제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a5910-231">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 다음 에서 `WithUrl`옵션 대리자를 사용하여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a5910-232">JavaScript 클라이언트에서 액세스 토큰은 다음 의 `accessTokenFactory` `withUrl`옵션 개체에 필드를 설정하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a5910-233">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder에](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)액세스 토큰 팩터리를 제공하여 인증에 사용할 베어러 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a5910-234">[와 사용AccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) [RxJava](https://github.com/ReactiveX/RxJava) [단일\<문자열>. ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="a5910-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a5910-235">[Single.defer를](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)호출하면 클라이언트에 대한 액세스 토큰을 생성하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a5910-236">시간 시간 및 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a5910-237">시간 시간 및 유지 동작을 구성하기 위한 추가 `HubConnection` 옵션은 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a5910-238">.NET</span><span class="sxs-lookup"><span data-stu-id="a5910-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a5910-239">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-239">Option</span></span> | <span data-ttu-id="a5910-240">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-240">Default value</span></span> | <span data-ttu-id="a5910-241">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a5910-242">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="a5910-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a5910-243">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-243">Timeout for server activity.</span></span> <span data-ttu-id="a5910-244">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `Closed` 것으로`onclose` 간주하고 JavaScript에서 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a5910-245">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a5910-246">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a5910-247">15초</span><span class="sxs-lookup"><span data-stu-id="a5910-247">15 seconds</span></span> | <span data-ttu-id="a5910-248">초기 서버 핸드셰이크에 대한 시간 시간 입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="a5910-249">서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` `onclose` JavaScript에서 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a5910-250">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a5910-251">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a5910-252">15초</span><span class="sxs-lookup"><span data-stu-id="a5910-252">15 seconds</span></span> | <span data-ttu-id="a5910-253">클라이언트가 ping 메시지를 보내는 간격을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a5910-254">클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a5910-255">클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a5910-256">.NET 클라이언트에서 시간 지정 값은 `TimeSpan` 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a5910-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a5910-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a5910-258">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-258">Option</span></span> | <span data-ttu-id="a5910-259">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-259">Default value</span></span> | <span data-ttu-id="a5910-260">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a5910-261">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="a5910-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a5910-262">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-262">Timeout for server activity.</span></span> <span data-ttu-id="a5910-263">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onclose` 것으로 간주하고 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a5910-264">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a5910-265">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a5910-266">15초(15,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="a5910-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a5910-267">클라이언트가 ping 메시지를 보내는 간격을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a5910-268">클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a5910-269">클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a5910-270">Java</span><span class="sxs-lookup"><span data-stu-id="a5910-270">Java</span></span>](#tab/java)

| <span data-ttu-id="a5910-271">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-271">Option</span></span> | <span data-ttu-id="a5910-272">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-272">Default value</span></span> | <span data-ttu-id="a5910-273">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a5910-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a5910-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a5910-275">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="a5910-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a5910-276">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-276">Timeout for server activity.</span></span> <span data-ttu-id="a5910-277">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onClose` 것으로 간주하고 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a5910-278">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a5910-279">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a5910-280">15초</span><span class="sxs-lookup"><span data-stu-id="a5910-280">15 seconds</span></span> | <span data-ttu-id="a5910-281">초기 서버 핸드셰이크에 대한 시간 시간 입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="a5910-282">서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a5910-283">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a5910-284">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a5910-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a5910-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a5910-286">15초(15,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="a5910-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a5910-287">클라이언트가 ping 메시지를 보내는 간격을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a5910-288">클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a5910-289">클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a5910-290">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-290">Configure additional options</span></span>

<span data-ttu-id="a5910-291">`WithUrl` 추가 옵션은 Java 클라이언트의`withUrl` 다양한 구성 API에 `HubConnectionBuilder` 있는 (JavaScript) 메서드에서 구성할 `HttpHubConnectionBuilder` 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a5910-292">.NET</span><span class="sxs-lookup"><span data-stu-id="a5910-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a5910-293">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-293">.NET Option</span></span> |  <span data-ttu-id="a5910-294">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-294">Default value</span></span> | <span data-ttu-id="a5910-295">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a5910-296">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a5910-297">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a5910-298">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="a5910-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a5910-299">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a5910-300">Empty</span><span class="sxs-lookup"><span data-stu-id="a5910-300">Empty</span></span> | <span data-ttu-id="a5910-301">요청을 인증하기 위해 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a5910-302">Empty</span><span class="sxs-lookup"><span data-stu-id="a5910-302">Empty</span></span> | <span data-ttu-id="a5910-303">모든 HTTP 요청과 함께 보낼 HTTP 쿠키 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a5910-304">Empty</span><span class="sxs-lookup"><span data-stu-id="a5910-304">Empty</span></span> | <span data-ttu-id="a5910-305">모든 HTTP 요청과 함께 보낼 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a5910-306">5초</span><span class="sxs-lookup"><span data-stu-id="a5910-306">5 seconds</span></span> | <span data-ttu-id="a5910-307">웹 소켓만.</span><span class="sxs-lookup"><span data-stu-id="a5910-307">WebSockets only.</span></span> <span data-ttu-id="a5910-308">서버가 닫기 요청을 승인할 때까지 클라이언트가 닫은 후 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a5910-309">이 시간 내에 서버가 닫기를 승인하지 않으면 클라이언트가 연결 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a5910-310">Empty</span><span class="sxs-lookup"><span data-stu-id="a5910-310">Empty</span></span> | <span data-ttu-id="a5910-311">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a5910-312">HTTP 요청을 보내는 데 사용되는 대리자를 구성하거나 교체하는 데 사용할 수 있는 `HttpMessageHandler` 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a5910-313">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="a5910-314">이 대리자는 null이 아닌 값을 반환해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a5910-315">기본값의 설정을 수정하여 반환하거나 새 `HttpMessageHandler` 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a5910-316">**처리기를 교체할 때 제공된 처리기에서 유지하려는 설정을 복사해야 하며, 그렇지 않으면 구성된 옵션(예: 쿠키 및 헤더)이 새 처리기에 적용되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="a5910-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a5910-317">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a5910-318">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 보내도록 이 부울을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a5910-319">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a5910-320">추가 WebSocket 옵션을 구성하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a5910-321">옵션을 구성하는 데 사용할 수 있는 [ClientWebSocketOptions의](/dotnet/api/system.net.websockets.clientwebsocketoptions) 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a5910-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a5910-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a5910-323">자바 스크립트 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-323">JavaScript Option</span></span> | <span data-ttu-id="a5910-324">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-324">Default Value</span></span> | <span data-ttu-id="a5910-325">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a5910-326">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a5910-327">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a5910-328">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="a5910-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a5910-329">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a5910-330">Java</span><span class="sxs-lookup"><span data-stu-id="a5910-330">Java</span></span>](#tab/java)

| <span data-ttu-id="a5910-331">자바 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-331">Java Option</span></span> | <span data-ttu-id="a5910-332">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-332">Default Value</span></span> | <span data-ttu-id="a5910-333">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a5910-334">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a5910-335">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a5910-336">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="a5910-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a5910-337">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a5910-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a5910-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a5910-339">Empty</span><span class="sxs-lookup"><span data-stu-id="a5910-339">Empty</span></span> | <span data-ttu-id="a5910-340">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a5910-341">.NET 클라이언트에서 이러한 옵션은 다음에 제공된 옵션 대리자에서 수정할 `WithUrl`수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a5910-342">자바 스크립트 클라이언트에서 이러한 옵션은 `withUrl`다음에 제공되는 자바 스크립트 개체에서 제공 할 수 있습니다 .</span><span class="sxs-lookup"><span data-stu-id="a5910-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a5910-343">Java 클라이언트에서 이러한 옵션은 `HttpHubConnectionBuilder` 반환된 메서드로 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a5910-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a5910-344">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a5910-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a5910-345">JSON/메시지팩 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a5910-346">ASP.NET 코어 시그널R는 메시지 인코딩을 위한 두 가지 프로토콜을 지원합니다: [JSON](https://www.json.org/) 및 [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="a5910-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a5910-347">각 프로토콜에는 직렬화 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a5910-348">JSON 직렬화는 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 `Startup.ConfigureServices` 수 있습니다., 메서드에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 후 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a5910-349">메서드는 `AddJsonProtocol` 개체를 받는 `options` 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a5910-350">해당 개체의 [페이로드 SerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 인수의 직렬화를 구성하고 값을 반환하는 데 사용할 수 있는 JSON.NET `JsonSerializerSettings` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a5910-351">자세한 내용은 JSON.NET [문서를](https://www.newtonsoft.com/json/help/html/Introduction.htm)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="a5910-352">예를 들어, 기본 "camelCase" 이름 대신 "PascalCase" 속성 이름을 사용하도록 serializer를 구성하려면 `Startup.ConfigureServices`다음 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="a5910-353">.NET 클라이언트에서 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a5910-354">확장 `Microsoft.Extensions.DependencyInjection` 메서드를 해결하려면 네임스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a5910-355">현재 는 JavaScript 클라이언트에서 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a5910-356">메시지팩 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-356">MessagePack serialization options</span></span>

<span data-ttu-id="a5910-357">메시지팩 일련화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대한 대리자를 제공하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a5910-358">자세한 내용은 [SignalR의 메시지 팩을](xref:signalr/messagepackhubprotocol) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a5910-359">현재 는 JavaScript 클라이언트에서 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a5910-360">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-360">Configure server options</span></span>

<span data-ttu-id="a5910-361">다음 표는 SignalR 허브를 구성하는 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a5910-362">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-362">Option</span></span> | <span data-ttu-id="a5910-363">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-363">Default Value</span></span> | <span data-ttu-id="a5910-364">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a5910-365">30초</span><span class="sxs-lookup"><span data-stu-id="a5910-365">30 seconds</span></span> | <span data-ttu-id="a5910-366">서버는 이 간격으로 클라이언트가 연결이 끊어진 것으로 간주합니다(유지-활성 포함)가 수신되지 않은 경우 클라이언트가 연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a5910-367">클라이언트가 실제로 연결이 끊어진 것으로 표시되는 데 이 시간 초과 간격보다 더 오래 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a5910-368">권장 값은 값의 `KeepAliveInterval` 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a5910-369">15초</span><span class="sxs-lookup"><span data-stu-id="a5910-369">15 seconds</span></span> | <span data-ttu-id="a5910-370">클라이언트가 이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a5910-371">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a5910-372">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a5910-373">15초</span><span class="sxs-lookup"><span data-stu-id="a5910-373">15 seconds</span></span> | <span data-ttu-id="a5910-374">서버가 이 간격 내에 메시지를 보내지 않은 경우 연결을 열어 두기 위해 ping 메시지가 자동으로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a5910-375">`KeepAliveInterval`변경하면 클라이언트의 `ServerTimeout` / `serverTimeoutInMilliseconds` 설정을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a5910-376">`ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 값의 `KeepAliveInterval` 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a5910-377">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="a5910-377">All installed protocols</span></span> | <span data-ttu-id="a5910-378">이 허브에서 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-378">Protocols supported by this hub.</span></span> <span data-ttu-id="a5910-379">기본적으로 서버에 등록된 모든 프로토콜은 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대한 특정 프로토콜을 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a5910-380">자세한 `true`예외 메시지가 Hub 메서드에서 예외를 throw하면 클라이언트에 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a5910-381">기본값은 `false`은 예외 메시지에 중요한 정보가 포함될 수 있으므로 입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="a5910-382">옵션에서 호출에 대한 옵션 대리자를 제공하여 모든 `AddSignalR` 허브에 대해 옵션을 구성할 수 있습니다. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="a5910-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a5910-383">단일 허브에 대한 옵션은 제공된 전역 `AddSignalR` 옵션을 재정의하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>다음을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a5910-384">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a5910-385">전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하는 데 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a5910-386">이러한 옵션은 [에서 MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 대리자를 전달하여 `Startup.Configure`구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a5910-387">다음 표는 코어 SignalR의 고급 http 옵션을 구성하기 ASP.NET 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a5910-388">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-388">Option</span></span> | <span data-ttu-id="a5910-389">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-389">Default Value</span></span> | <span data-ttu-id="a5910-390">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a5910-391">32KB</span><span class="sxs-lookup"><span data-stu-id="a5910-391">32 KB</span></span> | <span data-ttu-id="a5910-392">서버가 버퍼링하는 클라이언트에서 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="a5910-393">이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 소비에 부정적인 영향을 미칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a5910-394">Hub 클래스에 `Authorize` 적용된 특성에서 데이터가 자동으로 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a5910-395">클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a5910-396">32KB</span><span class="sxs-lookup"><span data-stu-id="a5910-396">32 KB</span></span> | <span data-ttu-id="a5910-397">서버가 버퍼링하는 앱에서 보내는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="a5910-398">이 값을 늘리면 서버가 더 큰 메시지를 보낼 수 있지만 메모리 소비에 부정적인 영향을 미칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a5910-399">모든 전송이 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-399">All Transports are enabled.</span></span> | <span data-ttu-id="a5910-400">비트는 클라이언트가 `HttpTransportType` 연결하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 열거형 플래그를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a5910-401">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-401">See below.</span></span> | <span data-ttu-id="a5910-402">긴 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a5910-403">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-403">See below.</span></span> | <span data-ttu-id="a5910-404">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a5910-405">Long Polling 전송에는 속성을 사용하여 구성할 `LongPolling` 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a5910-406">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-406">Option</span></span> | <span data-ttu-id="a5910-407">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-407">Default Value</span></span> | <span data-ttu-id="a5910-408">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a5910-409">90초</span><span class="sxs-lookup"><span data-stu-id="a5910-409">90 seconds</span></span> | <span data-ttu-id="a5910-410">단일 폴링 요청을 종료하기 전에 서버가 메시지가 클라이언트에 전송될 때까지 기다리는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a5910-411">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a5910-412">WebSocket 전송에는 `WebSockets` 속성을 사용하여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a5910-413">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-413">Option</span></span> | <span data-ttu-id="a5910-414">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-414">Default Value</span></span> | <span data-ttu-id="a5910-415">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a5910-416">5초</span><span class="sxs-lookup"><span data-stu-id="a5910-416">5 seconds</span></span> | <span data-ttu-id="a5910-417">서버가 닫히면 클라이언트가 이 시간 간격 내에 닫히지 않으면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a5910-418">헤더를 사용자 지정 값으로 `Sec-WebSocket-Protocol` 설정하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a5910-419">대리자는 클라이언트가 요청한 값을 입력으로 수신하고 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a5910-420">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-420">Configure client options</span></span>

<span data-ttu-id="a5910-421">클라이언트 옵션은 `HubConnectionBuilder` 유형(.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a5910-422">Java 클라이언트에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 빌더 구성 옵션과 그 자체가 `HubConnection` 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a5910-423">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-423">Configure logging</span></span>

<span data-ttu-id="a5910-424">로깅은 `ConfigureLogging` 메서드를 사용하여 .NET 클라이언트에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a5910-425">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a5910-426">자세한 내용은 [ASP.NET 핵심 로그인](xref:fundamentals/logging/index) 설명서를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a5910-427">로깅 공급자를 등록하려면 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a5910-428">전체 목록은 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a5910-429">예를 들어 콘솔 로깅을 `Microsoft.Extensions.Logging.Console` 사용하려면 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a5910-430">확장 `AddConsole` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a5910-431">JavaScript 클라이언트에서 비슷한 `configureLogging` 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a5910-432">생성할 `LogLevel` 로그 메시지의 최소 수준을 나타내는 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a5910-433">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a5910-434">로깅을 완전히 `signalR.LogLevel.None` 사용하지 `configureLogging` 않으려면 메서드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a5910-435">로깅에 대한 자세한 내용은 [SignalR 진단 문서를](xref:signalr/diagnostics)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a5910-436">SignalR Java 클라이언트는 로깅을 위해 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a5910-437">라이브러리의 사용자가 특정 로깅 종속성을 가져와 고유한 특정 로깅 구현을 선택할 수 있는 고급 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a5910-438">다음 코드 조각은 SignalR `java.util.logging` Java 클라이언트에서 사용하는 방법을 보여 주며 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a5910-439">종속성에 로깅을 구성하지 않은 경우 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음 로거를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a5910-440">이 무시 해도 안전 하 게 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a5910-441">허용된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-441">Configure allowed transports</span></span>

<span data-ttu-id="a5910-442">SignalR에서 사용하는 전송은 `WithUrl` 호출(자바스크립트)에서`withUrl` 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a5910-443">비트-OR값은 클라이언트가 `HttpTransportType` 지정된 전송만 사용하도록 제한하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a5910-444">모든 전송은 기본적으로 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-444">All transports are enabled by default.</span></span>

<span data-ttu-id="a5910-445">예를 들어 서버 전송 이벤트 전송을 사용하지 않도록 설정하지만 WebSocket 및 긴 폴링 연결을 허용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a5910-446">JavaScript 클라이언트에서 전송은 `transport` `withUrl`다음에 제공된 옵션 개체에 필드를 설정하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a5910-447">이 버전의 Java 클라이언트 웹소켓은 사용 가능한 유일한 전송입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a5910-448">베어러 인증 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-448">Configure bearer authentication</span></span>

<span data-ttu-id="a5910-449">SignalR 요청과 함께 인증 데이터를 `AccessTokenProvider` 제공하려면`accessTokenFactory` 옵션(JavaScript)을 사용하여 원하는 액세스 토큰을 반환하는 함수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a5910-450">.NET 클라이언트에서 이 액세스 토큰은 HTTP "보유자 인증" 토큰으로 `Authorization` 전달됩니다(유형이 `Bearer`있는 헤더 사용).</span><span class="sxs-lookup"><span data-stu-id="a5910-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a5910-451">JavaScript 클라이언트에서 액세스 토큰은 브라우저 API가 헤더를 적용하는 기능을 제한하는 경우(특히 서버 전송 이벤트 및 WebSockets 요청)를 **제외하고** 베어러 토큰으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a5910-452">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 `access_token`제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a5910-453">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 다음 에서 `WithUrl`옵션 대리자를 사용하여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a5910-454">JavaScript 클라이언트에서 액세스 토큰은 다음 의 `accessTokenFactory` `withUrl`옵션 개체에 필드를 설정하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a5910-455">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder에](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)액세스 토큰 팩터리를 제공하여 인증에 사용할 베어러 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a5910-456">[와 사용AccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) [RxJava](https://github.com/ReactiveX/RxJava) [단일\<문자열>. ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="a5910-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a5910-457">[Single.defer를](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)호출하면 클라이언트에 대한 액세스 토큰을 생성하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a5910-458">시간 시간 및 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a5910-459">시간 시간 및 유지 동작을 구성하기 위한 추가 `HubConnection` 옵션은 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a5910-460">.NET</span><span class="sxs-lookup"><span data-stu-id="a5910-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a5910-461">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-461">Option</span></span> | <span data-ttu-id="a5910-462">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-462">Default value</span></span> | <span data-ttu-id="a5910-463">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a5910-464">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="a5910-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a5910-465">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-465">Timeout for server activity.</span></span> <span data-ttu-id="a5910-466">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `Closed` 것으로`onclose` 간주하고 JavaScript에서 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a5910-467">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a5910-468">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a5910-469">15초</span><span class="sxs-lookup"><span data-stu-id="a5910-469">15 seconds</span></span> | <span data-ttu-id="a5910-470">초기 서버 핸드셰이크에 대한 시간 시간 입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="a5910-471">서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` `onclose` JavaScript에서 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a5910-472">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a5910-473">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a5910-474">15초</span><span class="sxs-lookup"><span data-stu-id="a5910-474">15 seconds</span></span> | <span data-ttu-id="a5910-475">클라이언트가 ping 메시지를 보내는 간격을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a5910-476">클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a5910-477">클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a5910-478">.NET 클라이언트에서 시간 지정 값은 `TimeSpan` 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a5910-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a5910-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a5910-480">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-480">Option</span></span> | <span data-ttu-id="a5910-481">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-481">Default value</span></span> | <span data-ttu-id="a5910-482">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a5910-483">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="a5910-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a5910-484">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-484">Timeout for server activity.</span></span> <span data-ttu-id="a5910-485">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onclose` 것으로 간주하고 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a5910-486">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a5910-487">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a5910-488">15초(15,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="a5910-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a5910-489">클라이언트가 ping 메시지를 보내는 간격을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a5910-490">클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a5910-491">클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a5910-492">Java</span><span class="sxs-lookup"><span data-stu-id="a5910-492">Java</span></span>](#tab/java)

| <span data-ttu-id="a5910-493">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-493">Option</span></span> | <span data-ttu-id="a5910-494">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-494">Default value</span></span> | <span data-ttu-id="a5910-495">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a5910-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a5910-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a5910-497">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="a5910-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a5910-498">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-498">Timeout for server activity.</span></span> <span data-ttu-id="a5910-499">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onClose` 것으로 간주하고 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a5910-500">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a5910-501">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a5910-502">15초</span><span class="sxs-lookup"><span data-stu-id="a5910-502">15 seconds</span></span> | <span data-ttu-id="a5910-503">초기 서버 핸드셰이크에 대한 시간 시간 입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="a5910-504">서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a5910-505">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a5910-506">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a5910-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a5910-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a5910-508">15초(15,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="a5910-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a5910-509">클라이언트가 ping 메시지를 보내는 간격을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a5910-510">클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a5910-511">클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a5910-512">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-512">Configure additional options</span></span>

<span data-ttu-id="a5910-513">`WithUrl` 추가 옵션은 Java 클라이언트의`withUrl` 다양한 구성 API에 `HubConnectionBuilder` 있는 (JavaScript) 메서드에서 구성할 `HttpHubConnectionBuilder` 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a5910-514">.NET</span><span class="sxs-lookup"><span data-stu-id="a5910-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a5910-515">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-515">.NET Option</span></span> |  <span data-ttu-id="a5910-516">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-516">Default value</span></span> | <span data-ttu-id="a5910-517">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a5910-518">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a5910-519">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a5910-520">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="a5910-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a5910-521">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a5910-522">Empty</span><span class="sxs-lookup"><span data-stu-id="a5910-522">Empty</span></span> | <span data-ttu-id="a5910-523">요청을 인증하기 위해 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a5910-524">Empty</span><span class="sxs-lookup"><span data-stu-id="a5910-524">Empty</span></span> | <span data-ttu-id="a5910-525">모든 HTTP 요청과 함께 보낼 HTTP 쿠키 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a5910-526">Empty</span><span class="sxs-lookup"><span data-stu-id="a5910-526">Empty</span></span> | <span data-ttu-id="a5910-527">모든 HTTP 요청과 함께 보낼 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a5910-528">5초</span><span class="sxs-lookup"><span data-stu-id="a5910-528">5 seconds</span></span> | <span data-ttu-id="a5910-529">웹 소켓만.</span><span class="sxs-lookup"><span data-stu-id="a5910-529">WebSockets only.</span></span> <span data-ttu-id="a5910-530">서버가 닫기 요청을 승인할 때까지 클라이언트가 닫은 후 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a5910-531">이 시간 내에 서버가 닫기를 승인하지 않으면 클라이언트가 연결 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a5910-532">Empty</span><span class="sxs-lookup"><span data-stu-id="a5910-532">Empty</span></span> | <span data-ttu-id="a5910-533">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a5910-534">HTTP 요청을 보내는 데 사용되는 대리자를 구성하거나 교체하는 데 사용할 수 있는 `HttpMessageHandler` 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a5910-535">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="a5910-536">이 대리자는 null이 아닌 값을 반환해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a5910-537">기본값의 설정을 수정하여 반환하거나 새 `HttpMessageHandler` 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a5910-538">**처리기를 교체할 때 제공된 처리기에서 유지하려는 설정을 복사해야 하며, 그렇지 않으면 구성된 옵션(예: 쿠키 및 헤더)이 새 처리기에 적용되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="a5910-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a5910-539">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a5910-540">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 보내도록 이 부울을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a5910-541">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a5910-542">추가 WebSocket 옵션을 구성하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a5910-543">옵션을 구성하는 데 사용할 수 있는 [ClientWebSocketOptions의](/dotnet/api/system.net.websockets.clientwebsocketoptions) 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a5910-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a5910-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a5910-545">자바 스크립트 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-545">JavaScript Option</span></span> | <span data-ttu-id="a5910-546">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-546">Default Value</span></span> | <span data-ttu-id="a5910-547">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a5910-548">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a5910-549">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a5910-550">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="a5910-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a5910-551">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a5910-552">Java</span><span class="sxs-lookup"><span data-stu-id="a5910-552">Java</span></span>](#tab/java)

| <span data-ttu-id="a5910-553">자바 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-553">Java Option</span></span> | <span data-ttu-id="a5910-554">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-554">Default Value</span></span> | <span data-ttu-id="a5910-555">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a5910-556">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a5910-557">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a5910-558">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="a5910-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a5910-559">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a5910-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a5910-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a5910-561">Empty</span><span class="sxs-lookup"><span data-stu-id="a5910-561">Empty</span></span> | <span data-ttu-id="a5910-562">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a5910-563">.NET 클라이언트에서 이러한 옵션은 다음에 제공된 옵션 대리자에서 수정할 `WithUrl`수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a5910-564">자바 스크립트 클라이언트에서 이러한 옵션은 `withUrl`다음에 제공되는 자바 스크립트 개체에서 제공 할 수 있습니다 .</span><span class="sxs-lookup"><span data-stu-id="a5910-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a5910-565">Java 클라이언트에서 이러한 옵션은 `HttpHubConnectionBuilder` 반환된 메서드로 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a5910-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a5910-566">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a5910-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a5910-567">JSON/메시지팩 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a5910-568">ASP.NET 코어 시그널R는 메시지 인코딩을 위한 두 가지 프로토콜을 지원합니다: [JSON](https://www.json.org/) 및 [MessagePack.](https://msgpack.org/index.html)</span><span class="sxs-lookup"><span data-stu-id="a5910-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a5910-569">각 프로토콜에는 직렬화 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a5910-570">JSON 직렬화는 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 `Startup.ConfigureServices` 수 있습니다., 메서드에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 후 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a5910-571">메서드는 `AddJsonProtocol` 개체를 받는 `options` 대리자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a5910-572">해당 개체의 [페이로드 SerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 인수의 직렬화를 구성하고 값을 반환하는 데 사용할 수 있는 JSON.NET `JsonSerializerSettings` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a5910-573">자세한 내용은 JSON.NET [문서를](https://www.newtonsoft.com/json/help/html/Introduction.htm)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="a5910-574">예를 들어, 기본 "camelCase" 이름 대신 "PascalCase" 속성 이름을 사용하도록 serializer를 구성하려면 `Startup.ConfigureServices`다음 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="a5910-575">.NET 클라이언트에서 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a5910-576">확장 `Microsoft.Extensions.DependencyInjection` 메서드를 해결하려면 네임스페이스를 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a5910-577">현재 는 JavaScript 클라이언트에서 JSON 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a5910-578">메시지팩 직렬화 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-578">MessagePack serialization options</span></span>

<span data-ttu-id="a5910-579">메시지팩 일련화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대한 대리자를 제공하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a5910-580">자세한 내용은 [SignalR의 메시지 팩을](xref:signalr/messagepackhubprotocol) 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a5910-581">현재 는 JavaScript 클라이언트에서 MessagePack 직렬화를 구성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a5910-582">서버 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-582">Configure server options</span></span>

<span data-ttu-id="a5910-583">다음 표는 SignalR 허브를 구성하는 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a5910-584">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-584">Option</span></span> | <span data-ttu-id="a5910-585">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-585">Default Value</span></span> | <span data-ttu-id="a5910-586">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="a5910-587">15초</span><span class="sxs-lookup"><span data-stu-id="a5910-587">15 seconds</span></span> | <span data-ttu-id="a5910-588">클라이언트가 이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a5910-589">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a5910-590">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a5910-591">15초</span><span class="sxs-lookup"><span data-stu-id="a5910-591">15 seconds</span></span> | <span data-ttu-id="a5910-592">서버가 이 간격 내에 메시지를 보내지 않은 경우 연결을 열어 두기 위해 ping 메시지가 자동으로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a5910-593">`KeepAliveInterval`변경하면 클라이언트의 `ServerTimeout` / `serverTimeoutInMilliseconds` 설정을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a5910-594">`ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 값의 `KeepAliveInterval` 두 배입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a5910-595">설치된 모든 프로토콜</span><span class="sxs-lookup"><span data-stu-id="a5910-595">All installed protocols</span></span> | <span data-ttu-id="a5910-596">이 허브에서 지원하는 프로토콜입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-596">Protocols supported by this hub.</span></span> <span data-ttu-id="a5910-597">기본적으로 서버에 등록된 모든 프로토콜은 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대한 특정 프로토콜을 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a5910-598">자세한 `true`예외 메시지가 Hub 메서드에서 예외를 throw하면 클라이언트에 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a5910-599">기본값은 `false`은 예외 메시지에 중요한 정보가 포함될 수 있으므로 입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="a5910-600">옵션에서 호출에 대한 옵션 대리자를 제공하여 모든 `AddSignalR` 허브에 대해 옵션을 구성할 수 있습니다. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="a5910-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a5910-601">단일 허브에 대한 옵션은 제공된 전역 `AddSignalR` 옵션을 재정의하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>다음을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a5910-602">고급 HTTP 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a5910-603">전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하는 데 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a5910-604">이러한 옵션은 [에서 MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 대리자를 전달하여 `Startup.Configure`구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a5910-605">다음 표는 코어 SignalR의 고급 http 옵션을 구성하기 ASP.NET 옵션에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a5910-606">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-606">Option</span></span> | <span data-ttu-id="a5910-607">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-607">Default Value</span></span> | <span data-ttu-id="a5910-608">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a5910-609">32KB</span><span class="sxs-lookup"><span data-stu-id="a5910-609">32 KB</span></span> | <span data-ttu-id="a5910-610">서버가 버퍼링하는 클라이언트에서 받은 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="a5910-611">이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 소비에 부정적인 영향을 미칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a5910-612">Hub 클래스에 `Authorize` 적용된 특성에서 데이터가 자동으로 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a5910-613">클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a5910-614">32KB</span><span class="sxs-lookup"><span data-stu-id="a5910-614">32 KB</span></span> | <span data-ttu-id="a5910-615">서버가 버퍼링하는 앱에서 보내는 최대 바이트 수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="a5910-616">이 값을 늘리면 서버가 더 큰 메시지를 보낼 수 있지만 메모리 소비에 부정적인 영향을 미칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a5910-617">모든 전송이 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-617">All Transports are enabled.</span></span> | <span data-ttu-id="a5910-618">비트는 클라이언트가 `HttpTransportType` 연결하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 열거형 플래그를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a5910-619">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-619">See below.</span></span> | <span data-ttu-id="a5910-620">긴 폴링 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a5910-621">아래를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-621">See below.</span></span> | <span data-ttu-id="a5910-622">WebSockets 전송과 관련된 추가 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a5910-623">Long Polling 전송에는 속성을 사용하여 구성할 `LongPolling` 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a5910-624">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-624">Option</span></span> | <span data-ttu-id="a5910-625">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-625">Default Value</span></span> | <span data-ttu-id="a5910-626">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a5910-627">90초</span><span class="sxs-lookup"><span data-stu-id="a5910-627">90 seconds</span></span> | <span data-ttu-id="a5910-628">단일 폴링 요청을 종료하기 전에 서버가 메시지가 클라이언트에 전송될 때까지 기다리는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a5910-629">이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 발급합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a5910-630">WebSocket 전송에는 `WebSockets` 속성을 사용하여 구성할 수 있는 추가 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a5910-631">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-631">Option</span></span> | <span data-ttu-id="a5910-632">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-632">Default Value</span></span> | <span data-ttu-id="a5910-633">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a5910-634">5초</span><span class="sxs-lookup"><span data-stu-id="a5910-634">5 seconds</span></span> | <span data-ttu-id="a5910-635">서버가 닫히면 클라이언트가 이 시간 간격 내에 닫히지 않으면 연결이 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a5910-636">헤더를 사용자 지정 값으로 `Sec-WebSocket-Protocol` 설정하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a5910-637">대리자는 클라이언트가 요청한 값을 입력으로 수신하고 원하는 값을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a5910-638">클라이언트 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-638">Configure client options</span></span>

<span data-ttu-id="a5910-639">클라이언트 옵션은 `HubConnectionBuilder` 유형(.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a5910-640">Java 클라이언트에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 빌더 구성 옵션과 그 자체가 `HubConnection` 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a5910-641">로깅 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-641">Configure logging</span></span>

<span data-ttu-id="a5910-642">로깅은 `ConfigureLogging` 메서드를 사용하여 .NET 클라이언트에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a5910-643">로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a5910-644">자세한 내용은 [ASP.NET 핵심 로그인](xref:fundamentals/logging/index) 설명서를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a5910-645">로깅 공급자를 등록하려면 필요한 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a5910-646">전체 목록은 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a5910-647">예를 들어 콘솔 로깅을 `Microsoft.Extensions.Logging.Console` 사용하려면 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a5910-648">확장 `AddConsole` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a5910-649">JavaScript 클라이언트에서 비슷한 `configureLogging` 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a5910-650">생성할 `LogLevel` 로그 메시지의 최소 수준을 나타내는 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a5910-651">로그는 브라우저 콘솔 창에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a5910-652">로깅을 완전히 `signalR.LogLevel.None` 사용하지 `configureLogging` 않으려면 메서드를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a5910-653">로깅에 대한 자세한 내용은 [SignalR 진단 문서를](xref:signalr/diagnostics)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a5910-654">SignalR Java 클라이언트는 로깅을 위해 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a5910-655">라이브러리의 사용자가 특정 로깅 종속성을 가져와 고유한 특정 로깅 구현을 선택할 수 있는 고급 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a5910-656">다음 코드 조각은 SignalR `java.util.logging` Java 클라이언트에서 사용하는 방법을 보여 주며 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a5910-657">종속성에 로깅을 구성하지 않은 경우 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음 로거를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a5910-658">이 무시 해도 안전 하 게 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a5910-659">허용된 전송 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-659">Configure allowed transports</span></span>

<span data-ttu-id="a5910-660">SignalR에서 사용하는 전송은 `WithUrl` 호출(자바스크립트)에서`withUrl` 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a5910-661">비트-OR값은 클라이언트가 `HttpTransportType` 지정된 전송만 사용하도록 제한하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a5910-662">모든 전송은 기본적으로 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-662">All transports are enabled by default.</span></span>

<span data-ttu-id="a5910-663">예를 들어 서버 전송 이벤트 전송을 사용하지 않도록 설정하지만 WebSocket 및 긴 폴링 연결을 허용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a5910-664">JavaScript 클라이언트에서 전송은 `transport` `withUrl`다음에 제공된 옵션 개체에 필드를 설정하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a5910-665">베어러 인증 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-665">Configure bearer authentication</span></span>

<span data-ttu-id="a5910-666">SignalR 요청과 함께 인증 데이터를 `AccessTokenProvider` 제공하려면`accessTokenFactory` 옵션(JavaScript)을 사용하여 원하는 액세스 토큰을 반환하는 함수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a5910-667">.NET 클라이언트에서 이 액세스 토큰은 HTTP "보유자 인증" 토큰으로 `Authorization` 전달됩니다(유형이 `Bearer`있는 헤더 사용).</span><span class="sxs-lookup"><span data-stu-id="a5910-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a5910-668">JavaScript 클라이언트에서 액세스 토큰은 브라우저 API가 헤더를 적용하는 기능을 제한하는 경우(특히 서버 전송 이벤트 및 WebSockets 요청)를 **제외하고** 베어러 토큰으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a5910-669">이러한 경우 액세스 토큰은 쿼리 문자열 값으로 `access_token`제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a5910-670">.NET 클라이언트에서 `AccessTokenProvider` 옵션은 다음 에서 `WithUrl`옵션 대리자를 사용하여 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a5910-671">JavaScript 클라이언트에서 액세스 토큰은 다음 의 `accessTokenFactory` `withUrl`옵션 개체에 필드를 설정하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a5910-672">SignalR Java 클라이언트에서 [HttpHubConnectionBuilder에](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)액세스 토큰 팩터리를 제공하여 인증에 사용할 베어러 토큰을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a5910-673">[와 사용AccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) [RxJava](https://github.com/ReactiveX/RxJava) [단일\<문자열>. ](https://reactivex.io/documentation/single.html)</span><span class="sxs-lookup"><span data-stu-id="a5910-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a5910-674">[Single.defer를](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)호출하면 클라이언트에 대한 액세스 토큰을 생성하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a5910-675">시간 시간 및 유지 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="a5910-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a5910-676">시간 시간 및 유지 동작을 구성하기 위한 추가 `HubConnection` 옵션은 개체 자체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a5910-677">.NET</span><span class="sxs-lookup"><span data-stu-id="a5910-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a5910-678">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-678">Option</span></span> | <span data-ttu-id="a5910-679">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-679">Default value</span></span> | <span data-ttu-id="a5910-680">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a5910-681">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="a5910-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a5910-682">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-682">Timeout for server activity.</span></span> <span data-ttu-id="a5910-683">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `Closed` 것으로`onclose` 간주하고 JavaScript에서 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a5910-684">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a5910-685">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a5910-686">15초</span><span class="sxs-lookup"><span data-stu-id="a5910-686">15 seconds</span></span> | <span data-ttu-id="a5910-687">초기 서버 핸드셰이크에 대한 시간 시간 입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="a5910-688">서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` `onclose` JavaScript에서 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a5910-689">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a5910-690">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="a5910-691">.NET 클라이언트에서 시간 지정 값은 `TimeSpan` 값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a5910-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a5910-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a5910-693">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-693">Option</span></span> | <span data-ttu-id="a5910-694">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-694">Default value</span></span> | <span data-ttu-id="a5910-695">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a5910-696">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="a5910-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a5910-697">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-697">Timeout for server activity.</span></span> <span data-ttu-id="a5910-698">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onclose` 것으로 간주하고 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a5910-699">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a5910-700">권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a5910-701">Java</span><span class="sxs-lookup"><span data-stu-id="a5910-701">Java</span></span>](#tab/java)

| <span data-ttu-id="a5910-702">옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-702">Option</span></span> | <span data-ttu-id="a5910-703">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-703">Default value</span></span> | <span data-ttu-id="a5910-704">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a5910-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a5910-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a5910-706">30초(30,000밀리초)</span><span class="sxs-lookup"><span data-stu-id="a5910-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a5910-707">서버 활동에 대한 시간 초과입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-707">Timeout for server activity.</span></span> <span data-ttu-id="a5910-708">서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onClose` 것으로 간주하고 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a5910-709">이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a5910-710">ping이 도착할 시간을 허용하기 위해 권장되는 값은 서버 값의 `KeepAliveInterval` 두 배 이상인 숫자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a5910-711">15초</span><span class="sxs-lookup"><span data-stu-id="a5910-711">15 seconds</span></span> | <span data-ttu-id="a5910-712">초기 서버 핸드셰이크에 대한 시간 시간 입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="a5910-713">서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `onClose` 이벤트를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a5910-714">이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a5910-715">핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="a5910-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a5910-716">추가 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-716">Configure additional options</span></span>

<span data-ttu-id="a5910-717">`WithUrl` 추가 옵션은 Java 클라이언트의`withUrl` 다양한 구성 API에 `HubConnectionBuilder` 있는 (JavaScript) 메서드에서 구성할 `HttpHubConnectionBuilder` 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a5910-718">.NET</span><span class="sxs-lookup"><span data-stu-id="a5910-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a5910-719">.NET 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-719">.NET Option</span></span> |  <span data-ttu-id="a5910-720">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-720">Default value</span></span> | <span data-ttu-id="a5910-721">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a5910-722">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a5910-723">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a5910-724">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="a5910-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a5910-725">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a5910-726">Empty</span><span class="sxs-lookup"><span data-stu-id="a5910-726">Empty</span></span> | <span data-ttu-id="a5910-727">요청을 인증하기 위해 보낼 TLS 인증서 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a5910-728">Empty</span><span class="sxs-lookup"><span data-stu-id="a5910-728">Empty</span></span> | <span data-ttu-id="a5910-729">모든 HTTP 요청과 함께 보낼 HTTP 쿠키 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a5910-730">Empty</span><span class="sxs-lookup"><span data-stu-id="a5910-730">Empty</span></span> | <span data-ttu-id="a5910-731">모든 HTTP 요청과 함께 보낼 자격 증명입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a5910-732">5초</span><span class="sxs-lookup"><span data-stu-id="a5910-732">5 seconds</span></span> | <span data-ttu-id="a5910-733">웹 소켓만.</span><span class="sxs-lookup"><span data-stu-id="a5910-733">WebSockets only.</span></span> <span data-ttu-id="a5910-734">서버가 닫기 요청을 승인할 때까지 클라이언트가 닫은 후 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a5910-735">이 시간 내에 서버가 닫기를 승인하지 않으면 클라이언트가 연결 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a5910-736">Empty</span><span class="sxs-lookup"><span data-stu-id="a5910-736">Empty</span></span> | <span data-ttu-id="a5910-737">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a5910-738">HTTP 요청을 보내는 데 사용되는 대리자를 구성하거나 교체하는 데 사용할 수 있는 `HttpMessageHandler` 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a5910-739">WebSocket 연결에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="a5910-740">이 대리자는 null이 아닌 값을 반환해야 하며 기본값을 매개 변수로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a5910-741">기본값의 설정을 수정하여 반환하거나 새 `HttpMessageHandler` 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a5910-742">**처리기를 교체할 때 제공된 처리기에서 유지하려는 설정을 복사해야 하며, 그렇지 않으면 구성된 옵션(예: 쿠키 및 헤더)이 새 처리기에 적용되지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="a5910-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a5910-743">HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a5910-744">HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 보내도록 이 부울을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a5910-745">이렇게 하면 Windows 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a5910-746">추가 WebSocket 옵션을 구성하는 데 사용할 수 있는 대리자입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a5910-747">옵션을 구성하는 데 사용할 수 있는 [ClientWebSocketOptions의](/dotnet/api/system.net.websockets.clientwebsocketoptions) 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a5910-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a5910-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a5910-749">자바 스크립트 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-749">JavaScript Option</span></span> | <span data-ttu-id="a5910-750">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-750">Default Value</span></span> | <span data-ttu-id="a5910-751">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a5910-752">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a5910-753">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a5910-754">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="a5910-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a5910-755">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a5910-756">Java</span><span class="sxs-lookup"><span data-stu-id="a5910-756">Java</span></span>](#tab/java)

| <span data-ttu-id="a5910-757">자바 옵션</span><span class="sxs-lookup"><span data-stu-id="a5910-757">Java Option</span></span> | <span data-ttu-id="a5910-758">기본값</span><span class="sxs-lookup"><span data-stu-id="a5910-758">Default Value</span></span> | <span data-ttu-id="a5910-759">Description</span><span class="sxs-lookup"><span data-stu-id="a5910-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a5910-760">HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a5910-761">협상 단계를 `true` 건너뛰도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a5910-762">**WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.**</span><span class="sxs-lookup"><span data-stu-id="a5910-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a5910-763">Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a5910-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a5910-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a5910-765">Empty</span><span class="sxs-lookup"><span data-stu-id="a5910-765">Empty</span></span> | <span data-ttu-id="a5910-766">모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a5910-767">.NET 클라이언트에서 이러한 옵션은 다음에 제공된 옵션 대리자에서 수정할 `WithUrl`수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5910-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a5910-768">자바 스크립트 클라이언트에서 이러한 옵션은 `withUrl`다음에 제공되는 자바 스크립트 개체에서 제공 할 수 있습니다 .</span><span class="sxs-lookup"><span data-stu-id="a5910-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a5910-769">Java 클라이언트에서 이러한 옵션은 `HttpHubConnectionBuilder` 반환된 메서드로 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a5910-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a5910-770">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a5910-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
