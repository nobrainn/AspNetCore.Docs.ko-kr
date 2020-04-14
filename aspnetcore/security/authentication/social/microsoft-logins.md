---
title: ASP.NET 코어와 마이크로 소프트 계정 외부 로그인 설정
author: rick-anderson
description: 이 샘플에서는 Microsoft 계정 사용자 인증을 기존 ASP.NET Core 앱에 통합했음을 보여 줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 32315267e0672b0747917228f08591a15e4449f8
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277251"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>ASP.NET 코어와 마이크로 소프트 계정 외부 로그인 설정

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 샘플에서는 [사용자가 이전 페이지에서](xref:security/authentication/social/index)만든 ASP.NET Core 3.0 프로젝트를 사용하여 Microsoft 계정으로 로그인할 수 있도록 하는 방법을 보여 주었습니다.

## <a name="create-the-app-in-microsoft-developer-portal"></a>Microsoft 개발자 포털에서 앱 만들기

* 프로젝트에 [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet 패키지를 추가합니다.
* [Azure 포털로](https://go.microsoft.com/fwlink/?linkid=2083908) 이동 - 앱 등록 페이지 및 Microsoft 계정을 만들거나 로그인합니다.

Microsoft 계정이 없는 경우 **하나 만들기를**선택합니다. 로그인하면 앱 등록 페이지로 **리디렉션됩니다.**

* **새 등록** 선택
* **이름**을 입력합니다.
* **지원되는 계정 유형에**대한 옵션을 선택합니다.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* **URI 리디렉션에서**추가된 개발 `/signin-microsoft` URL을 입력합니다. `https://localhost:5001/signin-microsoft`)을 입력합니다. 이 샘플의 후반부에서 구성된 Microsoft 인증 체계는 OAuth 흐름을 구현하기 위한 경로에서 `/signin-microsoft` 요청을 자동으로 처리합니다.
* **레지스터** 선택

### <a name="create-client-secret"></a>클라이언트 비밀 만들기

* 왼쪽 창에서 인증서 **& 비밀을 선택합니다.**
* **클라이언트 암호에서** **새 클라이언트 비밀을 선택합니다.**

  * 클라이언트 보안 에 대한 설명을 추가합니다.
  * **추가** 단추를 선택합니다.

* **클라이언트 암호에서**클라이언트 암호의 값을 복사합니다.

URI 세그먼트는 `/signin-microsoft` Microsoft 인증 공급자의 기본 콜백으로 설정됩니다. 상속된 [원격 인증Options.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) [MicrosoftAccount](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) 옵션 클래스의 CallbackPath 속성을 통해 Microsoft 인증 미들웨어를 구성하는 동안 기본 콜백 URI를 변경할 수 있습니다.

## <a name="store-the-microsoft-client-id-and-secret"></a>Microsoft 클라이언트 ID 및 비밀 저장

Microsoft 클라이언트 ID 및 비밀 값과 같은 중요한 설정을 [비밀 관리자로 저장합니다.](xref:security/app-secrets) 이 샘플의 경우 다음 단계를 사용합니다.

1. 비밀 저장소 활성화에서 지침에 따라 비밀 [저장소에](xref:security/app-secrets#enable-secret-storage)대한 프로젝트를 초기화합니다.
1. 로컬 비밀 저장소에 비밀 키와 `Authentication:Microsoft:ClientId` 함께 `Authentication:Microsoft:ClientSecret`중요한 설정을 저장하고 다음을 수행합니다.

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Microsoft 계정 인증 구성

`Startup.ConfigureServices`다음 에 Microsoft 계정 서비스를 추가합니다.

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Microsoft 계정 인증에서 지원하는 구성 옵션에 대한 자세한 내용은 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API 참조를 참조하십시오. 사용자에 대 한 다른 정보를 요청 하는 데 사용할 수 있습니다.

## <a name="sign-in-with-microsoft-account"></a>Microsoft 계정으로 로그인

앱을 실행하고 **로그인을 클릭합니다.** Microsoft로 로그인하는 옵션이 나타납니다. Microsoft를 클릭하면 인증을 위해 Microsoft로 리디렉션됩니다. Microsoft 계정으로 로그인하면 앱에서 사용자의 정보에 액세스할 수 있도록 하라는 메시지가 표시됩니다.

**예를** 누르면 이메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.

이제 Microsoft 자격 증명을 사용하여 로그인됩니다.

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>문제 해결

* Microsoft 계정 공급자가 로그인 오류 페이지로 리디렉션하는 경우 Uri의 `#` (해시태그) 바로 다음에 오류 제목 및 설명 쿼리 문자열 매개 변수를 기록합니다.

  오류 메시지가 Microsoft 인증에 문제가 있음을 나타내는 것처럼 보이지만 가장 일반적인 원인은 응용 프로그램 Uri가 **웹** 플랫폼에 지정된 **리디렉션 URI와** 일치하지 않기 때문에
* 에서 호출하여 `services.AddIdentity` `ConfigureServices`ID가 구성되지 않은 경우 인증을 시도하면 *인수 예외: 'SignInScheme' 옵션이 제공되어야 합니다.* 이 샘플에 사용된 프로젝트 템플릿은 이 작업을 수행하도록 합니다.
* 초기 마이그레이션을 적용하여 사이트 데이터베이스를 만들지 않은 경우 요청 오류를 *처리하는 동안 데이터베이스 작업이 실패하게* 됩니다. **마이그레이션 적용을** 탭하여 데이터베이스를 만들고 새로 고침하여 오류를 지나서 계속합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서에서는 Microsoft를 통해 인증하는 방법을 보여 주어 도있습니다. 이전 [페이지에](xref:security/authentication/social/index)나열된 다른 공급자와 함께 인증하려면 유사한 방법을 따를 수 있습니다.

* 웹 사이트를 Azure 웹 앱에 게시하면 Microsoft 개발자 포털에서 새 클라이언트 비밀을 만듭니다.

* Azure `Authentication:Microsoft:ClientId` 포털에서 및 `Authentication:Microsoft:ClientSecret` 응용 프로그램 설정을 설정합니다. 구성 시스템은 환경 변수에서 키를 읽도록 설정됩니다.
