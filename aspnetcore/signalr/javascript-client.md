---
title: JavaScript SignalR 클라이언트 ASP.NET Core
author: bradygaster
description: JavaScript 클라이언트 ASP.NET Core SignalR 개요
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 966e8e171752edb230f6da82203fd901b0fdeaab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768944"
---
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="7aaf3-103">JavaScript SignalR 클라이언트 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7aaf3-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="7aaf3-104">작성자: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="7aaf3-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="7aaf3-105">개발자는 SignalR ASP.NET Core JavaScript 클라이언트 라이브러리를 사용 하 여 서버 쪽 허브 코드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="7aaf3-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7aaf3-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="7aaf3-107">클라이언트 패키지 SignalR 를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-107">Install the SignalR client package</span></span>

<span data-ttu-id="7aaf3-108">SignalR JavaScript 클라이언트 라이브러리는 [npm](https://www.npmjs.com/) 패키지로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="7aaf3-109">다음 섹션에서는 클라이언트 라이브러리를 설치 하는 다양 한 방법에 대해 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="7aaf3-110">Npm를 사용 하 여 설치</span><span class="sxs-lookup"><span data-stu-id="7aaf3-110">Install with npm</span></span>

<span data-ttu-id="7aaf3-111">Visual Studio를 사용 하는 경우 루트 폴더에 있는 동안 **패키지 관리자 콘솔** 에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="7aaf3-112">Visual Studio Code의 경우 **통합 터미널**에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="7aaf3-113">npm 패키지 콘텐츠를 \*node_modules\\ \* 폴더에 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="7aaf3-114">*Wwwroot\\lib* 폴더 아래에 *signalr* 라는 새 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="7aaf3-115">*Signalr* 파일을 *wwwroot\lib\signalr* 폴더에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="7aaf3-116">npm 패키지 콘텐츠를 \*node_modules\\ \* 폴더에 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="7aaf3-117">*Wwwroot\\lib* 폴더 아래에 *signalr* 라는 새 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="7aaf3-118">*Signalr* 파일을 *wwwroot\lib\signalr* 폴더에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="7aaf3-119">요소에서 SignalR JavaScript 클라이언트를 참조 합니다. `<script>`</span><span class="sxs-lookup"><span data-stu-id="7aaf3-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="7aaf3-120">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="7aaf3-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="7aaf3-121">CDN (Content Delivery Network) 사용</span><span class="sxs-lookup"><span data-stu-id="7aaf3-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="7aaf3-122">Npm 필수 구성 요소 없이 클라이언트 라이브러리를 사용 하려면 클라이언트 라이브러리의 CDN 호스트 복사본을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="7aaf3-123">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="7aaf3-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="7aaf3-124">클라이언트 라이브러리는 다음의 CDNs에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="7aaf3-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="7aaf3-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="7aaf3-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="7aaf3-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="7aaf3-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="7aaf3-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="7aaf3-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="7aaf3-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="7aaf3-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="7aaf3-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="7aaf3-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="7aaf3-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="7aaf3-131">을 사용 하 여 설치</span><span class="sxs-lookup"><span data-stu-id="7aaf3-131">Install with LibMan</span></span>

<span data-ttu-id="7aaf3-132">라이브러리 라이브러리에서 특정 클라이언트 라이브러리 파일을 설치 하는 데 [사용 될 수 있습니다.](xref:client-side/libman/index)</span><span class="sxs-lookup"><span data-stu-id="7aaf3-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="7aaf3-133">예를 들어 프로젝트에는 축소 된 JavaScript 파일만 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="7aaf3-134">이 방법에 대 한 자세한 내용은 [ SignalR 클라이언트 라이브러리 추가](xref:tutorials/signalr#add-the-signalr-client-library)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="7aaf3-135">허브에 연결</span><span class="sxs-lookup"><span data-stu-id="7aaf3-135">Connect to a hub</span></span>

<span data-ttu-id="7aaf3-136">다음 코드에서는 연결을 만들고 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="7aaf3-137">허브 이름은 대/소문자를 구분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="7aaf3-138">크로스-원본 연결</span><span class="sxs-lookup"><span data-stu-id="7aaf3-138">Cross-origin connections</span></span>

<span data-ttu-id="7aaf3-139">일반적으로 브라우저는 요청 된 페이지와 동일한 도메인의 연결을 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="7aaf3-140">그러나 다른 도메인에 연결 해야 하는 경우도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="7aaf3-141">악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 하기 위해 [교차 원본 연결은](xref:security/cors) 기본적으로 사용 하지 않도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="7aaf3-142">원본 간 요청을 허용 하려면 `Startup` 클래스에서 해당 요청을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="7aaf3-143">클라이언트에서 허브 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="7aaf3-143">Call hub methods from client</span></span>

<span data-ttu-id="7aaf3-144">JavaScript 클라이언트는 [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)의 [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) 메서드를 통해 허브에서 공용 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="7aaf3-145">메서드 `invoke` 는 두 개의 인수를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="7aaf3-146">허브 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-146">The name of the hub method.</span></span> <span data-ttu-id="7aaf3-147">다음 예제에서 허브의 메서드 이름은 `SendMessage`입니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="7aaf3-148">허브 메서드에 정의 된 모든 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="7aaf3-149">다음 예에서는 인수 이름이 `message`입니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="7aaf3-150">예제 코드는 Internet Explorer를 제외한 모든 주요 브라우저의 현재 버전에서 지원 되는 화살표 함수 구문을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="7aaf3-151">*서버 리스 모드*에서 Azure SignalR 서비스를 사용 하는 경우 클라이언트에서 허브 메서드를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="7aaf3-152">자세한 내용은 [ SignalR 서비스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="7aaf3-153">메서드 `invoke` 는 JavaScript [약속](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="7aaf3-154">는 `Promise` 서버의 메서드가 반환 될 때 반환 값 (있는 경우)으로 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="7aaf3-155">서버에서 메서드가 오류를 throw 하는 경우 오류 메시지 `Promise` 와 함께이 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="7aaf3-156">이러한 경우 `then` 또는 `catch` `await` 구문을 처리 하려면 `Promise` 자체에서 및 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="7aaf3-157">메서드 `send` 는 JavaScript `Promise`를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="7aaf3-158">메시지가 `Promise` 서버에 전송 되 면이 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="7aaf3-159">메시지를 보내는 동안 오류가 발생 하는 경우 `Promise` 이 오류 메시지와 함께 거부 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="7aaf3-160">이러한 경우 `then` 또는 `catch` `await` 구문을 처리 하려면 `Promise` 자체에서 및 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="7aaf3-161">를 `send` 사용 하면 서버에서 메시지를 받을 때까지 기다리지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="7aaf3-162">따라서 서버에서 데이터 또는 오류를 반환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="7aaf3-163">허브에서 클라이언트 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="7aaf3-163">Call client methods from hub</span></span>

<span data-ttu-id="7aaf3-164">허브에서 메시지를 수신 하려면의 [on](/javascript/api/%40aspnet/signalr/hubconnection#on) 메서드를 사용 하 여 메서드를 정의 `HubConnection`합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="7aaf3-165">JavaScript 클라이언트 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="7aaf3-166">다음 예제에서 메서드 이름은 `ReceiveMessage`입니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="7aaf3-167">허브가 메서드에 전달 하는 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="7aaf3-168">다음 예제에서 인수 값은 `message`입니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="7aaf3-169">에서 `connection.on` 위의 코드는 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) 메서드를 사용 하 여 서버 쪽 코드가 호출 하는 경우 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="7aaf3-170">및 `SendAsync` `connection.on`에 정의 된 메서드 이름과 인수를 일치 시켜 호출할 클라이언트 메서드를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="7aaf3-171">모범 사례에 따라 `HubConnection` 이후 `on`에서 [start](/javascript/api/%40aspnet/signalr/hubconnection#start) 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="7aaf3-172">이렇게 하면 메시지를 받기 전에 처리기가 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="7aaf3-173">오류 처리 및 로깅</span><span class="sxs-lookup"><span data-stu-id="7aaf3-173">Error handling and logging</span></span>

<span data-ttu-id="7aaf3-174">메서드를 `catch` `start` 메서드의 끝에 연결 하 여 클라이언트 쪽 오류를 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="7aaf3-175">를 `console.error` 사용 하 여 브라우저의 콘솔에 오류를 출력 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="7aaf3-176">연결이 설정 될 때 기록할 이벤트의로 거와 유형을 전달 하 여 클라이언트 쪽 로그 추적을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="7aaf3-177">메시지는 지정 된 로그 수준 이상으로 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="7aaf3-178">사용 가능한 로그 수준은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="7aaf3-179">`signalR.LogLevel.Error`&ndash; 오류 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-179">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="7aaf3-180">메시지만 `Error` 로깅합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="7aaf3-181">`signalR.LogLevel.Warning`&ndash; 잠재적 오류에 대 한 경고 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-181">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="7aaf3-182">로그 `Warning`및 `Error` 메시지.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="7aaf3-183">`signalR.LogLevel.Information`&ndash; 오류가 없는 상태 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-183">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="7aaf3-184">, `Information` `Warning`및 `Error` 메시지를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="7aaf3-185">`signalR.LogLevel.Trace`&ndash; 추적 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-185">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="7aaf3-186">허브와 클라이언트 간에 전송 되는 데이터를 포함 하 여 모든 것을 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="7aaf3-187">[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) 에서 [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) 메서드를 사용 하 여 로그 수준을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="7aaf3-188">메시지는 브라우저 콘솔에 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="7aaf3-189">클라이언트 다시 연결</span><span class="sxs-lookup"><span data-stu-id="7aaf3-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="7aaf3-190">자동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="7aaf3-190">Automatically reconnect</span></span>

<span data-ttu-id="7aaf3-191">HubConnectionBuilder에서 메서드를 SignalR `withAutomaticReconnect` 사용 하 여 자동으로 다시 연결 되도록에 대 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)한 JavaScript 클라이언트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="7aaf3-192">기본적으로 자동으로 다시 연결 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="7aaf3-193">매개 변수를 `withAutomaticReconnect()` 사용 하지 않으면는 각 다시 연결 시도를 시도 하기 전에 0, 2, 10 및 30 초 동안 대기 하도록 클라이언트를 구성 하 고, 실패 한 네 번 시도 하면 중지 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="7aaf3-194">다시 연결 시도를 시작 하기 전에 `HubConnection` 는 상태로 전환 `HubConnectionState.Reconnecting` 되 고 자동 다시 연결이 `onreconnecting` 구성 `Disconnected` `onclose` `HubConnection` 되지 않은 등의 콜백 트리거를 상태로 전환 하는 대신 해당 콜백을 발생 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="7aaf3-195">이렇게 하면 연결이 손실 되었음을 사용자에 게 경고 하 고 UI 요소를 사용 하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="7aaf3-196">클라이언트에서 처음 네 번의 시도 내에 다시 연결 하 `HubConnection` 는 경우이 `Connected` 상태로 다시 전환 되 고 `onreconnected` 콜백이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="7aaf3-197">이렇게 하면 연결이 다시 설정 되었음을 사용자에 게 알릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="7aaf3-198">연결은 서버에서 완전히 새로운 것 이므로 `connectionId` `onreconnected` 콜백에 새가 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="7aaf3-199">가 `onreconnected` 협상을 `connectionId` `HubConnection` [건너뛰도록](xref:signalr/configuration#configure-client-options)구성 된 경우 콜백의 매개 변수는 정의 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="7aaf3-200">`withAutomaticReconnect()`초기 시작 오류 `HubConnection` 를 다시 시도 하도록를 구성 하지 않으므로 시작 실패를 수동으로 처리 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="7aaf3-201">클라이언트가 처음 네 번의 시도 내에서 성공적으로 다시 연결 되지 `HubConnection` 않으면이 `Disconnected` 상태로 전환 되 고 [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) 콜백이 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="7aaf3-202">이를 통해 사용자에 게 연결이 영구적으로 손실 되었음을 알리고 페이지 새로 고침을 권장 하는 기회를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="7aaf3-203">연결을 끊거나 다시 연결 하는 시간을 변경 하기 전에 사용자가 다시 연결 시도 횟수 `withAutomaticReconnect` 를 구성 하기 위해는 각 다시 연결 시도를 시작 하기 전에 대기할 지연 시간 (밀리초)을 나타내는 숫자 배열을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="7aaf3-204">앞의 예제에서는 연결이 `HubConnection` 끊긴 후 즉시 다시 연결 시도를 시작 하도록를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="7aaf3-205">기본 구성의 경우에도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="7aaf3-206">첫 번째 다시 연결 시도가 실패 하면 두 번째 다시 연결 시도도 기본 구성에서와 같이 2 초 동안 대기 하는 대신 즉시 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="7aaf3-207">두 번째 다시 연결 시도가 실패 하면 세 번째 다시 연결 시도가 10 초 후에 다시 시작 됩니다 .이는 기본 구성 처럼 다시 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="7aaf3-208">그런 다음 기본 구성에서와 같이 30 초 후에 다시 연결을 다시 시도 하는 대신 세 번째 다시 연결 시도 실패 후 중지 하 여 기본 동작에서 사용자 지정 동작이 다시 달라 지므로.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="7aaf3-209">자동 다시 연결 시도의 타이밍과 수를 더 많이 제어 하려는 경우는 라는 `withAutomaticReconnect` `IRetryPolicy` `nextRetryDelayInMilliseconds`단일 메서드가 있는 인터페이스를 구현 하는 개체를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="7aaf3-210">`nextRetryDelayInMilliseconds`는 형식의 `RetryContext`단일 인수를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="7aaf3-211">`RetryContext` 에는 `previousRetryCount`, `elapsedMilliseconds` 및 `retryReason` `number` `number` 각각, 및 인 세 가지 속성이 있습니다. `Error`</span><span class="sxs-lookup"><span data-stu-id="7aaf3-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="7aaf3-212">첫 번째 다시 연결을 시도 하기 `previousRetryCount` 전에 `elapsedMilliseconds` 와는 모두 0이 되며 `retryReason` 은 연결이 끊어지는 원인이 되는 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="7aaf3-213">실패 한 각 재시도 `previousRetryCount` 후에는 1 씩 증가 하 고 `elapsedMilliseconds` , 지금까지 밀리초 단위로 다시 연결 하는 데 걸린 시간을 반영 하도록 업데이트 되며, `retryReason` 는 마지막 다시 연결 시도가 실패 한 원인이 되는 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="7aaf3-214">`nextRetryDelayInMilliseconds`는 다음 다시 연결 시도 전에 대기 하는 시간 (밀리초)을 나타내는 숫자를 반환 `null` 해야 합니다 `HubConnection` . 그렇지 않으면에서 다시 연결을 중지 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

<span data-ttu-id="7aaf3-215">또는 [수동으로 다시 연결](#manually-reconnect)에 설명 된 대로 클라이언트를 수동으로 다시 연결 하는 코드를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="7aaf3-216">수동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="7aaf3-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="7aaf3-217">3.0 이전 버전의에 대 한 SignalR JavaScript 클라이언트는 자동으로 다시 연결 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="7aaf3-218">클라이언트를 수동으로 다시 연결 하는 코드를 작성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="7aaf3-219">다음 코드에서는 일반적인 수동 다시 연결 방식을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="7aaf3-220">함수 (이 경우 `start` 함수)가 생성 되어 연결을 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="7aaf3-221">연결의 `start` `onclose` 이벤트 처리기에서 함수를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="7aaf3-222">실제 구현에서는 지 수 백오프를 사용 하거나 지정 된 횟수 만큼 다시 시도 하 여 포기 합니다.</span><span class="sxs-lookup"><span data-stu-id="7aaf3-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7aaf3-223">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7aaf3-223">Additional resources</span></span>

* [<span data-ttu-id="7aaf3-224">JavaScript API 참조</span><span class="sxs-lookup"><span data-stu-id="7aaf3-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="7aaf3-225">JavaScript 자습서</span><span class="sxs-lookup"><span data-stu-id="7aaf3-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="7aaf3-226">WebPack 및 TypeScript 자습서</span><span class="sxs-lookup"><span data-stu-id="7aaf3-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="7aaf3-227">허브</span><span class="sxs-lookup"><span data-stu-id="7aaf3-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="7aaf3-228">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="7aaf3-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="7aaf3-229">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="7aaf3-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="7aaf3-230">원본 간 요청 (CORS)</span><span class="sxs-lookup"><span data-stu-id="7aaf3-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="7aaf3-231">[Azure SignalR 서비스 서버 리스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="7aaf3-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
