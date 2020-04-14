---
title: ASP.NET 코어에 SignalR 메시지 팩 허브 프로토콜사용
author: bradygaster
description: ASP.NET 코어에 SignalRMessagePack 허브 프로토콜을 추가합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: bbc34d790387a96bb3b6f75e841b45685eb137ce
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277238"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="c08b6-103">ASP.NET 코어에 SignalR 메시지 팩 허브 프로토콜사용</span><span class="sxs-lookup"><span data-stu-id="c08b6-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c08b6-104">이 문서에서는 독자가 [시작하기](xref:tutorials/signalr)에서 다루는 주제에 익숙하다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="c08b6-105">메시지 팩이란 무엇입니까?</span><span class="sxs-lookup"><span data-stu-id="c08b6-105">What is MessagePack?</span></span>

<span data-ttu-id="c08b6-106">[MessagePack은](https://msgpack.org/index.html) 빠르고 컴팩트한 이진 직렬화 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="c08b6-107">[JSON](https://www.json.org/)에 비해 작은 메시지를 생성하기 때문에 성능과 대역폭이 문제가 될 때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="c08b6-108">바이트가 MessagePack 파서를 통과하지 않는 한 네트워크 추적 및 로그를 볼 때 이진 메시지를 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="c08b6-109">는 MessagePack 형식에 대한 기본 제공 지원을 제공하며 클라이언트와 서버가 사용할 API를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="c08b6-110">서버에서 메시지 팩 구성</span><span class="sxs-lookup"><span data-stu-id="c08b6-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="c08b6-111">서버에서 MessagePack 허브 프로토콜을 사용하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 앱에 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="c08b6-112">메서드에서 `Startup.ConfigureServices` 호출에 `AddMessagePackProtocol` `AddSignalR` 추가하여 서버에서 MessagePack 지원을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="c08b6-113">JSON은 기본적으로 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-113">JSON is enabled by default.</span></span> <span data-ttu-id="c08b6-114">메시지 팩을 추가하면 JSON 및 MessagePack 클라이언트를 모두 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="c08b6-115">MessagePack이 데이터를 포맷하는 방법을 `AddMessagePackProtocol` 사용자 지정하려면 구성 옵션을 위해 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="c08b6-116">이 대리자에서 `SerializerOptions` 속성을 사용하여 MessagePack 직렬화 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="c08b6-117">확인자의 작동 방식에 대한 자세한 내용은 [MessagePack-CSharp의 MessagePack](https://github.com/neuecc/MessagePack-CSharp)라이브러리를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="c08b6-118">특성을 직렬화하려는 개체에서 사용하여 처리 방법을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="c08b6-119">[CVE-2020-5234를](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 검토하고 권장 패치를 적용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="c08b6-120">예를 들어, `.WithSecurity(MessagePackSecurity.UntrustedData)` 을 교체할 때 호출합니다. `SerializerOptions`</span><span class="sxs-lookup"><span data-stu-id="c08b6-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="c08b6-121">클라이언트에서 메시지 팩 구성</span><span class="sxs-lookup"><span data-stu-id="c08b6-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="c08b6-122">JSON은 지원되는 클라이언트에 대해 기본적으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="c08b6-123">클라이언트는 단일 프로토콜만 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="c08b6-124">MessagePack 지원을 추가하면 이전에 구성된 프로토콜이 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="c08b6-125">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c08b6-125">.NET client</span></span>

<span data-ttu-id="c08b6-126">.NET 클라이언트에서 MessagePack을 사용하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 패키지를 `AddMessagePackProtocol` 설치하고 `HubConnectionBuilder`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="c08b6-127">이 `AddMessagePackProtocol` 호출은 서버와 마찬가지로 옵션을 구성하기 위해 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="c08b6-128">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c08b6-128">JavaScript client</span></span>

<span data-ttu-id="c08b6-129">자바 스크립트 클라이언트에 대한 메시지 팩 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) 지원은 npm 패키지에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="c08b6-130">명령 셸에서 다음 명령을 실행 하여 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="c08b6-131">npm 패키지를 설치한 후 JavaScript 모듈 로더를 통해 직접 사용하거나 다음 파일을 참조하여 브라우저로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="c08b6-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="c08b6-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="c08b6-133">브라우저에서 `msgpack5` 라이브러리도 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="c08b6-134">`<script>` 태그를 사용하여 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="c08b6-135">라이브러리는 *node_modules\msgpack5\dist\msgpack5.js에서*찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="c08b6-136">`<script>` 요소를 사용할 때는 순서가 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="c08b6-137">*시그널러 프로토콜-msgpack.jsmsgpack5.js* 전에 참조 하는 경우, MessagePack에 연결 하려고 할 때 오류가 발생 합니다. *msgpack5.js*</span><span class="sxs-lookup"><span data-stu-id="c08b6-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="c08b6-138">*signalr.js* *시그널러-프로토콜-msgpack.js 전에도 시그널러.js가*필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="c08b6-139">서버에 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` 연결할 때 MessagePack 프로토콜을 사용하도록 클라이언트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="c08b6-140">현재 JavaScript 클라이언트의 MessagePack 프로토콜에 대한 구성 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="c08b6-141">메시지 팩 단점</span><span class="sxs-lookup"><span data-stu-id="c08b6-141">MessagePack quirks</span></span>

