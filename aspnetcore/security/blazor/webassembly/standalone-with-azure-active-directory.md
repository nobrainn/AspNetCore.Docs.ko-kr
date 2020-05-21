---
제목: ' Blazor Azure Active Directory ' 만든이를 사용 하 여 ASP.NET Core weasemboman 독립 실행형 앱에 보안을 설정 합니다. 설명: 설명: monikerRange: ms
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ‘SignalR’ uid: 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a>BlazorAzure Active Directory를 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호

[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)

Blazor인증을 위해 [AAD (Azure Active Directory)](https://azure.microsoft.com/services/active-directory/) 를 사용 하는 weasembom독립형 앱을 만들려면 다음을 수행 합니다.

[AAD 테 넌 트 및 웹 응용 프로그램을 만듭니다](/azure/active-directory/develop/v2-overview).

Azure Portal의 **Azure Active Directory**  >  **앱 등록** 영역에서 AAD 앱을 등록 합니다.

1. 앱에 대 한 **이름** (예: ** Blazor 독립 실행형 AAD**)을 제공 합니다.
1. **지원 되는 계정 유형을**선택 합니다. 이 환경에 대해서 **만이 조직 디렉터리에서 계정을** 선택할 수 있습니다.
1. **리디렉션 uri** 드롭다운 집합을 **웹** 으로 그대로 두고 다음 리디렉션 uri를 제공 `https://localhost:{PORT}/authentication/login-callback` 합니다. Kestrel에서 실행 되는 앱의 기본 포트는 5001입니다. 앱이 다른 Kestrel 포트에서 실행 되는 경우 앱의 포트를 사용 합니다. IIS Express의 경우 앱에 대해 임의로 생성 된 포트는 **디버그** 패널의 앱 속성에서 찾을 수 있습니다. 이 시점에 앱이 존재 하지 않고 IIS Express 포트를 알 수 없으므로 앱을 만든 후에이 단계로 돌아와서 리디렉션 URI를 업데이트 합니다. 사용자 IIS Express 리디렉션 URI를 업데이트 하는 것을 알리기 위해이 항목의 뒷부분에 설명 되어 있습니다.
1. **Permissions**  >  **Grant admin 동의 하도록 요구 to openid connect and offline_access permissions 확인란을** 사용 하지 않도록 설정 합니다.
1. **등록**을 선택합니다.

다음 정보를 기록 합니다.

* 응용 프로그램 ID (클라이언트 ID) (예: `11111111-1111-1111-1111-111111111111` )
* 디렉터리 ID (테 넌 트 ID) (예: `22222222-2222-2222-2222-222222222222` )

**인증**  >  **플랫폼 구성**  >  **웹**에서:

1. 의 **리디렉션 URI** 가 있는지 확인 `https://localhost:{PORT}/authentication/login-callback` 합니다.
1. **암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.
1. 앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.
1. **저장** 단추를 선택합니다.

앱을 만듭니다. 다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
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

회사 또는 학교 계정 ()을 사용 하도록 앱을 만들 때 `SingleOrg` 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) ()에 대 한 패키지 참조를 자동으로 받습니다 `Microsoft.Authentication.WebAssembly.Msal` . 패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.

앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

`Microsoft.Authentication.WebAssembly.Msal`패키지는 앱에 패키지를 전이적으로 추가 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 합니다.

## <a name="authentication-service-support"></a>인증 서비스 지원

사용자 인증에 대 한 지원은 패키지에서 제공 하는 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` . 이 메서드는 앱이 공급자 (IP)와 상호 작용 하는 데 필요한 서비스를 설정 Identity 합니다.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

`AddMsalAuthentication`메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다. 앱을 구성 하는 데 필요한 값은 앱을 등록할 때 AAD 구성에서 가져올 수 있습니다.

구성은 *wwwroot/appsettings. json* 파일에 의해 제공 됩니다.

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

예제:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a>액세스 토큰 범위

BlazorWeasembomtemplate은 보안 API에 대 한 액세스 토큰을 요청 하도록 앱을 자동으로 구성 하지 않습니다. 액세스 토큰을 로그인 흐름의 일부로 프로 비전 하려면의 기본 액세스 토큰 범위에 범위를 추가 합니다 `MsalProviderOptions` .

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

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 리소스

* <xref:security/blazor/webassembly/additional-scenarios>
* [보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Microsoft ID 플랫폼 설명서](/azure/active-directory/develop/)
