---
title: ASP.NET Core Blazor 호스팅 모델 구성
author: guardrex
description: ASP.NET Core Blazor 호스팅 모델 구성의 추가 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b28e4e43b88fcf8eab9e8959142cca21223c57ff
ms.sourcegitcommit: e216e8f4afa21215dc38124c28d5ee19f5ed7b1e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86239636"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="801d0-103">ASP.NET Core Blazor 호스팅 모델 구성</span><span class="sxs-lookup"><span data-stu-id="801d0-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="801d0-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="801d0-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="801d0-105">이 문서에서는 호스팅 모델 구성에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-105">This article covers hosting model configuration.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a>SignalR<span data-ttu-id="801d0-106"> 인증에 대한 원본 간 협상</span><span class="sxs-lookup"><span data-stu-id="801d0-106"> cross-origin negotiation for authentication</span></span>

<span data-ttu-id="801d0-107">‘이 섹션은 Blazor WebAssembly에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="801d0-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="801d0-108">쿠키 또는 HTTP 인증 헤더와 같은 자격 증명을 보내도록 SignalR의 기본 클라이언트를 구성하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="801d0-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>를 사용하여 원본 간 [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) 요청에 <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include>를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="801d0-110"><xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> 옵션에 <xref:System.Net.Http.HttpMessageHandler>를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="801d0-111">자세한 내용은 <xref:signalr/configuration#configure-additional-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="801d0-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="801d0-112">UI에 연결 상태 반영</span><span class="sxs-lookup"><span data-stu-id="801d0-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="801d0-113">‘이 섹션은 Blazor Server에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="801d0-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="801d0-114">클라이언트에서 연결이 끊어진 것을 감지하면, 클라이언트가 다시 연결하는 동안 기본 UI가 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="801d0-115">다시 연결하지 못한 경우 사용자에게 다시 시도 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="801d0-116">UI를 사용자 지정하려면 `_Host.cshtml` Razor 페이지의 `<body>`에서 `components-reconnect-modal`의 `id`를 사용하여 요소를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="801d0-117">앱의 스타일 시트(`wwwroot/css/app.css` 또는 `wwwroot/css/site.css`)에 다음을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="801d0-118">다음 표에서는 `components-reconnect-modal` 요소에 적용된 CSS 클래스에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="801d0-119">CSS 클래스</span><span class="sxs-lookup"><span data-stu-id="801d0-119">CSS class</span></span>                       | <span data-ttu-id="801d0-120">표시&hellip;</span><span class="sxs-lookup"><span data-stu-id="801d0-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="801d0-121">연결이 끊어졌습니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-121">A lost connection.</span></span> <span data-ttu-id="801d0-122">클라이언트가 다시 연결하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="801d0-123">모달을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="801d0-124">서버에 대해 활성 연결이 다시 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="801d0-125">모달을 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="801d0-126">네트워크 오류로 인해 다시 연결하지 못했습니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="801d0-127">다시 연결하려면 `window.Blazor.reconnect()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="801d0-128">다시 연결이 거부되었습니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-128">Reconnection rejected.</span></span> <span data-ttu-id="801d0-129">서버에 접근했지만 연결이 거부되었으며, 서버의 사용자 상태가 손실되었습니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="801d0-130">앱을 다시 로드하려면 `location.reload()`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="801d0-131">이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="801d0-132">서버 쪽 회로에서 크래시가 발생한 경우</span><span class="sxs-lookup"><span data-stu-id="801d0-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="801d0-133">서버에서 사용자 상태를 삭제하기에 충분한 기간에 클라이언트 연결이 끊긴 경우.</span><span class="sxs-lookup"><span data-stu-id="801d0-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="801d0-134">사용자가 조작 중인 구성 요소 인스턴스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="801d0-135">서버가 다시 시작되었거나, 앱의 작업자 프로세스가 재활용된 경우</span><span class="sxs-lookup"><span data-stu-id="801d0-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="801d0-136">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="801d0-136">Render mode</span></span>

<span data-ttu-id="801d0-137">‘이 섹션은 Blazor Server에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="801d0-137">*This section applies to Blazor Server.*</span></span>