<span data-ttu-id="c08b6-142">MessagePack 허브 프로토콜을 사용할 때 주의해야 할 몇 가지 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="c08b6-143">메시지 팩은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="c08b6-144">MessagePack 프로토콜은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="c08b6-145">예를 들어 다음 C# 클래스를 고려하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="c08b6-146">JavaScript 클라이언트에서 보낼 때 대/소문자가 C# 클래스와 정확히 일치해야 하므로 속성 이름을 사용해야 `PascalCased` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="c08b6-147">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="c08b6-148">이름을 `camelCased` 사용하면 C# 클래스에 제대로 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="c08b6-149">특성을 사용하여 MessagePack `Key` 속성에 대해 다른 이름을 지정하여 이 방법을 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="c08b6-150">자세한 내용은 [MessagePack-CSharp 문서를](https://github.com/neuecc/MessagePack-CSharp#object-serialization)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="c08b6-151">DateTime.Kind화/역직렬화 할 때 종류가 유지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="c08b6-152">MessagePack 프로토콜은 `Kind` `DateTime`의 값을 인코딩하는 방법을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="c08b6-153">따라서 날짜를 역직렬화할 때 MessagePack 허브 `DateTime.Kind` 프로토콜은 `DateTimeKind.Local` 그렇지 않으면 시간을 터치하지 않고 있는 것처럼 전달되지 않는 경우 UTC 형식으로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="c08b6-154">값으로 `DateTime` 작업하는 경우 값을 보내기 전에 UTC로 변환하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="c08b6-155">UTC를 받을 때 현지 시간으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="c08b6-156">DateTime.MinValue 자바 스크립트의 메시지 팩에 의해 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="c08b6-157">자바 스크립트 클라이언트에서 사용하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 `timestamp96` MessagePack의 형식을 지원하지 않습니다. SignalR</span><span class="sxs-lookup"><span data-stu-id="c08b6-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="c08b6-158">이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용됩니다(과거의 초기 또는 미래에는 매우 먼 날짜).</span><span class="sxs-lookup"><span data-stu-id="c08b6-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="c08b6-159">의 `DateTime.MinValue` 값은 `January 1, 0001`값으로 `timestamp96` 인코딩되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="c08b6-160">따라서 JavaScript `DateTime.MinValue` 클라이언트로 보내는 것은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="c08b6-161">`DateTime.MinValue` JavaScript 클라이언트에서 수신되면 다음과 같은 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="c08b6-162">`DateTime.MinValue` 일반적으로 "누락된" 또는 `null` 값을 인코딩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="c08b6-163">MessagePack에서 해당 값을 인코딩해야 하는 경우 `DateTime` nullable`DateTime?`값 () `bool` 을 사용하거나 날짜가 있는지 나타내는 별도의 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="c08b6-164">이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228)를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="c08b6-165">"사전" 컴파일 환경에서 메시지 팩 지원</span><span class="sxs-lookup"><span data-stu-id="c08b6-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="c08b6-166">.NET 클라이언트 및 서버에서 사용하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) 라이브러리는 코드 생성을 사용하여 직렬화를 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="c08b6-167">따라서 Xamarin iOS 또는 Unity와 같이 "사전" 컴파일을 사용하는 환경에서는 기본적으로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="c08b6-168">직렬화/디직렬화 코드를 "미리 생성"하여 이러한 환경에서 MessagePack을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="c08b6-169">자세한 내용은 [MessagePack-CSharp 문서를](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="c08b6-170">serializer를 미리 생성한 후에는 다음으로 전달된 구성 대리자를 사용하여 serializer를 등록할 `AddMessagePackProtocol`수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="c08b6-171">메시지 팩에서 형식 검사가 더 엄격합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="c08b6-172">JSON 허브 프로토콜은 직렬화 중에 형식 변환을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="c08b6-173">예를 들어 들어오는 개체에 숫자 ()`{ foo: 42 }`하지만 .NET 클래스의 속성이 형식인 `string`경우 값이 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="c08b6-174">그러나 MessagePack이 변환을 수행 하지 않습니다 및 서버 측 로그 (및 콘솔에서 볼 수 있는 예외)를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="c08b6-175">이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937)를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="c08b6-176">관련 참고 자료</span><span class="sxs-lookup"><span data-stu-id="c08b6-176">Related resources</span></span>

