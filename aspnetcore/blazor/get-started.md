---
title: ASP.NET Core Blazor 시작하기
author: guardrex
description: 선택한 도구로 Blazor 앱을 빌드하여 Blazor를 시작합니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 052a787fbe6411dbaa953f10fcd982dfbd41f1af
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769457"
---
# <a name="get-started-with-aspnet-core-blazor"></a>ASP.NET Core Blazor 시작

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor를 시작하려면 아래에서 원하는 도구의 지침을 따르세요.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Blazor 서버 앱을 만들려면 **ASP.NET 및 웹 개발** 워크로드가 있는 최신 버전의 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)를 설치합니다.

   Blazor 서버 앱과 Blazor WebAssembly 앱을 만들려면 **ASP.NET 및 웹 개발** 워크로드가 있는 최신 미리 보기 버전의 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/)를 설치합니다.

   두 가지 Blazor 호스팅 모델인 ‘Blazor WebAssembly’와 ‘Blazor 서버’에 대한 자세한 내용은 <xref:blazor/hosting-models> 문서를 참조하세요.  

1. 다음 명령을 실행하여 Blazor WebAssembly 미리 보기 템플릿을 설치합니다.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

1. 새 프로젝트를 만듭니다.

1. **Blazor 앱**을 선택합니다. **새로 만들기**를 선택합니다.

1. **프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다. **위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다. **만들기**를 선택합니다.

1. Blazor WebAssembly 환경의 경우(Visual Studio 16.6 미리 보기 2 이상) **Blazor WebAssembly 앱** 템플릿을 선택합니다. Blazor 서버 환경의 경우(Visual Studio 16.4 이상) **Blazor 서버 앱** 템플릿을 선택합니다. **만들기**를 선택합니다.

1. <kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 앱을 실행합니다.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치합니다.

