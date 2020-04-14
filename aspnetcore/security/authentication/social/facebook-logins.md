---
title: ASP.NET 코어에서 페이스 북 외부 로그인 설정
author: rick-anderson
description: 기존 ASP.NET 핵심 응용 프로그램에 페이스 북 계정 사용자 인증의 통합을 보여주는 코드 예제와 튜토리얼.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277303"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>ASP.NET 코어에서 페이스 북 외부 로그인 설정

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

코드 예제가 있는 이 자습서에서는 [사용자가 이전 페이지에서](xref:security/authentication/social/index)만든 Core 3.0 프로젝트를 ASP.NET 샘플을 사용하여 Facebook 계정으로 로그인할 수 있도록 하는 방법을 보여 줍니다. 우리는 [공식 단계에](https://developers.facebook.com)따라 페이스 북 앱 ID를 만드는 것으로 시작합니다.

## <a name="create-the-app-in-facebook"></a>페이스 북에서 응용 프로그램 만들기

* 프로젝트에 [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet 패키지를 추가합니다.

* Facebook 개발자 앱 페이지로 이동하여 [로그인합니다.](https://developers.facebook.com/apps/) 아직 Facebook 계정이 없는 경우 로그인 페이지에서 **Facebook 가입** 링크를 사용하여 계정을 만드세요.  페이스북 계정이 있으면 안내에 따라 페이스북 개발자로 등록하세요.

* 내 **앱** 메뉴에서 **앱 만들기를** 선택하여 새 앱 ID를 만듭니다.

   ![마이크로소프트 에지에서 열려 있는 개발자 포털에 대 한 페이스 북](index/_static/FBMyApps.png)

* 양식을 작성하고 **앱 ID 만들기** 버튼을 누릅니다.

  ![새 앱 ID 양식 만들기](index/_static/FBNewAppId.png)

* 새 앱 카드에서 **제품 추가를**선택합니다.  Facebook **로그인** 카드에서 **설정** 

  ![제품 설정 페이지](index/_static/FBProductSetup.png)

* **빠른 시작** 마법사는 첫 번째 페이지로 **플랫폼 선택으로** 시작됩니다. 왼쪽 하단의 메뉴에서 **페이스북 로그인** **설정** 링크를 클릭하여 마법사를 우회합니다.

  ![빠른 시작 건너뛰기](index/_static/FBSkipQuickStart.png)

* **클라이언트 OAuth 설정** 페이지가 표시됩니다.

  ![클라이언트 OAuth 설정 페이지](index/_static/FBOAuthSetup.png)

* **유효한 OAuth 리디렉션 URI** 필드(예: `https://localhost:44320/signin-facebook`)에 */signin-facebook이* 추가된 개발 URI를 입력합니다. 이 자습서의 나중에 구성된 Facebook 인증은 OAuth 흐름을 구현하기 위해 */signin-facebook* 경로에서 요청을 자동으로 처리합니다.

> [!NOTE]
> URI */signin-facebook은* Facebook 인증 공급자의 기본 콜백으로 설정됩니다. 상속된 원격 인증옵션을 통해 Facebook 인증 미들웨어를 구성하는 동안 기본 콜백 URI를 변경할 수 [있습니다.FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) 클래스의 [CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성.

* **변경 내용 저장**을 클릭합니다.

* 왼쪽 탐색에서 **설정** > **기본** 링크를 클릭합니다.

  이 페이지에서 및 의 메모를 `App ID` 작성합니다. `App Secret` 다음 섹션에서 ASP.NET 핵심 응용 프로그램에 둘 다 추가합니다.

* 사이트를 배포할 때 **Facebook 로그인** 설정 페이지를 다시 방문하고 새 공용 URI를 등록해야 합니다.

## <a name="store-the-facebook-app-id-and-secret"></a>페이스 북 앱 ID와 비밀을 저장

[비밀 관리자와](xref:security/app-secrets)페이스 북 앱 ID 및 비밀 값과 같은 중요한 설정을 저장합니다. 이 샘플의 경우 다음 단계를 사용합니다.

1. 비밀 저장소 활성화에서 지침에 따라 비밀 [저장소에](xref:security/app-secrets#enable-secret-storage)대한 프로젝트를 초기화합니다.
1. 로컬 비밀 저장소에 비밀 키와 `Authentication:Facebook:AppId` 함께 `Authentication:Facebook:AppSecret`중요한 설정을 저장하고 다음을 수행합니다.

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>페이스 북 인증 구성

`ConfigureServices` *Startup.cs* 파일의 메서드에 Facebook 서비스를 추가합니다.

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>페이스북으로 로그인

* 앱을 실행하고 **로그인을**선택합니다. 
* **로그인하려면 다른 서비스를 사용중입니다.**
* 인증을 위해 **Facebook으로** 리디렉션됩니다.
* Facebook 자격 증명을 입력합니다.
* 이메일로 설정할 수 있는 사이트로 리디렉션됩니다.

이제 Facebook 자격 증명을 사용하여 로그인했습니다.

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>외부 로그인 권한 부여 취소에 반응

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>사용자가 요청된 권한 부여 요구를 승인하지 않을 때 사용자 에이전트에 대한 리디렉션 경로를 제공할 수 있습니다.

다음 코드는 `AccessDeniedPath` 다음을 `"/AccessDeniedPathInfo"`설정합니다.

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

페이지에는 `AccessDeniedPath` 다음 정보가 포함되어 있는 것이 좋습니다.

*  원격 인증이 취소되었습니다.
* 이 응용 프로그램은 인증이 필요합니다.
* 다시 로그인을 시도하려면 로그인 링크를 선택합니다.

### <a name="test-accessdeniedpath"></a>테스트 액세스거부 경로

* [facebook.com](https://www.facebook.com/) 이동
* 로그인한 경우 로그아웃해야 합니다.
* 앱을 실행하고 Facebook 로그인을 선택합니다.
* **지금 하지 않음을**선택합니다. 지정된 `AccessDeniedPath` 페이지로 리디렉션됩니다.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Facebook 인증에서 지원하는 구성 옵션에 대한 자세한 내용은 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API 참조를 참조하십시오. 구성 옵션은 다음과 같은 데 사용할 수 있습니다.

* 사용자에 대한 다른 정보를 요청합니다.
* 쿼리 문자열 인수를 추가하여 로그인 환경을 사용자 지정합니다.

## <a name="troubleshooting"></a>문제 해결

* **ASP.NET 코어 2.x 만:** 에서 호출하여 `services.AddIdentity` `ConfigureServices`ID가 구성되지 않은 경우 인증을 시도하면 *인수 예외: 'SignInScheme' 옵션이 제공되어야 합니다.* 이 자습서에 사용된 프로젝트 템플릿은 이 작업을 수행하도록 합니다.
* 초기 마이그레이션을 적용하여 사이트 데이터베이스를 만들지 않은 경우 요청 오류를 *처리하는 동안 데이터베이스 작업이 실패합니다.* **마이그레이션 적용을** 탭하여 데이터베이스를 만들고 새로 고침하여 오류를 지나서 계속합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서는 페이스 북으로 인증 할 수있는 방법을 보여 주었다. 이전 [페이지에](xref:security/authentication/social/index)나열된 다른 공급자와 함께 인증하려면 유사한 방법을 따를 수 있습니다.

* 웹 사이트를 Azure 웹 앱에 게시하면 Facebook `AppSecret` 개발자 포털에서 웹 사이트를 재설정해야 합니다.

* Azure `Authentication:Facebook:AppId` 포털에서 및 `Authentication:Facebook:AppSecret` 응용 프로그램 설정을 설정합니다. 구성 시스템은 환경 변수에서 키를 읽도록 설정됩니다.
