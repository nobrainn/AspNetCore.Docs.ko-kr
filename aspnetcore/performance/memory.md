---
title: ASP.NET 코어의 메모리 관리 및 패턴
author: rick-anderson
description: ASP.NET Core에서 메모리를 관리하는 방법과 GC(가비지 수집기)의 작동 방식에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: performance/memory
ms.openlocfilehash: b2af9cb567cdb1d7b2d0942601fcc3ebd999a5d9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440950"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a>ASP.NET 코어의 메모리 관리 및 가비지 수집(GC)

[세바스티안 로스와](https://github.com/sebastienros) [릭 앤더슨](https://twitter.com/RickAndMSFT)

.NET과 같은 관리되는 프레임워크에서도 메모리 관리는 복잡합니다. 메모리 문제를 분석하고 이해하는 것은 어려울 수 있습니다. 이 문서의 내용:

* 많은 *메모리 누수* 및 GC가 작동하지 않는 문제로 인해 동기가 *부여되었습니다.* 이러한 문제의 대부분은 .NET Core에서 메모리 소비가 어떻게 작동하는지 이해하지 못하거나 메모리 소비가 어떻게 측정되는지 이해하지 못하여 발생했습니다.
* 문제가 있는 메모리 사용을 보여 주며 대체 방법을 제안합니다.

## <a name="how-garbage-collection-gc-works-in-net-core"></a>.NET Core에서 가비지 수집(GC)의 작동 방식

GC는 각 세그먼트가 연속메모리 범위인 힙 세그먼트를 할당합니다. 힙에 배치된 객체는 0, 1 또는 2의 3세대 중 하나로 분류됩니다. 생성은 GC가 앱에서 더 이상 참조하지 않는 관리되는 개체에서 메모리를 해제하려고 시도하는 빈도를 결정합니다. 번호가 낮은 세대는 GC가 더 자주 발생합니다.

개체는 수명에 따라 한 세대에서 다른 세대로 이동됩니다. 객체가 더 오래 살수록 더 높은 세대로 이동됩니다. 앞서 언급했듯이, 상위 세대는 GC의 빈도가 낮습니다. 단기 수명 개체는 항상 0세대에 유지됩니다. 예를 들어 웹 요청 수명 동안 참조되는 개체는 수명이 짧습니다. 응용 프로그램 수준 [싱글톤은](xref:fundamentals/dependency-injection#service-lifetimes) 일반적으로 2세대로 마이그레이션됩니다.

ASP.NET 핵심 앱이 시작되면 GC:

* 초기 힙 세그먼트에 대한 일부 메모리를 예약합니다.
* 런타임이 로드될 때 메모리의 작은 부분을 커밋합니다.

위의 메모리 할당은 성능상의 이유로 수행됩니다. 성능 이점은 연속 메모리의 힙 세그먼트에서 비롯됩니다.

### <a name="call-gccollect"></a>GC에 전화하십시오. 수집

[GC를 호출합니다. 명시적으로 수집:](xref:System.GC.Collect*)

* 프로덕션 ASP.NET 코어 앱에서 **수행해서는** 안 됩니다.
* 메모리 누수 를 조사할 때 유용합니다.
* 조사할 때 GC가 메모리에서 모든 매달려 있는 개체를 제거했는지 확인하여 메모리를 측정할 수 있습니다.

## <a name="analyzing-the-memory-usage-of-an-app"></a>앱의 메모리 사용량 분석

전용 도구는 메모리 사용량을 분석하는 데 도움이 될 수 있습니다.

- 개체 참조 계산
- GC가 CPU 사용량에 미치는 영향 측정
- 각 세대에 사용되는 메모리 공간 측정

다음 도구를 사용하여 메모리 사용량을 분석합니다.

* [도트넷 추적](/dotnet/core/diagnostics/dotnet-trace): 생산 기계에서 사용할 수 있습니다.
* [Visual Studio 디버거 없이 메모리 사용량 분석](/visualstudio/profiling/memory-usage-without-debugging2)
* [Visual Studio에서 프로필 메모리 사용량](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a>메모리 문제 감지

작업 관리자는 ASP.NET 앱이 사용하는 메모리의 양을 파악하는 데 사용할 수 있습니다. 작업 관리자 메모리 값:

* ASP.NET 프로세스에서 사용되는 메모리 양을 나타냅니다.
* 앱의 살아있는 개체 및 네이티브 메모리 사용과 같은 기타 메모리 소비자를 포함합니다.

작업 관리자 메모리 값이 무기한 증가하고 평평해지지 않으면 앱에 메모리 누수가 있습니다. 다음 섹션에서는 여러 메모리 사용 패턴을 보여 주며 설명합니다.

## <a name="sample-display-memory-usage-app"></a>샘플 디스플레이 메모리 사용 앱

[메모리리크 샘플 앱은](https://github.com/sebastienros/memoryleak) GitHub에서 사용할 수 있습니다. 메모리 리크 응용 프로그램 :

* 앱에 대한 실시간 메모리 및 GC 데이터를 수집하는 진단 컨트롤러가 포함되어 있습니다.
* 메모리 및 GC 데이터를 표시하는 인덱스 페이지가 있습니다. 인덱스 페이지는 매초 새로 고쳐집니다.
* 다양한 메모리 로드 패턴을 제공하는 API 컨트롤러가 포함되어 있습니다.
* 지원되는 도구는 아니지만 ASP.NET 핵심 앱의 메모리 사용 패턴을 표시하는 데 사용할 수 있습니다.

메모리 리크를 실행합니다. 할당된 메모리는 GC가 발생할 때까지 천천히 증가합니다. 도구가 데이터를 캡처하기 위해 사용자 지정 개체를 할당하기 때문에 메모리가 증가합니다. 다음 이미지는 Gen 0 GC가 발생할 때 MemoryLeak 인덱스 페이지를 보여 주며 있습니다. API 컨트롤러의 API 끝점이 호출되지 않으므로 차트에는 0 RPS(초당 요청 수)가 표시됩니다.

![이전 차트](memory/_static/0RPS.png)

차트에는 메모리 사용량에 대한 두 가지 값이 표시됩니다.

- 할당: 관리되는 개체가 차지하는 메모리 양
- [작업 집합](/windows/win32/memory/working-set): 현재 실제 메모리에 상주하는 프로세스의 가상 주소 공간에 있는 페이지 집합입니다. 표시된 작업 집합은 작업 관리자가 표시하는 것과 동일한 값입니다.

### <a name="transient-objects"></a>일시적인 개체

다음 API는 10KB 문자열 인스턴스를 만들고 클라이언트에 반환합니다. 각 요청에서 새 개체가 메모리에 할당되고 응답에 기록됩니다. 문자열은 .NET에 UTF-16 문자로 저장되므로 각 문자는 메모리에 2바이트를 사용합니다.

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

다음 그래프는 상대적으로 적은 부하로 생성되어 메모리 할당이 GC에 의해 어떻게 영향을 받는지 보여 준다.

![이전 차트](memory/_static/bigstring.png)

앞의 차트는 다음을 보여 주며, 다음을 보여 주면 됩니다.

* 4K RPS(초당 요청).
* 세대 0 GC 컬렉션은 약 2초마다 발생합니다.
* 작업 집합은 약 500MB에서 일정합니다.
* CPU는 12%입니다.
* GC를 통한 메모리 소비 및 릴리스는 안정적입니다.

다음 차트는 컴퓨터에서 처리할 수 있는 최대 처리량에서 수행됩니다.

![이전 차트](memory/_static/bigstring2.png)

앞의 차트는 다음을 보여 주며, 다음을 보여 주면 됩니다.

* 22K RPS
* 0세대 GC 컬렉션은 초당 여러 번 발생합니다.
* 앱이 초당 훨씬 더 많은 메모리를 할당했기 때문에 1세대 컬렉션이 트리거됩니다.
* 작업 집합은 약 500MB에서 일정합니다.
* CPU는 33%입니다.
* GC를 통한 메모리 소비 및 릴리스는 안정적입니다.
* CPU (33%) 과도하게 사용되지 않으므로 가비지 수집은 많은 수의 할당을 따라갈 수 있습니다.

### <a name="workstation-gc-vs-server-gc"></a>워크스테이션 GC 대 서버 GC

.NET 가비지 수집기에는 두 가지 모드가 있습니다.

* **워크스테이션 GC**: 데스크탑에 최적화되었습니다.
* **서버 GC**. ASP.NET 코어 앱의 기본 GC입니다. 서버에 최적화되었습니다.

GC 모드는 프로젝트 파일 또는 게시된 앱의 *runtimeconfig.json* 파일에서 명시적으로 설정할 수 있습니다. 다음 태그는 프로젝트 `ServerGarbageCollection` 파일의 설정을 보여 주며 다음과 같은 태그를 표시합니다.

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

프로젝트 `ServerGarbageCollection` 파일을 변경하려면 앱을 다시 빌드해야 합니다.

**참고:** 단일 코어가 있는 컴퓨터에서는 서버 가비지 수집을 사용할 수 **없습니다.** 자세한 내용은 <xref:System.Runtime.GCSettings.IsServerGC>를 참조하세요.

다음 이미지는 워크스테이션 GC를 사용하여 5K RPS 아래의 메모리 프로파일을 보여 주며, 이 에 대한 이미지입니다.

![이전 차트](memory/_static/workstation.png)

이 차트와 서버 버전간의 차이점은 다음과 같습니다.

- 작업 세트는 500MB에서 70MB로 떨어집니다.
- GC는 2초마다 가만쓰는 대신 초당 0세대 컬렉션을 여러 번 수행합니다.
- GC는 300MB에서 10MB로 떨어집니다.

일반적인 웹 서버 환경에서는 CPU 사용량이 메모리보다 중요하므로 서버 GC가 더 좋습니다. 메모리 사용률이 높고 CPU 사용량이 상대적으로 낮은 경우 워크스테이션 GC가 더 성능이 향상될 수 있습니다. 예를 들어 메모리가 부족한 여러 웹 앱을 고밀도로 호스팅합니다.

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a>도커 및 소형 컨테이너를 사용하는 GC

하나의 컴퓨터에서 여러 컨테이너화된 앱이 실행되는 경우 워크스테이션 GC가 서버 GC보다 더 많은 프리폼일 수 있습니다. 자세한 내용은 [작은 컨테이너에서 서버 GC로 실행하고 작은 컨테이너](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) 시나리오 [1부에서 서버 GC로 실행 - GC 힙에 대한 하드 제한을](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/)참조하십시오.

### <a name="persistent-object-references"></a>영구 개체 참조

GC는 참조되는 개체를 해제할 수 없습니다. 참조되지만 더 이상 필요하지 않은 개체는 메모리 누수로 이어집니다. 앱에서 개체를 자주 할당하고 개체가 더 이상 필요하지 않은 후 개체를 해제하지 못하면 시간이 지남에 따라 메모리 사용량이 증가합니다.

다음 API는 10KB 문자열 인스턴스를 만들고 클라이언트에 반환합니다. 이전 예제와의 차이점은 이 인스턴스가 정적 멤버에서 참조된다는 것입니다.

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

위의 코드:

* 일반적인 메모리 누수의 예입니다.
* 빈번한 호출을 사용하면 프로세스가 예외로 `OutOfMemory` 충돌할 때까지 앱 메모리가 증가합니다.

![이전 차트](memory/_static/eternal.png)

앞의 이미지에서:

* 엔드포인트를 `/api/staticstring` 부하 테스트하면 메모리가 선형으로 증가합니다.
* GC는 2세대 컬렉션을 호출하여 메모리 압력이 증가함에 따라 메모리를 확보하려고 시도합니다.
* GC는 유출된 메모리를 해제할 수 없습니다. 할당된 작업 집합은 시간이 지남에 따라 증가합니다.

캐싱과 같은 일부 시나리오에서는 메모리 압력으로 인해 개체참조가 해제될 때까지 개체 참조를 유지해야 합니다. <xref:System.WeakReference> 이 유형의 캐싱 코드에 클래스를 사용할 수 있습니다. 개체는 `WeakReference` 메모리 압력하에서 수집됩니다. 사용의 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> `WeakReference`기본 구현 .

### <a name="native-memory"></a>네이티브 메모리

일부 .NET Core 개체는 기본 메모리에 의존합니다. 네이티브 메모리는 GC에 의해 수집될 수 **없습니다.** 네이티브 메모리를 사용하는 .NET 개체는 네이티브 코드를 사용하여 해제해야 합니다.

.NET은 <xref:System.IDisposable> 개발자가 네이티브 메모리를 해제할 수 있도록 하는 인터페이스를 제공합니다. <xref:System.IDisposable.Dispose*> 호출되지 않더라도 [종료자가](/dotnet/csharp/programming-guide/classes-and-structs/destructors) 실행될 때 올바르게 구현된 클래스가 호출됩니다. `Dispose`

다음 코드를 살펴보세요.

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

[PhysicalFileProvider는](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) 관리되는 클래스이므로 모든 인스턴스는 요청이 끝날 때 수집됩니다.

다음 이미지는 `fileprovider` API를 계속 호출하는 동안 메모리 프로필을 보여 줍니다.

![이전 차트](memory/_static/fileprovider.png)

앞의 차트에서는 메모리 사용량이 계속 증가하므로 이 클래스의 구현에 대한 명백한 문제를 보여 준다. [이 문제에서](https://github.com/dotnet/aspnetcore/issues/3110)추적 중인 알려진 문제입니다.

사용자 코드에서 다음 중 하나에 의해 동일한 누설이 발생할 수 있습니다.

* 클래스를 올바르게 해제하지 않습니다.
* 삭제해야 하는 종속 `Dispose`개체의 메서드를 호출하는 것을 잊어버릅니다.

### <a name="large-objects-heap"></a>큰 개체 힙

빈번한 메모리 할당/사용 비 주기는 특히 큰 메모리 덩어리를 할당할 때 메모리를 조각화할 수 있습니다. 개체는 연속된 메모리 블록에 할당됩니다. 조각화를 완화하기 위해 GC가 메모리를 해제하면 조각 모음을 시도합니다. 이 프로세스를 **압축이라고**합니다. 다짐은 움직이는 물체를 포함한다. 큰 개체를 이동하면 성능이 저하됩니다. 이러한 이유로 GC는 _큰_ 개체 힙(LOH)이라고 하는 큰 개체에 대한 특수 메모리 영역을 만듭니다. [large object heap](/dotnet/standard/garbage-collection/large-object-heap) 85,000바이트(약 83KB)를 초과하는 개체는 다음과 같습니다.

* LOH에 놓습니다.
* 압축되지 않았습니다.
* 2세대 GC 동안 수집됩니다.

LOH가 가득 차면 GC는 2세대 컬렉션을 트리거합니다. 2세대 컬렉션:

* 본질적으로 느립니다.
* 또한 다른 모든 세대에서 컬렉션을 트리거하는 비용이 발생합니다.

다음 코드는 LOH를 즉시 압축합니다.

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

LOH 압축에 대한 자세한 내용은 를 참조하십시오. <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>

.NET Core 3.0 이상을 사용하는 컨테이너에서는 LOH가 자동으로 압축됩니다.

이 동작을 보여 주는 다음 API는 다음과 같은

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

다음 차트는 최대 부하에서 `/api/loh/84975` 끝점을 호출하는 메모리 프로필을 보여 주며 있습니다.

![이전 차트](memory/_static/loh1.png)

다음 차트는 끝점을 호출하는 `/api/loh/84976` 메모리 프로필을 보여 주며, *한 바이트만 더 할당합니다.*

![이전 차트](memory/_static/loh2.png)

참고: `byte[]` 구조에 오버헤드 바이트가 있습니다. 84,976바이트가 85,000개의 제한을 트리거하는 이유입니다.

앞의 두 차트 비교:

* 작업 집합은 두 시나리오 모두 약 450MB와 유사합니다.
* LOH 요청(84,975바이트)은 대부분 0세대 컬렉션을 보여 주며,
* OVER LOH 요청은 상수 세대 2 컬렉션을 생성합니다. 2세대 컬렉션은 비용이 많이 듭니다. 더 많은 CPU가 필요하며 처리량은 거의 50% 감소합니다.

임시 큰 개체는 gen2 GC를 유발하기 때문에 특히 문제가 됩니다.

성능을 극대화하기 위해 큰 개체 사용을 최소화해야 합니다. 가능하면 큰 개체를 분할합니다. 예를 들어 ASP.NET Core의 [응답 캐싱](xref:performance/caching/response) 미들웨어는 캐시 항목을 85,000바이트 미만의 블록으로 분할합니다.

다음 링크는 loH 제한 하에 개체를 유지하는 ASP.NET Core 접근 방식을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은

* [응답캐칭/스트림/스트림유틸리티.cs](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [응답캐칭/메모리응답캐시.cs](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

자세한 내용은 다음을 참조하세요.

* [큰 개체 힙 이 발견](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [큰 개체 힙](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a>HttpClient

잘못 사용하면 <xref:System.Net.Http.HttpClient> 리소스가 누출될 수 있습니다. 데이터베이스 연결, 소켓, 파일 핸들 등과 같은 시스템 리소스:

* 메모리보다 부족합니다.
* 메모리보다 누수될 때 더 문제가 됩니다.

경험이 풍부한 .NET 개발자는 을 <xref:System.IDisposable.Dispose*> <xref:System.IDisposable>구현하는 개체를 호출하는 방법을 알고 있습니다. 일반적으로 구현하는 `IDisposable` 개체를 삭제하지 않아 메모리가 유출되거나 시스템 리소스가 유출됩니다.

`HttpClient``IDisposable`구현하지만 모든 호출에 **삭제해서는** 안됩니다. 오히려 `HttpClient` 다시 사용해야합니다.

다음 끝점은 모든 요청에 대해 `HttpClient` 새 인스턴스를 만들고 삭제합니다.

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

로드 시 다음과 같은 오류 메시지가 기록됩니다.

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

인스턴스가 `HttpClient` 삭제되더라도 실제 네트워크 연결은 운영 체제에서 해제하는 데 다소 시간이 걸립니다. 새 연결을 지속적으로 생성하면 _포트가 고갈됩니다._ 각 클라이언트 연결에는 자체 클라이언트 포트가 필요합니다.

포트 소모를 방지하는 한 가지 방법은 동일한 `HttpClient` 인스턴스를 다시 사용하는 것입니다.

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

앱이 `HttpClient` 중지되면 인스턴스가 해제됩니다. 이 예제에서는 모든 일회용 리소스를 사용할 때마다 삭제해야 하는 것은 아닙니다.

인스턴스의 수명을 처리하는 더 나은 방법은 `HttpClient` 다음을 참조하십시오.

* [HttpClient 및 수명 관리](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [HTTP클라이언트 팩토리 블로그](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a>개체 풀링

이전 예제에서는 인스턴스를 `HttpClient` 정적으로 만들고 모든 요청에서 다시 사용할 수 있는 방법을 보여 주어 도 했습니다. 재사용은 리소스 가 부족하지 않도록 합니다.

개체 풀링:

* 재사용 패턴을 사용합니다.
* 만드는 데 비용이 많이 드는 개체를 위해 설계되었습니다.

풀은 스레드 간에 예약및 해제할 수 있는 미리 초기화된 개체의 모음입니다. 풀은 제한, 미리 정의된 크기 또는 증가율과 같은 할당 규칙을 정의할 수 있습니다.

NuGet 패키지 [Microsoft.Extensions.ObjectPool에는](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) 이러한 풀을 관리하는 데 도움이 되는 클래스가 포함되어 있습니다.

다음 API 끝점은 각 요청에 `byte` 난수로 채워진 버퍼를 인스턴스화합니다.

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

다음 차트 표시는 중간 로드를 가진 앞의 API를 호출합니다.

![이전 차트](memory/_static/array.png)

앞의 차트에서 0세대 컬렉션은 초당 약 한 번 발생합니다.

위의 코드는 [ArrayPool\<T ](xref:System.Buffers.ArrayPool`1)>`byte` 사용하여 버퍼를 풀링하여 최적화할 수 있습니다. 정적 인스턴스는 요청 간에 다시 사용됩니다.

이 방법의 다른 점은 풀이 된 개체가 API에서 반환된다는 것입니다. 즉, 다음을 의미합니다.

* 메서드에서 돌아오면 개체가 컨트롤을 벗어났습니다.
* 개체를 해제할 수 없습니다.

개체의 처리를 설정하려면 다음을 수행합니다.

* 풀린 배열을 일회용 개체에 캡슐화합니다.
* [HttpContext.Response.RegisterForDispose.](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)

`RegisterForDispose`은 HTTP 요청이 완료될 때만 해제되도록 대상 개체에 대한 호출을 `Dispose`처리합니다.

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

풀링되지 않은 버전과 동일한 하중을 적용하면 다음 차트가 생성됩니다.

![이전 차트](memory/_static/pooledarray.png)

주요 차이점은 바이트가 할당되고 결과적으로 0세대 컬렉션이 훨씬 줄어듭니다.

## <a name="additional-resources"></a>추가 자료

* [가비지 수집](/dotnet/standard/garbage-collection/)
* [동시성 시각화 도우미를 통해 다양한 GC 모드 이해](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [큰 개체 힙 이 발견](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [큰 개체 힙](/dotnet/standard/garbage-collection/large-object-heap)