Blazor Server<span data-ttu-id="801d0-138"> 앱은 기본적으로 클라이언트가 서버에 연결되기 전에 서버에서 UI를 미리 렌더링하도록 설정되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-138"> apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="801d0-139">이는 `_Host.cshtml` Razor 페이지에서 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="801d0-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>는 구성 요소에 대해 다음을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="801d0-141">페이지에 미리 렌더링할지 여부</span><span class="sxs-lookup"><span data-stu-id="801d0-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="801d0-142">페이지에 정적 HTML로 렌더링할지 여부 또는 사용자 에이전트에서 Blazor 앱을 부트스트랩하는 데 필요한 정보를 포함할지 여부</span><span class="sxs-lookup"><span data-stu-id="801d0-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="801d0-143">렌더링 모드</span><span class="sxs-lookup"><span data-stu-id="801d0-143">Render mode</span></span> | <span data-ttu-id="801d0-144">설명</span><span class="sxs-lookup"><span data-stu-id="801d0-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="801d0-145">구성 요소를 정적 HTML에 렌더링하고 Blazor Server 앱의 마커를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="801d0-146">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="801d0-147">Blazor Server 앱의 마커를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="801d0-148">구성 요소의 출력은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-148">Output from the component isn't included.</span></span> <span data-ttu-id="801d0-149">사용자 에이전트를 시작할 때 이 표식은 Blazor 앱을 부트스트랩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="801d0-150">구성 요소를 정적 HTML에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="801d0-151">정적 HTML 페이지에서 서버 구성 요소를 렌더링할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="801d0-152">Blazor Server 앱에 적합하게 SignalR 클라이언트 구성</span><span class="sxs-lookup"><span data-stu-id="801d0-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="801d0-153">‘이 섹션은 Blazor Server에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="801d0-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="801d0-154">`Pages/_Host.cshtml` 파일에서 Blazor Server 앱이 사용하는 SignalR 클라이언트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-154">Configure the SignalR client used by Blazor Server apps in the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="801d0-155">`Blazor.start`를 호출하는 스크립트를 `_framework/blazor.server.js` 스크립트 뒤, `</body>` 태그 안에 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-155">Place a script that calls `Blazor.start` after the `_framework/blazor.server.js` script and inside the `</body>` tag.</span></span>

### <a name="logging"></a><span data-ttu-id="801d0-156">로깅</span><span class="sxs-lookup"><span data-stu-id="801d0-156">Logging</span></span>

<span data-ttu-id="801d0-157">SignalR 클라이언트 로깅을 구성하려면</span><span class="sxs-lookup"><span data-stu-id="801d0-157">To configure SignalR client logging:</span></span>

* <span data-ttu-id="801d0-158">`blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-158">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="801d0-159">클라이언트 작성기에서 로그 수준을 사용하여 `configureLogging`을 호출하는 구성 개체(`configureSignalR`)를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-159">Pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder.</span></span>

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="801d0-160">앞의 예에서 `information`은 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>의 로그 수준과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="801d0-161">다시 연결 처리기 수정</span><span class="sxs-lookup"><span data-stu-id="801d0-161">Modify the reconnection handler</span></span>

<span data-ttu-id="801d0-162">다음과 같은 사용자 지정 동작에 대한 다시 연결 처리기의 회로 연결 이벤트를 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="801d0-163">연결이 삭제되는 경우 사용자에게 알립니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="801d0-164">회로가 연결된 경우 클라이언트에서 로깅을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="801d0-165">연결 이벤트를 수정하려면</span><span class="sxs-lookup"><span data-stu-id="801d0-165">To modify the connection events:</span></span>

* <span data-ttu-id="801d0-166">`blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-166">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="801d0-167">삭제된 연결(`onConnectionDown`) 및 설정/다시 설정된 연결(`onConnectionUp`)의 연결 변경에 대한 콜백을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-167">Register callbacks for connection changes for dropped connections (`onConnectionDown`) and established/re-established connections (`onConnectionUp`).</span></span> <span data-ttu-id="801d0-168">`onConnectionDown`과 `onConnectionUp`을 **모두** 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified.</span></span>

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="801d0-169">다시 연결 다시 시도 횟수 및 간격 조정</span><span class="sxs-lookup"><span data-stu-id="801d0-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="801d0-170">다시 연결 다시 시도 횟수 및 간격을 조정하려면</span><span class="sxs-lookup"><span data-stu-id="801d0-170">To adjust the reconnection retry count and interval:</span></span>

* <span data-ttu-id="801d0-171">`blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-171">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="801d0-172">다시 시도 횟수(`maxRetries`)와 각 다시 시도에 허용되는 기간(밀리초)(`retryIntervalMilliseconds`)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-172">Set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

### <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="801d0-173">다시 연결 표시 숨기기 또는 바꾸기</span><span class="sxs-lookup"><span data-stu-id="801d0-173">Hide or replace the reconnection display</span></span>

<span data-ttu-id="801d0-174">다시 연결 표시를 숨기려면</span><span class="sxs-lookup"><span data-stu-id="801d0-174">To hide the reconnection display:</span></span>

* <span data-ttu-id="801d0-175">`blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-175">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="801d0-176">다시 연결 처리기의 `_reconnectionDisplay`를 빈 개체(`{}` 또는 `new Object()`)로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-176">Set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

```cshtml
    ...

    <script src="_framework/blazor.server.js" autostart="false"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

<span data-ttu-id="801d0-177">다시 연결 표시를 바꾸려면 앞의 예에서 `_reconnectionDisplay`를 표시할 요소로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-177">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="801d0-178">자리 표시자 `{ELEMENT ID}`는 표시할 HTML 요소의 ID입니다.</span><span class="sxs-lookup"><span data-stu-id="801d0-178">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="801d0-179">추가 자료</span><span class="sxs-lookup"><span data-stu-id="801d0-179">Additional resources</span></span>

* <xref:fundamentals/logging/index>
