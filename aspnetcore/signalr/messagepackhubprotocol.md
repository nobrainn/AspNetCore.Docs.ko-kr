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
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>ASP.NET 코어에 SignalR 메시지 팩 허브 프로토콜사용

::: moniker range=">= aspnetcore-5.0"

이 문서에서는 독자가 [시작하기](xref:tutorials/signalr)에서 다루는 주제에 익숙하다고 가정합니다.

## <a name="what-is-messagepack"></a>메시지 팩이란 무엇입니까?

[MessagePack은](https://msgpack.org/index.html) 빠르고 컴팩트한 이진 직렬화 형식입니다. [JSON](https://www.json.org/)에 비해 작은 메시지를 생성하기 때문에 성능과 대역폭이 문제가 될 때 유용합니다. 바이트가 MessagePack 파서를 통과하지 않는 한 네트워크 추적 및 로그를 볼 때 이진 메시지를 읽을 수 없습니다. SignalR는 MessagePack 형식에 대한 기본 제공 지원을 제공하며 클라이언트와 서버가 사용할 API를 제공합니다.

## <a name="configure-messagepack-on-the-server"></a>서버에서 메시지 팩 구성

서버에서 MessagePack 허브 프로토콜을 사용하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 앱에 패키지를 설치합니다. 메서드에서 `Startup.ConfigureServices` 호출에 `AddMessagePackProtocol` `AddSignalR` 추가하여 서버에서 MessagePack 지원을 사용하도록 설정합니다.

> [!NOTE]
> JSON은 기본적으로 활성화되어 있습니다. 메시지 팩을 추가하면 JSON 및 MessagePack 클라이언트를 모두 지원할 수 있습니다.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack이 데이터를 포맷하는 방법을 `AddMessagePackProtocol` 사용자 지정하려면 구성 옵션을 위해 대리자를 사용합니다. 이 대리자에서 `SerializerOptions` 속성을 사용하여 MessagePack 직렬화 옵션을 구성할 수 있습니다. 확인자의 작동 방식에 대한 자세한 내용은 [MessagePack-CSharp의 MessagePack](https://github.com/neuecc/MessagePack-CSharp)라이브러리를 참조하십시오. 특성을 직렬화하려는 개체에서 사용하여 처리 방법을 정의할 수 있습니다.

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
> [CVE-2020-5234를](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 검토하고 권장 패치를 적용하는 것이 좋습니다. 예를 들어, `.WithSecurity(MessagePackSecurity.UntrustedData)` 을 교체할 때 호출합니다. `SerializerOptions`

## <a name="configure-messagepack-on-the-client"></a>클라이언트에서 메시지 팩 구성

> [!NOTE]
> JSON은 지원되는 클라이언트에 대해 기본적으로 활성화됩니다. 클라이언트는 단일 프로토콜만 지원할 수 있습니다. MessagePack 지원을 추가하면 이전에 구성된 프로토콜이 대체됩니다.

### <a name="net-client"></a>.NET 클라이언트

.NET 클라이언트에서 MessagePack을 사용하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 패키지를 `AddMessagePackProtocol` 설치하고 `HubConnectionBuilder`을 호출합니다.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> 이 `AddMessagePackProtocol` 호출은 서버와 마찬가지로 옵션을 구성하기 위해 대리자를 사용합니다.

### <a name="javascript-client"></a>JavaScript 클라이언트

자바 스크립트 클라이언트에 대한 메시지 팩 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) 지원은 npm 패키지에 의해 제공됩니다. 명령 셸에서 다음 명령을 실행 하여 패키지를 설치합니다.

```bash
npm install @microsoft/signalr-protocol-msgpack
```

npm 패키지를 설치한 후 JavaScript 모듈 로더를 통해 직접 사용하거나 다음 파일을 참조하여 브라우저로 가져올 수 있습니다.

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

브라우저에서 `msgpack5` 라이브러리도 참조해야 합니다. `<script>` 태그를 사용하여 참조를 만듭니다. 라이브러리는 *node_modules\msgpack5\dist\msgpack5.js에서*찾을 수 있습니다.

> [!NOTE]
> `<script>` 요소를 사용할 때는 순서가 중요합니다. *시그널러 프로토콜-msgpack.jsmsgpack5.js* 전에 참조 하는 경우, MessagePack에 연결 하려고 할 때 오류가 발생 합니다. *msgpack5.js* *signalr.js* *시그널러-프로토콜-msgpack.js 전에도 시그널러.js가*필요합니다.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

서버에 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` 연결할 때 MessagePack 프로토콜을 사용하도록 클라이언트를 구성합니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 현재 JavaScript 클라이언트의 MessagePack 프로토콜에 대한 구성 옵션이 없습니다.

## <a name="messagepack-quirks"></a>메시지 팩 단점

MessagePack 허브 프로토콜을 사용할 때 주의해야 할 몇 가지 문제가 있습니다.

### <a name="messagepack-is-case-sensitive"></a>메시지 팩은 대/소문자를 구분합니다.

MessagePack 프로토콜은 대/소문자를 구분합니다. 예를 들어 다음 C# 클래스를 고려하십시오.

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript 클라이언트에서 보낼 때 대/소문자가 C# 클래스와 정확히 일치해야 하므로 속성 이름을 사용해야 `PascalCased` 합니다. 다음은 그 예입니다.

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

이름을 `camelCased` 사용하면 C# 클래스에 제대로 바인딩되지 않습니다. 특성을 사용하여 MessagePack `Key` 속성에 대해 다른 이름을 지정하여 이 방법을 해결할 수 있습니다. 자세한 내용은 [MessagePack-CSharp 문서를](https://github.com/neuecc/MessagePack-CSharp#object-serialization)참조하십시오.

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind화/역직렬화 할 때 종류가 유지되지 않습니다.

MessagePack 프로토콜은 `Kind` `DateTime`의 값을 인코딩하는 방법을 제공하지 않습니다. 따라서 날짜를 역직렬화할 때 MessagePack 허브 `DateTime.Kind` 프로토콜은 `DateTimeKind.Local` 그렇지 않으면 시간을 터치하지 않고 있는 것처럼 전달되지 않는 경우 UTC 형식으로 변환됩니다. 값으로 `DateTime` 작업하는 경우 값을 보내기 전에 UTC로 변환하는 것이 좋습니다. UTC를 받을 때 현지 시간으로 변환합니다.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue 자바 스크립트의 메시지 팩에 의해 지원 되지 않습니다.

자바 스크립트 클라이언트에서 사용하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 `timestamp96` MessagePack의 형식을 지원하지 않습니다. SignalR 이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용됩니다(과거의 초기 또는 미래에는 매우 먼 날짜). 의 `DateTime.MinValue` 값은 `January 1, 0001`값으로 `timestamp96` 인코딩되어야 합니다. 따라서 JavaScript `DateTime.MinValue` 클라이언트로 보내는 것은 지원되지 않습니다. `DateTime.MinValue` JavaScript 클라이언트에서 수신되면 다음과 같은 오류가 발생합니다.

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

`DateTime.MinValue` 일반적으로 "누락된" 또는 `null` 값을 인코딩하는 데 사용됩니다. MessagePack에서 해당 값을 인코딩해야 하는 경우 `DateTime` nullable`DateTime?`값 () `bool` 을 사용하거나 날짜가 있는지 나타내는 별도의 값을 인코딩합니다.

이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228)를 참조하십시오.

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"사전" 컴파일 환경에서 메시지 팩 지원

.NET 클라이언트 및 서버에서 사용하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) 라이브러리는 코드 생성을 사용하여 직렬화를 최적화합니다. 따라서 Xamarin iOS 또는 Unity와 같이 "사전" 컴파일을 사용하는 환경에서는 기본적으로 지원되지 않습니다. 직렬화/디직렬화 코드를 "미리 생성"하여 이러한 환경에서 MessagePack을 사용할 수 있습니다. 자세한 내용은 [MessagePack-CSharp 문서를](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)참조하십시오. serializer를 미리 생성한 후에는 다음으로 전달된 구성 대리자를 사용하여 serializer를 등록할 `AddMessagePackProtocol`수 있습니다.

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>메시지 팩에서 형식 검사가 더 엄격합니다.

JSON 허브 프로토콜은 직렬화 중에 형식 변환을 수행합니다. 예를 들어 들어오는 개체에 숫자 ()`{ foo: 42 }`하지만 .NET 클래스의 속성이 형식인 `string`경우 값이 변환됩니다. 그러나 MessagePack이 변환을 수행 하지 않습니다 및 서버 측 로그 (및 콘솔에서 볼 수 있는 예외)를 throw 합니다.

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937)를 참조하십시오.

## <a name="related-resources"></a>관련 참고 자료

* [시작](xref:tutorials/signalr)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [JavaScript 클라이언트](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

이 문서에서는 독자가 [시작하기](xref:tutorials/signalr)에서 다루는 주제에 익숙하다고 가정합니다.

## <a name="what-is-messagepack"></a>메시지 팩이란 무엇입니까?

[MessagePack은](https://msgpack.org/index.html) 빠르고 컴팩트한 이진 직렬화 형식입니다. [JSON](https://www.json.org/)에 비해 작은 메시지를 생성하기 때문에 성능과 대역폭이 문제가 될 때 유용합니다. 바이트가 MessagePack 파서를 통과하지 않는 한 네트워크 추적 및 로그를 볼 때 이진 메시지를 읽을 수 없습니다. SignalR는 MessagePack 형식에 대한 기본 제공 지원을 제공하며 클라이언트와 서버가 사용할 API를 제공합니다.

## <a name="configure-messagepack-on-the-server"></a>서버에서 메시지 팩 구성

서버에서 MessagePack 허브 프로토콜을 사용하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 앱에 패키지를 설치합니다. 메서드에서 `Startup.ConfigureServices` 호출에 `AddMessagePackProtocol` `AddSignalR` 추가하여 서버에서 MessagePack 지원을 사용하도록 설정합니다.

> [!NOTE]
> JSON은 기본적으로 활성화되어 있습니다. 메시지 팩을 추가하면 JSON 및 MessagePack 클라이언트를 모두 지원할 수 있습니다.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack이 데이터를 포맷하는 방법을 `AddMessagePackProtocol` 사용자 지정하려면 구성 옵션을 위해 대리자를 사용합니다. 이 대리자에서 `FormatterResolvers` 속성을 사용하여 MessagePack 직렬화 옵션을 구성할 수 있습니다. 확인자의 작동 방식에 대한 자세한 내용은 [MessagePack-CSharp의 MessagePack](https://github.com/neuecc/MessagePack-CSharp)라이브러리를 참조하십시오. 특성을 직렬화하려는 개체에서 사용하여 처리 방법을 정의할 수 있습니다.

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
> [CVE-2020-5234를](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 검토하고 권장 패치를 적용하는 것이 좋습니다. 예를 들어 `MessagePackSecurity.Active` 정적 속성을 `MessagePackSecurity.UntrustedData`로 설정합니다. `MessagePackSecurity.Active` [1.9.x 버전의 MessagePack을](https://www.nuget.org/packages/MessagePack/1.9.3)수동으로 설치해야 하는 경우를 설정합니다. 1.9.x를 설치하면 `MessagePack` 버전에서 SignalR 사용하는 업그레이드가 사용됩니다. 로 설정되지 않은 경우 `MessagePackSecurity.Active` 악의적인 클라이언트로 인해 서비스 거부가 발생할 수 있습니다. `MessagePackSecurity.UntrustedData` 다음 `MessagePackSecurity.Active` `Program.Main`코드에 표시된 대로 을 설정합니다.

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>클라이언트에서 메시지 팩 구성

> [!NOTE]
> JSON은 지원되는 클라이언트에 대해 기본적으로 활성화됩니다. 클라이언트는 단일 프로토콜만 지원할 수 있습니다. MessagePack 지원을 추가하면 이전에 구성된 프로토콜이 대체됩니다.

### <a name="net-client"></a>.NET 클라이언트

.NET 클라이언트에서 MessagePack을 사용하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 패키지를 `AddMessagePackProtocol` 설치하고 `HubConnectionBuilder`을 호출합니다.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> 이 `AddMessagePackProtocol` 호출은 서버와 마찬가지로 옵션을 구성하기 위해 대리자를 사용합니다.

### <a name="javascript-client"></a>JavaScript 클라이언트

자바 스크립트 클라이언트에 대한 메시지 팩 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) 지원은 npm 패키지에 의해 제공됩니다. 명령 셸에서 다음 명령을 실행 하여 패키지를 설치합니다.

```bash
npm install @microsoft/signalr-protocol-msgpack
```

npm 패키지를 설치한 후 JavaScript 모듈 로더를 통해 직접 사용하거나 다음 파일을 참조하여 브라우저로 가져올 수 있습니다.

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

브라우저에서 `msgpack5` 라이브러리도 참조해야 합니다. `<script>` 태그를 사용하여 참조를 만듭니다. 라이브러리는 *node_modules\msgpack5\dist\msgpack5.js에서*찾을 수 있습니다.

> [!NOTE]
> `<script>` 요소를 사용할 때는 순서가 중요합니다. *시그널러 프로토콜-msgpack.jsmsgpack5.js* 전에 참조 하는 경우, MessagePack에 연결 하려고 할 때 오류가 발생 합니다. *msgpack5.js* *signalr.js* *시그널러-프로토콜-msgpack.js 전에도 시그널러.js가*필요합니다.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

서버에 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` 연결할 때 MessagePack 프로토콜을 사용하도록 클라이언트를 구성합니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 현재 JavaScript 클라이언트의 MessagePack 프로토콜에 대한 구성 옵션이 없습니다.

## <a name="messagepack-quirks"></a>메시지 팩 단점

MessagePack 허브 프로토콜을 사용할 때 주의해야 할 몇 가지 문제가 있습니다.

### <a name="messagepack-is-case-sensitive"></a>메시지 팩은 대/소문자를 구분합니다.

MessagePack 프로토콜은 대/소문자를 구분합니다. 예를 들어 다음 C# 클래스를 고려하십시오.

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript 클라이언트에서 보낼 때 대/소문자가 C# 클래스와 정확히 일치해야 하므로 속성 이름을 사용해야 `PascalCased` 합니다. 다음은 그 예입니다.

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

이름을 `camelCased` 사용하면 C# 클래스에 제대로 바인딩되지 않습니다. 특성을 사용하여 MessagePack `Key` 속성에 대해 다른 이름을 지정하여 이 방법을 해결할 수 있습니다. 자세한 내용은 [MessagePack-CSharp 문서를](https://github.com/neuecc/MessagePack-CSharp#object-serialization)참조하십시오.

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind화/역직렬화 할 때 종류가 유지되지 않습니다.

MessagePack 프로토콜은 `Kind` `DateTime`의 값을 인코딩하는 방법을 제공하지 않습니다. 따라서 날짜를 역직렬화할 때 MessagePack 허브 프로토콜은 들어오는 날짜가 UTC 형식으로 가정합니다. 현지 시간으로 `DateTime` 값을 작업하는 경우 값을 보내기 전에 UTC로 변환하는 것이 좋습니다. UTC를 받을 때 현지 시간으로 변환합니다.

이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2632SignalR](https://github.com/aspnet/SignalR/issues/2632)를 참조하십시오.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue 자바 스크립트의 메시지 팩에 의해 지원 되지 않습니다.

자바 스크립트 클라이언트에서 사용하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 `timestamp96` MessagePack의 형식을 지원하지 않습니다. SignalR 이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용됩니다(과거의 초기 또는 미래에는 매우 먼 날짜). 의 `DateTime.MinValue` 값은 `January 1, 0001`값으로 `timestamp96` 인코딩되어야 합니다. 따라서 JavaScript `DateTime.MinValue` 클라이언트로 보내는 것은 지원되지 않습니다. `DateTime.MinValue` JavaScript 클라이언트에서 수신되면 다음과 같은 오류가 발생합니다.

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

`DateTime.MinValue` 일반적으로 "누락된" 또는 `null` 값을 인코딩하는 데 사용됩니다. MessagePack에서 해당 값을 인코딩해야 하는 경우 `DateTime` nullable`DateTime?`값 () `bool` 을 사용하거나 날짜가 있는지 나타내는 별도의 값을 인코딩합니다.

이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228)를 참조하십시오.

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"사전" 컴파일 환경에서 메시지 팩 지원

.NET 클라이언트 및 서버에서 사용하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 라이브러리는 코드 생성을 사용하여 직렬화를 최적화합니다. 따라서 Xamarin iOS 또는 Unity와 같이 "사전" 컴파일을 사용하는 환경에서는 기본적으로 지원되지 않습니다. 직렬화/디직렬화 코드를 "미리 생성"하여 이러한 환경에서 MessagePack을 사용할 수 있습니다. 자세한 내용은 [MessagePack-CSharp 문서를](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)참조하십시오. serializer를 미리 생성한 후에는 다음으로 전달된 구성 대리자를 사용하여 serializer를 등록할 `AddMessagePackProtocol`수 있습니다.

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>메시지 팩에서 형식 검사가 더 엄격합니다.

JSON 허브 프로토콜은 직렬화 중에 형식 변환을 수행합니다. 예를 들어 들어오는 개체에 숫자 ()`{ foo: 42 }`하지만 .NET 클래스의 속성이 형식인 `string`경우 값이 변환됩니다. 그러나 MessagePack이 변환을 수행 하지 않습니다 및 서버 측 로그 (및 콘솔에서 볼 수 있는 예외)를 throw 합니다.

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937)를 참조하십시오.

