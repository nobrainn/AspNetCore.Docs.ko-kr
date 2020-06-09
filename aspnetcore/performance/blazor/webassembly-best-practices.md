---
title: ASP.NET Core Blazor weasembomperformance 성능 모범 사례
author: pranavkm
description: ASP.NET Core Blazor weasembmbomapps의 성능을 향상 하 고 일반적인 성능 문제를 방지 하기 위한 팁입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/blazor/webassembly-best-practices
ms.openlocfilehash: 950d87a6f09e998e47e96c93c5d68bb3f19ddafb
ms.sourcegitcommit: 74d80a36103fdbd54baba0118535a4647f511913
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84529634"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a>ASP.NET Core Blazor weasembomperformance 성능 모범 사례

사람, [Pranav Krishnamoorthy](https://github.com/pranavkm)

이 문서에서는 ASP.NET Core에 대 한 지침을 제공 Blazor 합니다.

## <a name="avoid-unnecessary-component-renders"></a>불필요 한 구성 요소 렌더링 방지

Blazor알고리즘에서 구성 요소가 변경 되지 않았다는 것을 인식 하는 경우의 diff 알고리즘은 rerendering를 방지 합니다. <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType>구성 요소 렌더링을 세밀 하 게 제어 하기 위해 재정의 합니다.

초기 렌더링 이후 변경 되지 않는 UI 전용 구성 요소를 작성 하는 경우를 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 반환 하도록를 구성 합니다 `false` .

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

대부분의 앱은 세부적인 제어를 요구 하지 않지만 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> UI 이벤트에 응답 하는 구성 요소를 선택적으로 렌더링 하는 데 사용할 수도 있습니다.

다음 예제에서,

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>는를 재정의 하 고 필드의 값으로 설정 합니다 .이 필드의 값은 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 처음에 `false` 구성 요소를 로드할 때입니다.
* 이 단추를 선택 하면 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 가로 설정 되어 `true` 구성 요소가 업데이트 된와 강제로 rerender 됩니다 `currentCount` .
* Rerendering 바로 뒤 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 의 값을로 설정 하 여 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> `false` 다음 번에 단추를 선택할 때까지 추가 rerendering 방지 합니다.

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

자세한 내용은 <xref:blazor/lifecycle#after-component-render>을 참조하세요.

## <a name="virtualize-re-usable-fragments"></a>다시 사용 가능한 조각 가상화

구성 요소는 코드 및 태그의 재사용 가능한 조각을 생성 하는 편리한 방법을 제공 합니다. 일반적으로 앱의 요구 사항에 가장 잘 맞는 개별 구성 요소를 작성 하는 것이 좋습니다. 한 가지 주의할 점은 각 추가 자식 구성 요소가 부모 구성 요소를 렌더링 하는 데 소요 되는 총 시간에 기여 한다는 것입니다. 대부분의 앱에서 추가 오버 헤드는 무시 됩니다. 많은 수의 구성 요소를 생성 하는 앱은 렌더링 된 구성 요소의 수를 제한 하는 것과 같은 처리 오버 헤드를 줄이기 위해 전략 사용을 고려해 야 합니다.

예를 들어 구성 요소를 포함 하는 수백 개의 행을 렌더링 하는 표 또는 목록은 프로세서를 많이 사용 하 여 렌더링 합니다. 지정 된 시간에 구성 요소의 하위 집합만 렌더링 되도록 그리드 또는 목록 레이아웃을 가상화 하는 것이 좋습니다. 구성 요소 하위 집합 렌더링의 예제는 [가상화 샘플 앱 (aspnet/Samples GitHub 리포지토리)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization)에서 다음 구성 요소를 참조 하세요.

* `Virtualize`구성 요소 ([공유/가상화. razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): <xref:Microsoft.AspNetCore.Components.ComponentBase> 사용자 스크롤을 기준으로 날씨 데이터 행 집합을 렌더링 하기 위해를 구현 하는 c #으로 작성 된 구성 요소입니다.
* `FetchData`구성 요소 ([Pages/fetchdata. razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): 구성 요소를 사용 하 여 `Virtualize` 한 번에 25 개의 기상 데이터 행을 표시 합니다.

## <a name="avoid-javascript-interop-to-marshal-data"></a>JavaScript interop를 사용 하 여 데이터 마샬링 방지

BlazorWeasembmbomommbommbomomomomomomomomomomoma를 호출 해야 합니다. 두 컨텍스트 간에 콘텐츠를 serialize 하 고 deserialize 하면 앱에 대 한 처리 오버 헤드가 발생 합니다. JS interop 호출이 자주 발생 하면 성능이 저하 되는 경우가 많습니다. 경계를 넘어 데이터의 마샬링을 줄이기 위해 앱이 여러 개의 작은 페이로드를 단일 대규모 페이로드로 통합 하 여 Weasembomommbmboma와 JS 간 컨텍스트 전환의 양을 피할 수 있는지 확인 합니다.

## <a name="use-systemtextjson"></a>System.object 사용

Blazor의 JS interop 구현은 <xref:System.Text.Json> 메모리 할당이 낮은 고성능 JSON 직렬화 라이브러리인를 사용 합니다. 를 사용 하면 <xref:System.Text.Json> 대체 JSON 라이브러리를 하나 이상 추가 하는 동안 추가 앱 페이로드 크기가 발생 하지 않습니다.

마이그레이션 지침은 [newtonsoft.json에서 system.object로 마이그레이션하는 방법](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)을 참조 하세요.

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>적절 한 경우 동기 및 마샬링됩니다 JS interop Api 사용

BlazorWeasembmboma <xref:Microsoft.JSInterop.IJSRuntime> 는 서버 앱에서 사용할 수 있는 단일 버전에 대 한 두 가지 추가 버전을 제공 합니다 Blazor .

* <xref:Microsoft.JSInterop.IJSInProcessRuntime>비동기 버전 보다 오버 헤드가 적게 발생 하는 JS interop 호출을 동기식으로 호출할 수 있습니다.

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>마샬링됩니다 JS interop 호출을 허용 합니다.

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > 를 사용 하는 경우 <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> JS interop 방법의 오버 헤드가 가장 적게 발생 하는 반면, 이러한 api와 상호 작용 하는 데 필요한 JavaScript api는 현재 문서화 되지 않으며 이후 릴리스에서 변경 될 수 있습니다.

## <a name="reduce-app-size"></a>앱 크기 줄이기

### <a name="intermediate-language-il-linking"></a>IL (중간 언어) 연결

[연결 Blazor Weasembomapps](xref:host-and-deploy/blazor/configure-linker) 는 앱의 이진에서 사용 하지 않는 코드를 잘라내어 앱 크기를 줄입니다. 기본적으로 링커는 구성에서 빌드하는 경우에만 사용할 수 `Release` 있습니다. 이를 활용 하려면 [-c |--구성](/dotnet/core/tools/dotnet-publish#options) 옵션이로 설정 된 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용 하 여 배포용 앱을 게시 합니다 `Release` .

```dotnetcli
dotnet publish -c Release
```

### <a name="compression"></a>압축

BlazorWeasembomambomapp이 게시 되 면 게시 하는 동안 출력을 정적으로 압축 하 여 앱의 크기를 줄이고 런타임 압축을 위한 오버 헤드를 제거 합니다. Blazor는 서버를 사용 하 여 콘텐츠 negotation를 수행 하 고 정적으로 압축 된 파일을 제공 합니다.

앱이 배포 된 후 앱이 압축 된 파일을 사용 하는지 확인 합니다. 브라우저의 개발자 도구에서 네트워크 탭을 검사 하 고 파일이 또는로 제공 되는지 확인 `Content-Encoding: br` `Content-Encoding: gz` 합니다. 호스트가 압축 된 파일을 처리 하지 않는 경우의 지침을 따르세요 <xref:host-and-deploy/blazor/webassembly#compression> .

### <a name="disable-unused-features"></a>사용 하지 않는 기능 사용 안 함

BlazorWeasemboms runtime에는 앱에서 더 작은 페이로드 크기를 요구 하지 않는 경우 사용 하지 않도록 설정할 수 있는 다음과 같은 .NET 기능이 포함 되어 있습니다.

* 표준 시간대 정보를 정확 하 게 만들기 위한 데이터 파일이 포함 되어 있습니다. 앱에이 기능이 필요 하지 않은 경우 `BlazorEnableTimeZoneSupport` 앱의 프로젝트 파일에서 MSBuild 속성을로 설정 하 여 사용 하지 않도록 설정 하는 것이 좋습니다 `false` .

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* 데이터 정렬 정보는와 같은 Api를 제대로 작동 하도록 하는 데 포함 되어 <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> 있습니다. 응용 프로그램에 데이터 정렬 데이터가 필요 하지 않은 경우 `BlazorWebAssemblyPreserveCollationData` 응용 프로그램의 프로젝트 파일에서 MSBuild 속성을로 설정 하 여 사용 하지 않도록 설정 하는 것이 좋습니다 `false` .

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
