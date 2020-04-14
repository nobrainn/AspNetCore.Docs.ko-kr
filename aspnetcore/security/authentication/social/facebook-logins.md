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
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="12b38-103">ASP.NET 코어에서 페이스 북 외부 로그인 설정</span><span class="sxs-lookup"><span data-stu-id="12b38-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="12b38-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="12b38-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="12b38-105">코드 예제가 있는 이 자습서에서는 [사용자가 이전 페이지에서](xref:security/authentication/social/index)만든 Core 3.0 프로젝트를 ASP.NET 샘플을 사용하여 Facebook 계정으로 로그인할 수 있도록 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="12b38-106">우리는 [공식 단계에](https://developers.facebook.com)따라 페이스 북 앱 ID를 만드는 것으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="12b38-107">페이스 북에서 응용 프로그램 만들기</span><span class="sxs-lookup"><span data-stu-id="12b38-107">Create the app in Facebook</span></span>

* <span data-ttu-id="12b38-108">프로젝트에 [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="12b38-109">Facebook 개발자 앱 페이지로 이동하여 [로그인합니다.](https://developers.facebook.com/apps/)</span><span class="sxs-lookup"><span data-stu-id="12b38-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="12b38-110">아직 Facebook 계정이 없는 경우 로그인 페이지에서 **Facebook 가입** 링크를 사용하여 계정을 만드세요.</span><span class="sxs-lookup"><span data-stu-id="12b38-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="12b38-111">페이스북 계정이 있으면 안내에 따라 페이스북 개발자로 등록하세요.</span><span class="sxs-lookup"><span data-stu-id="12b38-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="12b38-112">내 **앱** 메뉴에서 **앱 만들기를** 선택하여 새 앱 ID를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![마이크로소프트 에지에서 열려 있는 개발자 포털에 대 한 페이스 북](index/_static/FBMyApps.png)

* <span data-ttu-id="12b38-114">양식을 작성하고 **앱 ID 만들기** 버튼을 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![새 앱 ID 양식 만들기](index/_static/FBNewAppId.png)

* <span data-ttu-id="12b38-116">새 앱 카드에서 **제품 추가를**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="12b38-117">Facebook **로그인** 카드에서 **설정**</span><span class="sxs-lookup"><span data-stu-id="12b38-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![제품 설정 페이지](index/_static/FBProductSetup.png)

* <span data-ttu-id="12b38-119">**빠른 시작** 마법사는 첫 번째 페이지로 **플랫폼 선택으로** 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="12b38-120">왼쪽 하단의 메뉴에서 **페이스북 로그인** **설정** 링크를 클릭하여 마법사를 우회합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![빠른 시작 건너뛰기](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="12b38-122">**클라이언트 OAuth 설정** 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![클라이언트 OAuth 설정 페이지](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="12b38-124">**유효한 OAuth 리디렉션 URI** 필드(예: `https://localhost:44320/signin-facebook`)에 */signin-facebook이* 추가된 개발 URI를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="12b38-125">이 자습서의 나중에 구성된 Facebook 인증은 OAuth 흐름을 구현하기 위해 */signin-facebook* 경로에서 요청을 자동으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="12b38-126">URI */signin-facebook은* Facebook 인증 공급자의 기본 콜백으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="12b38-127">상속된 원격 인증옵션을 통해 Facebook 인증 미들웨어를 구성하는 동안 기본 콜백 URI를 변경할 수 [있습니다.FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) 클래스의 [CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성.</span><span class="sxs-lookup"><span data-stu-id="12b38-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="12b38-128">**변경 내용 저장**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="12b38-129">왼쪽 탐색에서 **설정** > **기본** 링크를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="12b38-130">이 페이지에서 및 의 메모를 `App ID` 작성합니다. `App Secret`</span><span class="sxs-lookup"><span data-stu-id="12b38-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="12b38-131">다음 섹션에서 ASP.NET 핵심 응용 프로그램에 둘 다 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="12b38-132">사이트를 배포할 때 **Facebook 로그인** 설정 페이지를 다시 방문하고 새 공용 URI를 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="12b38-133">페이스 북 앱 ID와 비밀을 저장</span><span class="sxs-lookup"><span data-stu-id="12b38-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="12b38-134">[비밀 관리자와](xref:security/app-secrets)페이스 북 앱 ID 및 비밀 값과 같은 중요한 설정을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="12b38-135">이 샘플의 경우 다음 단계를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="12b38-136">비밀 저장소 활성화에서 지침에 따라 비밀 [저장소에](xref:security/app-secrets#enable-secret-storage)대한 프로젝트를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="12b38-137">로컬 비밀 저장소에 비밀 키와 `Authentication:Facebook:AppId` 함께 `Authentication:Facebook:AppSecret`중요한 설정을 저장하고 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="12b38-138">페이스 북 인증 구성</span><span class="sxs-lookup"><span data-stu-id="12b38-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="12b38-139">`ConfigureServices` *Startup.cs* 파일의 메서드에 Facebook 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="12b38-140">페이스북으로 로그인</span><span class="sxs-lookup"><span data-stu-id="12b38-140">Sign in with Facebook</span></span>

* <span data-ttu-id="12b38-141">앱을 실행하고 **로그인을**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="12b38-142">**로그인하려면 다른 서비스를 사용중입니다.**</span><span class="sxs-lookup"><span data-stu-id="12b38-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="12b38-143">인증을 위해 **Facebook으로** 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="12b38-144">Facebook 자격 증명을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="12b38-145">이메일로 설정할 수 있는 사이트로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="12b38-146">이제 Facebook 자격 증명을 사용하여 로그인했습니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="12b38-147">외부 로그인 권한 부여 취소에 반응</span><span class="sxs-lookup"><span data-stu-id="12b38-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="12b38-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>사용자가 요청된 권한 부여 요구를 승인하지 않을 때 사용자 에이전트에 대한 리디렉션 경로를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="12b38-149">다음 코드는 `AccessDeniedPath` 다음을 `"/AccessDeniedPathInfo"`설정합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="12b38-150">페이지에는 `AccessDeniedPath` 다음 정보가 포함되어 있는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="12b38-151">원격 인증이 취소되었습니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="12b38-152">이 응용 프로그램은 인증이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-152">This app requires authentication.</span></span>
* <span data-ttu-id="12b38-153">다시 로그인을 시도하려면 로그인 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="12b38-154">테스트 액세스거부 경로</span><span class="sxs-lookup"><span data-stu-id="12b38-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="12b38-155">[facebook.com](https://www.facebook.com/) 이동</span><span class="sxs-lookup"><span data-stu-id="12b38-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="12b38-156">로그인한 경우 로그아웃해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="12b38-157">앱을 실행하고 Facebook 로그인을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="12b38-158">**지금 하지 않음을**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-158">Select **Not now**.</span></span> <span data-ttu-id="12b38-159">지정된 `AccessDeniedPath` 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="12b38-160">Facebook 인증에서 지원하는 구성 옵션에 대한 자세한 내용은 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API 참조를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="12b38-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="12b38-161">구성 옵션은 다음과 같은 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="12b38-162">사용자에 대한 다른 정보를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-162">Request different information about the user.</span></span>
* <span data-ttu-id="12b38-163">쿼리 문자열 인수를 추가하여 로그인 환경을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="12b38-164">문제 해결</span><span class="sxs-lookup"><span data-stu-id="12b38-164">Troubleshooting</span></span>

* <span data-ttu-id="12b38-165">**ASP.NET 코어 2.x 만:** 에서 호출하여 `services.AddIdentity` `ConfigureServices`ID가 구성되지 않은 경우 인증을 시도하면 *인수 예외: 'SignInScheme' 옵션이 제공되어야 합니다.*</span><span class="sxs-lookup"><span data-stu-id="12b38-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="12b38-166">이 자습서에 사용된 프로젝트 템플릿은 이 작업을 수행하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="12b38-167">초기 마이그레이션을 적용하여 사이트 데이터베이스를 만들지 않은 경우 요청 오류를 *처리하는 동안 데이터베이스 작업이 실패합니다.*</span><span class="sxs-lookup"><span data-stu-id="12b38-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="12b38-168">**마이그레이션 적용을** 탭하여 데이터베이스를 만들고 새로 고침하여 오류를 지나서 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="12b38-169">다음 단계</span><span class="sxs-lookup"><span data-stu-id="12b38-169">Next steps</span></span>

* <span data-ttu-id="12b38-170">이 문서는 페이스 북으로 인증 할 수있는 방법을 보여 주었다.</span><span class="sxs-lookup"><span data-stu-id="12b38-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="12b38-171">이전 [페이지에](xref:security/authentication/social/index)나열된 다른 공급자와 함께 인증하려면 유사한 방법을 따를 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="12b38-172">웹 사이트를 Azure 웹 앱에 게시하면 Facebook `AppSecret` 개발자 포털에서 웹 사이트를 재설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="12b38-173">Azure `Authentication:Facebook:AppId` 포털에서 및 `Authentication:Facebook:AppSecret` 응용 프로그램 설정을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="12b38-174">구성 시스템은 환경 변수에서 키를 읽도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="12b38-174">The configuration system is set up to read keys from environment variables.</span></span>
