---
title: ASP.NET Core Blazor WebAssembly에서 웹 API 호출
author: guardrex
description: CORS(원본 간 리소스 공유) 요청 만들기를 포함하여 JSON 도우미를 사용하여 Blazor WebAssembly 앱에서 웹 API를 호출하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: abc546cc0079a01e3999b2c7c083235d3fff9b06
ms.sourcegitcommit: e94ecfae6a3ef568fa197da791c8bc595917d17a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82122226"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="7b940-103">ASP.NET Core Blazor에서 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="7b940-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="7b940-104">작성자: [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) 및 [Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="7b940-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="7b940-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 앱은 미리 구성된 `HttpClient` 서비스를 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="7b940-106">Blazor JSON 도우미 또는 <xref:System.Net.Http.HttpRequestMessage>를 사용하여 JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 옵션을 포함할 수 있는 요청을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="7b940-107">Blazor WebAssembly 앱의 `HttpClient` 서비스는 원본 서버에 다시 요청하는 데 중점을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="7b940-108">이 항목의 지침은 Blazor WebAssembly 앱에만 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="7b940-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) 앱은 일반적으로 <xref:System.Net.Http.IHttpClientFactory>를 사용하여 만든 <xref:System.Net.Http.HttpClient> 인스턴스를 통해 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="7b940-110">이 항목의 지침은 Blazor Server 앱과는 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="7b940-111">Blazor Server 앱을 개발하는 경우 <xref:fundamentals/http-requests>의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="7b940-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="7b940-112">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)([다운로드 방법](xref:index#how-to-download-a-sample)) &ndash; *BlazorWebAssemblySample* 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="7b940-113">*BlazorWebAssemblySample* 샘플 앱에서 다음 구성 요소를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b940-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="7b940-114">웹 API(*Pages/CallWebAPI.razor*) 호출</span><span class="sxs-lookup"><span data-stu-id="7b940-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="7b940-115">HTTP 요청 테스터(*Components/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="7b940-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="7b940-116">패키지</span><span class="sxs-lookup"><span data-stu-id="7b940-116">Packages</span></span>

<span data-ttu-id="7b940-117">프로젝트 파일에서 [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="7b940-118">HttpClient 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="7b940-118">Add the HttpClient service</span></span>

<span data-ttu-id="7b940-119">`Program.Main`에서 아직 없는 경우 `HttpClient` 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="7b940-120">HttpClient 및 JSON 도우미</span><span class="sxs-lookup"><span data-stu-id="7b940-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="7b940-121">Blazor WebAssembly 앱에서 [HttpClient](xref:fundamentals/http-requests)는 원본 서버에 다시 요청하기 위한 미리 구성된 서비스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="7b940-122">Blazor Server 앱은 기본적으로 `HttpClient` 서비스를 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="7b940-123">[HttpClient 팩터리 인프라](xref:fundamentals/http-requests)를 사용하여 앱에 `HttpClient`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="7b940-124">`HttpClient` 및 JSON 도우미는 타사 웹 API 엔드포인트를 호출하는 데에도 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="7b940-125">`HttpClient`는 브라우저 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)를 사용하여 구현되며 동일한 원본 정책 적용을 비롯한 제한 사항이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="7b940-126">클라이언트의 기본 주소가 원래 서버의 주소로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="7b940-127">`@inject` 지시문을 사용하여 `HttpClient` 인스턴스를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="7b940-128">다음 예제에서 Todo 웹 API는 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="7b940-129">예제는 다음을 저장하는 `TodoItem` 클래스를 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="7b940-130">ID(`Id`, `long`) &ndash; 항목의 고유한 ID입니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="7b940-131">이름(`Name`, `string`) &ndash; 항목의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="7b940-132">상태(`IsComplete`, `bool`) &ndash;) Todo 항목이 완료되었는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="7b940-133">JSON 도우미 메서드는 URI(다음 예제의 웹 API)에 요청을 보내고 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="7b940-134">`GetFromJsonAsync` &ndash; HTTP GET 요청을 보내고 JSON 응답 본문을 구문 분석하여 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="7b940-135">다음 코드에서 `_todoItems`는 구성 요소에 의해 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="7b940-136">`GetTodoItems` 메서드는 구성 요소가 렌더링을 완료할 때 트리거됩니다([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="7b940-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="7b940-137">전체 예제는 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b940-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="7b940-138">`PostAsJsonAsync` &ndash; JSON 인코딩 콘텐츠를 포함하여 HTTP POST 요청을 보내고 JSON 응답 본문을 구문 분석하여 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="7b940-139">다음 코드에서 구성 요소의 바인딩된 요소는 `_newItemName`을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="7b940-140">`AddItem` 메서드는 `<button>` 요소를 선택하여 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="7b940-141">전체 예제는 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b940-141">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="7b940-142">`PostAsJsonAsync`를 호출하면 <xref:System.Net.Http.HttpResponseMessage>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="7b940-143">응답 메시지에서 JSON 콘텐츠를 역직렬화하려면 `ReadFromJsonAsync<T>` 확장 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="7b940-144">`PutAsJsonAsync` &ndash; JSON 인코딩 콘텐츠를 포함하여 HTTP PUT 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="7b940-145">다음 코드에서 구성 요소의 바인딩된 요소는 `Name` 및 `IsCompleted`에 대한 `_editItem` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="7b940-146">항목의 `Id`는 UI의 다른 부분에서 해당 항목을 선택하고 `EditItem`을 호출하면 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="7b940-147">`SaveItem` 메서드는 Save `<button>` 요소를 선택하여 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="7b940-148">전체 예제는 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b940-148">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```
  
  <span data-ttu-id="7b940-149">`PutAsJsonAsync`를 호출하면 <xref:System.Net.Http.HttpResponseMessage>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="7b940-150">응답 메시지에서 JSON 콘텐츠를 역직렬화하려면 `ReadFromJsonAsync<T>` 확장 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="7b940-151"><xref:System.Net.Http>는 HTTP 요청을 보내고 HTTP 응답을 받기 위한 추가 확장 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="7b940-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*)는 웹 API에 HTTP DELETE 요청을 보내는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="7b940-153">다음 코드에서 Delete `<button>` 요소는 `DeleteItem` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="7b940-154">바인딩된 `<input>` 요소는 삭제할 항목의 `id`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="7b940-155">전체 예제는 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b940-155">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="7b940-156">CORS(원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="7b940-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="7b940-157">브라우저 보안 때문에 웹 페이지에서 해당 웹 페이지를 제공한 도메인이 아닌 다른 도메인에 요청을 수행할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="7b940-158">이러한 제한 사항을 *동일 원본 정책*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="7b940-159">동일 원본 정책은 악성 사이트에서 다른 사이트의 중요한 데이터를 읽지 못하도록 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="7b940-160">브라우저에서 원본이 다른 엔드포인트로의 요청을 만들려면 *엔드포인트*가 [CORS(원본 간 리소스 공유)](https://www.w3.org/TR/cors/)를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="7b940-161">[Blazor WebAssembly 샘플 앱(BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)은 Web API 호출 구성 요소(*Pages/CallWebAPI.razor*)에서 CORS를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="7b940-162">다른 사이트에서 앱에 대해 CORS(원본 간 리소스 공유) 요청을 수행하도록 허용하려면 <xref:security/cors>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b940-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="7b940-163">Fetch API 요청 옵션이 포함된 HttpClient 및 HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="7b940-163">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="7b940-164">Blazor WebAssembly 앱의 WebAssembly에서 실행되는 경우 [HttpClient](xref:fundamentals/http-requests) 및 <xref:System.Net.Http.HttpRequestMessage>를 사용하여 요청을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-164">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="7b940-165">예를 들어, 요청 URI, HTTP 메서드 및 원하는 요청 헤더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-165">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                JsonContent.Create(new TodoItem
                { 
                    Name: "A New Todo Item",
                    IsComplete: false
                })
        };
        
        requestMessage.Headers.Authorization = 
           new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="7b940-166">.NET WebAssembly의 `HttpClient` 구현은 [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-166">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="7b940-167">Fetch를 사용하면 여러 [요청 관련 옵션](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-167">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="7b940-168">HTTP 페치 요청 옵션은 다음 표에 표시된 `HttpRequestMessage` 확장 메서드로 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-168">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="7b940-169">`HttpRequestMessage` 확장 메서드</span><span class="sxs-lookup"><span data-stu-id="7b940-169">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="7b940-170">Fetch 요청 속성</span><span class="sxs-lookup"><span data-stu-id="7b940-170">Fetch request property</span></span> |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [<span data-ttu-id="7b940-171">credentials</span><span class="sxs-lookup"><span data-stu-id="7b940-171">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [<span data-ttu-id="7b940-172">캐시</span><span class="sxs-lookup"><span data-stu-id="7b940-172">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [<span data-ttu-id="7b940-173">mode</span><span class="sxs-lookup"><span data-stu-id="7b940-173">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [<span data-ttu-id="7b940-174">무결성</span><span class="sxs-lookup"><span data-stu-id="7b940-174">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="7b940-175">보다 일반적인 `SetBrowserRequestOption` 확장 메서드를 사용하여 추가 옵션을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-175">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="7b940-176">HTTP 응답은 일반적으로 응답 콘텐츠에서 동기화 읽기를 지원할 수 있도록 Blazor WebAssembly 앱에서 버퍼링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-176">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="7b940-177">응답 스트리밍을 지원하도록 설정하려면 요청에서 `SetBrowserResponseStreamingEnabled` 확장 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-177">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="7b940-178">원본 간 요청에 자격 증명을 포함하려면 `SetBrowserRequestCredentials` 확장 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-178">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="7b940-179">Fetch API 옵션에 대한 자세한 내용은 [MDN 웹 설명서: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b940-179">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="7b940-180">CORS 요청에 대한 자격 증명(권한 부여 쿠키/헤더)을 보낼 때 CORS 정책에서 `Authorization` 헤더를 허용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-180">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="7b940-181">다음 정책에는 해당 구성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-181">The following policy includes configuration for:</span></span>

* <span data-ttu-id="7b940-182">요청 원본(`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="7b940-182">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="7b940-183">임의 메서드(동사)</span><span class="sxs-lookup"><span data-stu-id="7b940-183">Any method (verb).</span></span>
* <span data-ttu-id="7b940-184">`Content-Type` 및 `Authorization` 헤더.</span><span class="sxs-lookup"><span data-stu-id="7b940-184">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="7b940-185">사용자 지정 헤더(예: `x-custom-header`)를 허용하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>를 호출할 때 헤더를 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="7b940-185">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="7b940-186">클라이언트 쪽 JavaScript 코드에 의해 설정된 자격 증명(`credentials` 속성이 `include`로 설정됨).</span><span class="sxs-lookup"><span data-stu-id="7b940-186">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="7b940-187">자세한 내용은 <xref:security/cors> 및 샘플 앱의 HTTP 요청 테스터 구성 요소(*Components/HTTPRequestTester.razor*)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b940-187">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7b940-188">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7b940-188">Additional resources</span></span>

* [<span data-ttu-id="7b940-189">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="7b940-189">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="7b940-190">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="7b940-190">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="7b940-191">Kestrel HTTPS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="7b940-191">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="7b940-192">W3C의 CORS(원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="7b940-192">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
