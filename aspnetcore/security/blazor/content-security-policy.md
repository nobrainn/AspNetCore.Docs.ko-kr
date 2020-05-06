---
title: ASP.NET Core에 대 한 콘텐츠 보안 정책 적용Blazor
author: guardrex
description: ASP.NET Core Blazor APPS에서 CSP (콘텐츠 보안 정책)를 사용 하 여 XSS (사이트 간 스크립팅) 공격 으로부터 보호 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/content-security-policy
ms.openlocfilehash: 8c5e1c5dd2d41efade91a612bea2855569a61fee
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775585"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a>ASP.NET Core에 대 한 콘텐츠 보안 정책 적용Blazor

[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[XSS (교차 사이트 스크립팅)](xref:security/cross-site-scripting) 는 공격자가 하나 이상의 악의적인 클라이언트 쪽 스크립트를 앱의 렌더링 된 콘텐츠에 배치 하는 보안 취약점입니다. CSP (콘텐츠 보안 정책)는 브라우저에 유효한 지를 알리는 방식으로 XSS 공격 으로부터 보호 하는 데 도움이 됩니다.

* 스크립트, 스타일 시트 및 이미지를 포함 하 여 로드 된 콘텐츠의 원본입니다.
* 폼의 허용 되는 URL 대상을 지정 하 여 페이지에서 수행 하는 작업입니다.
* 로드할 수 있는 플러그 인입니다.

응용 프로그램에 CSP를 적용 하기 위해 개발자는 하나 이상의 `Content-Security-Policy` 헤더 또는 `<meta>` 태그에 여러 csp 콘텐츠 보안 *지시문* 을 지정 합니다.

페이지를 로드 하는 동안 브라우저에서 정책을 평가 합니다. 브라우저는 페이지의 소스를 검사 하 여 콘텐츠 보안 지시문의 요구 사항을 충족 하는지 확인 합니다. 리소스에 대해 정책 지시문이 충족 되지 않으면 브라우저에서 리소스를 로드 하지 않습니다. 예를 들어 타사 스크립트를 허용 하지 않는 정책을 고려해 보세요. 페이지에 `src` 특성에서 타사 `<script>` 원본을 사용 하는 태그가 포함 되어 있으면 브라우저에서 스크립트가 로드 되지 않도록 방지 합니다.

CSP는 Chrome, Edge, Firefox, Opera 및 Safari를 비롯 한 대부분의 최신 데스크톱 및 모바일 브라우저에서 지원 됩니다. 앱에 Blazor 는 CSP가 권장 됩니다.

## <a name="policy-directives"></a>정책 지시문

최소한 앱에 대해 Blazor 다음 지시문 및 소스를 지정 합니다. 필요에 따라 추가 지시문 및 소스를 추가 합니다. 다음 지시문은이 문서의 [정책 적용](#apply-the-policy) 섹션에 사용 됩니다. 여기서는 weasembmbomand Blazor Blazor Server에 대 한 보안 정책 예제를 제공 합니다.

* [기본 uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; 는 페이지의 `<base>` 태그에 대 한 url을 제한 합니다. 체계 `self` 및 포트 번호를 포함 하 여 앱의 원본이 유효한 원본 임을 나타내려면를 지정 합니다.
* [블록-모두-혼합 콘텐츠](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; 는 HTTP 및 HTTPS 콘텐츠 혼합을 로드 하지 않습니다.
* [기본값-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; 는 정책에 의해 명시적으로 지정 되지 않은 소스 지시문에 대 한 대체 (fallback)를 나타냅니다. 체계 `self` 및 포트 번호를 포함 하 여 앱의 원본이 유효한 원본 임을 나타내려면를 지정 합니다.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; 는 이미지에 대 한 유효한 소스를 나타냅니다.
  * Url `data:` 에서 `data:` 이미지 로드를 허용 하도록 지정 합니다.
  * HTTPS `https:` 끝점에서 이미지 로드를 허용 하도록 지정 합니다.
* [개체-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; 는 `<object>`, 및 `<embed>` `<applet>` 태그의 유효한 소스를 나타냅니다. 모든 `none` URL 원본을 방지 하도록 지정 합니다.
* [스크립트-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; 는 스크립트의 유효한 원본을 나타냅니다.
  * 부트스트랩 스크립트 `https://stackpath.bootstrapcdn.com/` 에 대 한 호스트 소스를 지정 합니다.
  * 체계 `self` 및 포트 번호를 포함 하 여 앱의 원본이 유효한 원본 임을 나타내려면를 지정 합니다.
  * Blazor Weasembomapp에서:
    * 필요한 Blazor weasembomin인라인 스크립트를 로드 하도록 허용 하려면 다음 해시를 지정 합니다.
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * 및 `unsafe-eval` 메서드를 `eval()` 사용 하 여 문자열에서 코드를 만드는 방법을 지정 합니다.
  * Blazor 서버 앱에서 스타일 시트에 대 `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` 한 대체 (fallback) 검색을 수행 하는 인라인 스크립트에 대 한 해시를 지정 합니다.
* [스타일-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; 는 스타일 시트의 유효한 원본을 나타냅니다.
  * 부트스트랩 스타일 `https://stackpath.bootstrapcdn.com/` 시트에 대 한 호스트 소스를 지정 합니다.
  * 체계 `self` 및 포트 번호를 포함 하 여 앱의 원본이 유효한 원본 임을 나타내려면를 지정 합니다.
  * 인라인 `unsafe-inline` 스타일을 사용할 수 있도록 지정 합니다. 서버 앱의 Blazor UI에서 초기 요청 후 클라이언트와 서버를 다시 연결 하려면 인라인 선언이 필요 합니다. 이후 릴리스에서는 `unsafe-inline` 가 더 이상 필요 하지 않도록 인라인 스타일이 제거 될 수 있습니다.
* [업그레이드-안전](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; 하지 않은 요청은 안전 하지 않은 (HTTP) 원본의 콘텐츠 url을 HTTPS를 통해 안전 하 게 가져와야 함을 나타냅니다.

이전 지시문은 Microsoft Internet Explorer를 제외한 모든 브라우저에서 지원 됩니다.

추가 인라인 스크립트에 대 한 SHA 해시를 얻으려면:

* [정책 적용](#apply-the-policy) 섹션에 표시 된 CSP를 적용 합니다.
* 응용 프로그램을 로컬로 실행 하는 동안 브라우저의 개발자 도구 콘솔에 액세스 합니다. 브라우저는 CSP 헤더 또는 `meta` 태그가 있을 때 차단 된 스크립트에 대 한 해시를 계산 하 고 표시 합니다.
* 브라우저에서 제공 하는 해시를 `script-src` 원본에 복사 합니다. 각 해시 주위에 작은따옴표를 사용 합니다.

콘텐츠 보안 정책 수준 2 브라우저 지원 매트릭스는 [사용 가능: 콘텐츠 보안 정책 수준 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)를 참조 하세요.

## <a name="apply-the-policy"></a>정책 적용

태그를 `<meta>` 사용 하 여 정책을 적용 합니다.

* `http-equiv` 특성의 값을로 `Content-Security-Policy`설정 합니다.
* `content` 특성 값에 지시문을 추가 합니다. 지시문을 세미콜론 (`;`)으로 구분 합니다.
* 항상 `<head>` 콘텐츠에 태그 `meta` 를 추가 합니다.

다음 섹션에는 weasembmbambmbmbmbommbmboma 및 Blazor Blazor Server에 대 한 이러한 예제는의 Blazor각 릴리스에 대 한이 문서에서 버전 관리 됩니다. 릴리스에 적절 한 버전을 사용 하려면이 웹 페이지의 **버전** 드롭다운 선택기를 사용 하 여 문서 버전을 선택 합니다.

### <a name="blazor-webassembly"></a>Blazor WebAssembly

Wwwroot/index.html 호스트 `<head>` 페이지의 내용 *wwwroot/index.html* 에서 [정책 지시문](#policy-directives) 섹션에 설명 된 지시문을 적용 합니다.

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### <a name="blazor-server"></a>Blazor 서버

`<head>` *Pages/_Host* 의 콘텐츠 페이지에서 [정책 지시문](#policy-directives) 섹션에 설명 된 지시문을 적용 합니다.

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a>Meta 태그 제한 사항

`<meta>` 태그 정책은 다음 지시문을 지원 하지 않습니다.

* [프레임-상위 항목](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [보고서-대상](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [report-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [샌드박스에서](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

이전 지시문을 지원 하려면 라는 `Content-Security-Policy`헤더를 사용 합니다. 지시문 문자열은 헤더의 값입니다.

## <a name="test-a-policy-and-receive-violation-reports"></a>정책 테스트 및 위반 보고서 받기

테스트를 통해 초기 정책을 빌드할 때 타사 스크립트가 실수로 차단 되지 않았는지 확인할 수 있습니다.

정책 지시문을 적용 하지 않고 일정 시간 동안 정책을 테스트 하려면 헤더 기반 정책의 `<meta>` 태그 `http-equiv` 특성 또는 헤더 이름을로 `Content-Security-Policy-Report-Only`설정 합니다. 오류 보고서는 지정 된 URL에 JSON 문서로 전송 됩니다. 자세한 내용은 [MDN 웹 문서: 내용-보안-정책-보고서 전용](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)을 참조 하세요.

정책이 활성화 된 동안 위반에 대 한 보고는 다음 문서를 참조 하세요.

* [보고서-대상](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [report-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

`report-uri` 는 더 이상 사용 하지 않는 것이 좋지만 모든 주요 브라우저에서를 `report-to` 지원할 때까지 두 지시문을 모두 사용 해야 합니다. 에 대 한 `report-uri` `report-uri` 지원은 브라우저에서 언제 *든* 지 삭제 될 수 있으므로 독점적으로 사용 하지 마세요. 가 완전히 지원 `report-uri` 되는 경우 `report-to` 정책에서에 대 한 지원을 제거 합니다. 채택을 `report-to`추적 하려면 [사용 가능: 보고 대상](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)을 참조 하세요.

릴리스 마다 앱 정책을 테스트 하 고 업데이트 합니다.

## <a name="troubleshoot"></a>문제 해결

* 브라우저의 개발자 도구 콘솔에 오류가 표시 됩니다. 브라우저는 다음에 대 한 정보를 제공 합니다.
  * 정책을 준수 하지 않는 요소입니다.
  * 차단 된 항목을 허용 하도록 정책을 수정 하는 방법입니다.
* 정책은 클라이언트의 브라우저에서 포함 된 모든 지시문을 지 원하는 경우에만 완전히 적용 됩니다. 현재 브라우저 지원 매트릭스는 [사용 가능: 내용-보안 정책](https://caniuse.com/#search=Content-Security-Policy)을 참조 하세요.

## <a name="additional-resources"></a>추가 자료

* [MDN 웹 문서: 콘텐츠-보안-정책](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [콘텐츠 보안 정책 수준 2](https://www.w3.org/TR/CSP2/)
* [Google CSP 계산기](https://csp-evaluator.withgoogle.com/)
