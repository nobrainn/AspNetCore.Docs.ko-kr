---
title: Microsoft 계정을 사용 Blazor 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 6f884cbfc9ac50f38c415af522d3d09a3ef38712
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976846"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a>Microsoft 계정을 사용 Blazor 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호

[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

인증을 위해 Blazor [AAD (Azure Active Directory)를](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) 사용 하는 Microsoft 계정을 사용 하는 weasembom독립형 앱을 만들려면:

1. [AAD 테 넌 트 및 웹 응용 프로그램 만들기](/azure/active-directory/develop/v2-overview)

   Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 AAD 앱을 등록 합니다.

   1\. 응용 프로그램의 **이름** (예: ** Blazor 클라이언트 AAD**)을 제공 합니다.<br>
   2\. **지원 되는 계정 유형**에서 **조직 디렉터리의 계정**을 선택 합니다.<br>
   3. **리디렉션 uri** 드롭다운을 **웹**으로 유지 하 고의 `https://localhost:5001/authentication/login-callback`리디렉션 uri를 제공 합니다.<br>
   4 \. **Permissions** > **Grant admin 동의 하도록 요구 to openid connect and offline_access permissions 확인란을** 사용 하지 않도록 설정 합니다.<br>
   5. **등록**을 선택합니다.

   **인증** > **플랫폼 구성** > **웹**에서:

   1\. 의 `https://localhost:5001/authentication/login-callback` **리디렉션 URI** 가 있는지 확인 합니다.<br>
   2\. **암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.<br>
   3. 앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.<br>
   4 \. **저장** 단추를 선택합니다.

   응용 프로그램 ID (클라이언트 ID)를 기록 합니다 (예 `11111111-1111-1111-1111-111111111111`:).

1. 다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우 `-o BlazorSample`)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예:). 또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.

앱을 만든 후 다음을 수행할 수 있습니다.

* Microsoft 계정을 사용 하 여 앱에 로그인 합니다.
* 앱을 올바르게 구성 하는 경우 독립 실행형 Blazor 앱과 동일한 방법을 사용 하 여 Microsoft api에 대 한 액세스 토큰을 요청 합니다. 자세한 내용은 [빠른 시작: 웹 api를 노출 하도록 응용 프로그램 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)을 참조 하세요.

## <a name="authentication-package"></a>인증 패키지

회사 또는 학교 계정 (`SingleOrg`)을 사용 하도록 앱을 만들 때 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`)에 대 한 패키지 참조를 자동으로 받습니다. 패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.

앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

위의 `{VERSION}` 패키지 참조에서를 `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> 문서에 표시 된 패키지의 버전으로 바꿉니다.

`Microsoft.Authentication.WebAssembly.Msal` 패키지는 앱에 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 전이적으로 추가 합니다.

## <a name="authentication-service-support"></a>인증 서비스 지원

사용자 인증에 대 한 지원은 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` 패키지에서 제공 하는 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다. 이 메서드는 앱이 Identity 공급자 (IP)와 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

메서드 `AddMsalAuthentication` 는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다. 앱을 구성 하는 데 필요한 값은 앱을 등록할 때 Microsoft 계정 구성에서 가져올 수 있습니다.

구성은 *wwwroot/appsettings. json* 파일에 의해 제공 됩니다.

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}"
  }
}
```

예제:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a>액세스 토큰 범위

Weasembomtemplate은 Blazor 보안 API에 대 한 액세스 토큰을 요청 하도록 앱을 자동으로 구성 하지 않습니다. 액세스 토큰을 로그인 흐름의 일부로 프로 비전 하려면의 기본 액세스 토큰 범위에 범위를 추가 합니다 `MsalProviderOptions`.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Azure Portal에서 범위 URI를 제공 하 고 앱이 API에서 *401 권한 없음* 응답을 받을 때 **처리 되지 않은 예외를 throw** 하는 경우 스키마와 호스트를 포함 하지 않는 범위 uri를 사용해 보세요. 예를 들어 Azure Portal는 다음 범위 URI 형식 중 하나를 제공할 수 있습니다.
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> 스키마 및 호스트 없이 범위 URI를 제공 합니다.
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

자세한 내용은 *추가 시나리오* 문서의 다음 섹션을 참조 하십시오.

* [추가 액세스 토큰 요청](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [나가는 요청에 토큰 연결](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>파일 가져오기

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>앱 구성 요소

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin 구성 요소

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay 구성 요소

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>인증 구성 요소

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 리소스

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/blazor/webassembly/aad-groups-roles>
* [빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [빠른 시작: 웹 API를 공개하는 애플리케이션 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
