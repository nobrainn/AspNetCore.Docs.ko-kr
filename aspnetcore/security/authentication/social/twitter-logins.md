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
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="7a961-103">ASP.NET 코어와 트위터 외부 로그인 설정</span><span class="sxs-lookup"><span data-stu-id="7a961-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="7a961-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7a961-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7a961-105">이 샘플에서는 [사용자가 이전 페이지에서](xref:security/authentication/social/index)만든 Core 3.0 프로젝트 ASP.NET 샘플을 사용하여 트위터 [계정으로 로그인할](https://dev.twitter.com/web/sign-in/desktop-browser) 수 있도록 하는 방법을 보여 주었습니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="7a961-106">트위터에서 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="7a961-106">Create the app in Twitter</span></span>

* <span data-ttu-id="7a961-107">프로젝트에 [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="7a961-108">로 [https://apps.twitter.com/](https://apps.twitter.com/) 이동하여 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="7a961-109">아직 트위터 계정이 없는 경우 **[지금 가입](https://twitter.com/signup)** 링크를 사용하여 계정을 만드세요.</span><span class="sxs-lookup"><span data-stu-id="7a961-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="7a961-110">**앱 만들기를 선택합니다.**</span><span class="sxs-lookup"><span data-stu-id="7a961-110">Select **Create an app**.</span></span> <span data-ttu-id="7a961-111">**앱 이름,** **응용 프로그램 설명** 및 공용 웹 **사이트** URI를 입력합니다(도메인 이름을 등록할 때까지 일시적일 수 있음).</span><span class="sxs-lookup"><span data-stu-id="7a961-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="7a961-112">**트위터로 로그인 사용 설정** 옆의 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="7a961-113">Microsoft.AspNetCore.Identity는 사용자에게 기본적으로 전자 메일 주소를 가져야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="7a961-114">**사용 권한** 탭으로 이동하여 **편집** 단추를 클릭하고 사용자의 전자 메일 주소 요청 옆의 **확인란을**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="7a961-115">**콜백 URL** `/signin-twitter` 필드(예: `https://webapp128.azurewebsites.net/signin-twitter`)에 추가된 개발 URI를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="7a961-116">이 샘플의 후반부에서 구성된 Twitter 인증 체계는 OAuth 흐름을 구현하기 위한 경로에서 `/signin-twitter` 요청을 자동으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="7a961-117">URI 세그먼트는 `/signin-twitter` 트위터 인증 공급자의 기본 콜백으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="7a961-118">상속된 [원격 인증Options.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) 클래스를 통해 트위터 인증 미들웨어를 구성하는 동안 기본 콜백 URI를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="7a961-119">양식의 나머지 부분을 작성하고 **만들기를**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="7a961-120">새 응용 프로그램 세부 정보가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-120">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="7a961-121">트위터 소비자 API 키 및 비밀 저장</span><span class="sxs-lookup"><span data-stu-id="7a961-121">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="7a961-122">[비밀 관리자와](xref:security/app-secrets)트위터 소비자 API 키 및 비밀과 같은 중요한 설정을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-122">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="7a961-123">이 샘플의 경우 다음 단계를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-123">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="7a961-124">비밀 저장소 활성화에서 지침에 따라 비밀 [저장소에](xref:security/app-secrets#enable-secret-storage)대한 프로젝트를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-124">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="7a961-125">로컬 비밀 저장소에 비밀 키와 함께 `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret`중요한 설정을 저장하고 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-125">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="7a961-126">이러한 토큰은 새 Twitter 응용 프로그램을 만든 후 키 및 액세스 토큰 탭에서 찾을 수 **있습니다.**</span><span class="sxs-lookup"><span data-stu-id="7a961-126">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="7a961-127">트위터 인증 구성</span><span class="sxs-lookup"><span data-stu-id="7a961-127">Configure Twitter Authentication</span></span>

<span data-ttu-id="7a961-128">`ConfigureServices` *Startup.cs* 파일의 메서드에 트위터 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-128">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="7a961-129">트위터 인증에서 지원하는 구성 옵션에 대한 자세한 내용은 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API 참조를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7a961-129">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="7a961-130">사용자에 대 한 다른 정보를 요청 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-130">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="7a961-131">트위터로 로그인</span><span class="sxs-lookup"><span data-stu-id="7a961-131">Sign in with Twitter</span></span>

<span data-ttu-id="7a961-132">앱을 실행하고 **로그인을**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-132">Run the app and select **Log in**.</span></span> <span data-ttu-id="7a961-133">트위터로 로그인할 수 있는 옵션이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-133">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="7a961-134">**트위터를** 클릭하면 인증을 위해 트위터로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-134">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="7a961-135">트위터 자격 증명을 입력하면 이메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-135">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="7a961-136">이제 트위터 자격 증명을 사용하여 로그인했습니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-136">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="7a961-137">문제 해결</span><span class="sxs-lookup"><span data-stu-id="7a961-137">Troubleshooting</span></span>

* <span data-ttu-id="7a961-138">**ASP.NET 코어 2.x 만:** 에서 호출하여 `services.AddIdentity` `ConfigureServices`ID가 구성되지 않은 경우 인증을 시도하면 *인수 예외: 'SignInScheme' 옵션이 제공되어야 합니다.*</span><span class="sxs-lookup"><span data-stu-id="7a961-138">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="7a961-139">이 샘플에 사용된 프로젝트 템플릿은 이 작업을 수행하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-139">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="7a961-140">초기 마이그레이션을 적용하여 사이트 데이터베이스를 만들지 않은 경우 요청 오류를 *처리하는 동안 데이터베이스 작업이 실패하게* 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-140">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="7a961-141">**마이그레이션 적용을** 탭하여 데이터베이스를 만들고 새로 고침하여 오류를 지나서 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-141">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7a961-142">다음 단계</span><span class="sxs-lookup"><span data-stu-id="7a961-142">Next steps</span></span>

* <span data-ttu-id="7a961-143">이 문서에서는 트위터로 인증하는 방법을 보여 주어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-143">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="7a961-144">이전 [페이지에](xref:security/authentication/social/index)나열된 다른 공급자와 함께 인증하려면 유사한 방법을 따를 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-144">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="7a961-145">웹 사이트를 Azure 웹 앱에 게시하면 Twitter `ConsumerSecret` 개발자 포털에서 웹 사이트를 재설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-145">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="7a961-146">Azure `Authentication:Twitter:ConsumerKey` 포털에서 및 `Authentication:Twitter:ConsumerSecret` 응용 프로그램 설정을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-146">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="7a961-147">구성 시스템은 환경 변수에서 키를 읽도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a961-147">The configuration system is set up to read keys from environment variables.</span></span>