1. 필요에 따라 다음 명령을 실행하여 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 미리 보기 템플릿을 설치합니다.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   두 가지 Blazor 호스팅 모델인 ‘Blazor WebAssembly’와 ‘Blazor 서버’에 대한 자세한 내용은 <xref:blazor/hosting-models> 문서를 참조하세요.  

   > [!NOTE]
   > 3\.2 미리 보기 Blazor WebAssembly 템플릿을 사용하려면 [.NET Core SDK 버전 3.1.201 이상](https://dotnet.microsoft.com/download/dotnet-core/3.1)이 **필요**합니다. 명령 셸에서 `dotnet --version`을 실행하여 설치된 .NET Core SDK 버전을 확인합니다.

1. [Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.

1. 최신 [Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)을 설치하고, `debug.javascript.usePreview`를 `true`로 설정하여 [JavaScript Debugger(Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 확장을 설치합니다.

1. Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

1. Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.

1. IDE에서 프로젝트를 빌드 및 디버그하기 위한 자산을 추가하도록 요청합니다. **Yes**를 선택합니다.

1. Blazor 서버를 사용하는 경우 Visual Studio Code 디버거를 사용하여 앱을 실행합니다.

   Blazor WebAssembly에서 **.NET Core 시작(Blazor 독립 실행형)** 시작 구성을 사용하여 앱을 시작한 다음 **Chrome의 .NET Core Debug Blazor Web Assembly** 시작 구성(Chrome 필요)을 사용하여 브라우저를 시작합니다. 자세한 내용은 <xref:blazor/debug#visual-studio-code>를 참조하세요.

1. 브라우저에서 `https://localhost:5001`로 이동합니다.

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

Mac용 Visual Studio에서는 Blazor 서버가 지원됩니다. 현재 Blazor WebAssembly는 지원되지 않습니다. macOS에서 Blazor WebAssembly 앱을 만들려면 **.NET Core CLI** 탭의 지침을 따르세요. 두 가지 Blazor 호스팅 모델인 ‘Blazor WebAssembly’와 ‘Blazor 서버’에 대한 자세한 내용은 <xref:blazor/hosting-models> 문서를 참조하세요.  

1. [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치합니다.

1. **파일** > **새 솔루션**을 선택하거나 **시작 창**에서 **새** 프로젝트를 만듭니다.

1. 사이드바에서 **.NET Core** > **앱**을 선택합니다.

1. **Blazor 서버 앱** 템플릿을 선택합니다. **새로 만들기**를 선택합니다.

1. 다음 구성을 확인합니다.

   * **대상 프레임워크**가 **.NET Core 3.1**로 설정되어 있는지
   * **인증**이 **인증 안 함**으로 설정되어 있는지
   
   **새로 만들기**를 선택합니다.

1. **프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 지정합니다. **만들기**를 선택합니다.

1. **실행** > **디버깅하지 않고 시작**을 선택하여 ‘디버거 없이’ 앱을 실행합니다.  지금은 디버깅이 지원되지 않습니다.

<!-- HOLD FOR 8.6 GA

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

-->

개발 인증서를 신뢰하라는 메시지가 표시되면 인증서를 신뢰하고 계속합니다. 인증서를 신뢰하려면 사용자 및 키 집합 암호가 필요합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치합니다.

1. 필요에 따라 다음 명령을 실행하여 Blazor WebAssembly 미리 보기 템플릿을 설치합니다.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   두 가지 Blazor 호스팅 모델인 ‘Blazor WebAssembly’와 ‘Blazor 서버’에 대한 자세한 내용은 <xref:blazor/hosting-models> 문서를 참조하세요.  

   > [!NOTE]
   > 3\.2 미리 보기 Blazor WebAssembly 템플릿을 사용하려면 [.NET Core SDK 버전 3.1.201 이상](https://dotnet.microsoft.com/download/dotnet-core/3.1)이 **필요**합니다. 명령 셸에서 `dotnet --version`을 실행하여 설치된 .NET Core SDK 버전을 확인합니다.

1. Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. 브라우저에서 `https://localhost:5001`로 이동합니다.

---

사이드바의 탭에서 여러 페이지를 사용할 수 있습니다.

* Home
* 카운터
* 데이터 가져오기

Counter 페이지에서 **Click me** 단추를 선택하여 페이지 새로 고침 없이 카운터를 증가시킵니다. 웹 페이지에서 카운터를 증가하려면 일반적으로 JavaScript를 작성해야 하지만, Blazor에서는 C#을 사용할 수 있습니다.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

맨 위에 있는 `@page` 지시문에 지정된 대로 브라우저에서 `/counter`를 요청하면 `Counter` 구성 요소가 해당 콘텐츠를 렌더링합니다. 구성 요소는 유연하고 효율적인 방법으로 UI를 업데이트하는 데 사용할 수 있는 렌더링 트리의 메모리 내 표시로 렌더링됩니다.

**Click me** 단추를 선택할 때마다 다음 작업이 수행됩니다.

* `onclick` 이벤트가 발생합니다.
* `IncrementCount` 메서드가 호출됩니다.
* `currentCount`가 증가합니다.
* 구성 요소가 다시 렌더링됩니다.

런타임은 이전 콘텐츠와 새 콘텐츠를 비교하고 변경된 콘텐츠만 DOM(문서 개체 모델)에 적용합니다.

HTML 구문을 사용하여 구성 요소를 다른 구성 요소에 추가합니다. 예를 들어 `Index` 구성 요소에 `<Counter />` 요소를 추가하여 앱의 홈페이지에 `Counter` 구성 요소를 추가합니다.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

앱을 실행합니다. 홈페이지에는 `Counter` 구성 요소가 제공하는 고유한 카운터가 있습니다.

구성 요소 매개 변수는 자식 구성 요소에 속성을 설정하게 해주는 특성 또는 [자식 콘텐츠](xref:blazor/components#child-content)를 사용하여 지정합니다. `Counter` 구성 요소에 매개 변수를 추가하려면 구성 요소의 `@code` 블록을 업데이트합니다.

* `[Parameter]` 특성을 사용하여 `IncrementAmount`의 공용 속성을 추가합니다.
* `currentCount` 값을 증가시킬 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

특성을 사용하여 `Index` 구성 요소의 `<Counter>` 요소에 `IncrementAmount`를 지정합니다.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

앱을 실행합니다. `Index` 구성 요소에는 **Click me** 단추를 선택할 때마다 10씩 증가하는 고유한 카운터가 있습니다. `/counter`의 `Counter` 구성 요소(*Counter.razor*)는 계속 1씩 증가합니다.

## <a name="next-steps"></a>다음 단계

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/templates>
* <xref:signalr/introduction>
