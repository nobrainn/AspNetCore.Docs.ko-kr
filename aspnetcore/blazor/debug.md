---
title: "제목: ‘ASP.NET Core 디버그 Blazor WebAssembly’ author: guardrex description: 'Blazor 앱을 디버그하는 방법을 알아봅니다.'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/31/2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 193dc656c2ee0154f0ae534bc00f8dc29bab3258
ms.sourcegitcommit: 2e9973cea5c36d0dd64d5e946bb776b8bb73a4b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84239225"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a>'Blazor'

'Identity'

'Let's Encrypt' 'Razor'

'SignalR' uid: blazor/debug

* ASP.NET Core Blazor WebAssembly 디버그
* [Daniel Roth](https://github.com/danroth27)
* Blazor WebAssembly 앱은 Chromium 기반 브라우저(Edge/Chrome)의 브라우저 개발 도구를 사용하여 디버그할 수 있습니다.
* 또는 Visual Studio나 Visual Studio Code를 사용하여 앱을 디버그할 수도 있습니다.
* 사용 가능한 시나리오는 다음과 같습니다.
* 중단점을 설정하고 제거합니다.

Visual Studio 및 Visual Studio Code에서 디버깅 지원으로 앱을 실행합니다(<kbd>F5</kbd> 키 지원).

* 코드를 한 단계씩 실행합니다(<kbd>F10</kbd> 키).
* 브라우저에서 <kbd>F8</kbd> 키를 사용하여 또는 Visual Studio나 Visual Studio Code에서 <kbd>F5</kbd> 키를 사용하여 코드 실행을 다시 시작합니다.

‘로컬’ 표시에서 지역 변수의 값을 확인합니다.

## <a name="prerequisites"></a>JavaScript에서 .NET으로 이동하고 .NET에서 JavaScript로 이동하는 호출 체인을 포함하여 호출 스택을 확인합니다.

현재 사용 가능하지 ‘않은’ 시나리오는 다음과 같습니다.

* 처리되지 않은 예외에서 중단합니다.
* 앱 시작 중에 중단점을 적중시킵니다.

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>향후 릴리스에서 계속해서 디버깅 환경을 개선하기 위해 노력하겠습니다.

필수 구성 요소

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

디버깅 작업에는 다음 브라우저 중 하나가 필요합니다.

[!code-json[](debug/launchSettings.json?highlight=14,22)]

Microsoft Edge(버전 80 이상)

* Google Chrome(버전 70 이상)
* Visual Studio 및 Visual Studio Code의 디버깅 사용 설정

기존 Blazor WebAssembly 앱에서 디버깅을 사용하도록 설정하려면 각 시작 프로필이 다음 `inspectUri` 속성을 포함하도록 프로젝트의 *launchSettings.json* 파일을 업데이트합니다.

## <a name="visual-studio"></a>업데이트하면 *launchSettings.json* 파일이 다음 예와 비슷해집니다.

`inspectUri` 속성은

1. 앱이 Blazor WebAssembly 앱인지를 감지할 수 있도록 IDE를 설정합니다.
1. Blazor의 디버깅 프록시를 통해 브라우저에 연결하도록 스크립트 디버깅 인프라에 지시합니다.
1. 시작된 브라우저(`browserInspectUri`)의 Websocket 프로토콜(`wsProtocol`), 호스트(`url.hostname`), 포트(`url.port`) 및 검사기 URI에 대한 자리 표시자 값은 프레임워크에서 제공됩니다.
1. Visual Studio

   ![Visual Studio에서 Blazor WebAssembly 앱을 디버그하려면](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. ASP.NET Core가 호스트한 새 Blazor WebAssembly 앱을 만듭니다.

   ![<kbd>F5</kbd> 키를 눌러 디버거에서 앱을 실행합니다.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. `IncrementCount` 메서드의 *Counter.razor*에 중단점을 설정합니다.

**카운터** 탭으로 이동하여 중단점을 적중시키도록 단추를 선택합니다.

1. 디버그 카운터
1. 로컬 창에서 `currentCount` 필드의 값을 확인합니다.
1. 로컬 보기

   ![<kbd>F5</kbd> 키를 눌러 실행을 계속합니다.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Blazor WebAssembly 앱을 디버그하는 동안 서버 코드도 디버그할 수 있습니다.

   ![<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>의 *FetchData.razor* 페이지에 중단점을 설정합니다.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. `Get` 작업 메서드의 `WeatherForecastController`에 중단점을 설정합니다.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>**데이터 페치** 탭으로 이동하여 `FetchData` 구성 요소가 서버에 HTTP 요청을 보내기 직전에 첫 번째 중단점을 적중시킵니다.

데이터 페치 디버그
 
<kbd>F5</kbd> 키를 눌러 실행을 계속하고 서버에서 `WeatherForecastController`의 중단점을 적중시킵니다.

![서버 디버그](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![<kbd>F5</kbd> 키를 다시 눌러 실행이 계속되도록 하고 날씨 예측 테이블이 렌더링되는지 확인합니다.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a>Visual Studio Code

1. Visual Studio Code에서 Blazor WebAssembly 앱을 디버그하려면

   [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)을 설치하고, `debug.javascript.usePreview`를 `true`로 설정하여 [JavaScript Debugger(Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 확장을 설치합니다.
   
   * 확장
   * JS 미리 보기 디버거
   * 디버그 독립 실행형 Blazor WebAssembly

   ![VS Code에서 독립 실행형 Blazor WebAssembly 앱을 엽니다.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. 디버깅을 사용하기 위해 추가 설정이 필요하다는 다음 알림이 표시되면

1. 올바른 확장을 설치했는지 확인하고

   ![JavaScript 미리 보기 디버깅이 사용되는지 확인합니다.](index/_static/blazor-vscode-debugtypes.png)

1. 윈도우를 다시 로드합니다.

1. 추가 설정 필요

   ![<kbd>F5</kbd> 바로 가기 키 또는 메뉴 항목을 사용하여 디버깅을 시작합니다.](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a>메시지가 표시되면 **Blazor WebAssembly 디버그** 옵션을 선택하여 디버깅을 시작합니다.

1. 사용 가능한 디버그 옵션 목록

1. 독립 실행형 앱이 시작되고 디버깅 브라우저가 열립니다. `Counter` 구성 요소의 `IncrementCount` 메서드에 중단점을 설정하고 단추를 선택하여 중단점을 적중시킵니다.

   ![VS Code의 카운터 디버그](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. 호스트된 Blazor WebAssembly를 디버그합니다.

VS Code에서 호스트된 Blazor WebAssembly 앱을 엽니다.

### <a name="attach-to-an-existing-debugging-session"></a>프로젝트에 대한 시작 구성 집합이 없는 경우 다음과 같은 알림이 나타납니다.

**예**를 선택합니다.

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> 필요한 자산 추가 선택 창에서 호스트된 솔루션 내의 서버 프로젝트를 선택합니다.

### <a name="launch-configuration-options"></a>launch.json 파일은 디버거를 시작하기 위한 시작 구성으로 생성됩니다.

기존 디버깅 세션에 연결

| 실행 중인 Blazor 앱에 연결하려면 다음 구성을 사용하여 launch.json 파일을 만듭니다.    | 독립 실행형 앱에서만 디버깅 세션에 연결할 수 있습니다. |
| --------- | ----------- |
| `request` | 전체 스택 디버깅을 사용하려면 VS Code에서 앱을 시작해야 합니다. |
| `url`     | 구성 옵션 시작 `blazorwasm` 디버그 형식에 대해 지원되는 시작 구성 옵션은 다음과 같습니다. |
| `browser` | 옵션 Description `launch`를 사용해 시작하고 Blazor WebAssembly 앱에 디버깅 세션을 연결하거나 `attach`를 사용해 이미 실행 중인 앱에 디버깅 세션을 연결할 수 있습니다. |
| `trace`   | 디버깅할 때 브라우저에서 열 URL입니다. 기본값은 `https://localhost:5001`입니다. |
| `hosted`  | 디버깅 세션을 시작하는 브라우저입니다. |
| `webRoot` | `edge` 또는 `chrome`로 설정합니다. 기본값은 `chrome`입니다. |
| `timeout` | JS 디버거에서 로그를 생성하는 데 사용됩니다. 로그를 생성하려면 `true`로 설정합니다. |
| `program` | 호스트된 Blazor WebAssembly 앱을 시작하고 디버깅하는 경우 `true`로 설정해야 합니다. 웹 서버의 절대 경로를 지정합니다. |
| `cwd`     | 하위 경로에서 앱이 제공되는 경우 설정해야 합니다. 디버깅 세션이 연결되기를 기다릴 밀리초 수입니다. |
| `env`     | 기본값은 30,000 밀리초(30초)입니다. 호스트된 앱의 서버를 실행하는 실행 파일에 대한 참조입니다. |

### <a name="example-launch-configurations"></a>`hosted`가 `true`일 경우 설정해야 합니다.

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a>앱을 시작할 작업 디렉터리입니다.

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>`hosted`가 `true`일 경우 설정해야 합니다.

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a>시작된 프로세스에 제공할 환경 변수입니다.

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a>`hosted`가 `true`로 설정된 경우에만 적용됩니다.

1. 시작 구성 예제

1. 독립 실행형 Blazor WebAssembly 앱 시작 및 디버그

1. 지정된 URL에서 실행 중인 앱에 연결 호스트된 Blazor WebAssembly 앱 시작 및 디버그 브라우저에서 디버그 개발 환경에서 앱의 디버그 빌드를 실행합니다.

<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>를 누릅니다. 브라우저는 원격 디버깅이 설정된 상태로 실행되어야 합니다. 원격 디버깅을 사용하지 않도록 설정한 경우, **디버그 가능한 브라우저 탭을 찾을 수 없음** 오류 페이지가 생성됩니다. 오류 페이지에는 Blazor 디버깅 프록시가 앱에 연결할 수 있도록 디버깅 포트가 열려 있는 상태에서 브라우저를 실행하는 작업에 대한 지침이 포함되어 있습니다.

‘모든 브라우저 인스턴스를 닫고’ 지침에 따라 브라우저를 다시 시작합니다. 원격 디버깅을 사용하도록 설정하여 브라우저를 실행한 후 디버깅 바로 가기 키를 누르면 새 디버거 탭이 열립니다. 잠시 후에 **소스** 탭에 앱의 .NET 어셈블리 목록이 표시됩니다. 각 어셈블리를 펼쳐 디버깅에 사용할 수 있는 *.cs*/ *.razor* 소스 파일을 찾습니다.

## <a name="browser-source-maps"></a>중단점을 설정하고 앱의 탭으로 다시 전환한 다음, 코드를 실행하면 중단점이 적중됩니다.

중단점이 적중되면 코드를 한 단계씩 실행(<kbd>F10</kbd> 키)하거나 코드 실행을 정상적으로 다시 시작(<kbd>F8</kbd> 키)합니다. Blazor에서는 [Chrome DevTools 프로토콜](https://chromedevtools.github.io/devtools-protocol/)을 구현하고 .NET 특정 정보를 사용하여 프로토콜을 보강하는 디버깅 프록시를 제공합니다. 디버깅 바로 가기 키를 누르면 Blazor는 프록시에 있는 Chrome DevTools를 가리킵니다.

## <a name="troubleshoot"></a>프록시는 디버그하려는 브라우저 창에 연결되므로 원격 디버깅을 사용하도록 설정해야 합니다.

브라우저 소스 맵

* 브라우저 소스 맵을 사용하면 브라우저에서 컴파일된 파일을 원래 소스 파일에 다시 매핑할 수 있으며, 일반적으로 클라이언트 쪽 디버깅에 사용됩니다. 그러나 Blazor는 현재 C#을 JavaScript/WASM에 직접 매핑하지 않습니다.
* 대신, Blazor가 브라우저 내에서 IL 해석을 수행하므로 소스 맵은 관련이 없습니다. 문제 해결
