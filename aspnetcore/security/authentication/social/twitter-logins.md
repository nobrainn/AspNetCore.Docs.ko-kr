---
title: ASP.NET 코어와 트위터 외부 로그인 설정
author: rick-anderson
description: 이 자습서에서는 기존 ASP.NET 코어 앱에 트위터 계정 사용자 인증을 통합하는 것을 보여 줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277290"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>ASP.NET 코어와 트위터 외부 로그인 설정

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 샘플에서는 [사용자가 이전 페이지에서](xref:security/authentication/social/index)만든 Core 3.0 프로젝트 ASP.NET 샘플을 사용하여 트위터 [계정으로 로그인할](https://dev.twitter.com/web/sign-in/desktop-browser) 수 있도록 하는 방법을 보여 주었습니다.

## <a name="create-the-app-in-twitter"></a>트위터에서 앱 만들기

* 프로젝트에 [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet 패키지를 추가합니다.

* 로 [https://apps.twitter.com/](https://apps.twitter.com/) 이동하여 로그인합니다. 아직 트위터 계정이 없는 경우 **[지금 가입](https://twitter.com/signup)** 링크를 사용하여 계정을 만드세요.

* **앱 만들기를 선택합니다.** **앱 이름,** **응용 프로그램 설명** 및 공용 웹 **사이트** URI를 입력합니다(도메인 이름을 등록할 때까지 일시적일 수 있음).

* **트위터로 로그인 사용 설정** 옆의 확인란을 선택합니다.

* Microsoft.AspNetCore.Identity는 사용자에게 기본적으로 전자 메일 주소를 가져야 합니다. **사용 권한** 탭으로 이동하여 **편집** 단추를 클릭하고 사용자의 전자 메일 주소 요청 옆의 **확인란을**선택합니다.

* **콜백 URL** `/signin-twitter` 필드(예: `https://webapp128.azurewebsites.net/signin-twitter`)에 추가된 개발 URI를 입력합니다. 이 샘플의 후반부에서 구성된 Twitter 인증 체계는 OAuth 흐름을 구현하기 위한 경로에서 `/signin-twitter` 요청을 자동으로 처리합니다.

  > [!NOTE]
  > URI 세그먼트는 `/signin-twitter` 트위터 인증 공급자의 기본 콜백으로 설정됩니다. 상속된 [원격 인증Options.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) 클래스를 통해 트위터 인증 미들웨어를 구성하는 동안 기본 콜백 URI를 변경할 수 있습니다.

* 양식의 나머지 부분을 작성하고 **만들기를**선택합니다. 새 응용 프로그램 세부 정보가 표시됩니다.

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>트위터 소비자 API 키 및 비밀 저장

[비밀 관리자와](xref:security/app-secrets)트위터 소비자 API 키 및 비밀과 같은 중요한 설정을 저장합니다. 이 샘플의 경우 다음 단계를 사용합니다.

1. 비밀 저장소 활성화에서 지침에 따라 비밀 [저장소에](xref:security/app-secrets#enable-secret-storage)대한 프로젝트를 초기화합니다.
1. 로컬 비밀 저장소에 비밀 키와 함께 `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret`중요한 설정을 저장하고 다음을 수행합니다.

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

이러한 토큰은 새 Twitter 응용 프로그램을 만든 후 키 및 액세스 토큰 탭에서 찾을 수 **있습니다.**

## <a name="configure-twitter-authentication"></a>트위터 인증 구성

`ConfigureServices` *Startup.cs* 파일의 메서드에 트위터 서비스를 추가합니다.

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

트위터 인증에서 지원하는 구성 옵션에 대한 자세한 내용은 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API 참조를 참조하십시오. 사용자에 대 한 다른 정보를 요청 하는 데 사용할 수 있습니다.

## <a name="sign-in-with-twitter"></a>트위터로 로그인

앱을 실행하고 **로그인을**선택합니다. 트위터로 로그인할 수 있는 옵션이 나타납니다.

**트위터를** 클릭하면 인증을 위해 트위터로 리디렉션됩니다.

트위터 자격 증명을 입력하면 이메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.

이제 트위터 자격 증명을 사용하여 로그인했습니다.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>문제 해결

* **ASP.NET 코어 2.x 만:** 에서 호출하여 `services.AddIdentity` `ConfigureServices`ID가 구성되지 않은 경우 인증을 시도하면 *인수 예외: 'SignInScheme' 옵션이 제공되어야 합니다.* 이 샘플에 사용된 프로젝트 템플릿은 이 작업을 수행하도록 합니다.
* 초기 마이그레이션을 적용하여 사이트 데이터베이스를 만들지 않은 경우 요청 오류를 *처리하는 동안 데이터베이스 작업이 실패하게* 됩니다. **마이그레이션 적용을** 탭하여 데이터베이스를 만들고 새로 고침하여 오류를 지나서 계속합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서에서는 트위터로 인증하는 방법을 보여 주어 있습니다. 이전 [페이지에](xref:security/authentication/social/index)나열된 다른 공급자와 함께 인증하려면 유사한 방법을 따를 수 있습니다.

* 웹 사이트를 Azure 웹 앱에 게시하면 Twitter `ConsumerSecret` 개발자 포털에서 웹 사이트를 재설정해야 합니다.

* Azure `Authentication:Twitter:ConsumerKey` 포털에서 및 `Authentication:Twitter:ConsumerSecret` 응용 프로그램 설정을 설정합니다. 구성 시스템은 환경 변수에서 키를 읽도록 설정됩니다.