## <a name="related-resources"></a>관련 참고 자료

* [시작](xref:tutorials/signalr)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [JavaScript 클라이언트](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 문서에서는 독자가 [시작하기](xref:tutorials/signalr)에서 다루는 주제에 익숙하다고 가정합니다.

## <a name="what-is-messagepack"></a>메시지 팩이란 무엇입니까?

[MessagePack은](https://msgpack.org/index.html) 빠르고 컴팩트한 이진 직렬화 형식입니다. [JSON](https://www.json.org/)에 비해 작은 메시지를 생성하기 때문에 성능과 대역폭이 문제가 될 때 유용합니다. 바이트가 MessagePack 파서를 통과하지 않는 한 네트워크 추적 및 로그를 볼 때 이진 메시지를 읽을 수 없습니다. SignalR는 MessagePack 형식에 대한 기본 제공 지원을 제공하며 클라이언트와 서버가 사용할 API를 제공합니다.

## <a name="configure-messagepack-on-the-server"></a>서버에서 메시지 팩 구성

서버에서 MessagePack 허브 프로토콜을 사용하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 앱에 패키지를 설치합니다. 메서드에서 `Startup.ConfigureServices` 호출에 `AddMessagePackProtocol` `AddSignalR` 추가하여 서버에서 MessagePack 지원을 사용하도록 설정합니다.

> [!NOTE]
> JSON은 기본적으로 활성화되어 있습니다. 메시지 팩을 추가하면 JSON 및 MessagePack 클라이언트를 모두 지원할 수 있습니다.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack이 데이터를 포맷하는 방법을 `AddMessagePackProtocol` 사용자 지정하려면 구성 옵션을 위해 대리자를 사용합니다. 이 대리자에서 `FormatterResolvers` 속성을 사용하여 MessagePack 직렬화 옵션을 구성할 수 있습니다. 확인자의 작동 방식에 대한 자세한 내용은 [MessagePack-CSharp의 MessagePack](https://github.com/neuecc/MessagePack-CSharp)라이브러리를 참조하십시오. 특성을 직렬화하려는 개체에서 사용하여 처리 방법을 정의할 수 있습니다.

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
> [CVE-2020-5234를](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 검토하고 권장 패치를 적용하는 것이 좋습니다. 예를 들어 `MessagePackSecurity.Active` 정적 속성을 `MessagePackSecurity.UntrustedData`로 설정합니다. `MessagePackSecurity.Active` [1.9.x 버전의 MessagePack을](https://www.nuget.org/packages/MessagePack/1.9.3)수동으로 설치해야 하는 경우를 설정합니다. 1.9.x를 설치하면 `MessagePack` 버전에서 SignalR 사용하는 업그레이드가 사용됩니다. 로 설정되지 않은 경우 `MessagePackSecurity.Active` 악의적인 클라이언트로 인해 서비스 거부가 발생할 수 있습니다. `MessagePackSecurity.UntrustedData` 다음 `MessagePackSecurity.Active` `Program.Main`코드에 표시된 대로 을 설정합니다.

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>클라이언트에서 메시지 팩 구성

> [!NOTE]
> JSON은 지원되는 클라이언트에 대해 기본적으로 활성화됩니다. 클라이언트는 단일 프로토콜만 지원할 수 있습니다. MessagePack 지원을 추가하면 이전에 구성된 프로토콜이 대체됩니다.

### <a name="net-client"></a>.NET 클라이언트

.NET 클라이언트에서 MessagePack을 사용하려면 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 패키지를 `AddMessagePackProtocol` 설치하고 `HubConnectionBuilder`을 호출합니다.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> 이 `AddMessagePackProtocol` 호출은 서버와 마찬가지로 옵션을 구성하기 위해 대리자를 사용합니다.

### <a name="javascript-client"></a>JavaScript 클라이언트

자바 스크립트 클라이언트에 대한 메시지 팩 [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) 지원은 npm 패키지에 의해 제공됩니다. 명령 셸에서 다음 명령을 실행 하여 패키지를 설치합니다.

```bash
npm install @aspnet/signalr-protocol-msgpack
```

npm 패키지를 설치한 후 JavaScript 모듈 로더를 통해 직접 사용하거나 다음 파일을 참조하여 브라우저로 가져올 수 있습니다.

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

브라우저에서 `msgpack5` 라이브러리도 참조해야 합니다. `<script>` 태그를 사용하여 참조를 만듭니다. 라이브러리는 *node_modules\msgpack5\dist\msgpack5.js에서*찾을 수 있습니다.

> [!NOTE]
> `<script>` 요소를 사용할 때는 순서가 중요합니다. *시그널러 프로토콜-msgpack.jsmsgpack5.js* 전에 참조 하는 경우, MessagePack에 연결 하려고 할 때 오류가 발생 합니다. *msgpack5.js* *signalr.js* *시그널러-프로토콜-msgpack.js 전에도 시그널러.js가*필요합니다.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

서버에 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` `HubConnectionBuilder` 연결할 때 MessagePack 프로토콜을 사용하도록 클라이언트를 구성합니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 현재 JavaScript 클라이언트의 MessagePack 프로토콜에 대한 구성 옵션이 없습니다.

## <a name="messagepack-quirks"></a>메시지 팩 단점

MessagePack 허브 프로토콜을 사용할 때 주의해야 할 몇 가지 문제가 있습니다.

### <a name="messagepack-is-case-sensitive"></a>메시지 팩은 대/소문자를 구분합니다.

MessagePack 프로토콜은 대/소문자를 구분합니다. 예를 들어 다음 C# 클래스를 고려하십시오.

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

JavaScript 클라이언트에서 보낼 때 대/소문자가 C# 클래스와 정확히 일치해야 하므로 속성 이름을 사용해야 `PascalCased` 합니다. 다음은 그 예입니다.

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

이름을 `camelCased` 사용하면 C# 클래스에 제대로 바인딩되지 않습니다. 특성을 사용하여 MessagePack `Key` 속성에 대해 다른 이름을 지정하여 이 방법을 해결할 수 있습니다. 자세한 내용은 [MessagePack-CSharp 문서를](https://github.com/neuecc/MessagePack-CSharp#object-serialization)참조하십시오.

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind화/역직렬화 할 때 종류가 유지되지 않습니다.

MessagePack 프로토콜은 `Kind` `DateTime`의 값을 인코딩하는 방법을 제공하지 않습니다. 따라서 날짜를 역직렬화할 때 MessagePack 허브 프로토콜은 들어오는 날짜가 UTC 형식으로 가정합니다. 현지 시간으로 `DateTime` 값을 작업하는 경우 값을 보내기 전에 UTC로 변환하는 것이 좋습니다. UTC를 받을 때 현지 시간으로 변환합니다.

이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2632SignalR](https://github.com/aspnet/SignalR/issues/2632)를 참조하십시오.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue 자바 스크립트의 메시지 팩에 의해 지원 되지 않습니다.

자바 스크립트 클라이언트에서 사용하는 [msgpack5](https://github.com/mcollina/msgpack5) 라이브러리는 `timestamp96` MessagePack의 형식을 지원하지 않습니다. SignalR 이 형식은 매우 큰 날짜 값을 인코딩하는 데 사용됩니다(과거의 초기 또는 미래에는 매우 먼 날짜). `DateTime.MinValue` 값은 `January 1, 0001` `timestamp96` 값으로 인코딩되어야 합니다. 따라서 JavaScript `DateTime.MinValue` 클라이언트로 보내는 것은 지원되지 않습니다. `DateTime.MinValue` JavaScript 클라이언트에서 수신되면 다음과 같은 오류가 발생합니다.

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

`DateTime.MinValue` 일반적으로 "누락된" 또는 `null` 값을 인코딩하는 데 사용됩니다. MessagePack에서 해당 값을 인코딩해야 하는 경우 `DateTime` nullable`DateTime?`값 () `bool` 을 사용하거나 날짜가 있는지 나타내는 별도의 값을 인코딩합니다.

이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228)를 참조하십시오.

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"사전" 컴파일 환경에서 메시지 팩 지원

.NET 클라이언트 및 서버에서 사용하는 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 라이브러리는 코드 생성을 사용하여 직렬화를 최적화합니다. 따라서 Xamarin iOS 또는 Unity와 같이 "사전" 컴파일을 사용하는 환경에서는 기본적으로 지원되지 않습니다. 직렬화/디직렬화 코드를 "미리 생성"하여 이러한 환경에서 MessagePack을 사용할 수 있습니다. 자세한 내용은 [MessagePack-CSharp 문서를](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)참조하십시오. serializer를 미리 생성한 후에는 다음으로 전달된 구성 대리자를 사용하여 serializer를 등록할 `AddMessagePackProtocol`수 있습니다.

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>메시지 팩에서 형식 검사가 더 엄격합니다.

JSON 허브 프로토콜은 직렬화 중에 형식 변환을 수행합니다. 예를 들어 들어오는 개체에 숫자 ()`{ foo: 42 }`하지만 .NET 클래스의 속성이 형식인 `string`경우 값이 변환됩니다. 그러나 MessagePack이 변환을 수행 하지 않습니다 및 서버 측 로그 (및 콘솔에서 볼 수 있는 예외)를 throw 합니다.

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

이 제한사항에 대한 자세한 내용은 GitHub 문제 [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937)를 참조하십시오.

## <a name="related-resources"></a>관련 참고 자료

* [시작](xref:tutorials/signalr)
* [.NET 클라이언트](xref:signalr/dotnet-client)
* [JavaScript 클라이언트](xref:signalr/javascript-client)

::: moniker-end
