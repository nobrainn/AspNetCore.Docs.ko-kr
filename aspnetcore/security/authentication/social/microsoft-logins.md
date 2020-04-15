---
title: ASP.NET 코어와 마이크로 소프트 계정 외부 로그인 설정
author: rick-anderson
description: 이 샘플에서는 Microsoft 계정 사용자 인증을 기존 ASP.NET Core 앱에 통합했음을 보여 줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 12c86456dad86731b86487a3a4dd725f36677002
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384042"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="e865b-103">ASP.NET 코어와 마이크로 소프트 계정 외부 로그인 설정</span><span class="sxs-lookup"><span data-stu-id="e865b-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="e865b-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e865b-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e865b-105">이 샘플에서는 [사용자가 이전 페이지에서](xref:security/authentication/social/index)만든 ASP.NET Core 3.0 프로젝트를 사용하여 사용자 자신의 업무, 학교 또는 개인 Microsoft 계정으로 로그인할 수 있도록 하는 방법을 보여 주었습니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="e865b-106">Microsoft 개발자 포털에서 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="e865b-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="e865b-107">프로젝트에 [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="e865b-108">[Azure 포털로](https://go.microsoft.com/fwlink/?linkid=2083908) 이동 - 앱 등록 페이지 및 Microsoft 계정을 만들거나 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="e865b-109">Microsoft 계정이 없는 경우 **하나 만들기를**선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="e865b-110">로그인하면 앱 등록 페이지로 **리디렉션됩니다.**</span><span class="sxs-lookup"><span data-stu-id="e865b-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="e865b-111">**새 등록** 선택</span><span class="sxs-lookup"><span data-stu-id="e865b-111">Select **New registration**</span></span>
* <span data-ttu-id="e865b-112">**이름**을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-112">Enter a **Name**.</span></span>
* <span data-ttu-id="e865b-113">**지원되는 계정 유형에**대한 옵션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="e865b-114">**URI 리디렉션에서**추가된 개발 `/signin-microsoft` URL을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="e865b-115">`https://localhost:5001/signin-microsoft`)을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="e865b-116">이 샘플의 후반부에서 구성된 Microsoft 인증 체계는 OAuth 흐름을 구현하기 위한 경로에서 `/signin-microsoft` 요청을 자동으로 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="e865b-117">**레지스터** 선택</span><span class="sxs-lookup"><span data-stu-id="e865b-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="e865b-118">클라이언트 비밀 만들기</span><span class="sxs-lookup"><span data-stu-id="e865b-118">Create client secret</span></span>

* <span data-ttu-id="e865b-119">왼쪽 창에서 인증서 **& 비밀을 선택합니다.**</span><span class="sxs-lookup"><span data-stu-id="e865b-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="e865b-120">**클라이언트 암호에서** **새 클라이언트 비밀을 선택합니다.**</span><span class="sxs-lookup"><span data-stu-id="e865b-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="e865b-121">클라이언트 보안 에 대한 설명을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="e865b-122">**추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-122">Select the **Add** button.</span></span>

* <span data-ttu-id="e865b-123">**클라이언트 암호에서**클라이언트 암호의 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="e865b-124">URI 세그먼트는 `/signin-microsoft` Microsoft 인증 공급자의 기본 콜백으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="e865b-125">상속된 [원격 인증Options.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) [MicrosoftAccount](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) 옵션 클래스의 CallbackPath 속성을 통해 Microsoft 인증 미들웨어를 구성하는 동안 기본 콜백 URI를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="e865b-126">Microsoft 클라이언트 ID 및 비밀 저장</span><span class="sxs-lookup"><span data-stu-id="e865b-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="e865b-127">Microsoft 클라이언트 ID 및 비밀 값과 같은 중요한 설정을 [비밀 관리자로 저장합니다.](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="e865b-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="e865b-128">이 샘플의 경우 다음 단계를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="e865b-129">비밀 저장소 활성화에서 지침에 따라 비밀 [저장소에](xref:security/app-secrets#enable-secret-storage)대한 프로젝트를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="e865b-130">로컬 비밀 저장소에 비밀 키와 `Authentication:Microsoft:ClientId` 함께 `Authentication:Microsoft:ClientSecret`중요한 설정을 저장하고 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="e865b-131">Microsoft 계정 인증 구성</span><span class="sxs-lookup"><span data-stu-id="e865b-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="e865b-132">`Startup.ConfigureServices`다음 에 Microsoft 계정 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="e865b-133">Microsoft 계정 인증에서 지원하는 구성 옵션에 대한 자세한 내용은 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API 참조를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="e865b-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="e865b-134">사용자에 대 한 다른 정보를 요청 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="e865b-135">Microsoft 계정으로 로그인</span><span class="sxs-lookup"><span data-stu-id="e865b-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="e865b-136">앱을 실행하고 **로그인을 클릭합니다.**</span><span class="sxs-lookup"><span data-stu-id="e865b-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="e865b-137">Microsoft로 로그인하는 옵션이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="e865b-138">Microsoft를 클릭하면 인증을 위해 Microsoft로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="e865b-139">Microsoft 계정으로 로그인하면 앱에서 사용자의 정보에 액세스할 수 있도록 하라는 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="e865b-140">**예를** 누르면 이메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="e865b-141">이제 Microsoft 자격 증명을 사용하여 로그인됩니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="e865b-142">문제 해결</span><span class="sxs-lookup"><span data-stu-id="e865b-142">Troubleshooting</span></span>

* <span data-ttu-id="e865b-143">Microsoft 계정 공급자가 로그인 오류 페이지로 리디렉션하는 경우 Uri의 `#` (해시태그) 바로 다음에 오류 제목 및 설명 쿼리 문자열 매개 변수를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="e865b-144">오류 메시지가 Microsoft 인증에 문제가 있음을 나타내는 것처럼 보이지만 가장 일반적인 원인은 응용 프로그램 Uri가 **웹** 플랫폼에 지정된 **리디렉션 URI와** 일치하지 않기 때문에</span><span class="sxs-lookup"><span data-stu-id="e865b-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="e865b-145">에서 호출하여 `services.AddIdentity` `ConfigureServices`ID가 구성되지 않은 경우 인증을 시도하면 *인수 예외: 'SignInScheme' 옵션이 제공되어야 합니다.*</span><span class="sxs-lookup"><span data-stu-id="e865b-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="e865b-146">이 샘플에 사용된 프로젝트 템플릿은 이 작업을 수행하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="e865b-147">초기 마이그레이션을 적용하여 사이트 데이터베이스를 만들지 않은 경우 요청 오류를 *처리하는 동안 데이터베이스 작업이 실패하게* 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="e865b-148">**마이그레이션 적용을** 탭하여 데이터베이스를 만들고 새로 고침하여 오류를 지나서 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e865b-149">다음 단계</span><span class="sxs-lookup"><span data-stu-id="e865b-149">Next steps</span></span>

* <span data-ttu-id="e865b-150">이 문서에서는 Microsoft를 통해 인증하는 방법을 보여 주어 도있습니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="e865b-151">이전 [페이지에](xref:security/authentication/social/index)나열된 다른 공급자와 함께 인증하려면 유사한 방법을 따를 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="e865b-152">웹 사이트를 Azure 웹 앱에 게시하면 Microsoft 개발자 포털에서 새 클라이언트 비밀을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="e865b-153">Azure `Authentication:Microsoft:ClientId` 포털에서 및 `Authentication:Microsoft:ClientSecret` 응용 프로그램 설정을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="e865b-154">구성 시스템은 환경 변수에서 키를 읽도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e865b-154">The configuration system is set up to read keys from environment variables.</span></span>
