---
title: Blazor 할 일 목록 앱 빌드
author: guardrex
description: Blazor 앱을 단계별로 빌드합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 726380c42c952f47d6fdff09a811f35a20462d96
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944927"
---
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="0229a-103">Blazor 할 일 목록 앱 빌드</span><span class="sxs-lookup"><span data-stu-id="0229a-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="0229a-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0229a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0229a-105">이 자습서에서는 Blazor 앱을 만들고 수정하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="0229a-106">다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0229a-107">할 일 목록 Blazor 앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="0229a-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="0229a-108">Razor 구성 요소 수정</span><span class="sxs-lookup"><span data-stu-id="0229a-108">Modify Razor components</span></span>
> * <span data-ttu-id="0229a-109">구성 요소에서 이벤트 처리 및 데이터 바인딩 사용</span><span class="sxs-lookup"><span data-stu-id="0229a-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="0229a-110">Blazor 앱에서 DI(종속성 주입) 및 라우팅 사용</span><span class="sxs-lookup"><span data-stu-id="0229a-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="0229a-111">이 자습서의 내용을 마치면 할 일 목록 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

1. <span data-ttu-id="0229a-112">명령 셸에서 `TodoList`라는 새 Blazor 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-112">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="0229a-113">위의 명령은 `TodoList`라는 폴더를 만들어 앱을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-113">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="0229a-114">다음 명령을 사용하여 디렉터리를 `TodoList` 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-114">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="0229a-115">다음 명령을 사용하여 `Pages` 폴더의 앱에 새 `Todo` Razor 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-115">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razor<span data-ttu-id="0229a-116"> 구성 요소 파일 이름에서 첫 문자는 대문자여야 하므로 `Todo` 구성 요소 파일 이름이 대문자 `T`로 시작하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-116"> component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="0229a-117">`Pages/Todo.razor`에서 구성 요소에 초기 태그를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-117">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="0229a-118">`Todo` 구성 요소를 탐색 모음에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-118">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="0229a-119">`NavMenu` 구성 요소(`Shared/NavMenu.razor`)는 앱의 레이아웃에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-119">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="0229a-120">레이아웃은 앱의 콘텐츠 중복을 방지할 수 있는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-120">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="0229a-121">`Shared/NavMenu.razor` 파일의 기존 목록 항목 아래에 다음 목록 항목 태그를 추가하여 `Todo` 구성 요소에 대한 `<NavLink>` 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-121">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="0229a-122">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-122">Rebuild and run the app.</span></span> <span data-ttu-id="0229a-123">새 Todo 페이지를 방문하여 `Todo` 구성 요소에 대한 링크가 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-123">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="0229a-124">프로젝트 루트에 `TodoItem.cs` 파일을 추가하여 todo 항목을 나타내는 클래스를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-124">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="0229a-125">`TodoItem` 클래스에 대해 다음 C# 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-125">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="0229a-126">`Todo` 구성 요소(`Pages/Todo.razor`)로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-126">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="0229a-127">`@code` 블록에 있는 할 일 항목에 대한 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-127">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="0229a-128">`Todo` 구성 요소는 이 필드를 사용하여 할 일 목록의 상태를 유지 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-128">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="0229a-129">순서가 지정되지 않은 목록 태그 및 `foreach` 루프를 추가하여 각 할 일 항목을 목록 항목(`<li>`)으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-129">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="0229a-130">목록에 할 일 항목을 추가하려면 앱에 UI 요소가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-130">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="0229a-131">순서가 지정되지 않은 목록(`<ul>...</ul>`) 아래에 텍스트 입력(`<input>`)과 단추(`<button>`)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-131">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="0229a-132">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-132">Rebuild and run the app.</span></span> <span data-ttu-id="0229a-133">단추에 이벤트 처리기가 연결되어 있지 않으므로 **`Add todo`** 단추를 선택해도 아무 일도 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-133">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="0229a-134">`Todo` 구성 요소에 `AddTodo` 메서드를 추가하고 `@onclick` 특성을 사용하여 이를 단추 선택에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-134">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="0229a-135">단추가 선택되면 `AddTodo` C# 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-135">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="0229a-136">새 할 일 항목의 제목을 가져오기 위해 `@code` 블록의 상단에 `newTodo` 문자열 필드를 추가하고 `<input>` 요소에서 `bind` 특성을 사용하여 이를 텍스트 입력 값에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-136">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="0229a-137">`AddTodo` 메서드를 수정하여 지정한 제목으로 `TodoItem`을 목록에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-137">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="0229a-138">`newTodo`를 빈 문자열로 설정하여 텍스트 입력 값을 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-138">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="0229a-139">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-139">Rebuild and run the app.</span></span> <span data-ttu-id="0229a-140">할 일 목록에 몇 개의 할 일 항목을 추가하여 새 코드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-140">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="0229a-141">각 할 일 항목의 제목 텍스트를 편집 가능하게 설정하고 확인란을 통해 사용자가 완료된 항목을 추적하도록 도울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-141">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="0229a-142">각 할 일 항목의 확인란 입력을 추가하고 해당 값을 `IsDone` 속성에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-142">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="0229a-143">`@todo.Title`을 `@todo.Title`에 바인딩된 `<input>` 요소로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-143">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="0229a-144">해당 값이 바인딩되었는지 확인하기 위해 `<h3>` 헤더를 수정하여 완료되지 않은(`IsDone`이 `false`) 할 일 항목 수를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-144">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="0229a-145">완료된 `Todo` 구성 요소(`Pages/Todo.razor`)는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-145">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="0229a-146">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-146">Rebuild and run the app.</span></span> <span data-ttu-id="0229a-147">할 일 항목을 추가하여 새 코드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-147">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0229a-148">다음 단계</span><span class="sxs-lookup"><span data-stu-id="0229a-148">Next steps</span></span>

<span data-ttu-id="0229a-149">본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-149">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0229a-150">할 일 목록 Blazor 앱 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="0229a-150">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="0229a-151">Razor 구성 요소 수정</span><span class="sxs-lookup"><span data-stu-id="0229a-151">Modify Razor components</span></span>
> * <span data-ttu-id="0229a-152">구성 요소에서 이벤트 처리 및 데이터 바인딩 사용</span><span class="sxs-lookup"><span data-stu-id="0229a-152">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="0229a-153">Blazor 앱에서 DI(종속성 주입) 및 라우팅 사용</span><span class="sxs-lookup"><span data-stu-id="0229a-153">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="0229a-154">Blazor 프로젝트 템플릿에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="0229a-154">Learn about the Blazor project templates:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/templates>
