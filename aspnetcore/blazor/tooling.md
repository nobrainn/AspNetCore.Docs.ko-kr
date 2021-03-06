---
title: ASP.NET Core Blazor용 도구
author: guardrex
description: Blazor 앱을 빌드하는 데 사용할 수 있는 도구에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 33245e669b317ed577a8a1652b2eed8f9ea5b915
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2020
ms.locfileid: "86407647"
---
# <a name="tooling-for-aspnet-core-blazor"></a>ASP.NET Core Blazor용 도구

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

::: zone pivot="os-windows"

1. **ASP.NET 및 웹 개발** 워크로드가 있는 최신 버전의 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)를 설치합니다.

1. 새 프로젝트를 만듭니다.

1. **Blazor 앱**을 선택합니다. **새로 만들기**를 선택합니다.

1. **프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다. **위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다. **만들기**를 선택합니다.

1. Blazor WebAssembly 환경의 경우 **Blazor WebAssembly 앱** 템플릿을 선택합니다. Blazor Server 환경의 경우 **Blazor Server 앱** 템플릿을 선택합니다. **만들기**를 선택합니다.

   두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* 및 *Blazor Server* 에 관한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.

1. <kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 앱을 실행합니다.

ASP.NET Core HTTPS 개발 인증서 신뢰에 대한 자세한 내용은 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>를 참조하세요.

::: zone-end

::: zone pivot="os-linux"

1. [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)의 최신 버전을 설치합니다. 이전에 SDK를 설치한 경우 명령 셸에서 다음 명령을 실행하여 설치된 버전을 확인할 수 있습니다.

   ```dotnetcli
   dotnet --version
   ```

1. 최신 버전의 [Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.

1. 최신 [Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)을 설치합니다.

1. Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Blazor Server 환경의 경우 명령 셸에서 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* 및 *Blazor Server* 에 관한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.

1. Visual Studio Code에서 `WebApplication1` 폴더를 엽니다.

1. IDE에서 프로젝트를 빌드 및 디버그하기 위한 자산을 추가하도록 요청합니다. **Yes**를 선택합니다.

1. <kbd>Ctrl</kbd>+<kbd>F5</kbd>를 눌러 앱을 실행합니다.

## <a name="trust-a-development-certificate"></a>개발 인증서 신뢰

Linux에서 인증서를 신뢰할 수 있는 중앙 집중식 방법은 없습니다. 일반적으로 다음 방법 중 하나를 채택합니다.

* 브라우저의 제외 목록에서 애플리케이션의 URL을 제외합니다.
* `localhost`에 대한 모든 자체 서명된 인증서를 신뢰합니다.
* 브라우저의 신뢰할 수 있는 인증서 목록에 인증서를 추가합니다.

자세한 내용은 브라우저 및 Linux 배포판에서 제공하는 지침을 참조하세요.

::: zone-end

::: zone pivot="os-macos"

1. [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치합니다.

1. **파일** > **새 솔루션**을 선택하거나 **시작 창**에서 **새** 프로젝트를 만듭니다.

1. 사이드바에서 **웹 및 콘솔** > **앱**을 선택합니다.

   Blazor WebAssembly 환경의 경우 **Blazor WebAssembly 앱** 템플릿을 선택합니다. Blazor Server 환경의 경우 **Blazor Server 앱** 템플릿을 선택합니다. **새로 만들기**를 선택합니다.

   두 가지 Blazor 호스팅 모델인 *Blazor WebAssembly* 및 *Blazor Server* 에 관한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.

1. **인증**이 **인증 없음**으로 설정되었는지 확인합니다. **새로 만들기**를 선택합니다.

1. **프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 지정합니다. **만들기**를 선택합니다.

1. **실행** > **디버깅하지 않고 시작**을 선택하여 ‘디버거 없이’ 앱을 실행합니다. **실행** > **디버깅 시작**으로 앱을 실행하거나 실행(&#9654;) 단추를 사용하여 ‘디버거 없이’ 앱을 실행합니다.

개발 인증서를 신뢰하라는 메시지가 표시되면 인증서를 신뢰하고 계속합니다. 인증서를 신뢰하려면 사용자 및 키 집합 암호가 필요합니다. ASP.NET Core HTTPS 개발 인증서 신뢰에 대한 자세한 내용은 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>를 참조하세요.

::: zone-end
