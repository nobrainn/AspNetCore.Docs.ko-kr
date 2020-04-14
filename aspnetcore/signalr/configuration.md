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
# <a name="aspnet-core-signalr-configuration"></a>ASP.NET Core SignalR 구성

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>JSON/메시지팩 직렬화 옵션

ASP.NET 코어 시그널R는 메시지 인코딩을 위한 두 가지 프로토콜을 지원합니다: [JSON](https://www.json.org/) 및 [MessagePack.](https://msgpack.org/index.html) 각 프로토콜에는 직렬화 구성 옵션이 있습니다.

JSON [직렬화AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 방법을 사용 하 여 서버에서 구성할 수 있습니다. `AddJsonProtocol`[에서 AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 후 `Startup.ConfigureServices`추가할 수 있습니다. 메서드는 `AddJsonProtocol` 개체를 받는 `options` 대리자를 사용 합니다. 해당 개체의 [Payload SerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 속성은 인수의 직렬화를 구성하고 값을 반환하는 데 사용할 수 있는 개체입니다. 자세한 내용은 [System.Text.Json 문서를](/dotnet/api/system.text.json)참조하십시오.

예를 들어, 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경하지 않도록 serializer를 구성하려면 다음 코드를 `Startup.ConfigureServices`사용합니다.

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

.NET 클라이언트에서 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다. 확장 `Microsoft.Extensions.DependencyInjection` 메서드를 해결하려면 네임스페이스를 가져와야 합니다.

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
> 현재 는 JavaScript 클라이언트에서 JSON 직렬화를 구성할 수 없습니다.

### <a name="switch-to-newtonsoftjson"></a>뉴턴소프트.Json으로 전환

에서 `System.Text.Json`지원되지 `Newtonsoft.Json` 않는 기능이 필요한 경우 [[ 뉴턴소프트.Json](xref:migration/22-to-30#switch-to-newtonsoftjson)로 전환 보기]

### <a name="messagepack-serialization-options"></a>메시지팩 직렬화 옵션

메시지팩 일련화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대한 대리자를 제공하여 구성할 수 있습니다. 자세한 내용은 [SignalR의 메시지 팩을](xref:signalr/messagepackhubprotocol) 참조하십시오.

> [!NOTE]
> 현재 는 JavaScript 클라이언트에서 MessagePack 직렬화를 구성할 수 없습니다.

## <a name="configure-server-options"></a>서버 옵션 구성

다음 표는 SignalR 허브를 구성하는 옵션에 대해 설명합니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30초 | 서버는 이 간격으로 클라이언트가 연결이 끊어진 것으로 간주합니다(유지-활성 포함)가 수신되지 않은 경우 클라이언트가 연결이 끊어진 것으로 간주합니다. 클라이언트가 실제로 연결이 끊어진 것으로 표시되는 데 이 시간 초과 간격보다 더 오래 걸릴 수 있습니다. 권장 값은 값의 `KeepAliveInterval` 두 배입니다.|
| `HandshakeTimeout` | 15초 | 클라이언트가 이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다. 이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오. |
| `KeepAliveInterval` | 15초 | 서버가 이 간격 내에 메시지를 보내지 않은 경우 연결을 열어 두기 위해 ping 메시지가 자동으로 전송됩니다. `KeepAliveInterval`변경하면 클라이언트의 `ServerTimeout` / `serverTimeoutInMilliseconds` 설정을 변경합니다. `ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 값의 `KeepAliveInterval` 두 배입니다.  |
| `SupportedProtocols` | 설치된 모든 프로토콜 | 이 허브에서 지원하는 프로토콜입니다. 기본적으로 서버에 등록된 모든 프로토콜은 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대한 특정 프로토콜을 비활성화할 수 있습니다. |
| `EnableDetailedErrors` | `false` | 자세한 `true`예외 메시지가 Hub 메서드에서 예외를 throw하면 클라이언트에 반환됩니다. 기본값은 `false`은 예외 메시지에 중요한 정보가 포함될 수 있으므로 입니다. |
| `StreamBufferCapacity` | `10` | 클라이언트 업로드 스트림에 대해 버퍼링할 수 있는 최대 항목 수입니다. 이 제한에 도달하면 서버가 스트림 항목을 처리할 때까지 호출 처리가 차단됩니다.|
| `MaximumReceiveMessageSize` | 32KB | 들어오는 단일 허브 메시지의 최대 크기입니다. |

옵션에서 호출에 대한 옵션 대리자를 제공하여 모든 `AddSignalR` 허브에 대해 옵션을 구성할 수 있습니다. `Startup.ConfigureServices`

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

단일 허브에 대한 옵션은 제공된 전역 `AddSignalR` 옵션을 재정의하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>다음을 사용하여 구성할 수 있습니다.

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>고급 HTTP 구성 옵션

전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하는 데 사용합니다. 이러한 옵션은 [에서 MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 대리자를 전달하여 `Startup.Configure`구성됩니다.

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

다음 표는 코어 SignalR의 고급 http 옵션을 구성하기 ASP.NET 옵션에 대해 설명합니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32KB | 배압을 적용하기 전에 서버가 버퍼링하는 클라이언트에서 받은 최대 바이트 수입니다. 이 값을 늘리면 서버가 역압을 적용하지 않고 더 큰 메시지를 더 빨리 받을 수 있지만 메모리 소비가 증가할 수 있습니다. |
| `AuthorizationData` | Hub 클래스에 `Authorize` 적용된 특성에서 데이터가 자동으로 수집됩니다. | 클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체 목록입니다. |
| `TransportMaxBufferSize` | 32KB | 배압을 관찰하기 전에 서버가 버퍼링하는 앱에서 전송되는 최대 바이트 수입니다. 이 값을 늘리면 서버가 배압을 기다리지 않고 더 큰 메시지를 더 빨리 버퍼링할 수 있지만 메모리 소비가 증가할 수 있습니다. |
| `Transports` | 모든 전송이 활성화됩니다. | 비트는 클라이언트가 `HttpTransportType` 연결하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 열거형 플래그를 지정합니다. |
| `LongPolling` | 아래를 참조하십시오. | 긴 폴링 전송과 관련된 추가 옵션입니다. |
| `WebSockets` | 아래를 참조하십시오. | WebSockets 전송과 관련된 추가 옵션입니다. |
| `MinimumProtocolVersion` | 0 | 협상 프로토콜의 최소 버전을 지정합니다. 클라이언트를 최신 버전으로 제한하는 데 사용됩니다. |

Long Polling 전송에는 속성을 사용하여 구성할 `LongPolling` 수 있는 추가 옵션이 있습니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90초 | 단일 폴링 요청을 종료하기 전에 서버가 메시지가 클라이언트에 전송될 때까지 기다리는 최대 시간입니다. 이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 발급합니다. |

WebSocket 전송에는 `WebSockets` 속성을 사용하여 구성할 수 있는 추가 옵션이 있습니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5초 | 서버가 닫히면 클라이언트가 이 시간 간격 내에 닫히지 않으면 연결이 종료됩니다. |
| `SubProtocolSelector` | `null` | 헤더를 사용자 지정 값으로 `Sec-WebSocket-Protocol` 설정하는 데 사용할 수 있는 대리자입니다. 대리자는 클라이언트가 요청한 값을 입력으로 수신하고 원하는 값을 반환해야 합니다. |

## <a name="configure-client-options"></a>클라이언트 옵션 구성

클라이언트 옵션은 `HubConnectionBuilder` 유형(.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다. Java 클라이언트에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 빌더 구성 옵션과 그 자체가 `HubConnection` 포함되어 있습니다.

### <a name="configure-logging"></a>로깅 구성

로깅은 `ConfigureLogging` 메서드를 사용하여 .NET 클라이언트에서 구성됩니다. 로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다. 자세한 내용은 [ASP.NET 핵심 로그인](xref:fundamentals/logging/index) 설명서를 참조하십시오.

> [!NOTE]
> 로깅 공급자를 등록하려면 필요한 패키지를 설치해야 합니다. 전체 목록은 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조하십시오.

예를 들어 콘솔 로깅을 `Microsoft.Extensions.Logging.Console` 사용하려면 NuGet 패키지를 설치합니다. 확장 `AddConsole` 메서드를 호출합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript 클라이언트에서 비슷한 `configureLogging` 메서드가 존재합니다. 생성할 `LogLevel` 로그 메시지의 최소 수준을 나타내는 값을 제공합니다. 로그는 브라우저 콘솔 창에 기록됩니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

`LogLevel` 값 대신 로그 수준 이름을 `string` 나타내는 값을 제공할 수도 있습니다. 이 기능은 상수에 액세스할 수 없는 환경에서 SignalR 로깅을 `LogLevel` 구성할 때 유용합니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

다음 표에는 사용 가능한 로그 수준이 나열되어 있습니다. 기록할 최소 `configureLogging` 로그 **minimum** 수준을 설정하기 위해 제공하는 값입니다. 이 수준에서 기록된 메시지 **또는 테이블에 나열된 수준이**기록됩니다.

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**또는**`information` | `LogLevel.Information` |
| `warn`**또는**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> 로깅을 완전히 `signalR.LogLevel.None` 사용하지 `configureLogging` 않으려면 메서드를 지정합니다.

로깅에 대한 자세한 내용은 [SignalR 진단 문서를](xref:signalr/diagnostics)참조하십시오.

SignalR Java 클라이언트는 로깅을 위해 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용합니다. 라이브러리의 사용자가 특정 로깅 종속성을 가져와 고유한 특정 로깅 구현을 선택할 수 있는 고급 로깅 API입니다. 다음 코드 조각은 SignalR `java.util.logging` Java 클라이언트에서 사용하는 방법을 보여 주며 있습니다.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

종속성에 로깅을 구성하지 않은 경우 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음 로거를 로드합니다.

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

이 무시 해도 안전 하 게 무시할 수 있습니다.

### <a name="configure-allowed-transports"></a>허용된 전송 구성

SignalR에서 사용하는 전송은 `WithUrl` 호출(자바스크립트)에서`withUrl` 구성할 수 있습니다. 비트-OR값은 클라이언트가 `HttpTransportType` 지정된 전송만 사용하도록 제한하는 데 사용할 수 있습니다. 모든 전송은 기본적으로 활성화되어 있습니다.

예를 들어 서버 전송 이벤트 전송을 사용하지 않도록 설정하지만 WebSocket 및 긴 폴링 연결을 허용하려면 다음을 수행합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript 클라이언트에서 전송은 `transport` `withUrl`다음에 제공된 옵션 개체에 필드를 설정하여 구성됩니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

이 버전의 Java 클라이언트 웹소켓은 사용 가능한 유일한 전송입니다.

Java 클라이언트에서 전송은 `withTransport` `HttpHubConnectionBuilder`의 메서드를 사용하여 선택됩니다. Java 클라이언트는 기본적으로 WebSockets 전송을 사용합니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalR Java 클라이언트는 아직 전송 대체를 지원하지 않습니다.

### <a name="configure-bearer-authentication"></a>베어러 인증 구성

SignalR 요청과 함께 인증 데이터를 `AccessTokenProvider` 제공하려면`accessTokenFactory` 옵션(JavaScript)을 사용하여 원하는 액세스 토큰을 반환하는 함수를 지정합니다. .NET 클라이언트에서 이 액세스 토큰은 HTTP "보유자 인증" 토큰으로 `Authorization` 전달됩니다(유형이 `Bearer`있는 헤더 사용). JavaScript 클라이언트에서 액세스 토큰은 브라우저 API가 헤더를 적용하는 기능을 제한하는 경우(특히 서버 전송 이벤트 및 WebSockets 요청)를 **제외하고** 베어러 토큰으로 사용됩니다. 이러한 경우 액세스 토큰은 쿼리 문자열 값으로 `access_token`제공됩니다.

.NET 클라이언트에서 `AccessTokenProvider` 옵션은 다음 에서 `WithUrl`옵션 대리자를 사용하여 지정할 수 있습니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript 클라이언트에서 액세스 토큰은 다음 의 `accessTokenFactory` `withUrl`옵션 개체에 필드를 설정하여 구성됩니다.

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

SignalR Java 클라이언트에서 [HttpHubConnectionBuilder에](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)액세스 토큰 팩터리를 제공하여 인증에 사용할 베어러 토큰을 구성할 수 있습니다. [와 사용AccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) [RxJava](https://github.com/ReactiveX/RxJava) [단일\<문자열>. ](https://reactivex.io/documentation/single.html) [Single.defer를](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)호출하면 클라이언트에 대한 액세스 토큰을 생성하는 논리를 작성할 수 있습니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>시간 시간 및 유지 옵션 구성

시간 시간 및 유지 동작을 구성하기 위한 추가 `HubConnection` 옵션은 개체 자체에서 사용할 수 있습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30초(30,000밀리초) | 서버 활동에 대한 시간 초과입니다. 서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `Closed` 것으로`onclose` 간주하고 JavaScript에서 이벤트를 트리거합니다. 이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다. 권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다. |
| `HandshakeTimeout` | 15초 | 초기 서버 핸드셰이크에 대한 시간 시간 입니다. 서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` `onclose` JavaScript에서 이벤트를 트리거합니다. 이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오. |
| `KeepAliveInterval` | 15초 | 클라이언트가 ping 메시지를 보내는 간격을 결정합니다. 클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다. 클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다. |

.NET 클라이언트에서 시간 지정 값은 `TimeSpan` 값으로 지정됩니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30초(30,000밀리초) | 서버 활동에 대한 시간 초과입니다. 서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onclose` 것으로 간주하고 이벤트를 트리거합니다. 이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다. 권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다. |
| `keepAliveIntervalInMilliseconds` | 15초(15,000밀리초) | 클라이언트가 ping 메시지를 보내는 간격을 결정합니다. 클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다. 클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다. |

# <a name="java"></a>[Java](#tab/java)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30초(30,000밀리초) | 서버 활동에 대한 시간 초과입니다. 서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onClose` 것으로 간주하고 이벤트를 트리거합니다. 이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다. 권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다. |
| `withHandshakeResponseTimeout` | 15초 | 초기 서버 핸드셰이크에 대한 시간 시간 입니다. 서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `onClose` 이벤트를 트리거합니다. 이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오. |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15초(15,000밀리초) | 클라이언트가 ping 메시지를 보내는 간격을 결정합니다. 클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다. 클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다. |

---

### <a name="configure-additional-options"></a>추가 구성 옵션

`WithUrl` 추가 옵션은 Java 클라이언트의`withUrl` 다양한 구성 API에 `HubConnectionBuilder` 있는 (JavaScript) 메서드에서 구성할 `HttpHubConnectionBuilder` 수 있습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 옵션 |  기본값 | Description |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다. |
| `SkipNegotiation` | `false` | 협상 단계를 `true` 건너뛰도록 설정합니다. **WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.** Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다. |
| `ClientCertificates` | Empty | 요청을 인증하기 위해 보낼 TLS 인증서 컬렉션입니다. |
| `Cookies` | Empty | 모든 HTTP 요청과 함께 보낼 HTTP 쿠키 모음입니다. |
| `Credentials` | Empty | 모든 HTTP 요청과 함께 보낼 자격 증명입니다. |
| `CloseTimeout` | 5초 | 웹 소켓만. 서버가 닫기 요청을 승인할 때까지 클라이언트가 닫은 후 대기하는 최대 시간입니다. 이 시간 내에 서버가 닫기를 승인하지 않으면 클라이언트가 연결 해제됩니다. |
| `Headers` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |
| `HttpMessageHandlerFactory` | `null` | HTTP 요청을 보내는 데 사용되는 대리자를 구성하거나 교체하는 데 사용할 수 있는 `HttpMessageHandler` 대리자입니다. WebSocket 연결에는 사용되지 않습니다. 이 대리자는 null이 아닌 값을 반환해야 하며 기본값을 매개 변수로 받습니다. 기본값의 설정을 수정하여 반환하거나 새 `HttpMessageHandler` 인스턴스를 반환합니다. **처리기를 교체할 때 제공된 처리기에서 유지하려는 설정을 복사해야 하며, 그렇지 않으면 구성된 옵션(예: 쿠키 및 헤더)이 새 처리기에 적용되지 않습니다.** |
| `Proxy` | `null` | HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다. |
| `UseDefaultCredentials` | `false` | HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 보내도록 이 부울을 설정합니다. 이렇게 하면 Windows 인증을 사용할 수 있습니다. |
| `WebSocketConfiguration` | `null` | 추가 WebSocket 옵션을 구성하는 데 사용할 수 있는 대리자입니다. 옵션을 구성하는 데 사용할 수 있는 [ClientWebSocketOptions의](/dotnet/api/system.net.websockets.clientwebsocketoptions) 인스턴스를 받습니다. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 자바 스크립트 옵션 | 기본값 | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다. |
| `skipNegotiation` | `false` | 협상 단계를 `true` 건너뛰도록 설정합니다. **WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.** Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다. |

# <a name="java"></a>[Java](#tab/java)

| 자바 옵션 | 기본값 | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다. |
| `shouldSkipNegotiate` | `false` | 협상 단계를 `true` 건너뛰도록 설정합니다. **WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.** Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다. |
| `withHeader` `withHeaders` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |

---

.NET 클라이언트에서 이러한 옵션은 다음에 제공된 옵션 대리자에서 수정할 `WithUrl`수 있습니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

자바 스크립트 클라이언트에서 이러한 옵션은 `withUrl`다음에 제공되는 자바 스크립트 개체에서 제공 할 수 있습니다 .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java 클라이언트에서 이러한 옵션은 `HttpHubConnectionBuilder` 반환된 메서드로 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>추가 자료

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>JSON/메시지팩 직렬화 옵션

ASP.NET 코어 시그널R는 메시지 인코딩을 위한 두 가지 프로토콜을 지원합니다: [JSON](https://www.json.org/) 및 [MessagePack.](https://msgpack.org/index.html) 각 프로토콜에는 직렬화 구성 옵션이 있습니다.

JSON [직렬화AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 방법을 사용 하 여 서버에서 구성할 수 있습니다. `AddJsonProtocol`[에서 AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 후 `Startup.ConfigureServices`추가할 수 있습니다. 메서드는 `AddJsonProtocol` 개체를 받는 `options` 대리자를 사용 합니다. 해당 개체의 [Payload SerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 속성은 인수의 직렬화를 구성하고 값을 반환하는 데 사용할 수 있는 개체입니다. 자세한 내용은 [System.Text.Json 문서를](/dotnet/api/system.text.json)참조하십시오.

예를 들어, 기본 "camelCase" 이름 대신 속성 이름의 대/소문자를 변경하지 않도록 serializer를 구성하려면 다음 코드를 `Startup.ConfigureServices`사용합니다.

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

.NET 클라이언트에서 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다. 확장 `Microsoft.Extensions.DependencyInjection` 메서드를 해결하려면 네임스페이스를 가져와야 합니다.

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
> 현재 는 JavaScript 클라이언트에서 JSON 직렬화를 구성할 수 없습니다.

### <a name="switch-to-newtonsoftjson"></a>뉴턴소프트.Json으로 전환

에서 `System.Text.Json`지원되지 `Newtonsoft.Json` 않는 기능이 필요한 경우 [[ 뉴턴소프트.Json](xref:migration/22-to-30#switch-to-newtonsoftjson)로 전환 보기]

### <a name="messagepack-serialization-options"></a>메시지팩 직렬화 옵션

메시지팩 일련화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대한 대리자를 제공하여 구성할 수 있습니다. 자세한 내용은 [SignalR의 메시지 팩을](xref:signalr/messagepackhubprotocol) 참조하십시오.

> [!NOTE]
> 현재 는 JavaScript 클라이언트에서 MessagePack 직렬화를 구성할 수 없습니다.

## <a name="configure-server-options"></a>서버 옵션 구성

다음 표는 SignalR 허브를 구성하는 옵션에 대해 설명합니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30초 | 서버는 이 간격으로 클라이언트가 연결이 끊어진 것으로 간주합니다(유지-활성 포함)가 수신되지 않은 경우 클라이언트가 연결이 끊어진 것으로 간주합니다. 클라이언트가 실제로 연결이 끊어진 것으로 표시되는 데 이 시간 초과 간격보다 더 오래 걸릴 수 있습니다. 권장 값은 값의 `KeepAliveInterval` 두 배입니다.|
| `HandshakeTimeout` | 15초 | 클라이언트가 이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다. 이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오. |
| `KeepAliveInterval` | 15초 | 서버가 이 간격 내에 메시지를 보내지 않은 경우 연결을 열어 두기 위해 ping 메시지가 자동으로 전송됩니다. `KeepAliveInterval`변경하면 클라이언트의 `ServerTimeout` / `serverTimeoutInMilliseconds` 설정을 변경합니다. `ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 값의 `KeepAliveInterval` 두 배입니다.  |
| `SupportedProtocols` | 설치된 모든 프로토콜 | 이 허브에서 지원하는 프로토콜입니다. 기본적으로 서버에 등록된 모든 프로토콜은 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대한 특정 프로토콜을 비활성화할 수 있습니다. |
| `EnableDetailedErrors` | `false` | 자세한 `true`예외 메시지가 Hub 메서드에서 예외를 throw하면 클라이언트에 반환됩니다. 기본값은 `false`은 예외 메시지에 중요한 정보가 포함될 수 있으므로 입니다. |
| `StreamBufferCapacity` | `10` | 클라이언트 업로드 스트림에 대해 버퍼링할 수 있는 최대 항목 수입니다. 이 제한에 도달하면 서버가 스트림 항목을 처리할 때까지 호출 처리가 차단됩니다.|
| `MaximumReceiveMessageSize` | 32KB | 들어오는 단일 허브 메시지의 최대 크기입니다. |

옵션에서 호출에 대한 옵션 대리자를 제공하여 모든 `AddSignalR` 허브에 대해 옵션을 구성할 수 있습니다. `Startup.ConfigureServices`

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

단일 허브에 대한 옵션은 제공된 전역 `AddSignalR` 옵션을 재정의하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>다음을 사용하여 구성할 수 있습니다.

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>고급 HTTP 구성 옵션

전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하는 데 사용합니다. 이러한 옵션은 [에서 MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) 대리자를 전달하여 `Startup.Configure`구성됩니다.

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

다음 표는 코어 SignalR의 고급 http 옵션을 구성하기 ASP.NET 옵션에 대해 설명합니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32KB | 배압을 적용하기 전에 서버가 버퍼링하는 클라이언트에서 받은 최대 바이트 수입니다. 이 값을 늘리면 서버가 역압을 적용하지 않고 더 큰 메시지를 더 빨리 받을 수 있지만 메모리 소비가 증가할 수 있습니다. |
| `AuthorizationData` | Hub 클래스에 `Authorize` 적용된 특성에서 데이터가 자동으로 수집됩니다. | 클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체 목록입니다. |
| `TransportMaxBufferSize` | 32KB | 배압을 관찰하기 전에 서버가 버퍼링하는 앱에서 전송되는 최대 바이트 수입니다. 이 값을 늘리면 서버가 배압을 기다리지 않고 더 큰 메시지를 더 빨리 버퍼링할 수 있지만 메모리 소비가 증가할 수 있습니다. |
| `Transports` | 모든 전송이 활성화됩니다. | 비트는 클라이언트가 `HttpTransportType` 연결하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 열거형 플래그를 지정합니다. |
| `LongPolling` | 아래를 참조하십시오. | 긴 폴링 전송과 관련된 추가 옵션입니다. |
| `WebSockets` | 아래를 참조하십시오. | WebSockets 전송과 관련된 추가 옵션입니다. |

Long Polling 전송에는 속성을 사용하여 구성할 `LongPolling` 수 있는 추가 옵션이 있습니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90초 | 단일 폴링 요청을 종료하기 전에 서버가 메시지가 클라이언트에 전송될 때까지 기다리는 최대 시간입니다. 이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 발급합니다. |

WebSocket 전송에는 `WebSockets` 속성을 사용하여 구성할 수 있는 추가 옵션이 있습니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5초 | 서버가 닫히면 클라이언트가 이 시간 간격 내에 닫히지 않으면 연결이 종료됩니다. |
| `SubProtocolSelector` | `null` | 헤더를 사용자 지정 값으로 `Sec-WebSocket-Protocol` 설정하는 데 사용할 수 있는 대리자입니다. 대리자는 클라이언트가 요청한 값을 입력으로 수신하고 원하는 값을 반환해야 합니다. |

## <a name="configure-client-options"></a>클라이언트 옵션 구성

클라이언트 옵션은 `HubConnectionBuilder` 유형(.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다. Java 클라이언트에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 빌더 구성 옵션과 그 자체가 `HubConnection` 포함되어 있습니다.

### <a name="configure-logging"></a>로깅 구성

로깅은 `ConfigureLogging` 메서드를 사용하여 .NET 클라이언트에서 구성됩니다. 로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다. 자세한 내용은 [ASP.NET 핵심 로그인](xref:fundamentals/logging/index) 설명서를 참조하십시오.

> [!NOTE]
> 로깅 공급자를 등록하려면 필요한 패키지를 설치해야 합니다. 전체 목록은 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조하십시오.

예를 들어 콘솔 로깅을 `Microsoft.Extensions.Logging.Console` 사용하려면 NuGet 패키지를 설치합니다. 확장 `AddConsole` 메서드를 호출합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript 클라이언트에서 비슷한 `configureLogging` 메서드가 존재합니다. 생성할 `LogLevel` 로그 메시지의 최소 수준을 나타내는 값을 제공합니다. 로그는 브라우저 콘솔 창에 기록됩니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

`LogLevel` 값 대신 로그 수준 이름을 `string` 나타내는 값을 제공할 수도 있습니다. 이 기능은 상수에 액세스할 수 없는 환경에서 SignalR 로깅을 `LogLevel` 구성할 때 유용합니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

다음 표에는 사용 가능한 로그 수준이 나열되어 있습니다. 기록할 최소 `configureLogging` 로그 **minimum** 수준을 설정하기 위해 제공하는 값입니다. 이 수준에서 기록된 메시지 **또는 테이블에 나열된 수준이**기록됩니다.

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**또는**`information` | `LogLevel.Information` |
| `warn`**또는**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> 로깅을 완전히 `signalR.LogLevel.None` 사용하지 `configureLogging` 않으려면 메서드를 지정합니다.

로깅에 대한 자세한 내용은 [SignalR 진단 문서를](xref:signalr/diagnostics)참조하십시오.

SignalR Java 클라이언트는 로깅을 위해 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용합니다. 라이브러리의 사용자가 특정 로깅 종속성을 가져와 고유한 특정 로깅 구현을 선택할 수 있는 고급 로깅 API입니다. 다음 코드 조각은 SignalR `java.util.logging` Java 클라이언트에서 사용하는 방법을 보여 주며 있습니다.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

종속성에 로깅을 구성하지 않은 경우 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음 로거를 로드합니다.

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

이 무시 해도 안전 하 게 무시할 수 있습니다.

### <a name="configure-allowed-transports"></a>허용된 전송 구성

SignalR에서 사용하는 전송은 `WithUrl` 호출(자바스크립트)에서`withUrl` 구성할 수 있습니다. 비트-OR값은 클라이언트가 `HttpTransportType` 지정된 전송만 사용하도록 제한하는 데 사용할 수 있습니다. 모든 전송은 기본적으로 활성화되어 있습니다.

예를 들어 서버 전송 이벤트 전송을 사용하지 않도록 설정하지만 WebSocket 및 긴 폴링 연결을 허용하려면 다음을 수행합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript 클라이언트에서 전송은 `transport` `withUrl`다음에 제공된 옵션 개체에 필드를 설정하여 구성됩니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

이 버전의 Java 클라이언트 웹소켓은 사용 가능한 유일한 전송입니다.

Java 클라이언트에서 전송은 `withTransport` `HttpHubConnectionBuilder`의 메서드를 사용하여 선택됩니다. Java 클라이언트는 기본적으로 WebSockets 전송을 사용합니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalR Java 클라이언트는 아직 전송 대체를 지원하지 않습니다.

### <a name="configure-bearer-authentication"></a>베어러 인증 구성

SignalR 요청과 함께 인증 데이터를 `AccessTokenProvider` 제공하려면`accessTokenFactory` 옵션(JavaScript)을 사용하여 원하는 액세스 토큰을 반환하는 함수를 지정합니다. .NET 클라이언트에서 이 액세스 토큰은 HTTP "보유자 인증" 토큰으로 `Authorization` 전달됩니다(유형이 `Bearer`있는 헤더 사용). JavaScript 클라이언트에서 액세스 토큰은 브라우저 API가 헤더를 적용하는 기능을 제한하는 경우(특히 서버 전송 이벤트 및 WebSockets 요청)를 **제외하고** 베어러 토큰으로 사용됩니다. 이러한 경우 액세스 토큰은 쿼리 문자열 값으로 `access_token`제공됩니다.

.NET 클라이언트에서 `AccessTokenProvider` 옵션은 다음 에서 `WithUrl`옵션 대리자를 사용하여 지정할 수 있습니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript 클라이언트에서 액세스 토큰은 다음 의 `accessTokenFactory` `withUrl`옵션 개체에 필드를 설정하여 구성됩니다.

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

SignalR Java 클라이언트에서 [HttpHubConnectionBuilder에](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)액세스 토큰 팩터리를 제공하여 인증에 사용할 베어러 토큰을 구성할 수 있습니다. [와 사용AccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) [RxJava](https://github.com/ReactiveX/RxJava) [단일\<문자열>. ](https://reactivex.io/documentation/single.html) [Single.defer를](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)호출하면 클라이언트에 대한 액세스 토큰을 생성하는 논리를 작성할 수 있습니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>시간 시간 및 유지 옵션 구성

시간 시간 및 유지 동작을 구성하기 위한 추가 `HubConnection` 옵션은 개체 자체에서 사용할 수 있습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30초(30,000밀리초) | 서버 활동에 대한 시간 초과입니다. 서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `Closed` 것으로`onclose` 간주하고 JavaScript에서 이벤트를 트리거합니다. 이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다. 권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다. |
| `HandshakeTimeout` | 15초 | 초기 서버 핸드셰이크에 대한 시간 시간 입니다. 서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` `onclose` JavaScript에서 이벤트를 트리거합니다. 이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오. |
| `KeepAliveInterval` | 15초 | 클라이언트가 ping 메시지를 보내는 간격을 결정합니다. 클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다. 클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다. |

.NET 클라이언트에서 시간 지정 값은 `TimeSpan` 값으로 지정됩니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30초(30,000밀리초) | 서버 활동에 대한 시간 초과입니다. 서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onclose` 것으로 간주하고 이벤트를 트리거합니다. 이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다. 권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다. |
| `keepAliveIntervalInMilliseconds` | 15초(15,000밀리초) | 클라이언트가 ping 메시지를 보내는 간격을 결정합니다. 클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다. 클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다. |

# <a name="java"></a>[Java](#tab/java)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30초(30,000밀리초) | 서버 활동에 대한 시간 초과입니다. 서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onClose` 것으로 간주하고 이벤트를 트리거합니다. 이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다. 권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다. |
| `withHandshakeResponseTimeout` | 15초 | 초기 서버 핸드셰이크에 대한 시간 시간 입니다. 서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `onClose` 이벤트를 트리거합니다. 이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오. |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15초(15,000밀리초) | 클라이언트가 ping 메시지를 보내는 간격을 결정합니다. 클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다. 클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다. |

---

### <a name="configure-additional-options"></a>추가 구성 옵션

`WithUrl` 추가 옵션은 Java 클라이언트의`withUrl` 다양한 구성 API에 `HubConnectionBuilder` 있는 (JavaScript) 메서드에서 구성할 `HttpHubConnectionBuilder` 수 있습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 옵션 |  기본값 | Description |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다. |
| `SkipNegotiation` | `false` | 협상 단계를 `true` 건너뛰도록 설정합니다. **WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.** Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다. |
| `ClientCertificates` | Empty | 요청을 인증하기 위해 보낼 TLS 인증서 컬렉션입니다. |
| `Cookies` | Empty | 모든 HTTP 요청과 함께 보낼 HTTP 쿠키 모음입니다. |
| `Credentials` | Empty | 모든 HTTP 요청과 함께 보낼 자격 증명입니다. |
| `CloseTimeout` | 5초 | 웹 소켓만. 서버가 닫기 요청을 승인할 때까지 클라이언트가 닫은 후 대기하는 최대 시간입니다. 이 시간 내에 서버가 닫기를 승인하지 않으면 클라이언트가 연결 해제됩니다. |
| `Headers` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |
| `HttpMessageHandlerFactory` | `null` | HTTP 요청을 보내는 데 사용되는 대리자를 구성하거나 교체하는 데 사용할 수 있는 `HttpMessageHandler` 대리자입니다. WebSocket 연결에는 사용되지 않습니다. 이 대리자는 null이 아닌 값을 반환해야 하며 기본값을 매개 변수로 받습니다. 기본값의 설정을 수정하여 반환하거나 새 `HttpMessageHandler` 인스턴스를 반환합니다. **처리기를 교체할 때 제공된 처리기에서 유지하려는 설정을 복사해야 하며, 그렇지 않으면 구성된 옵션(예: 쿠키 및 헤더)이 새 처리기에 적용되지 않습니다.** |
| `Proxy` | `null` | HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다. |
| `UseDefaultCredentials` | `false` | HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 보내도록 이 부울을 설정합니다. 이렇게 하면 Windows 인증을 사용할 수 있습니다. |
| `WebSocketConfiguration` | `null` | 추가 WebSocket 옵션을 구성하는 데 사용할 수 있는 대리자입니다. 옵션을 구성하는 데 사용할 수 있는 [ClientWebSocketOptions의](/dotnet/api/system.net.websockets.clientwebsocketoptions) 인스턴스를 받습니다. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 자바 스크립트 옵션 | 기본값 | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다. |
| `skipNegotiation` | `false` | 협상 단계를 `true` 건너뛰도록 설정합니다. **WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.** Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다. |

# <a name="java"></a>[Java](#tab/java)

| 자바 옵션 | 기본값 | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다. |
| `shouldSkipNegotiate` | `false` | 협상 단계를 `true` 건너뛰도록 설정합니다. **WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.** Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다. |
| `withHeader` `withHeaders` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |

---

.NET 클라이언트에서 이러한 옵션은 다음에 제공된 옵션 대리자에서 수정할 `WithUrl`수 있습니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

자바 스크립트 클라이언트에서 이러한 옵션은 `withUrl`다음에 제공되는 자바 스크립트 개체에서 제공 할 수 있습니다 .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java 클라이언트에서 이러한 옵션은 `HttpHubConnectionBuilder` 반환된 메서드로 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>추가 자료

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/메시지팩 직렬화 옵션

ASP.NET 코어 시그널R는 메시지 인코딩을 위한 두 가지 프로토콜을 지원합니다: [JSON](https://www.json.org/) 및 [MessagePack.](https://msgpack.org/index.html) 각 프로토콜에는 직렬화 구성 옵션이 있습니다.

JSON 직렬화는 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 `Startup.ConfigureServices` 수 있습니다., 메서드에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 후 추가할 수 있습니다. 메서드는 `AddJsonProtocol` 개체를 받는 `options` 대리자를 사용 합니다. 해당 개체의 [페이로드 SerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 인수의 직렬화를 구성하고 값을 반환하는 데 사용할 수 있는 JSON.NET `JsonSerializerSettings` 개체입니다. 자세한 내용은 JSON.NET [문서를](https://www.newtonsoft.com/json/help/html/Introduction.htm)참조하십시오.
 
예를 들어, 기본 "camelCase" 이름 대신 "PascalCase" 속성 이름을 사용하도록 serializer를 구성하려면 `Startup.ConfigureServices`다음 코드를 사용합니다.
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

.NET 클라이언트에서 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다. 확장 `Microsoft.Extensions.DependencyInjection` 메서드를 해결하려면 네임스페이스를 가져와야 합니다.

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
> 현재 는 JavaScript 클라이언트에서 JSON 직렬화를 구성할 수 없습니다.

### <a name="messagepack-serialization-options"></a>메시지팩 직렬화 옵션

메시지팩 일련화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대한 대리자를 제공하여 구성할 수 있습니다. 자세한 내용은 [SignalR의 메시지 팩을](xref:signalr/messagepackhubprotocol) 참조하십시오.

> [!NOTE]
> 현재 는 JavaScript 클라이언트에서 MessagePack 직렬화를 구성할 수 없습니다.

## <a name="configure-server-options"></a>서버 옵션 구성

다음 표는 SignalR 허브를 구성하는 옵션에 대해 설명합니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30초 | 서버는 이 간격으로 클라이언트가 연결이 끊어진 것으로 간주합니다(유지-활성 포함)가 수신되지 않은 경우 클라이언트가 연결이 끊어진 것으로 간주합니다. 클라이언트가 실제로 연결이 끊어진 것으로 표시되는 데 이 시간 초과 간격보다 더 오래 걸릴 수 있습니다. 권장 값은 값의 `KeepAliveInterval` 두 배입니다.|
| `HandshakeTimeout` | 15초 | 클라이언트가 이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다. 이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오. |
| `KeepAliveInterval` | 15초 | 서버가 이 간격 내에 메시지를 보내지 않은 경우 연결을 열어 두기 위해 ping 메시지가 자동으로 전송됩니다. `KeepAliveInterval`변경하면 클라이언트의 `ServerTimeout` / `serverTimeoutInMilliseconds` 설정을 변경합니다. `ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 값의 `KeepAliveInterval` 두 배입니다.  |
| `SupportedProtocols` | 설치된 모든 프로토콜 | 이 허브에서 지원하는 프로토콜입니다. 기본적으로 서버에 등록된 모든 프로토콜은 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대한 특정 프로토콜을 비활성화할 수 있습니다. |
| `EnableDetailedErrors` | `false` | 자세한 `true`예외 메시지가 Hub 메서드에서 예외를 throw하면 클라이언트에 반환됩니다. 기본값은 `false`은 예외 메시지에 중요한 정보가 포함될 수 있으므로 입니다. |

옵션에서 호출에 대한 옵션 대리자를 제공하여 모든 `AddSignalR` 허브에 대해 옵션을 구성할 수 있습니다. `Startup.ConfigureServices`

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

단일 허브에 대한 옵션은 제공된 전역 `AddSignalR` 옵션을 재정의하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>다음을 사용하여 구성할 수 있습니다.

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>고급 HTTP 구성 옵션

전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하는 데 사용합니다. 이러한 옵션은 [에서 MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 대리자를 전달하여 `Startup.Configure`구성됩니다.

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

다음 표는 코어 SignalR의 고급 http 옵션을 구성하기 ASP.NET 옵션에 대해 설명합니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32KB | 서버가 버퍼링하는 클라이언트에서 받은 최대 바이트 수입니다. 이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 소비에 부정적인 영향을 미칠 수 있습니다. |
| `AuthorizationData` | Hub 클래스에 `Authorize` 적용된 특성에서 데이터가 자동으로 수집됩니다. | 클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체 목록입니다. |
| `TransportMaxBufferSize` | 32KB | 서버가 버퍼링하는 앱에서 보내는 최대 바이트 수입니다. 이 값을 늘리면 서버가 더 큰 메시지를 보낼 수 있지만 메모리 소비에 부정적인 영향을 미칠 수 있습니다. |
| `Transports` | 모든 전송이 활성화됩니다. | 비트는 클라이언트가 `HttpTransportType` 연결하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 열거형 플래그를 지정합니다. |
| `LongPolling` | 아래를 참조하십시오. | 긴 폴링 전송과 관련된 추가 옵션입니다. |
| `WebSockets` | 아래를 참조하십시오. | WebSockets 전송과 관련된 추가 옵션입니다. |

Long Polling 전송에는 속성을 사용하여 구성할 `LongPolling` 수 있는 추가 옵션이 있습니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90초 | 단일 폴링 요청을 종료하기 전에 서버가 메시지가 클라이언트에 전송될 때까지 기다리는 최대 시간입니다. 이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 발급합니다. |

WebSocket 전송에는 `WebSockets` 속성을 사용하여 구성할 수 있는 추가 옵션이 있습니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5초 | 서버가 닫히면 클라이언트가 이 시간 간격 내에 닫히지 않으면 연결이 종료됩니다. |
| `SubProtocolSelector` | `null` | 헤더를 사용자 지정 값으로 `Sec-WebSocket-Protocol` 설정하는 데 사용할 수 있는 대리자입니다. 대리자는 클라이언트가 요청한 값을 입력으로 수신하고 원하는 값을 반환해야 합니다. |

## <a name="configure-client-options"></a>클라이언트 옵션 구성

클라이언트 옵션은 `HubConnectionBuilder` 유형(.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다. Java 클라이언트에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 빌더 구성 옵션과 그 자체가 `HubConnection` 포함되어 있습니다.

### <a name="configure-logging"></a>로깅 구성

로깅은 `ConfigureLogging` 메서드를 사용하여 .NET 클라이언트에서 구성됩니다. 로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다. 자세한 내용은 [ASP.NET 핵심 로그인](xref:fundamentals/logging/index) 설명서를 참조하십시오.

> [!NOTE]
> 로깅 공급자를 등록하려면 필요한 패키지를 설치해야 합니다. 전체 목록은 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조하십시오.

예를 들어 콘솔 로깅을 `Microsoft.Extensions.Logging.Console` 사용하려면 NuGet 패키지를 설치합니다. 확장 `AddConsole` 메서드를 호출합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript 클라이언트에서 비슷한 `configureLogging` 메서드가 존재합니다. 생성할 `LogLevel` 로그 메시지의 최소 수준을 나타내는 값을 제공합니다. 로그는 브라우저 콘솔 창에 기록됩니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> 로깅을 완전히 `signalR.LogLevel.None` 사용하지 `configureLogging` 않으려면 메서드를 지정합니다.

로깅에 대한 자세한 내용은 [SignalR 진단 문서를](xref:signalr/diagnostics)참조하십시오.

SignalR Java 클라이언트는 로깅을 위해 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용합니다. 라이브러리의 사용자가 특정 로깅 종속성을 가져와 고유한 특정 로깅 구현을 선택할 수 있는 고급 로깅 API입니다. 다음 코드 조각은 SignalR `java.util.logging` Java 클라이언트에서 사용하는 방법을 보여 주며 있습니다.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

종속성에 로깅을 구성하지 않은 경우 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음 로거를 로드합니다.

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

이 무시 해도 안전 하 게 무시할 수 있습니다.

### <a name="configure-allowed-transports"></a>허용된 전송 구성

SignalR에서 사용하는 전송은 `WithUrl` 호출(자바스크립트)에서`withUrl` 구성할 수 있습니다. 비트-OR값은 클라이언트가 `HttpTransportType` 지정된 전송만 사용하도록 제한하는 데 사용할 수 있습니다. 모든 전송은 기본적으로 활성화되어 있습니다.

예를 들어 서버 전송 이벤트 전송을 사용하지 않도록 설정하지만 WebSocket 및 긴 폴링 연결을 허용하려면 다음을 수행합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript 클라이언트에서 전송은 `transport` `withUrl`다음에 제공된 옵션 개체에 필드를 설정하여 구성됩니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

이 버전의 Java 클라이언트 웹소켓은 사용 가능한 유일한 전송입니다.

### <a name="configure-bearer-authentication"></a>베어러 인증 구성

SignalR 요청과 함께 인증 데이터를 `AccessTokenProvider` 제공하려면`accessTokenFactory` 옵션(JavaScript)을 사용하여 원하는 액세스 토큰을 반환하는 함수를 지정합니다. .NET 클라이언트에서 이 액세스 토큰은 HTTP "보유자 인증" 토큰으로 `Authorization` 전달됩니다(유형이 `Bearer`있는 헤더 사용). JavaScript 클라이언트에서 액세스 토큰은 브라우저 API가 헤더를 적용하는 기능을 제한하는 경우(특히 서버 전송 이벤트 및 WebSockets 요청)를 **제외하고** 베어러 토큰으로 사용됩니다. 이러한 경우 액세스 토큰은 쿼리 문자열 값으로 `access_token`제공됩니다.

.NET 클라이언트에서 `AccessTokenProvider` 옵션은 다음 에서 `WithUrl`옵션 대리자를 사용하여 지정할 수 있습니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript 클라이언트에서 액세스 토큰은 다음 의 `accessTokenFactory` `withUrl`옵션 개체에 필드를 설정하여 구성됩니다.

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

SignalR Java 클라이언트에서 [HttpHubConnectionBuilder에](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)액세스 토큰 팩터리를 제공하여 인증에 사용할 베어러 토큰을 구성할 수 있습니다. [와 사용AccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) [RxJava](https://github.com/ReactiveX/RxJava) [단일\<문자열>. ](https://reactivex.io/documentation/single.html) [Single.defer를](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)호출하면 클라이언트에 대한 액세스 토큰을 생성하는 논리를 작성할 수 있습니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>시간 시간 및 유지 옵션 구성

시간 시간 및 유지 동작을 구성하기 위한 추가 `HubConnection` 옵션은 개체 자체에서 사용할 수 있습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30초(30,000밀리초) | 서버 활동에 대한 시간 초과입니다. 서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `Closed` 것으로`onclose` 간주하고 JavaScript에서 이벤트를 트리거합니다. 이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다. 권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다. |
| `HandshakeTimeout` | 15초 | 초기 서버 핸드셰이크에 대한 시간 시간 입니다. 서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` `onclose` JavaScript에서 이벤트를 트리거합니다. 이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오. |
| `KeepAliveInterval` | 15초 | 클라이언트가 ping 메시지를 보내는 간격을 결정합니다. 클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다. 클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다. |

.NET 클라이언트에서 시간 지정 값은 `TimeSpan` 값으로 지정됩니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30초(30,000밀리초) | 서버 활동에 대한 시간 초과입니다. 서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onclose` 것으로 간주하고 이벤트를 트리거합니다. 이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다. 권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다. |
| `keepAliveIntervalInMilliseconds` | 15초(15,000밀리초) | 클라이언트가 ping 메시지를 보내는 간격을 결정합니다. 클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다. 클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다. |

# <a name="java"></a>[Java](#tab/java)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30초(30,000밀리초) | 서버 활동에 대한 시간 초과입니다. 서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onClose` 것으로 간주하고 이벤트를 트리거합니다. 이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다. 권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다. |
| `withHandshakeResponseTimeout` | 15초 | 초기 서버 핸드셰이크에 대한 시간 시간 입니다. 서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `onClose` 이벤트를 트리거합니다. 이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오. |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15초(15,000밀리초) | 클라이언트가 ping 메시지를 보내는 간격을 결정합니다. 클라이언트에서 메시지를 보내면 타이머가 간격의 시작으로 재설정됩니다. 클라이언트가 서버의 `ClientTimeoutInterval` 집합에서 메시지를 보내지 않은 경우 서버는 클라이언트연결이 끊어진 것으로 간주합니다. |

---

### <a name="configure-additional-options"></a>추가 구성 옵션

`WithUrl` 추가 옵션은 Java 클라이언트의`withUrl` 다양한 구성 API에 `HubConnectionBuilder` 있는 (JavaScript) 메서드에서 구성할 `HttpHubConnectionBuilder` 수 있습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 옵션 |  기본값 | Description |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다. |
| `SkipNegotiation` | `false` | 협상 단계를 `true` 건너뛰도록 설정합니다. **WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.** Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다. |
| `ClientCertificates` | Empty | 요청을 인증하기 위해 보낼 TLS 인증서 컬렉션입니다. |
| `Cookies` | Empty | 모든 HTTP 요청과 함께 보낼 HTTP 쿠키 모음입니다. |
| `Credentials` | Empty | 모든 HTTP 요청과 함께 보낼 자격 증명입니다. |
| `CloseTimeout` | 5초 | 웹 소켓만. 서버가 닫기 요청을 승인할 때까지 클라이언트가 닫은 후 대기하는 최대 시간입니다. 이 시간 내에 서버가 닫기를 승인하지 않으면 클라이언트가 연결 해제됩니다. |
| `Headers` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |
| `HttpMessageHandlerFactory` | `null` | HTTP 요청을 보내는 데 사용되는 대리자를 구성하거나 교체하는 데 사용할 수 있는 `HttpMessageHandler` 대리자입니다. WebSocket 연결에는 사용되지 않습니다. 이 대리자는 null이 아닌 값을 반환해야 하며 기본값을 매개 변수로 받습니다. 기본값의 설정을 수정하여 반환하거나 새 `HttpMessageHandler` 인스턴스를 반환합니다. **처리기를 교체할 때 제공된 처리기에서 유지하려는 설정을 복사해야 하며, 그렇지 않으면 구성된 옵션(예: 쿠키 및 헤더)이 새 처리기에 적용되지 않습니다.** |
| `Proxy` | `null` | HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다. |
| `UseDefaultCredentials` | `false` | HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 보내도록 이 부울을 설정합니다. 이렇게 하면 Windows 인증을 사용할 수 있습니다. |
| `WebSocketConfiguration` | `null` | 추가 WebSocket 옵션을 구성하는 데 사용할 수 있는 대리자입니다. 옵션을 구성하는 데 사용할 수 있는 [ClientWebSocketOptions의](/dotnet/api/system.net.websockets.clientwebsocketoptions) 인스턴스를 받습니다. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 자바 스크립트 옵션 | 기본값 | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다. |
| `skipNegotiation` | `false` | 협상 단계를 `true` 건너뛰도록 설정합니다. **WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.** Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다. |

# <a name="java"></a>[Java](#tab/java)

| 자바 옵션 | 기본값 | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다. |
| `shouldSkipNegotiate` | `false` | 협상 단계를 `true` 건너뛰도록 설정합니다. **WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.** Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다. |
| `withHeader` `withHeaders` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |

---

.NET 클라이언트에서 이러한 옵션은 다음에 제공된 옵션 대리자에서 수정할 `WithUrl`수 있습니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

자바 스크립트 클라이언트에서 이러한 옵션은 `withUrl`다음에 제공되는 자바 스크립트 개체에서 제공 할 수 있습니다 .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java 클라이언트에서 이러한 옵션은 `HttpHubConnectionBuilder` 반환된 메서드로 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>추가 자료

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/메시지팩 직렬화 옵션

ASP.NET 코어 시그널R는 메시지 인코딩을 위한 두 가지 프로토콜을 지원합니다: [JSON](https://www.json.org/) 및 [MessagePack.](https://msgpack.org/index.html) 각 프로토콜에는 직렬화 구성 옵션이 있습니다.

JSON 직렬화는 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 확장 메서드를 사용 하 여 서버에서 구성할 `Startup.ConfigureServices` 수 있습니다., 메서드에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 후 추가할 수 있습니다. 메서드는 `AddJsonProtocol` 개체를 받는 `options` 대리자를 사용 합니다. 해당 개체의 [페이로드 SerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) 속성은 인수의 직렬화를 구성하고 값을 반환하는 데 사용할 수 있는 JSON.NET `JsonSerializerSettings` 개체입니다. 자세한 내용은 JSON.NET [문서를](https://www.newtonsoft.com/json/help/html/Introduction.htm)참조하십시오.
 
예를 들어, 기본 "camelCase" 이름 대신 "PascalCase" 속성 이름을 사용하도록 serializer를 구성하려면 `Startup.ConfigureServices`다음 코드를 사용합니다.
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

.NET 클라이언트에서 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)에 동일한 확장 메서드가 있습니다. 확장 `Microsoft.Extensions.DependencyInjection` 메서드를 해결하려면 네임스페이스를 가져와야 합니다.

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
> 현재 는 JavaScript 클라이언트에서 JSON 직렬화를 구성할 수 없습니다.

### <a name="messagepack-serialization-options"></a>메시지팩 직렬화 옵션

메시지팩 일련화는 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 호출에 대한 대리자를 제공하여 구성할 수 있습니다. 자세한 내용은 [SignalR의 메시지 팩을](xref:signalr/messagepackhubprotocol) 참조하십시오.

> [!NOTE]
> 현재 는 JavaScript 클라이언트에서 MessagePack 직렬화를 구성할 수 없습니다.

## <a name="configure-server-options"></a>서버 옵션 구성

다음 표는 SignalR 허브를 구성하는 옵션에 대해 설명합니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15초 | 클라이언트가 이 시간 간격 내에 초기 핸드셰이크 메시지를 보내지 않으면 연결이 닫힙니다. 이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오. |
| `KeepAliveInterval` | 15초 | 서버가 이 간격 내에 메시지를 보내지 않은 경우 연결을 열어 두기 위해 ping 메시지가 자동으로 전송됩니다. `KeepAliveInterval`변경하면 클라이언트의 `ServerTimeout` / `serverTimeoutInMilliseconds` 설정을 변경합니다. `ServerTimeout` / 권장 `serverTimeoutInMilliseconds` 값은 값의 `KeepAliveInterval` 두 배입니다.  |
| `SupportedProtocols` | 설치된 모든 프로토콜 | 이 허브에서 지원하는 프로토콜입니다. 기본적으로 서버에 등록된 모든 프로토콜은 허용되지만 이 목록에서 프로토콜을 제거하여 개별 허브에 대한 특정 프로토콜을 비활성화할 수 있습니다. |
| `EnableDetailedErrors` | `false` | 자세한 `true`예외 메시지가 Hub 메서드에서 예외를 throw하면 클라이언트에 반환됩니다. 기본값은 `false`은 예외 메시지에 중요한 정보가 포함될 수 있으므로 입니다. |

옵션에서 호출에 대한 옵션 대리자를 제공하여 모든 `AddSignalR` 허브에 대해 옵션을 구성할 수 있습니다. `Startup.ConfigureServices`

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

단일 허브에 대한 옵션은 제공된 전역 `AddSignalR` 옵션을 재정의하며 <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>다음을 사용하여 구성할 수 있습니다.

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>고급 HTTP 구성 옵션

전송 `HttpConnectionDispatcherOptions` 및 메모리 버퍼 관리와 관련된 고급 설정을 구성하는 데 사용합니다. 이러한 옵션은 [에서 MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) 대리자를 전달하여 `Startup.Configure`구성됩니다.

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

다음 표는 코어 SignalR의 고급 http 옵션을 구성하기 ASP.NET 옵션에 대해 설명합니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32KB | 서버가 버퍼링하는 클라이언트에서 받은 최대 바이트 수입니다. 이 값을 늘리면 서버가 더 큰 메시지를 받을 수 있지만 메모리 소비에 부정적인 영향을 미칠 수 있습니다. |
| `AuthorizationData` | Hub 클래스에 `Authorize` 적용된 특성에서 데이터가 자동으로 수집됩니다. | 클라이언트가 허브에 연결할 권한이 있는지 확인하는 데 사용되는 [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) 개체 목록입니다. |
| `TransportMaxBufferSize` | 32KB | 서버가 버퍼링하는 앱에서 보내는 최대 바이트 수입니다. 이 값을 늘리면 서버가 더 큰 메시지를 보낼 수 있지만 메모리 소비에 부정적인 영향을 미칠 수 있습니다. |
| `Transports` | 모든 전송이 활성화됩니다. | 비트는 클라이언트가 `HttpTransportType` 연결하는 데 사용할 수 있는 전송을 제한할 수 있는 값의 열거형 플래그를 지정합니다. |
| `LongPolling` | 아래를 참조하십시오. | 긴 폴링 전송과 관련된 추가 옵션입니다. |
| `WebSockets` | 아래를 참조하십시오. | WebSockets 전송과 관련된 추가 옵션입니다. |

Long Polling 전송에는 속성을 사용하여 구성할 `LongPolling` 수 있는 추가 옵션이 있습니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90초 | 단일 폴링 요청을 종료하기 전에 서버가 메시지가 클라이언트에 전송될 때까지 기다리는 최대 시간입니다. 이 값을 줄이면 클라이언트가 새 폴링 요청을 더 자주 발급합니다. |

WebSocket 전송에는 `WebSockets` 속성을 사용하여 구성할 수 있는 추가 옵션이 있습니다.

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5초 | 서버가 닫히면 클라이언트가 이 시간 간격 내에 닫히지 않으면 연결이 종료됩니다. |
| `SubProtocolSelector` | `null` | 헤더를 사용자 지정 값으로 `Sec-WebSocket-Protocol` 설정하는 데 사용할 수 있는 대리자입니다. 대리자는 클라이언트가 요청한 값을 입력으로 수신하고 원하는 값을 반환해야 합니다. |

## <a name="configure-client-options"></a>클라이언트 옵션 구성

클라이언트 옵션은 `HubConnectionBuilder` 유형(.NET 및 JavaScript 클라이언트에서 사용 가능)에서 구성할 수 있습니다. Java 클라이언트에서도 사용할 수 있지만 `HttpHubConnectionBuilder` 하위 클래스에는 빌더 구성 옵션과 그 자체가 `HubConnection` 포함되어 있습니다.

### <a name="configure-logging"></a>로깅 구성

로깅은 `ConfigureLogging` 메서드를 사용하여 .NET 클라이언트에서 구성됩니다. 로깅 공급자와 필터는 서버에 있는 것과 동일한 방식으로 등록할 수 있습니다. 자세한 내용은 [ASP.NET 핵심 로그인](xref:fundamentals/logging/index) 설명서를 참조하십시오.

> [!NOTE]
> 로깅 공급자를 등록하려면 필요한 패키지를 설치해야 합니다. 전체 목록은 문서의 [기본 제공 로깅 공급자](xref:fundamentals/logging/index#built-in-logging-providers) 섹션을 참조하십시오.

예를 들어 콘솔 로깅을 `Microsoft.Extensions.Logging.Console` 사용하려면 NuGet 패키지를 설치합니다. 확장 `AddConsole` 메서드를 호출합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript 클라이언트에서 비슷한 `configureLogging` 메서드가 존재합니다. 생성할 `LogLevel` 로그 메시지의 최소 수준을 나타내는 값을 제공합니다. 로그는 브라우저 콘솔 창에 기록됩니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> 로깅을 완전히 `signalR.LogLevel.None` 사용하지 `configureLogging` 않으려면 메서드를 지정합니다.

로깅에 대한 자세한 내용은 [SignalR 진단 문서를](xref:signalr/diagnostics)참조하십시오.

SignalR Java 클라이언트는 로깅을 위해 [SLF4J](https://www.slf4j.org/) 라이브러리를 사용합니다. 라이브러리의 사용자가 특정 로깅 종속성을 가져와 고유한 특정 로깅 구현을 선택할 수 있는 고급 로깅 API입니다. 다음 코드 조각은 SignalR `java.util.logging` Java 클라이언트에서 사용하는 방법을 보여 주며 있습니다.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

종속성에 로깅을 구성하지 않은 경우 SLF4J는 다음과 같은 경고 메시지와 함께 기본 작업 없음 로거를 로드합니다.

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

이 무시 해도 안전 하 게 무시할 수 있습니다.

### <a name="configure-allowed-transports"></a>허용된 전송 구성

SignalR에서 사용하는 전송은 `WithUrl` 호출(자바스크립트)에서`withUrl` 구성할 수 있습니다. 비트-OR값은 클라이언트가 `HttpTransportType` 지정된 전송만 사용하도록 제한하는 데 사용할 수 있습니다. 모든 전송은 기본적으로 활성화되어 있습니다.

예를 들어 서버 전송 이벤트 전송을 사용하지 않도록 설정하지만 WebSocket 및 긴 폴링 연결을 허용하려면 다음을 수행합니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript 클라이언트에서 전송은 `transport` `withUrl`다음에 제공된 옵션 개체에 필드를 설정하여 구성됩니다.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>베어러 인증 구성

SignalR 요청과 함께 인증 데이터를 `AccessTokenProvider` 제공하려면`accessTokenFactory` 옵션(JavaScript)을 사용하여 원하는 액세스 토큰을 반환하는 함수를 지정합니다. .NET 클라이언트에서 이 액세스 토큰은 HTTP "보유자 인증" 토큰으로 `Authorization` 전달됩니다(유형이 `Bearer`있는 헤더 사용). JavaScript 클라이언트에서 액세스 토큰은 브라우저 API가 헤더를 적용하는 기능을 제한하는 경우(특히 서버 전송 이벤트 및 WebSockets 요청)를 **제외하고** 베어러 토큰으로 사용됩니다. 이러한 경우 액세스 토큰은 쿼리 문자열 값으로 `access_token`제공됩니다.

.NET 클라이언트에서 `AccessTokenProvider` 옵션은 다음 에서 `WithUrl`옵션 대리자를 사용하여 지정할 수 있습니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript 클라이언트에서 액세스 토큰은 다음 의 `accessTokenFactory` `withUrl`옵션 개체에 필드를 설정하여 구성됩니다.

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

SignalR Java 클라이언트에서 [HttpHubConnectionBuilder에](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)액세스 토큰 팩터리를 제공하여 인증에 사용할 베어러 토큰을 구성할 수 있습니다. [와 사용AccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) [RxJava](https://github.com/ReactiveX/RxJava) [단일\<문자열>. ](https://reactivex.io/documentation/single.html) [Single.defer를](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)호출하면 클라이언트에 대한 액세스 토큰을 생성하는 논리를 작성할 수 있습니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>시간 시간 및 유지 옵션 구성

시간 시간 및 유지 동작을 구성하기 위한 추가 `HubConnection` 옵션은 개체 자체에서 사용할 수 있습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30초(30,000밀리초) | 서버 활동에 대한 시간 초과입니다. 서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `Closed` 것으로`onclose` 간주하고 JavaScript에서 이벤트를 트리거합니다. 이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다. 권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다. |
| `HandshakeTimeout` | 15초 | 초기 서버 핸드셰이크에 대한 시간 시간 입니다. 서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `Closed` `onclose` JavaScript에서 이벤트를 트리거합니다. 이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오. |

.NET 클라이언트에서 시간 지정 값은 `TimeSpan` 값으로 지정됩니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30초(30,000밀리초) | 서버 활동에 대한 시간 초과입니다. 서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onclose` 것으로 간주하고 이벤트를 트리거합니다. 이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다. 권장되는 값은 ping이 도착할 때까지 `KeepAliveInterval` 서버 값을 두 배 이상 늘리는 숫자입니다. |

# <a name="java"></a>[Java](#tab/java)

| 옵션 | 기본값 | Description |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30초(30,000밀리초) | 서버 활동에 대한 시간 초과입니다. 서버가 이 간격으로 메시지를 보내지 않은 경우 클라이언트는 서버연결이 끊어진 `onClose` 것으로 간주하고 이벤트를 트리거합니다. 이 값은 **ping** 메시지를 서버에서 보내고 시간 간격 내에 클라이언트에서 수신할 수 있을 만큼 충분히 커야 합니다. ping이 도착할 시간을 허용하기 위해 권장되는 값은 서버 값의 `KeepAliveInterval` 두 배 이상인 숫자입니다. |
| `withHandshakeResponseTimeout` | 15초 | 초기 서버 핸드셰이크에 대한 시간 시간 입니다. 서버가 이 간격으로 핸드셰이크 응답을 보내지 않으면 클라이언트는 핸드셰이크를 취소하고 `onClose` 이벤트를 트리거합니다. 이 설정은 심각한 네트워크 대기 시간으로 인해 핸드셰이크 시간 지정 오류가 발생하는 경우에만 수정해야 하는 고급 설정입니다. 핸드셰이크 프로세스에 대한 자세한 내용은 [SignalR 허브 프로토콜 사양을](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)참조하십시오. |

---

### <a name="configure-additional-options"></a>추가 구성 옵션

`WithUrl` 추가 옵션은 Java 클라이언트의`withUrl` 다양한 구성 API에 `HubConnectionBuilder` 있는 (JavaScript) 메서드에서 구성할 `HttpHubConnectionBuilder` 수 있습니다.

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 옵션 |  기본값 | Description |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다. |
| `SkipNegotiation` | `false` | 협상 단계를 `true` 건너뛰도록 설정합니다. **WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.** Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다. |
| `ClientCertificates` | Empty | 요청을 인증하기 위해 보낼 TLS 인증서 컬렉션입니다. |
| `Cookies` | Empty | 모든 HTTP 요청과 함께 보낼 HTTP 쿠키 모음입니다. |
| `Credentials` | Empty | 모든 HTTP 요청과 함께 보낼 자격 증명입니다. |
| `CloseTimeout` | 5초 | 웹 소켓만. 서버가 닫기 요청을 승인할 때까지 클라이언트가 닫은 후 대기하는 최대 시간입니다. 이 시간 내에 서버가 닫기를 승인하지 않으면 클라이언트가 연결 해제됩니다. |
| `Headers` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |
| `HttpMessageHandlerFactory` | `null` | HTTP 요청을 보내는 데 사용되는 대리자를 구성하거나 교체하는 데 사용할 수 있는 `HttpMessageHandler` 대리자입니다. WebSocket 연결에는 사용되지 않습니다. 이 대리자는 null이 아닌 값을 반환해야 하며 기본값을 매개 변수로 받습니다. 기본값의 설정을 수정하여 반환하거나 새 `HttpMessageHandler` 인스턴스를 반환합니다. **처리기를 교체할 때 제공된 처리기에서 유지하려는 설정을 복사해야 하며, 그렇지 않으면 구성된 옵션(예: 쿠키 및 헤더)이 새 처리기에 적용되지 않습니다.** |
| `Proxy` | `null` | HTTP 요청을 보낼 때 사용할 HTTP 프록시입니다. |
| `UseDefaultCredentials` | `false` | HTTP 및 WebSockets 요청에 대한 기본 자격 증명을 보내도록 이 부울을 설정합니다. 이렇게 하면 Windows 인증을 사용할 수 있습니다. |
| `WebSocketConfiguration` | `null` | 추가 WebSocket 옵션을 구성하는 데 사용할 수 있는 대리자입니다. 옵션을 구성하는 데 사용할 수 있는 [ClientWebSocketOptions의](/dotnet/api/system.net.websockets.clientwebsocketoptions) 인스턴스를 받습니다. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 자바 스크립트 옵션 | 기본값 | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다. |
| `skipNegotiation` | `false` | 협상 단계를 `true` 건너뛰도록 설정합니다. **WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.** Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다. |

# <a name="java"></a>[Java](#tab/java)

| 자바 옵션 | 기본값 | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 요청에서 Bearer 인증 토큰으로 제공되는 문자열을 반환하는 함수입니다. |
| `shouldSkipNegotiate` | `false` | 협상 단계를 `true` 건너뛰도록 설정합니다. **WebSockets 전송이 유일한 사용 가능한 전송인 경우에만 지원됩니다.** Azure SignalR 서비스를 사용할 때이 설정을 사용할 수 없습니다. |
| `withHeader` `withHeaders` | Empty | 모든 HTTP 요청과 함께 보낼 추가 HTTP 헤더의 맵입니다. |

---

.NET 클라이언트에서 이러한 옵션은 다음에 제공된 옵션 대리자에서 수정할 `WithUrl`수 있습니다.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

자바 스크립트 클라이언트에서 이러한 옵션은 `withUrl`다음에 제공되는 자바 스크립트 개체에서 제공 할 수 있습니다 .

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java 클라이언트에서 이러한 옵션은 `HttpHubConnectionBuilder` 반환된 메서드로 구성할 수 있습니다.`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>추가 자료

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
