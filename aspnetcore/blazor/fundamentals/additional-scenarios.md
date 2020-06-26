---
title: ASP.NET Core Blazor 호스팅 모델 구성
author: guardrex
description: ASP.NET Core Blazor 호스팅 모델 구성의 추가 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 72a8b59b06e40f6f85abe41217ae564f82c8d89c
ms.sourcegitcommit: 1833870ad0845326fb764fef1b530a07b9b5b099
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2020
ms.locfileid: "85347075"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="2f46f-103">ASP.NET Core Blazor 호스팅 모델 구성</span><span class="sxs-lookup"><span data-stu-id="2f46f-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="2f46f-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2f46f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2f46f-105">이 문서에서는 호스팅 모델 구성에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="2f46f-106"> 인증에 대한 원본 간 협상</span><span class="sxs-lookup"><span data-stu-id="2f46f-106"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="2f46f-107">‘이 섹션은 Blazor WebAssembly에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="2f46f-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="2f46f-108">쿠키 또는 HTTP 인증 헤더와 같은 자격 증명을 보내도록 SignalR의 기본 클라이언트를 구성하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="2f46f-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>를 사용하여 원본 간 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 요청에 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="2f46f-110"><xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 옵션에 <xref:System.Net.Http.HttpMessageHandler>를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="2f46f-111">자세한 내용은 <xref:signalr/configuration#configure-additional-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2f46f-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="2f46f-112">UI에 연결 상태 반영</span><span class="sxs-lookup"><span data-stu-id="2f46f-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="2f46f-113">‘이 섹션은 Blazor Server에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="2f46f-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="2f46f-114">클라이언트에서 연결이 끊어진 것을 감지하면, 클라이언트가 다시 연결하는 동안 기본 UI가 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="2f46f-115">다시 연결하지 못한 경우 사용자에게 다시 시도 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="2f46f-116">UI를 사용자 지정하려면 `_Host.cshtml` Razor 페이지의 `<body>`에서 `components-reconnect-modal`의 `id`를 사용하여 요소를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="2f46f-117">다음 표에서는 `components-reconnect-modal` 요소에 적용된 CSS 클래스에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-117">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="2f46f-118">CSS 클래스</span><span class="sxs-lookup"><span data-stu-id="2f46f-118">CSS class</span></span>                       | <span data-ttu-id="2f46f-119">표시&hellip;</span><span class="sxs-lookup"><span data-stu-id="2f46f-119">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="2f46f-120">연결이 끊어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-120">A lost connection.</span></span> <span data-ttu-id="2f46f-121">클라이언트가 다시 연결하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-121">The client is attempting to reconnect.</span></span> <span data-ttu-id="2f46f-122">모달을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-122">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="2f46f-123">서버에 대해 활성 연결이 다시 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-123">An active connection is re-established to the server.</span></span> <span data-ttu-id="2f46f-124">모달을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-124">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="2f46f-125">네트워크 오류로 인해 다시 연결하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-125">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="2f46f-126">다시 연결하려면 `window.Blazor.reconnect()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-126">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="2f46f-127">다시 연결이 거부되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-127">Reconnection rejected.</span></span> <span data-ttu-id="2f46f-128">서버에 접근했지만 연결이 거부되었으며, 서버의 사용자 상태가 손실되었습니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-128">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="2f46f-129">앱을 다시 로드하려면 `location.reload()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-129">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="2f46f-130">이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-130">This connection state may result when:</span></span><ul><li><span data-ttu-id="2f46f-131">서버 쪽 회로에서 크래시가 발생한 경우</span><span class="sxs-lookup"><span data-stu-id="2f46f-131">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="2f46f-132">서버에서 사용자 상태를 삭제하기에 충분한 기간에 클라이언트 연결이 끊긴 경우.</span><span class="sxs-lookup"><span data-stu-id="2f46f-132">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="2f46f-133">사용자가 조작 중인 구성 요소 인스턴스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-133">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="2f46f-134">서버가 다시 시작되었거나, 앱의 작업자 프로세스가 재활용된 경우</span><span class="sxs-lookup"><span data-stu-id="2f46f-134">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="2f46f-135">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="2f46f-135">Render mode</span></span>

<span data-ttu-id="2f46f-136">‘이 섹션은 Blazor Server에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="2f46f-136">*This section applies to Blazor Server.*</span></span>

Blazor<span data-ttu-id="2f46f-137"> 서버 앱은 기본적으로 클라이언트가 서버에 연결되기 전에 서버에서 UI를 미리 렌더링하도록 설정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-137"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="2f46f-138">이는 `_Host.cshtml` Razor 페이지에서 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-138">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="2f46f-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>는 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-139"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="2f46f-140">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="2f46f-140">Is prerendered into the page.</span></span>
* <span data-ttu-id="2f46f-141">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="2f46f-141">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="2f46f-142">설명</span><span class="sxs-lookup"><span data-stu-id="2f46f-142">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="2f46f-143">구성 요소를 정적 HTML에 렌더링하고 Blazor 서버 앱의 표식을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-143">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="2f46f-144">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-144">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="2f46f-145">Blazor 서버 앱의 표식을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-145">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="2f46f-146">구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-146">Output from the component isn't included.</span></span> <span data-ttu-id="2f46f-147">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-147">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="2f46f-148">구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-148">Renders the component into static HTML.</span></span> |

<span data-ttu-id="2f46f-149">정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-149">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="2f46f-150">Blazor 서버 앱에 적합하게 SignalR 클라이언트 구성</span><span class="sxs-lookup"><span data-stu-id="2f46f-150">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="2f46f-151">‘이 섹션은 Blazor Server에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="2f46f-151">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="2f46f-152">Blazor 서버 앱에서 사용하는 SignalR 클라이언트를 구성해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-152">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="2f46f-153">예를 들어 연결 문제를 진단하기 위해 SignalR 클라이언트에서 로깅을 구성하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-153">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="2f46f-154">`Pages/_Host.cshtml` 파일에서 SignalR 클라이언트를 구성하려면:</span><span class="sxs-lookup"><span data-stu-id="2f46f-154">To configure the SignalR client in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="2f46f-155">`blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="2f46f-156">`Blazor.start`를 호출하고 SignalR 작성기를 지정하는 구성 개체를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="2f46f-156">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="2f46f-157">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2f46f-157">Additional resources</span></span>

* <xref:fundamentals/logging/index>
