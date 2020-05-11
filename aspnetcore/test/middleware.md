---
title: ASP.NET Core 미들웨어 테스트
author: tratcher
description: TestServer를 사용하여 ASP.NET Core 미들웨어를 테스트하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: 06ff7167e32fbd613c18709e31ecd078b3dfc926
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876424"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="07469-103">ASP.NET Core 미들웨어 테스트</span><span class="sxs-lookup"><span data-stu-id="07469-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="07469-104">작성자: [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="07469-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="07469-105">미들웨어는 <xref:Microsoft.AspNetCore.TestHost.TestServer>와 격리하여 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07469-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="07469-106">다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07469-106">It allows you to:</span></span>

* <span data-ttu-id="07469-107">테스트해야 하는 구성 요소만 포함하는 앱 파이프라인을 인스턴스화합니다.</span><span class="sxs-lookup"><span data-stu-id="07469-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="07469-108">미들웨어 동작을 확인하는 사용자 지정 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="07469-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="07469-109">장점</span><span class="sxs-lookup"><span data-stu-id="07469-109">Advantages:</span></span>

* <span data-ttu-id="07469-110">요청은 네트워크를 통해 직렬화되는 것이 아니라 메모리 내에 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="07469-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="07469-111">이렇게 하면 포트 관리 및 HTTPS 인증서와 같은 추가 문제가 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="07469-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="07469-112">미들웨어의 예외는 호출하는 테스트로 직접 다시 이동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07469-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="07469-113">테스트에서 직접 <xref:Microsoft.AspNetCore.Http.HttpContext>와 같은 서버 데이터 구조를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07469-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="07469-114">TestServer 설정</span><span class="sxs-lookup"><span data-stu-id="07469-114">Set up the TestServer</span></span>

<span data-ttu-id="07469-115">테스트 프로젝트에서 테스트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07469-115">In the test project, create a test:</span></span>

* <span data-ttu-id="07469-116"><xref:Microsoft.AspNetCore.TestHost.TestServer>를 사용하는 호스트를 빌드하고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="07469-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="07469-117">미들웨어에서 사용하는 모든 필요한 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07469-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="07469-118">테스트에 미들웨어를 사용하도록 처리 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="07469-118">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="07469-119">HttpClient를 사용하여 요청 보내기</span><span class="sxs-lookup"><span data-stu-id="07469-119">Send requests with HttpClient</span></span>
<span data-ttu-id="07469-120"><xref:System.Net.Http.HttpClient>를 사용하여 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="07469-120">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="07469-121">결과를 어설션합니다.</span><span class="sxs-lookup"><span data-stu-id="07469-121">Assert the result.</span></span> <span data-ttu-id="07469-122">먼저 어설션을 원하는 결과의 반대로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07469-122">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="07469-123">가양성 어설션을 사용한 초기 실행에서는 미들웨어가 제대로 수행되는 동안 테스트가 실패하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="07469-123">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="07469-124">테스트를 실행하고 테스트가 실패하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="07469-124">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="07469-125">다음 예제에서 미들웨어는 루트 엔드포인트가 요청될 때 404 상태 코드(‘찾을 수 없음’)를 반환해야 합니다. </span><span class="sxs-lookup"><span data-stu-id="07469-125">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="07469-126">`Assert.NotEqual( ... );`을 사용하여 실패해야 하는 첫 번째 테스트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="07469-126">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="07469-127">정상적인 작동 조건에서 미들웨어를 테스트하도록 어설션을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="07469-127">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="07469-128">최종 테스트는 `Assert.Equal( ... );`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="07469-128">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="07469-129">테스트를 다시 실행하여 통과하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="07469-129">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="07469-130">HttpContext를 사용하여 요청 보내기</span><span class="sxs-lookup"><span data-stu-id="07469-130">Send requests with HttpContext</span></span>

<span data-ttu-id="07469-131">테스트 앱은 [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A)를 사용하여 요청을 보낼 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07469-131">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="07469-132">다음 예제에서는 미들웨어가 `https://example.com/A/Path/?and=query`를 처리할 때 몇 가지 항목이 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="07469-132">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<span data-ttu-id="07469-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>는 <xref:System.Net.Http.HttpClient> 추상화를 사용하는 대신 <xref:Microsoft.AspNetCore.Http.HttpContext> 개체를 직접 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07469-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="07469-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>를 사용하여 서버에서만 사용할 수 있는 구조를 조작합니다(예: [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) 또는 [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features)).</span><span class="sxs-lookup"><span data-stu-id="07469-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="07469-135">‘404 - 찾을 수 없음’ 응답을 테스트한 이전 예제와 마찬가지로 이전 테스트에서 각 `Assert` 문의 반대를 확인합니다. </span><span class="sxs-lookup"><span data-stu-id="07469-135">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="07469-136">이 확인에서는 미들웨어가 정상적으로 작동할 때 테스트가 제대로 실패하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="07469-136">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="07469-137">가양성 테스트가 작동하는지 확인한 후에는 테스트의 예상 조건 및 값에 관한 마지막 `Assert` 문을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07469-137">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="07469-138">테스트를 다시 실행하여 테스트가 통과하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="07469-138">Run it again to confirm that the test passes.</span></span>
