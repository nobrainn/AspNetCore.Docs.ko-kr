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
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="bf786-103">ASP.NET 코어의 메모리 관리 및 가비지 수집(GC)</span><span class="sxs-lookup"><span data-stu-id="bf786-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="bf786-104">[세바스티안 로스와](https://github.com/sebastienros) [릭 앤더슨](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bf786-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bf786-105">.NET과 같은 관리되는 프레임워크에서도 메모리 관리는 복잡합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="bf786-106">메모리 문제를 분석하고 이해하는 것은 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="bf786-107">이 문서의 내용:</span><span class="sxs-lookup"><span data-stu-id="bf786-107">This article:</span></span>

* <span data-ttu-id="bf786-108">많은 *메모리 누수* 및 GC가 작동하지 않는 문제로 인해 동기가 *부여되었습니다.*</span><span class="sxs-lookup"><span data-stu-id="bf786-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="bf786-109">이러한 문제의 대부분은 .NET Core에서 메모리 소비가 어떻게 작동하는지 이해하지 못하거나 메모리 소비가 어떻게 측정되는지 이해하지 못하여 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="bf786-110">문제가 있는 메모리 사용을 보여 주며 대체 방법을 제안합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="bf786-111">.NET Core에서 가비지 수집(GC)의 작동 방식</span><span class="sxs-lookup"><span data-stu-id="bf786-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="bf786-112">GC는 각 세그먼트가 연속메모리 범위인 힙 세그먼트를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="bf786-113">힙에 배치된 객체는 0, 1 또는 2의 3세대 중 하나로 분류됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="bf786-114">생성은 GC가 앱에서 더 이상 참조하지 않는 관리되는 개체에서 메모리를 해제하려고 시도하는 빈도를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="bf786-115">번호가 낮은 세대는 GC가 더 자주 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="bf786-116">개체는 수명에 따라 한 세대에서 다른 세대로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="bf786-117">객체가 더 오래 살수록 더 높은 세대로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="bf786-118">앞서 언급했듯이, 상위 세대는 GC의 빈도가 낮습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="bf786-119">단기 수명 개체는 항상 0세대에 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="bf786-120">예를 들어 웹 요청 수명 동안 참조되는 개체는 수명이 짧습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="bf786-121">응용 프로그램 수준 [싱글톤은](xref:fundamentals/dependency-injection#service-lifetimes) 일반적으로 2세대로 마이그레이션됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="bf786-122">ASP.NET 핵심 앱이 시작되면 GC:</span><span class="sxs-lookup"><span data-stu-id="bf786-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="bf786-123">초기 힙 세그먼트에 대한 일부 메모리를 예약합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="bf786-124">런타임이 로드될 때 메모리의 작은 부분을 커밋합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="bf786-125">위의 메모리 할당은 성능상의 이유로 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="bf786-126">성능 이점은 연속 메모리의 힙 세그먼트에서 비롯됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="bf786-127">GC에 전화하십시오. 수집</span><span class="sxs-lookup"><span data-stu-id="bf786-127">Call GC.Collect</span></span>

<span data-ttu-id="bf786-128">[GC를 호출합니다. 명시적으로 수집:](xref:System.GC.Collect*)</span><span class="sxs-lookup"><span data-stu-id="bf786-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="bf786-129">프로덕션 ASP.NET 코어 앱에서 **수행해서는** 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="bf786-130">메모리 누수 를 조사할 때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="bf786-131">조사할 때 GC가 메모리에서 모든 매달려 있는 개체를 제거했는지 확인하여 메모리를 측정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="bf786-132">앱의 메모리 사용량 분석</span><span class="sxs-lookup"><span data-stu-id="bf786-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="bf786-133">전용 도구는 메모리 사용량을 분석하는 데 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="bf786-134">개체 참조 계산</span><span class="sxs-lookup"><span data-stu-id="bf786-134">Counting object references</span></span>
- <span data-ttu-id="bf786-135">GC가 CPU 사용량에 미치는 영향 측정</span><span class="sxs-lookup"><span data-stu-id="bf786-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="bf786-136">각 세대에 사용되는 메모리 공간 측정</span><span class="sxs-lookup"><span data-stu-id="bf786-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="bf786-137">다음 도구를 사용하여 메모리 사용량을 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="bf786-138">[도트넷 추적](/dotnet/core/diagnostics/dotnet-trace): 생산 기계에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="bf786-139">Visual Studio 디버거 없이 메모리 사용량 분석</span><span class="sxs-lookup"><span data-stu-id="bf786-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="bf786-140">Visual Studio에서 프로필 메모리 사용량</span><span class="sxs-lookup"><span data-stu-id="bf786-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="bf786-141">메모리 문제 감지</span><span class="sxs-lookup"><span data-stu-id="bf786-141">Detecting memory issues</span></span>

<span data-ttu-id="bf786-142">작업 관리자는 ASP.NET 앱이 사용하는 메모리의 양을 파악하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="bf786-143">작업 관리자 메모리 값:</span><span class="sxs-lookup"><span data-stu-id="bf786-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="bf786-144">ASP.NET 프로세스에서 사용되는 메모리 양을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="bf786-145">앱의 살아있는 개체 및 네이티브 메모리 사용과 같은 기타 메모리 소비자를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="bf786-146">작업 관리자 메모리 값이 무기한 증가하고 평평해지지 않으면 앱에 메모리 누수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="bf786-147">다음 섹션에서는 여러 메모리 사용 패턴을 보여 주며 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="bf786-148">샘플 디스플레이 메모리 사용 앱</span><span class="sxs-lookup"><span data-stu-id="bf786-148">Sample display memory usage app</span></span>

<span data-ttu-id="bf786-149">[메모리리크 샘플 앱은](https://github.com/sebastienros/memoryleak) GitHub에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="bf786-150">메모리 리크 응용 프로그램 :</span><span class="sxs-lookup"><span data-stu-id="bf786-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="bf786-151">앱에 대한 실시간 메모리 및 GC 데이터를 수집하는 진단 컨트롤러가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="bf786-152">메모리 및 GC 데이터를 표시하는 인덱스 페이지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="bf786-153">인덱스 페이지는 매초 새로 고쳐집니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="bf786-154">다양한 메모리 로드 패턴을 제공하는 API 컨트롤러가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="bf786-155">지원되는 도구는 아니지만 ASP.NET 핵심 앱의 메모리 사용 패턴을 표시하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="bf786-156">메모리 리크를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-156">Run MemoryLeak.</span></span> <span data-ttu-id="bf786-157">할당된 메모리는 GC가 발생할 때까지 천천히 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="bf786-158">도구가 데이터를 캡처하기 위해 사용자 지정 개체를 할당하기 때문에 메모리가 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="bf786-159">다음 이미지는 Gen 0 GC가 발생할 때 MemoryLeak 인덱스 페이지를 보여 주며 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="bf786-160">API 컨트롤러의 API 끝점이 호출되지 않으므로 차트에는 0 RPS(초당 요청 수)가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![이전 차트](memory/_static/0RPS.png)

<span data-ttu-id="bf786-162">차트에는 메모리 사용량에 대한 두 가지 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="bf786-163">할당: 관리되는 개체가 차지하는 메모리 양</span><span class="sxs-lookup"><span data-stu-id="bf786-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="bf786-164">[작업 집합](/windows/win32/memory/working-set): 현재 실제 메모리에 상주하는 프로세스의 가상 주소 공간에 있는 페이지 집합입니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="bf786-165">표시된 작업 집합은 작업 관리자가 표시하는 것과 동일한 값입니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="bf786-166">일시적인 개체</span><span class="sxs-lookup"><span data-stu-id="bf786-166">Transient objects</span></span>

<span data-ttu-id="bf786-167">다음 API는 10KB 문자열 인스턴스를 만들고 클라이언트에 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="bf786-168">각 요청에서 새 개체가 메모리에 할당되고 응답에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="bf786-169">문자열은 .NET에 UTF-16 문자로 저장되므로 각 문자는 메모리에 2바이트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="bf786-170">다음 그래프는 상대적으로 적은 부하로 생성되어 메모리 할당이 GC에 의해 어떻게 영향을 받는지 보여 준다.</span><span class="sxs-lookup"><span data-stu-id="bf786-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![이전 차트](memory/_static/bigstring.png)

<span data-ttu-id="bf786-172">앞의 차트는 다음을 보여 주며, 다음을 보여 주면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-172">The preceding chart shows:</span></span>

* <span data-ttu-id="bf786-173">4K RPS(초당 요청).</span><span class="sxs-lookup"><span data-stu-id="bf786-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="bf786-174">세대 0 GC 컬렉션은 약 2초마다 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="bf786-175">작업 집합은 약 500MB에서 일정합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="bf786-176">CPU는 12%입니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-176">CPU is 12%.</span></span>
* <span data-ttu-id="bf786-177">GC를 통한 메모리 소비 및 릴리스는 안정적입니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="bf786-178">다음 차트는 컴퓨터에서 처리할 수 있는 최대 처리량에서 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![이전 차트](memory/_static/bigstring2.png)

<span data-ttu-id="bf786-180">앞의 차트는 다음을 보여 주며, 다음을 보여 주면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-180">The preceding chart shows:</span></span>

* <span data-ttu-id="bf786-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="bf786-181">22K RPS</span></span>
* <span data-ttu-id="bf786-182">0세대 GC 컬렉션은 초당 여러 번 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="bf786-183">앱이 초당 훨씬 더 많은 메모리를 할당했기 때문에 1세대 컬렉션이 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="bf786-184">작업 집합은 약 500MB에서 일정합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="bf786-185">CPU는 33%입니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-185">CPU is 33%.</span></span>
* <span data-ttu-id="bf786-186">GC를 통한 메모리 소비 및 릴리스는 안정적입니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="bf786-187">CPU (33%) 과도하게 사용되지 않으므로 가비지 수집은 많은 수의 할당을 따라갈 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="bf786-188">워크스테이션 GC 대 서버 GC</span><span class="sxs-lookup"><span data-stu-id="bf786-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="bf786-189">.NET 가비지 수집기에는 두 가지 모드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="bf786-190">**워크스테이션 GC**: 데스크탑에 최적화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="bf786-191">**서버 GC**.</span><span class="sxs-lookup"><span data-stu-id="bf786-191">**Server GC**.</span></span> <span data-ttu-id="bf786-192">ASP.NET 코어 앱의 기본 GC입니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="bf786-193">서버에 최적화되었습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-193">Optimized for the server.</span></span>

<span data-ttu-id="bf786-194">GC 모드는 프로젝트 파일 또는 게시된 앱의 *runtimeconfig.json* 파일에서 명시적으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="bf786-195">다음 태그는 프로젝트 `ServerGarbageCollection` 파일의 설정을 보여 주며 다음과 같은 태그를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="bf786-196">프로젝트 `ServerGarbageCollection` 파일을 변경하려면 앱을 다시 빌드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="bf786-197">**참고:** 단일 코어가 있는 컴퓨터에서는 서버 가비지 수집을 사용할 수 **없습니다.**</span><span class="sxs-lookup"><span data-stu-id="bf786-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="bf786-198">자세한 내용은 <xref:System.Runtime.GCSettings.IsServerGC>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bf786-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="bf786-199">다음 이미지는 워크스테이션 GC를 사용하여 5K RPS 아래의 메모리 프로파일을 보여 주며, 이 에 대한 이미지입니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![이전 차트](memory/_static/workstation.png)

<span data-ttu-id="bf786-201">이 차트와 서버 버전간의 차이점은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="bf786-202">작업 세트는 500MB에서 70MB로 떨어집니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="bf786-203">GC는 2초마다 가만쓰는 대신 초당 0세대 컬렉션을 여러 번 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="bf786-204">GC는 300MB에서 10MB로 떨어집니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="bf786-205">일반적인 웹 서버 환경에서는 CPU 사용량이 메모리보다 중요하므로 서버 GC가 더 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="bf786-206">메모리 사용률이 높고 CPU 사용량이 상대적으로 낮은 경우 워크스테이션 GC가 더 성능이 향상될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="bf786-207">예를 들어 메모리가 부족한 여러 웹 앱을 고밀도로 호스팅합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="bf786-208">도커 및 소형 컨테이너를 사용하는 GC</span><span class="sxs-lookup"><span data-stu-id="bf786-208">GC using Docker and small containers</span></span>

<span data-ttu-id="bf786-209">하나의 컴퓨터에서 여러 컨테이너화된 앱이 실행되는 경우 워크스테이션 GC가 서버 GC보다 더 많은 프리폼일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="bf786-210">자세한 내용은 [작은 컨테이너에서 서버 GC로 실행하고 작은 컨테이너](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) 시나리오 [1부에서 서버 GC로 실행 - GC 힙에 대한 하드 제한을](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="bf786-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="bf786-211">영구 개체 참조</span><span class="sxs-lookup"><span data-stu-id="bf786-211">Persistent object references</span></span>

<span data-ttu-id="bf786-212">GC는 참조되는 개체를 해제할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="bf786-213">참조되지만 더 이상 필요하지 않은 개체는 메모리 누수로 이어집니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="bf786-214">앱에서 개체를 자주 할당하고 개체가 더 이상 필요하지 않은 후 개체를 해제하지 못하면 시간이 지남에 따라 메모리 사용량이 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="bf786-215">다음 API는 10KB 문자열 인스턴스를 만들고 클라이언트에 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="bf786-216">이전 예제와의 차이점은 이 인스턴스가 정적 멤버에서 참조된다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

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

<span data-ttu-id="bf786-217">위의 코드:</span><span class="sxs-lookup"><span data-stu-id="bf786-217">The preceding code:</span></span>

* <span data-ttu-id="bf786-218">일반적인 메모리 누수의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="bf786-219">빈번한 호출을 사용하면 프로세스가 예외로 `OutOfMemory` 충돌할 때까지 앱 메모리가 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![이전 차트](memory/_static/eternal.png)

<span data-ttu-id="bf786-221">앞의 이미지에서:</span><span class="sxs-lookup"><span data-stu-id="bf786-221">In the preceding image:</span></span>

* <span data-ttu-id="bf786-222">엔드포인트를 `/api/staticstring` 부하 테스트하면 메모리가 선형으로 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="bf786-223">GC는 2세대 컬렉션을 호출하여 메모리 압력이 증가함에 따라 메모리를 확보하려고 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="bf786-224">GC는 유출된 메모리를 해제할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="bf786-225">할당된 작업 집합은 시간이 지남에 따라 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="bf786-226">캐싱과 같은 일부 시나리오에서는 메모리 압력으로 인해 개체참조가 해제될 때까지 개체 참조를 유지해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="bf786-227"><xref:System.WeakReference> 이 유형의 캐싱 코드에 클래스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="bf786-228">개체는 `WeakReference` 메모리 압력하에서 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="bf786-229">사용의 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> `WeakReference`기본 구현 .</span><span class="sxs-lookup"><span data-stu-id="bf786-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="bf786-230">네이티브 메모리</span><span class="sxs-lookup"><span data-stu-id="bf786-230">Native memory</span></span>

<span data-ttu-id="bf786-231">일부 .NET Core 개체는 기본 메모리에 의존합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="bf786-232">네이티브 메모리는 GC에 의해 수집될 수 **없습니다.**</span><span class="sxs-lookup"><span data-stu-id="bf786-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="bf786-233">네이티브 메모리를 사용하는 .NET 개체는 네이티브 코드를 사용하여 해제해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="bf786-234">.NET은 <xref:System.IDisposable> 개발자가 네이티브 메모리를 해제할 수 있도록 하는 인터페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="bf786-235"><xref:System.IDisposable.Dispose*> 호출되지 않더라도 [종료자가](/dotnet/csharp/programming-guide/classes-and-structs/destructors) 실행될 때 올바르게 구현된 클래스가 호출됩니다. `Dispose`</span><span class="sxs-lookup"><span data-stu-id="bf786-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="bf786-236">다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="bf786-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="bf786-237">[PhysicalFileProvider는](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) 관리되는 클래스이므로 모든 인스턴스는 요청이 끝날 때 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="bf786-238">다음 이미지는 `fileprovider` API를 계속 호출하는 동안 메모리 프로필을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![이전 차트](memory/_static/fileprovider.png)

<span data-ttu-id="bf786-240">앞의 차트에서는 메모리 사용량이 계속 증가하므로 이 클래스의 구현에 대한 명백한 문제를 보여 준다.</span><span class="sxs-lookup"><span data-stu-id="bf786-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="bf786-241">[이 문제에서](https://github.com/dotnet/aspnetcore/issues/3110)추적 중인 알려진 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="bf786-242">사용자 코드에서 다음 중 하나에 의해 동일한 누설이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="bf786-243">클래스를 올바르게 해제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="bf786-244">삭제해야 하는 종속 `Dispose`개체의 메서드를 호출하는 것을 잊어버릅니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="bf786-245">큰 개체 힙</span><span class="sxs-lookup"><span data-stu-id="bf786-245">Large objects heap</span></span>

<span data-ttu-id="bf786-246">빈번한 메모리 할당/사용 비 주기는 특히 큰 메모리 덩어리를 할당할 때 메모리를 조각화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="bf786-247">개체는 연속된 메모리 블록에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="bf786-248">조각화를 완화하기 위해 GC가 메모리를 해제하면 조각 모음을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-248">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="bf786-249">이 프로세스를 **압축이라고**합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-249">This process is called **compaction**.</span></span> <span data-ttu-id="bf786-250">다짐은 움직이는 물체를 포함한다.</span><span class="sxs-lookup"><span data-stu-id="bf786-250">Compaction involves moving objects.</span></span> <span data-ttu-id="bf786-251">큰 개체를 이동하면 성능이 저하됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="bf786-252">이러한 이유로 GC는 _큰_ 개체 힙(LOH)이라고 하는 큰 개체에 대한 특수 메모리 영역을 만듭니다. [large object heap](/dotnet/standard/garbage-collection/large-object-heap)</span><span class="sxs-lookup"><span data-stu-id="bf786-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="bf786-253">85,000바이트(약 83KB)를 초과하는 개체는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="bf786-254">LOH에 놓습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-254">Placed on the LOH.</span></span>
* <span data-ttu-id="bf786-255">압축되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-255">Not compacted.</span></span>
* <span data-ttu-id="bf786-256">2세대 GC 동안 수집됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="bf786-257">LOH가 가득 차면 GC는 2세대 컬렉션을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="bf786-258">2세대 컬렉션:</span><span class="sxs-lookup"><span data-stu-id="bf786-258">Generation 2 collections:</span></span>

* <span data-ttu-id="bf786-259">본질적으로 느립니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-259">Are inherently slow.</span></span>
* <span data-ttu-id="bf786-260">또한 다른 모든 세대에서 컬렉션을 트리거하는 비용이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="bf786-261">다음 코드는 LOH를 즉시 압축합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="bf786-262">LOH 압축에 대한 자세한 내용은 를 참조하십시오. <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode></span><span class="sxs-lookup"><span data-stu-id="bf786-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="bf786-263">.NET Core 3.0 이상을 사용하는 컨테이너에서는 LOH가 자동으로 압축됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="bf786-264">이 동작을 보여 주는 다음 API는 다음과 같은</span><span class="sxs-lookup"><span data-stu-id="bf786-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="bf786-265">다음 차트는 최대 부하에서 `/api/loh/84975` 끝점을 호출하는 메모리 프로필을 보여 주며 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![이전 차트](memory/_static/loh1.png)

<span data-ttu-id="bf786-267">다음 차트는 끝점을 호출하는 `/api/loh/84976` 메모리 프로필을 보여 주며, *한 바이트만 더 할당합니다.*</span><span class="sxs-lookup"><span data-stu-id="bf786-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![이전 차트](memory/_static/loh2.png)

<span data-ttu-id="bf786-269">참고: `byte[]` 구조에 오버헤드 바이트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="bf786-270">84,976바이트가 85,000개의 제한을 트리거하는 이유입니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="bf786-271">앞의 두 차트 비교:</span><span class="sxs-lookup"><span data-stu-id="bf786-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="bf786-272">작업 집합은 두 시나리오 모두 약 450MB와 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="bf786-273">LOH 요청(84,975바이트)은 대부분 0세대 컬렉션을 보여 주며,</span><span class="sxs-lookup"><span data-stu-id="bf786-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="bf786-274">OVER LOH 요청은 상수 세대 2 컬렉션을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="bf786-275">2세대 컬렉션은 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="bf786-276">더 많은 CPU가 필요하며 처리량은 거의 50% 감소합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="bf786-277">임시 큰 개체는 gen2 GC를 유발하기 때문에 특히 문제가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="bf786-278">성능을 극대화하기 위해 큰 개체 사용을 최소화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="bf786-279">가능하면 큰 개체를 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-279">If possible, split up large objects.</span></span> <span data-ttu-id="bf786-280">예를 들어 ASP.NET Core의 [응답 캐싱](xref:performance/caching/response) 미들웨어는 캐시 항목을 85,000바이트 미만의 블록으로 분할합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="bf786-281">다음 링크는 loH 제한 하에 개체를 유지하는 ASP.NET Core 접근 방식을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은</span><span class="sxs-lookup"><span data-stu-id="bf786-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="bf786-282">응답캐칭/스트림/스트림유틸리티.cs</span><span class="sxs-lookup"><span data-stu-id="bf786-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="bf786-283">응답캐칭/메모리응답캐시.cs</span><span class="sxs-lookup"><span data-stu-id="bf786-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="bf786-284">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="bf786-284">For more information, see:</span></span>

* [<span data-ttu-id="bf786-285">큰 개체 힙 이 발견</span><span class="sxs-lookup"><span data-stu-id="bf786-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="bf786-286">큰 개체 힙</span><span class="sxs-lookup"><span data-stu-id="bf786-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="bf786-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="bf786-287">HttpClient</span></span>

<span data-ttu-id="bf786-288">잘못 사용하면 <xref:System.Net.Http.HttpClient> 리소스가 누출될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="bf786-289">데이터베이스 연결, 소켓, 파일 핸들 등과 같은 시스템 리소스:</span><span class="sxs-lookup"><span data-stu-id="bf786-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="bf786-290">메모리보다 부족합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="bf786-291">메모리보다 누수될 때 더 문제가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="bf786-292">경험이 풍부한 .NET 개발자는 을 <xref:System.IDisposable.Dispose*> <xref:System.IDisposable>구현하는 개체를 호출하는 방법을 알고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="bf786-293">일반적으로 구현하는 `IDisposable` 개체를 삭제하지 않아 메모리가 유출되거나 시스템 리소스가 유출됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="bf786-294">`HttpClient``IDisposable`구현하지만 모든 호출에 **삭제해서는** 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="bf786-295">오히려 `HttpClient` 다시 사용해야합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="bf786-296">다음 끝점은 모든 요청에 대해 `HttpClient` 새 인스턴스를 만들고 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

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

<span data-ttu-id="bf786-297">로드 시 다음과 같은 오류 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-297">Under load, the following error messages are logged:</span></span>

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

<span data-ttu-id="bf786-298">인스턴스가 `HttpClient` 삭제되더라도 실제 네트워크 연결은 운영 체제에서 해제하는 데 다소 시간이 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="bf786-299">새 연결을 지속적으로 생성하면 _포트가 고갈됩니다._</span><span class="sxs-lookup"><span data-stu-id="bf786-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="bf786-300">각 클라이언트 연결에는 자체 클라이언트 포트가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="bf786-301">포트 소모를 방지하는 한 가지 방법은 동일한 `HttpClient` 인스턴스를 다시 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="bf786-302">앱이 `HttpClient` 중지되면 인스턴스가 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="bf786-303">이 예제에서는 모든 일회용 리소스를 사용할 때마다 삭제해야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="bf786-304">인스턴스의 수명을 처리하는 더 나은 방법은 `HttpClient` 다음을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="bf786-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="bf786-305">HttpClient 및 수명 관리</span><span class="sxs-lookup"><span data-stu-id="bf786-305">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="bf786-306">HTTP클라이언트 팩토리 블로그</span><span class="sxs-lookup"><span data-stu-id="bf786-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="bf786-307">개체 풀링</span><span class="sxs-lookup"><span data-stu-id="bf786-307">Object pooling</span></span>

<span data-ttu-id="bf786-308">이전 예제에서는 인스턴스를 `HttpClient` 정적으로 만들고 모든 요청에서 다시 사용할 수 있는 방법을 보여 주어 도 했습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="bf786-309">재사용은 리소스 가 부족하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="bf786-310">개체 풀링:</span><span class="sxs-lookup"><span data-stu-id="bf786-310">Object pooling:</span></span>

* <span data-ttu-id="bf786-311">재사용 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="bf786-312">만드는 데 비용이 많이 드는 개체를 위해 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="bf786-313">풀은 스레드 간에 예약및 해제할 수 있는 미리 초기화된 개체의 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="bf786-314">풀은 제한, 미리 정의된 크기 또는 증가율과 같은 할당 규칙을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="bf786-315">NuGet 패키지 [Microsoft.Extensions.ObjectPool에는](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) 이러한 풀을 관리하는 데 도움이 되는 클래스가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="bf786-316">다음 API 끝점은 각 요청에 `byte` 난수로 채워진 버퍼를 인스턴스화합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

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

<span data-ttu-id="bf786-317">다음 차트 표시는 중간 로드를 가진 앞의 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-317">The following chart display calling the preceding API with moderate load:</span></span>

![이전 차트](memory/_static/array.png)

<span data-ttu-id="bf786-319">앞의 차트에서 0세대 컬렉션은 초당 약 한 번 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="bf786-320">위의 코드는 [ArrayPool\<T ](xref:System.Buffers.ArrayPool`1)>`byte` 사용하여 버퍼를 풀링하여 최적화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="bf786-321">정적 인스턴스는 요청 간에 다시 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="bf786-322">이 방법의 다른 점은 풀이 된 개체가 API에서 반환된다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="bf786-323">즉, 다음을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-323">That means:</span></span>

* <span data-ttu-id="bf786-324">메서드에서 돌아오면 개체가 컨트롤을 벗어났습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="bf786-325">개체를 해제할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-325">You can't release the object.</span></span>

<span data-ttu-id="bf786-326">개체의 처리를 설정하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="bf786-327">풀린 배열을 일회용 개체에 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="bf786-328">[HttpContext.Response.RegisterForDispose.](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)</span><span class="sxs-lookup"><span data-stu-id="bf786-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="bf786-329">`RegisterForDispose`은 HTTP 요청이 완료될 때만 해제되도록 대상 개체에 대한 호출을 `Dispose`처리합니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

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

<span data-ttu-id="bf786-330">풀링되지 않은 버전과 동일한 하중을 적용하면 다음 차트가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![이전 차트](memory/_static/pooledarray.png)

<span data-ttu-id="bf786-332">주요 차이점은 바이트가 할당되고 결과적으로 0세대 컬렉션이 훨씬 줄어듭니다.</span><span class="sxs-lookup"><span data-stu-id="bf786-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bf786-333">추가 자료</span><span class="sxs-lookup"><span data-stu-id="bf786-333">Additional resources</span></span>

* [<span data-ttu-id="bf786-334">가비지 수집</span><span class="sxs-lookup"><span data-stu-id="bf786-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="bf786-335">동시성 시각화 도우미를 통해 다양한 GC 모드 이해</span><span class="sxs-lookup"><span data-stu-id="bf786-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="bf786-336">큰 개체 힙 이 발견</span><span class="sxs-lookup"><span data-stu-id="bf786-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="bf786-337">큰 개체 힙</span><span class="sxs-lookup"><span data-stu-id="bf786-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
