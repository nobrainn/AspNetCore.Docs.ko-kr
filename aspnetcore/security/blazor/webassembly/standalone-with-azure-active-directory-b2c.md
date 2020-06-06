---
title: BlazorAzure Active Directory B2C를 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호
author: guardrex
description: ''
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
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 911eeffbe632d8f285d54ae78b62f17851205e83
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84214962"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>BlazorAzure Active Directory B2C를 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호

[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)

Blazor인증을 위해 [AAD (AZURE ACTIVE DIRECTORY) B2C](/azure/active-directory-b2c/overview) 를 사용 하는 weasembom독립형 앱을 만들려면 다음을 수행 합니다.

다음 항목의 지침에 따라 테 넌 트를 만들고 Azure Portal에서 웹 앱을 등록 합니다.

[AAD B2C 테 넌 트 만들기](/azure/active-directory-b2c/tutorial-create-tenant)

다음 정보를 기록 합니다.

* AAD B2C 인스턴스입니다 (예: `https://contoso.b2clogin.com/` 후행 슬래시를 포함 하는).
* AAD B2C 테 넌 트 도메인 (예: `contoso.onmicrosoft.com` ).

[자습서: Azure Active Directory B2C에 응용 프로그램 등록](/azure/active-directory-b2c/tutorial-register-applications) 의 지침에 따라 *클라이언트 앱* 에 대 한 AAD 앱을 등록 하 고 다음을 수행 합니다.

1. **Azure Active Directory**  >  **앱 등록**에서 **새 등록**을 선택 합니다.
1. 앱에 대 한 **이름** (예: ** Blazor 독립 실행형 AAD B2C**)을 제공 합니다.
1. **지원 되는 계정 유형**에 대해 다중 테 넌 트 옵션 ( **모든 조직 디렉터리 또는 모든 Id 공급자의 계정)을 선택 합니다. Azure AD B2C를 사용 하 여 사용자를 인증 합니다.**
1. **리디렉션 uri** 드롭다운 집합을 **웹** 으로 그대로 두고 다음 리디렉션 uri를 제공 `https://localhost:{PORT}/authentication/login-callback` 합니다. Kestrel에서 실행 되는 앱의 기본 포트는 5001입니다. 앱이 다른 Kestrel 포트에서 실행 되는 경우 앱의 포트를 사용 합니다. IIS Express의 경우 앱에 대해 임의로 생성 된 포트는 **디버그** 패널의 앱 속성에서 찾을 수 있습니다. 이 시점에 앱이 존재 하지 않고 IIS Express 포트를 알 수 없으므로 앱을 만든 후에이 단계로 돌아와서 리디렉션 URI를 업데이트 합니다. 사용자 IIS Express 리디렉션 URI를 업데이트 하는 것을 알리기 위해이 항목의 뒷부분에 설명 되어 있습니다.
1. **Permissions**  >  **Openid connect에 관리자 동의 하도록 요구 권한을 부여 하 고 offline_access 권한이** 설정 되어 있는지 확인 합니다.
1. **등록**을 선택합니다.

응용 프로그램 ID (클라이언트 ID)를 기록 합니다 (예: `11111111-1111-1111-1111-111111111111` ).

**인증**  >  **플랫폼 구성**  >  **웹**에서:

1. 의 **리디렉션 URI** 가 있는지 확인 `https://localhost:{PORT}/authentication/login-callback` 합니다.
1. **암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.
1. 앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.
1. **저장** 단추를 선택합니다.

**홈**  >  **Azure AD B2C**  >  **사용자 흐름**:

[가입 및 로그인 사용자 흐름 만들기](/azure/active-directory-b2c/tutorial-create-user-flows)

최소한 **응용 프로그램 클레임**  >  **표시 이름** 사용자 특성을 선택 하 여 `context.User.Identity.Name` `LoginDisplay` 구성 요소 (*Shared/LoginDisplay*)에를 채웁니다.

앱에 대해 만든 등록 및 로그인 사용자 흐름 이름을 기록 합니다 (예: `B2C_1_signupsignin` ).

다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예: `-o BlazorSample` ). 또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.

> [!NOTE]
> Azure Portal에서 앱의 **인증**  >  **플랫폼 구성**  >  **웹**  >  **리디렉션 URI** 는 기본 설정으로 kestrel 서버에서 실행 되는 앱에 대 한 포트 5001에 대해 구성 됩니다.
>
> 앱이 임의의 IIS Express 포트에서 실행 되는 경우 앱에 대 한 포트는 **디버그** 패널의 앱 속성에서 찾을 수 있습니다.
>
> 이전에 앱의 알려진 포트를 사용 하 여 포트를 구성 하지 않은 경우 Azure Portal에서 앱 등록으로 돌아가서 리디렉션 URI를 올바른 포트로 업데이트 합니다.

앱을 만든 후 다음을 수행할 수 있습니다.

* AAD 사용자 계정을 사용 하 여 앱에 로그인 합니다.
* Microsoft Api에 대 한 액세스 토큰을 요청 합니다. 자세한 내용은 다음을 참조하세요.
  * [액세스 토큰 범위](#access-token-scopes)
  * [빠른 시작: 웹 api를 노출 하도록 응용 프로그램을 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)합니다.

## <a name="authentication-package"></a>인증 패키지

개별 B2C 계정 ()을 사용 하도록 앱을 만들면 `IndividualB2C` 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) ()에 대 한 패키지 참조를 자동으로[Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)받습니다. 패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.

앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

AspNetCore [패키지는 응용](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 프로그램에 비 전이적으로 인증 패키지를 추가 하 여 전이적으로 추가 합니다 ( [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) ).

## <a name="authentication-service-support"></a>인증 서비스 지원

사용자 인증 지원은 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> [Microsoft. Authentication. WebAssembly](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) 패키지에서 제공 하는 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다. 이 메서드는 앱이 공급자 (IP)와 상호 작용 하는 데 필요한 모든 서비스를 설정 Identity 합니다.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다. 앱을 구성 하는 데 필요한 값은 앱을 등록할 때 AAD 구성에서 가져올 수 있습니다.

구성은 *wwwroot/appsettings. json* 파일에 의해 제공 됩니다.

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

예제:

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a>액세스 토큰 범위

BlazorWeasembomtemplate은 보안 API에 대 한 액세스 토큰을 요청 하도록 앱을 자동으로 구성 하지 않습니다. 액세스 토큰을 로그인 흐름의 일부로 프로 비전 하려면의 기본 액세스 토큰 범위에 범위를 추가 합니다 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> .

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

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

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 리소스

* <xref:security/blazor/webassembly/additional-scenarios>
* [보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [자습서: Azure Active Directory B2C 테넌트 만들기](/azure/active-directory-b2c/tutorial-create-tenant)
* [Microsoft ID 플랫폼 설명서](/azure/active-directory/develop/)
