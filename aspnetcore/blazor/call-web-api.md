---
title: ASP.NET Core Blazor WebAssembly에서 웹 API 호출
author: guardrex
description: CORS(원본 간 리소스 공유) 요청 만들기를 포함하여 JSON 도우미를 사용하여 Blazor WebAssembly 앱에서 웹 API를 호출하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: d823db3688e05f6befefacc9f390e0dcdbf329a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767150"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="2d0f5-103">ASP.NET Core Blazor에서 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="2d0f5-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="2d0f5-104">작성자: [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) 및 [Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="2d0f5-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2d0f5-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 앱은 미리 구성된 `HttpClient` 서비스를 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="2d0f5-106">Blazor JSON 도우미 또는 <xref:System.Net.Http.HttpRequestMessage>를 사용하여 JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 옵션을 포함할 수 있는 요청을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="2d0f5-107">Blazor WebAssembly 앱의 `HttpClient` 서비스는 원본 서버에 대해 다시 요청을 수행하는 데 중점을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="2d0f5-108">이 항목의 지침은 Blazor WebAssembly 앱과만 관련이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="2d0f5-109">[Blazor 서버](xref:blazor/hosting-models#blazor-server) 앱은 일반적으로 <xref:System.Net.Http.IHttpClientFactory>를 사용하여 만드는 <xref:System.Net.Http.HttpClient> 인스턴스를 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="2d0f5-110">이 항목의 지침은 Blazor 서버 앱과는 관련이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="2d0f5-111">Blazor 서버 앱을 개발하는 경우 <xref:fundamentals/http-requests>의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="2d0f5-112">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)([다운로드 방법](xref:index#how-to-download-a-sample)) &ndash; *BlazorWebAssemblySample* 앱을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="2d0f5-113">*BlazorWebAssemblySample* 샘플 앱에서 다음 구성 요소를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="2d0f5-114">웹 API(*Pages/CallWebAPI.razor*) 호출</span><span class="sxs-lookup"><span data-stu-id="2d0f5-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="2d0f5-115">HTTP 요청 테스터(*Components/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="2d0f5-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="2d0f5-116">패키지</span><span class="sxs-lookup"><span data-stu-id="2d0f5-116">Packages</span></span>

<span data-ttu-id="2d0f5-117">프로젝트 파일에서 [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="2d0f5-118">HttpClient 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="2d0f5-118">Add the HttpClient service</span></span>

<span data-ttu-id="2d0f5-119">`Program.Main`에서 아직 없는 경우 `HttpClient` 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="2d0f5-120">HttpClient 및 JSON 도우미</span><span class="sxs-lookup"><span data-stu-id="2d0f5-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="2d0f5-121">Blazor WebAssembly 앱에서 [HttpClient](xref:fundamentals/http-requests)는 원본 서버에 대해 다시 요청을 수행하기 위해 미리 구성된 서비스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="2d0f5-122">Blazor 서버 앱은 기본적으로 `HttpClient` 서비스를 포함하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="2d0f5-123">[HttpClient 팩터리 인프라](xref:fundamentals/http-requests)를 사용하여 앱에 `HttpClient`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="2d0f5-124">`HttpClient` 및 JSON 도우미는 타사 웹 API 엔드포인트를 호출하는 데에도 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="2d0f5-125">`HttpClient`는 브라우저 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)를 사용하여 구현되며 동일한 원본 정책 적용을 비롯한 제한 사항이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="2d0f5-126">클라이언트의 기본 주소가 원래 서버의 주소로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="2d0f5-127">`@inject` 지시문을 사용하여 `HttpClient` 인스턴스를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="2d0f5-128">다음 예제에서 Todo 웹 API는 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="2d0f5-129">예제는 다음을 저장하는 `TodoItem` 클래스를 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="2d0f5-130">ID(`Id`, `long`) &ndash; 항목의 고유한 ID입니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="2d0f5-131">이름(`Name`, `string`) &ndash; 항목의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="2d0f5-132">상태(`IsComplete`, `bool`) &ndash;) Todo 항목이 완료되었는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="2d0f5-133">JSON 도우미 메서드는 URI(다음 예제의 웹 API)에 요청을 보내고 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="2d0f5-134">`GetFromJsonAsync` &ndash; HTTP GET 요청을 보내고 JSON 응답 본문을 구문 분석하여 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="2d0f5-135">다음 코드에서 `_todoItems`는 구성 요소에 의해 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="2d0f5-136">`GetTodoItems` 메서드는 구성 요소가 렌더링을 완료할 때 트리거됩니다([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="2d0f5-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="2d0f5-137">전체 예제는 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="2d0f5-138">`PostAsJsonAsync` &ndash; JSON 인코딩 콘텐츠를 포함하여 HTTP POST 요청을 보내고 JSON 응답 본문을 구문 분석하여 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="2d0f5-139">다음 코드에서 구성 요소의 바인딩된 요소는 `_newItemName`을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="2d0f5-140">`AddItem` 메서드는 `<button>` 요소를 선택하여 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="2d0f5-141">전체 예제는 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="2d0f5-142">`PostAsJsonAsync`를 호출하면 <xref:System.Net.Http.HttpResponseMessage>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="2d0f5-143">응답 메시지에서 JSON 콘텐츠를 역직렬화하려면 `ReadFromJsonAsync<T>` 확장 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="2d0f5-144">`PutAsJsonAsync` &ndash; JSON 인코딩 콘텐츠를 포함하여 HTTP PUT 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="2d0f5-145">다음 코드에서 구성 요소의 바인딩된 요소는 `Name` 및 `IsCompleted`에 대한 `_editItem` 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="2d0f5-146">항목의 `Id`는 UI의 다른 부분에서 해당 항목을 선택하고 `EditItem`을 호출하면 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="2d0f5-147">`SaveItem` 메서드는 Save `<button>` 요소를 선택하여 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="2d0f5-148">전체 예제는 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-148">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="2d0f5-149">`PutAsJsonAsync`를 호출하면 <xref:System.Net.Http.HttpResponseMessage>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="2d0f5-150">응답 메시지에서 JSON 콘텐츠를 역직렬화하려면 `ReadFromJsonAsync<T>` 확장 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="2d0f5-151"><xref:System.Net.Http>는 HTTP 요청을 보내고 HTTP 응답을 받기 위한 추가 확장 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="2d0f5-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*)는 웹 API에 HTTP DELETE 요청을 보내는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="2d0f5-153">다음 코드에서 Delete `<button>` 요소는 `DeleteItem` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="2d0f5-154">바인딩된 `<input>` 요소는 삭제할 항목의 `id`를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="2d0f5-155">전체 예제는 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-155">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="2d0f5-156">CORS(원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="2d0f5-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="2d0f5-157">브라우저 보안 때문에 웹 페이지에서 해당 웹 페이지를 제공한 도메인이 아닌 다른 도메인에 요청을 수행할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="2d0f5-158">이러한 제한 사항을 *동일 원본 정책*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="2d0f5-159">동일 원본 정책은 악성 사이트에서 다른 사이트의 중요한 데이터를 읽지 못하도록 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="2d0f5-160">브라우저에서 원본이 다른 엔드포인트로의 요청을 만들려면 *엔드포인트*가 [CORS(원본 간 리소스 공유)](https://www.w3.org/TR/cors/)를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="2d0f5-161">[Blazor WebAssembly 샘플 앱(BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)은 웹 API 호출 구성 요소(*Pages/CallWebAPI.razor*)에서 CORS를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="2d0f5-162">다른 사이트에서 앱에 대해 CORS(원본 간 리소스 공유) 요청을 수행하도록 허용하려면 <xref:security/cors>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2d0f5-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2d0f5-163">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2d0f5-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="2d0f5-164">Kestrel HTTPS 엔드포인트 구성</span><span class="sxs-lookup"><span data-stu-id="2d0f5-164">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="2d0f5-165">W3C의 CORS(원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="2d0f5-165">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
