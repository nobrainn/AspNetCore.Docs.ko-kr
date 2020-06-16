---
title: ASP.NET Core Blazor 지원 플랫폼
author: guardrex
description: ASP.NET Core Blazor 지원 플랫폼에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: c10132c87c93346af89c548363e786967609f3da
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/12/2020
ms.locfileid: "83607981"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor 지원 플랫폼

[Luke Latham](https://github.com/guardrex)으로

## <a name="browser-requirements"></a>브라우저 요구 사항

### <a name="blazor-webassembly"></a>Blazor WebAssembly

| 브라우저                          | 버전               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | 현재               |
| Mozilla Firefox                  | 현재               |
| Google Chrome(Android 포함) | 현재               |
| Safari(iOS 포함)            | 현재               |
| Microsoft Internet Explorer      | 지원되지 않음&dagger; |

&dagger;Microsoft Internet Explorer는 [WebAssembly](https://webassembly.org)를 지원하지 않습니다.

### <a name="blazor-server"></a>Blazor 서버

| 브라우저                          | 버전    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | 현재    |
| Mozilla Firefox                  | 현재    |
| Google Chrome(Android 포함) | 현재    |
| Safari(iOS 포함)            | 현재    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;추가 보충 기능이 필요합니다(예: [Polyfill.io](https://polyfill.io/v3/) 번들을 통해 프라미스를 추가할 수 있음).

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/hosting-models>