* [<span data-ttu-id="c08b6-177">시작</span><span class="sxs-lookup"><span data-stu-id="c08b6-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c08b6-178">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c08b6-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c08b6-179">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c08b6-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="c08b6-180">이 문서에서는 독자가 [시작하기](xref:tutorials/signalr)에서 다루는 주제에 익숙하다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="c08b6-181">메시지 팩이란 무엇입니까?</span><span class="sxs-lookup"><span data-stu-id="c08b6-181">What is MessagePack?</span></span>

<span data-ttu-id="c08b6-182">[MessagePack은](https://msgpack.org/index.html) 빠르고 컴팩트한 이진 직렬화 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="c08b6-183">[JSON](https://www.json.org/)에 비해 작은 메시지를 생성하기 때문에 성능과 대역폭이 문제가 될 때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="c08b6-184">바이트가 MessagePack 파서를 통과하지 않는 한 네트워크 추적 및 로그를 볼 때 이진 메시지를 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="c08b6-185">는 MessagePack 형식에 대한 기본 제공 지원을 제공하며 클라이언트와 서버가 사용할 API를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="c08b6-186">서버에서 메시지 팩 구성</span><span class="sxs-lookup"><span data-stu-id="c08b6-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="c08b6-187">서버에서 MessagePack 허브 프로토콜을 사용하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 앱에 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="c08b6-188">메서드에서 `Startup.ConfigureServices` 호출에 `AddMessagePackProtocol` `AddSignalR` 추가하여 서버에서 MessagePack 지원을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="c08b6-189">JSON은 기본적으로 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-189">JSON is enabled by default.</span></span> <span data-ttu-id="c08b6-190">메시지 팩을 추가하면 JSON 및 MessagePack 클라이언트를 모두 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="c08b6-191">MessagePack이 데이터를 포맷하는 방법을 `AddMessagePackProtocol` 사용자 지정하려면 구성 옵션을 위해 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="c08b6-192">이 대리자에서 `FormatterResolvers` 속성을 사용하여 MessagePack 직렬화 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="c08b6-193">확인자의 작동 방식에 대한 자세한 내용은 [MessagePack-CSharp의 MessagePack](https://github.com/neuecc/MessagePack-CSharp)라이브러리를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="c08b6-194">특성을 직렬화하려는 개체에서 사용하여 처리 방법을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="c08b6-195">[CVE-2020-5234를](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 검토하고 권장 패치를 적용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="c08b6-196">예를 들어 `MessagePackSecurity.Active` 정적 속성을 `MessagePackSecurity.UntrustedData`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="c08b6-197">`MessagePackSecurity.Active` [1.9.x 버전의 MessagePack을](https://www.nuget.org/packages/MessagePack/1.9.3)수동으로 설치해야 하는 경우를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="c08b6-198">1.9.x를 설치하면 `MessagePack` 버전에서 SignalR 사용하는 업그레이드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="c08b6-199">로 설정되지 않은 경우 `MessagePackSecurity.Active` 악의적인 클라이언트로 인해 서비스 거부가 발생할 수 있습니다. `MessagePackSecurity.UntrustedData`</span><span class="sxs-lookup"><span data-stu-id="c08b6-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="c08b6-200">다음 `MessagePackSecurity.Active` `Program.Main`코드에 표시된 대로 을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="c08b6-201">클라이언트에서 메시지 팩 구성</span><span class="sxs-lookup"><span data-stu-id="c08b6-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="c08b6-202">JSON은 지원되는 클라이언트에 대해 기본적으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="c08b6-203">클라이언트는 단일 프로토콜만 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="c08b6-204">MessagePack 지원을 추가하면 이전에 구성된 프로토콜이 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="c08b6-205">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c08b6-205">.NET client</span></span>

<span data-ttu-id="c08b6-206">.NET 클라이언트에서 MessagePack을 사용하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 패키지를 `AddMessagePackProtocol` 설치하고 `HubConnectionBuilder`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="c08b6-207">이 `AddMessagePackProtocol` 호출은 서버와 마찬가지로 옵션을 구성하기 위해 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="c08b6-208">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c08b6-208">JavaScript client</span></span>

<span data-ttu-id="c08b6-209">자바 스크립트 클라이언트에 대한 메시지 팩 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) 지원은 npm 패키지에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="c08b6-210">명령 셸에서 다음 명령을 실행 하여 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="c08b6-211">npm 패키지를 설치한 후 JavaScript 모듈 로더를 통해 직접 사용하거나 다음 파일을 참조하여 브라우저로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="c08b6-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="c08b6-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="c08b6-213">브라우저에서 `msgpack5` 라이브러리도 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="c08b6-214">`<script>` 태그를 사용하여 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="c08b6-215">라이브러리는 *node_modules\msgpack5\dist\msgpack5.js에서*찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="c08b6-216">`<script>` 요소를 사용할 때는 순서가 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="c08b6-217">*시그널러 프로토콜-msgpack.jsmsgpack5.js* 전에 참조 하는 경우, MessagePack에 연결 하려고 할 때 오류가 발생 합니다. *msgpack5.js*</span><span class="sxs-lookup"><span data-stu-id="c08b6-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="c08b6-218">*signalr.js* *시그널러-프로토콜-msgpack.js 전에도 시그널러.js가*필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="c08b6-219">서버에 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` 연결할 때 MessagePack 프로토콜을 사용하도록 클라이언트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="c08b6-220">현재 JavaScript 클라이언트의 MessagePack 프로토콜에 대한 구성 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="c08b6-221">메시지 팩 단점</span><span class="sxs-lookup"><span data-stu-id="c08b6-221">MessagePack quirks</span></span>

<span data-ttu-id="c08b6-222">MessagePack 허브 프로토콜을 사용할 때 주의해야 할 몇 가지 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="c08b6-223">메시지 팩은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="c08b6-224">MessagePack 프로토콜은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="c08b6-225">예를 들어 다음 C# 클래스를 고려하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="c08b6-226">JavaScript 클라이언트에서 보낼 때 대/소문자가 C# 클래스와 정확히 일치해야 하므로 속성 이름을 사용해야 `PascalCased` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="c08b6-227">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="c08b6-228">이름을 `camelCased` 사용하면 C# 클래스에 제대로 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="c08b6-229">특성을 사용하여 MessagePack `Key` 속성에 대해 다른 이름을 지정하여 이 방법을 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="c08b6-230">자세한 내용은 [MessagePack-CSharp 문서를](https://github.com/neuecc/MessagePack-CSharp#object-serialization)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="c08b6-231">DateTime.Kind화/역직렬화 할 때 종류가 유지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="c08b6-232">MessagePack 프로토콜은 `Kind` `DateTime`의 값을 인코딩하는 방법을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="c08b6-233">따라서 날짜를 역직렬화할 때 MessagePack 허브 프로토콜은 들어오는 날짜가 UTC 형식으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="c08b6-234">현지 시간으로 `DateTime` 값을 작업하는 경우 값을 보내기 전에 UTC로 변환하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="c08b6-235">UTC를 받을 때 현지 시간으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="c08b6-236">이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2632SignalR](https://github.com/aspnet/SignalR/issues/2632)를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="c08b6-237">DateTime.MinValue 자바 스크립트의 메시지 팩에 의해 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="c08b6-238">자바 스크립트 클라이언트에서 사용하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 `timestamp96` MessagePack의 형식을 지원하지 않습니다. SignalR</span><span class="sxs-lookup"><span data-stu-id="c08b6-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="c08b6-239">이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용됩니다(과거의 초기 또는 미래에는 매우 먼 날짜).</span><span class="sxs-lookup"><span data-stu-id="c08b6-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="c08b6-240">의 `DateTime.MinValue` 값은 `January 1, 0001`값으로 `timestamp96` 인코딩되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="c08b6-241">따라서 JavaScript `DateTime.MinValue` 클라이언트로 보내는 것은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="c08b6-242">`DateTime.MinValue` JavaScript 클라이언트에서 수신되면 다음과 같은 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="c08b6-243">`DateTime.MinValue` 일반적으로 "누락된" 또는 `null` 값을 인코딩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="c08b6-244">MessagePack에서 해당 값을 인코딩해야 하는 경우 `DateTime` nullable`DateTime?`값 () `bool` 을 사용하거나 날짜가 있는지 나타내는 별도의 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="c08b6-245">이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228)를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="c08b6-246">"사전" 컴파일 환경에서 메시지 팩 지원</span><span class="sxs-lookup"><span data-stu-id="c08b6-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="c08b6-247">.NET 클라이언트 및 서버에서 사용하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 라이브러리는 코드 생성을 사용하여 직렬화를 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="c08b6-248">따라서 Xamarin iOS 또는 Unity와 같이 "사전" 컴파일을 사용하는 환경에서는 기본적으로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="c08b6-249">직렬화/디직렬화 코드를 "미리 생성"하여 이러한 환경에서 MessagePack을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="c08b6-250">자세한 내용은 [MessagePack-CSharp 문서를](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="c08b6-251">serializer를 미리 생성한 후에는 다음으로 전달된 구성 대리자를 사용하여 serializer를 등록할 `AddMessagePackProtocol`수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="c08b6-252">메시지 팩에서 형식 검사가 더 엄격합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="c08b6-253">JSON 허브 프로토콜은 직렬화 중에 형식 변환을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="c08b6-254">예를 들어 들어오는 개체에 숫자 ()`{ foo: 42 }`하지만 .NET 클래스의 속성이 형식인 `string`경우 값이 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="c08b6-255">그러나 MessagePack이 변환을 수행 하지 않습니다 및 서버 측 로그 (및 콘솔에서 볼 수 있는 예외)를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="c08b6-256">이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937)를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="c08b6-257">관련 참고 자료</span><span class="sxs-lookup"><span data-stu-id="c08b6-257">Related resources</span></span>

* [<span data-ttu-id="c08b6-258">시작</span><span class="sxs-lookup"><span data-stu-id="c08b6-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c08b6-259">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c08b6-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c08b6-260">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c08b6-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c08b6-261">이 문서에서는 독자가 [시작하기](xref:tutorials/signalr)에서 다루는 주제에 익숙하다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="c08b6-262">메시지 팩이란 무엇입니까?</span><span class="sxs-lookup"><span data-stu-id="c08b6-262">What is MessagePack?</span></span>

<span data-ttu-id="c08b6-263">[MessagePack은](https://msgpack.org/index.html) 빠르고 컴팩트한 이진 직렬화 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="c08b6-264">[JSON](https://www.json.org/)에 비해 작은 메시지를 생성하기 때문에 성능과 대역폭이 문제가 될 때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="c08b6-265">바이트가 MessagePack 파서를 통과하지 않는 한 네트워크 추적 및 로그를 볼 때 이진 메시지를 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="c08b6-266">는 MessagePack 형식에 대한 기본 제공 지원을 제공하며 클라이언트와 서버가 사용할 API를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="c08b6-267">서버에서 메시지 팩 구성</span><span class="sxs-lookup"><span data-stu-id="c08b6-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="c08b6-268">서버에서 MessagePack 허브 프로토콜을 사용하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 앱에 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="c08b6-269">메서드에서 `Startup.ConfigureServices` 호출에 `AddMessagePackProtocol` `AddSignalR` 추가하여 서버에서 MessagePack 지원을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="c08b6-270">JSON은 기본적으로 활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-270">JSON is enabled by default.</span></span> <span data-ttu-id="c08b6-271">메시지 팩을 추가하면 JSON 및 MessagePack 클라이언트를 모두 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="c08b6-272">MessagePack이 데이터를 포맷하는 방법을 `AddMessagePackProtocol` 사용자 지정하려면 구성 옵션을 위해 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="c08b6-273">이 대리자에서 `FormatterResolvers` 속성을 사용하여 MessagePack 직렬화 옵션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="c08b6-274">확인자의 작동 방식에 대한 자세한 내용은 [MessagePack-CSharp의 MessagePack](https://github.com/neuecc/MessagePack-CSharp)라이브러리를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="c08b6-275">특성을 직렬화하려는 개체에서 사용하여 처리 방법을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="c08b6-276">[CVE-2020-5234를](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 검토하고 권장 패치를 적용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="c08b6-277">예를 들어 `MessagePackSecurity.Active` 정적 속성을 `MessagePackSecurity.UntrustedData`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="c08b6-278">`MessagePackSecurity.Active` [1.9.x 버전의 MessagePack을](https://www.nuget.org/packages/MessagePack/1.9.3)수동으로 설치해야 하는 경우를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="c08b6-279">1.9.x를 설치하면 `MessagePack` 버전에서 SignalR 사용하는 업그레이드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="c08b6-280">로 설정되지 않은 경우 `MessagePackSecurity.Active` 악의적인 클라이언트로 인해 서비스 거부가 발생할 수 있습니다. `MessagePackSecurity.UntrustedData`</span><span class="sxs-lookup"><span data-stu-id="c08b6-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="c08b6-281">다음 `MessagePackSecurity.Active` `Program.Main`코드에 표시된 대로 을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="c08b6-282">클라이언트에서 메시지 팩 구성</span><span class="sxs-lookup"><span data-stu-id="c08b6-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="c08b6-283">JSON은 지원되는 클라이언트에 대해 기본적으로 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="c08b6-284">클라이언트는 단일 프로토콜만 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="c08b6-285">MessagePack 지원을 추가하면 이전에 구성된 프로토콜이 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="c08b6-286">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c08b6-286">.NET client</span></span>

<span data-ttu-id="c08b6-287">.NET 클라이언트에서 MessagePack을 사용하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 패키지를 `AddMessagePackProtocol` 설치하고 `HubConnectionBuilder`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="c08b6-288">이 `AddMessagePackProtocol` 호출은 서버와 마찬가지로 옵션을 구성하기 위해 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="c08b6-289">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c08b6-289">JavaScript client</span></span>

<span data-ttu-id="c08b6-290">자바 스크립트 클라이언트에 대한 메시지 팩 [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) 지원은 npm 패키지에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="c08b6-291">명령 셸에서 다음 명령을 실행 하여 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="c08b6-292">npm 패키지를 설치한 후 JavaScript 모듈 로더를 통해 직접 사용하거나 다음 파일을 참조하여 브라우저로 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="c08b6-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="c08b6-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="c08b6-294">브라우저에서 `msgpack5` 라이브러리도 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="c08b6-295">`<script>` 태그를 사용하여 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="c08b6-296">라이브러리는 *node_modules\msgpack5\dist\msgpack5.js에서*찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="c08b6-297">`<script>` 요소를 사용할 때는 순서가 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="c08b6-298">*시그널러 프로토콜-msgpack.jsmsgpack5.js* 전에 참조 하는 경우, MessagePack에 연결 하려고 할 때 오류가 발생 합니다. *msgpack5.js*</span><span class="sxs-lookup"><span data-stu-id="c08b6-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="c08b6-299">*signalr.js* *시그널러-프로토콜-msgpack.js 전에도 시그널러.js가*필요합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="c08b6-300">서버에 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` 연결할 때 MessagePack 프로토콜을 사용하도록 클라이언트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="c08b6-301">현재 JavaScript 클라이언트의 MessagePack 프로토콜에 대한 구성 옵션이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="c08b6-302">메시지 팩 단점</span><span class="sxs-lookup"><span data-stu-id="c08b6-302">MessagePack quirks</span></span>

<span data-ttu-id="c08b6-303">MessagePack 허브 프로토콜을 사용할 때 주의해야 할 몇 가지 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="c08b6-304">메시지 팩은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="c08b6-305">MessagePack 프로토콜은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="c08b6-306">예를 들어 다음 C# 클래스를 고려하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="c08b6-307">JavaScript 클라이언트에서 보낼 때 대/소문자가 C# 클래스와 정확히 일치해야 하므로 속성 이름을 사용해야 `PascalCased` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="c08b6-308">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="c08b6-309">이름을 `camelCased` 사용하면 C# 클래스에 제대로 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="c08b6-310">특성을 사용하여 MessagePack `Key` 속성에 대해 다른 이름을 지정하여 이 방법을 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="c08b6-311">자세한 내용은 [MessagePack-CSharp 문서를](https://github.com/neuecc/MessagePack-CSharp#object-serialization)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="c08b6-312">DateTime.Kind화/역직렬화 할 때 종류가 유지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="c08b6-313">MessagePack 프로토콜은 `Kind` `DateTime`의 값을 인코딩하는 방법을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="c08b6-314">따라서 날짜를 역직렬화할 때 MessagePack 허브 프로토콜은 들어오는 날짜가 UTC 형식으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="c08b6-315">현지 시간으로 `DateTime` 값을 작업하는 경우 값을 보내기 전에 UTC로 변환하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="c08b6-316">UTC를 받을 때 현지 시간으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="c08b6-317">이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2632SignalR](https://github.com/aspnet/SignalR/issues/2632)를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="c08b6-318">DateTime.MinValue 자바 스크립트의 메시지 팩에 의해 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="c08b6-319">자바 스크립트 클라이언트에서 사용하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 `timestamp96` MessagePack의 형식을 지원하지 않습니다. SignalR</span><span class="sxs-lookup"><span data-stu-id="c08b6-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="c08b6-320">이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용됩니다(과거의 초기 또는 미래에는 매우 먼 날짜).</span><span class="sxs-lookup"><span data-stu-id="c08b6-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="c08b6-321">`DateTime.MinValue` 값은 `January 1, 0001` `timestamp96` 값으로 인코딩되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="c08b6-322">따라서 JavaScript `DateTime.MinValue` 클라이언트로 보내는 것은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="c08b6-323">`DateTime.MinValue` JavaScript 클라이언트에서 수신되면 다음과 같은 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="c08b6-324">`DateTime.MinValue` 일반적으로 "누락된" 또는 `null` 값을 인코딩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="c08b6-325">MessagePack에서 해당 값을 인코딩해야 하는 경우 `DateTime` nullable`DateTime?`값 () `bool` 을 사용하거나 날짜가 있는지 나타내는 별도의 값을 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="c08b6-326">이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228)를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="c08b6-327">"사전" 컴파일 환경에서 메시지 팩 지원</span><span class="sxs-lookup"><span data-stu-id="c08b6-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="c08b6-328">.NET 클라이언트 및 서버에서 사용하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 라이브러리는 코드 생성을 사용하여 직렬화를 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="c08b6-329">따라서 Xamarin iOS 또는 Unity와 같이 "사전" 컴파일을 사용하는 환경에서는 기본적으로 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="c08b6-330">직렬화/디직렬화 코드를 "미리 생성"하여 이러한 환경에서 MessagePack을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="c08b6-331">자세한 내용은 [MessagePack-CSharp 문서를](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="c08b6-332">serializer를 미리 생성한 후에는 다음으로 전달된 구성 대리자를 사용하여 serializer를 등록할 `AddMessagePackProtocol`수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="c08b6-333">메시지 팩에서 형식 검사가 더 엄격합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="c08b6-334">JSON 허브 프로토콜은 직렬화 중에 형식 변환을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="c08b6-335">예를 들어 들어오는 개체에 숫자 ()`{ foo: 42 }`하지만 .NET 클래스의 속성이 형식인 `string`경우 값이 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="c08b6-336">그러나 MessagePack이 변환을 수행 하지 않습니다 및 서버 측 로그 (및 콘솔에서 볼 수 있는 예외)를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="c08b6-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="c08b6-337">이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937)를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="c08b6-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="c08b6-338">관련 참고 자료</span><span class="sxs-lookup"><span data-stu-id="c08b6-338">Related resources</span></span>

* [<span data-ttu-id="c08b6-339">시작</span><span class="sxs-lookup"><span data-stu-id="c08b6-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c08b6-340">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c08b6-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c08b6-341">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="c08b6-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
