---
title: ASP.NET Core의 계정 확인 및 암호 복구
author: rick-anderson
description: 전자 메일 확인 및 암호 재설정을 사용 하 여 ASP.NET Core 앱을 빌드하는 방법을 알아봅니다.
ms.author: riande
ms.date: 03/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/accconfirm
ms.openlocfilehash: d5e0e3865702fe4e5cbe49e7f452f367a8a53de9
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451747"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="1a40d-103">ASP.NET Core의 계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="1a40d-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="1a40d-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)및 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="1a40d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="1a40d-105">이 자습서에서는 전자 메일 확인 및 암호 재설정을 사용 하 여 ASP.NET Core 앱을 빌드하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="1a40d-106">이 자습서는 시작 항목이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="1a40d-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="1a40d-107">다음에 대해 잘 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-107">You should be familiar with:</span></span>

* [<span data-ttu-id="1a40d-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1a40d-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="1a40d-109">인증</span><span class="sxs-lookup"><span data-stu-id="1a40d-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="1a40d-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="1a40d-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="1a40d-111">ASP.NET Core 1.1 버전은 [이 PDF 파일](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1a40d-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="1a40d-112">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="1a40d-112">Prerequisites</span></span>

[<span data-ttu-id="1a40d-113">.NET Core 3.0 SDK 이상</span><span class="sxs-lookup"><span data-stu-id="1a40d-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="1a40d-114">인증을 사용 하 여 웹 앱 만들기 및 테스트</span><span class="sxs-lookup"><span data-stu-id="1a40d-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="1a40d-115">다음 명령을 실행 하 여 인증을 사용 하는 웹 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="1a40d-116">앱을 실행 하 고, **등록** 링크를 선택 하 고, 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="1a40d-117">등록 되 면 `/Identity/Account/RegisterConfirmation` 전자 메일 확인을 시뮬레이트하는 링크를 포함 하는 to 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="1a40d-118">링크를 선택 `Click here to confirm your account` 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="1a40d-119">**로그인** 링크를 선택 하 고 동일한 자격 증명을 사용 하 여 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="1a40d-120">`Hello YourEmail@provider.com!`페이지로 리디렉션되는 링크를 선택 합니다 `/Identity/Account/Manage/PersonalData` .</span><span class="sxs-lookup"><span data-stu-id="1a40d-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="1a40d-121">왼쪽에서 **개인 데이터** 탭을 선택 하 고 **삭제**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="1a40d-122">전자 메일 공급자 구성</span><span class="sxs-lookup"><span data-stu-id="1a40d-122">Configure an email provider</span></span>

<span data-ttu-id="1a40d-123">이 자습서에서는 [SendGrid](https://sendgrid.com) 를 사용 하 여 전자 메일을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="1a40d-124">전자 메일을 보내려면 SendGrid 계정 및 키가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="1a40d-125">다른 전자 메일 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-125">You can use other email providers.</span></span> <span data-ttu-id="1a40d-126">SendGrid 또는 다른 전자 메일 서비스를 사용 하 여 전자 메일을 보내는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="1a40d-127">SMTP는 안전 하 게 보호 하 고 올바르게 설정 하기 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="1a40d-128">SendGrid 계정에는 [발신자를 추가](https://sendgrid.com/docs/ui/sending-email/senders/)해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-128">The SendGrid account my require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="1a40d-129">보안 전자 메일 키를 인출 하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-129">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="1a40d-130">이 샘플의 경우 *Services/AuthMessageSenderOptions*을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-130">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="1a40d-131">SendGrid 사용자 비밀 구성</span><span class="sxs-lookup"><span data-stu-id="1a40d-131">Configure SendGrid user secrets</span></span>

<span data-ttu-id="1a40d-132">`SendGridUser` `SendGridKey` [암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 및를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-132">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="1a40d-133">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-133">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="1a40d-134">Windows에서 Secret Manager는 디렉터리에 있는 *비밀. json* 파일의 키/값 쌍을 저장 합니다 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` .</span><span class="sxs-lookup"><span data-stu-id="1a40d-134">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="1a40d-135">*비밀. json* 파일의 내용이 암호화 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-135">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="1a40d-136">다음 태그는 *비밀. json* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-136">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="1a40d-137">`SendGridKey`값이 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-137">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="1a40d-138">자세한 내용은 [옵션 패턴](xref:fundamentals/configuration/options) 및 [구성](xref:fundamentals/configuration/index)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1a40d-138">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="1a40d-139">SendGrid 설치</span><span class="sxs-lookup"><span data-stu-id="1a40d-139">Install SendGrid</span></span>

<span data-ttu-id="1a40d-140">이 자습서에서는 [SendGrid](https://sendgrid.com/)를 통해 전자 메일 알림을 추가 하는 방법을 보여 주지만 SMTP 및 기타 메커니즘을 사용 하 여 전자 메일을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-140">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="1a40d-141">NuGet 패키지를 설치 합니다 `SendGrid` .</span><span class="sxs-lookup"><span data-stu-id="1a40d-141">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1a40d-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a40d-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1a40d-143">패키지 관리자 콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-143">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="1a40d-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1a40d-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1a40d-145">콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-145">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="1a40d-146">무료 SendGrid 계정에 등록 하려면 [무료로 SendGrid 시작](https://sendgrid.com/free/) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1a40d-146">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="1a40d-147">IEmailSender 구현</span><span class="sxs-lookup"><span data-stu-id="1a40d-147">Implement IEmailSender</span></span>

<span data-ttu-id="1a40d-148">를 구현 하려면 `IEmailSender` 다음과 유사한 코드를 사용 하 여 *서비스/emailsender .cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-148">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="1a40d-149">전자 메일을 지원 하도록 시작 구성</span><span class="sxs-lookup"><span data-stu-id="1a40d-149">Configure startup to support email</span></span>

<span data-ttu-id="1a40d-150">`ConfigureServices` *Startup.cs* 파일의 메서드에 다음 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-150">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="1a40d-151">을 `EmailSender` 임시 서비스로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-151">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="1a40d-152">`AuthMessageSenderOptions`구성 인스턴스를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-152">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="1a40d-153">등록, 전자 메일 확인 및 암호 재설정</span><span class="sxs-lookup"><span data-stu-id="1a40d-153">Register, confirm email, and reset password</span></span>

<span data-ttu-id="1a40d-154">웹 앱을 실행 하 고 계정 확인 및 암호 복구 흐름을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-154">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="1a40d-155">앱을 실행 하 고 새 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-155">Run the app and register a new user</span></span>
* <span data-ttu-id="1a40d-156">계정 확인 링크에 대 한 전자 메일을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-156">Check your email for the account confirmation link.</span></span> <span data-ttu-id="1a40d-157">전자 메일을 받을 수 없는 경우 [디버그 전자 메일](#debug) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1a40d-157">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="1a40d-158">링크를 클릭 하 여 전자 메일을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-158">Click the link to confirm your email.</span></span>
* <span data-ttu-id="1a40d-159">전자 메일 및 암호를 사용 하 여 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-159">Sign in with your email and password.</span></span>
* <span data-ttu-id="1a40d-160">로그아웃합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-160">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="1a40d-161">암호 재설정 테스트</span><span class="sxs-lookup"><span data-stu-id="1a40d-161">Test password reset</span></span>

* <span data-ttu-id="1a40d-162">로그인 하는 경우 **로그 아웃**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-162">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="1a40d-163">**로그인** 링크를 선택 하 고 암호를 **잊으셨나요?** 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-163">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="1a40d-164">계정을 등록 하는 데 사용한 전자 메일을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-164">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="1a40d-165">암호를 재설정 하는 링크가 포함 된 전자 메일이 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-165">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="1a40d-166">전자 메일을 확인 하 고 링크를 클릭 하 여 암호를 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-166">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="1a40d-167">암호를 성공적으로 재설정 한 후에는 전자 메일 및 새 암호로 로그인 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-167">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="1a40d-168">전자 메일 및 작업 시간 제한 변경</span><span class="sxs-lookup"><span data-stu-id="1a40d-168">Change email and activity timeout</span></span>

<span data-ttu-id="1a40d-169">기본 비활성 시간 제한은 14 일입니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-169">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="1a40d-170">다음 코드는 비활성 시간 제한을 5 일로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-170">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="1a40d-171">모든 데이터 보호 토큰 변경 lifespans</span><span class="sxs-lookup"><span data-stu-id="1a40d-171">Change all data protection token lifespans</span></span>

<span data-ttu-id="1a40d-172">다음 코드는 모든 데이터 보호 토큰 제한 시간을 3 시간으로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-172">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="1a40d-173">기본 제공 Identity 사용자 토큰 ( [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 참조)에는 [1 일 시간 제한이](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-173">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="1a40d-174">전자 메일 토큰 수명 변경</span><span class="sxs-lookup"><span data-stu-id="1a40d-174">Change the email token lifespan</span></span>

<span data-ttu-id="1a40d-175">[ Identity 사용자 토큰](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 의 기본 토큰 수명은 [1 일](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)입니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-175">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="1a40d-176">이 섹션에서는 전자 메일 토큰 수명을 변경 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-176">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="1a40d-177">사용자 지정 [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) 및를 추가 합니다 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> .</span><span class="sxs-lookup"><span data-stu-id="1a40d-177">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="1a40d-178">서비스 컨테이너에 사용자 지정 공급자를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-178">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="1a40d-179">전자 메일 다시 보내기 확인</span><span class="sxs-lookup"><span data-stu-id="1a40d-179">Resend email confirmation</span></span>

<span data-ttu-id="1a40d-180">이 [GitHub 문제](https://github.com/dotnet/AspNetCore/issues/5410)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1a40d-180">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="1a40d-181">디버그 전자 메일</span><span class="sxs-lookup"><span data-stu-id="1a40d-181">Debug email</span></span>

<span data-ttu-id="1a40d-182">전자 메일을 사용할 수 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="1a40d-182">If you can't get email working:</span></span>

* <span data-ttu-id="1a40d-183">`EmailSender.Execute`가 호출 되었는지 확인 하려면에서 중단점을 설정 `SendGridClient.SendEmailAsync` 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-183">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="1a40d-184">비슷한 코드를 사용 하 여 [전자 메일을 보내는 콘솔 앱](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) 을 만듭니다 `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="1a40d-184">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="1a40d-185">[전자 메일 작업](https://sendgrid.com/docs/User_Guide/email_activity.html) 페이지를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-185">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="1a40d-186">스팸 폴더를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-186">Check your spam folder.</span></span>
* <span data-ttu-id="1a40d-187">다른 전자 메일 공급자 (Microsoft, Yahoo, Gmail 등)에서 다른 전자 메일 별칭을 사용해 보세요.</span><span class="sxs-lookup"><span data-stu-id="1a40d-187">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="1a40d-188">다른 전자 메일 계정으로 전송 해 보세요.</span><span class="sxs-lookup"><span data-stu-id="1a40d-188">Try sending to different email accounts.</span></span>

<span data-ttu-id="1a40d-189">**보안 모범 사례** 는 테스트 및 개발에서 프로덕션 암호를 사용 **하지 않는** 것입니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-189">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="1a40d-190">Azure에 앱을 게시 하는 경우 Azure 웹 앱 포털에서 SendGrid 비밀을 응용 프로그램 설정으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-190">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="1a40d-191">구성 시스템은 환경 변수에서 키를 읽도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-191">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="1a40d-192">소셜 및 로컬 로그인 계정 결합</span><span class="sxs-lookup"><span data-stu-id="1a40d-192">Combine social and local login accounts</span></span>

<span data-ttu-id="1a40d-193">이 섹션을 완료 하려면 먼저 외부 인증 공급자를 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-193">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="1a40d-194">[Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1a40d-194">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="1a40d-195">전자 메일 링크를 클릭 하 여 로컬 및 소셜 계정을 결합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-195">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="1a40d-196">다음 시퀀스에서는 " RickAndMSFT@gmail.com "이 (가) 로컬 로그인으로 먼저 생성 되지만 계정을 소셜 로그인으로 먼저 만든 다음 로컬 로그인을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-196">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![웹 응용 프로그램: RickAndMSFT@gmail.com 사용자 인증 됨](accconfirm/_static/rick.png)

<span data-ttu-id="1a40d-198">**관리** 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-198">Click on the **Manage** link.</span></span> <span data-ttu-id="1a40d-199">이 계정과 연결 된 0 외부 (소셜 로그인)를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-199">Note the 0 external (social logins) associated with this account.</span></span>

![뷰 관리](accconfirm/_static/manage.png)

<span data-ttu-id="1a40d-201">다른 로그인 서비스에 대 한 링크를 클릭 하 고 앱 요청을 수락 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-201">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="1a40d-202">다음 이미지에서 Facebook은 외부 인증 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-202">In the following image, Facebook is the external authentication provider:</span></span>

![외부 로그인 보기 관리 Facebook 목록](accconfirm/_static/fb.png)

<span data-ttu-id="1a40d-204">두 계정을 결합 했습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-204">The two accounts have been combined.</span></span> <span data-ttu-id="1a40d-205">두 계정을 사용 하 여 로그인 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-205">You are able to sign in with either account.</span></span> <span data-ttu-id="1a40d-206">소셜 로그인 인증 서비스가 다운 되거나 소셜 계정에 대 한 액세스 권한이 손실 될 가능성이 있는 경우 사용자가 로컬 계정을 추가 하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-206">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="1a40d-207">사이트에 사용자가 있는 후 계정 확인 사용</span><span class="sxs-lookup"><span data-stu-id="1a40d-207">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="1a40d-208">사용자가 있는 사이트에서 계정 확인을 사용 하도록 설정 하면 모든 기존 사용자가 잠깁니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-208">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="1a40d-209">계정이 확인 되지 않았기 때문에 기존 사용자가 잠깁니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-209">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="1a40d-210">기존 사용자 잠금을 해결 하려면 다음 방법 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-210">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="1a40d-211">기존 사용자를 모두 확인 된 것으로 표시 하도록 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-211">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="1a40d-212">기존 사용자를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-212">Confirm existing users.</span></span> <span data-ttu-id="1a40d-213">예를 들어, 확인 링크가 포함 된 메일을 일괄 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-213">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="1a40d-214">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="1a40d-214">Prerequisites</span></span>

[<span data-ttu-id="1a40d-215">.NET Core 2.2 SDK 이상</span><span class="sxs-lookup"><span data-stu-id="1a40d-215">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="1a40d-216">웹 앱 및 스 캐 폴드 만들기Identity</span><span class="sxs-lookup"><span data-stu-id="1a40d-216">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="1a40d-217">다음 명령을 실행 하 여 인증을 사용 하는 웹 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-217">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="1a40d-218">새 사용자 등록 테스트</span><span class="sxs-lookup"><span data-stu-id="1a40d-218">Test new user registration</span></span>

<span data-ttu-id="1a40d-219">앱을 실행 하 고, **등록** 링크를 선택 하 고, 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-219">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="1a40d-220">이때 전자 메일에 대 한 유일한 유효성 검사는 특성을 사용 [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-220">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="1a40d-221">등록을 제출 하면 앱에 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-221">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="1a40d-222">자습서의 뒷부분에서 새로운 사용자가 전자 메일의 유효성을 검사할 때까지 로그인 할 수 없도록 코드를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-222">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="1a40d-223">테이블의 필드는 `EmailConfirmed` `False` 입니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-223">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="1a40d-224">앱에서 확인 전자 메일을 보낼 때 다음 단계에서이 전자 메일을 다시 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-224">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="1a40d-225">행을 마우스 오른쪽 단추로 클릭 하 고 **삭제**를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-225">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="1a40d-226">전자 메일 별칭을 삭제 하면 다음 단계에서 보다 쉽게 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-226">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="1a40d-227">전자 메일 확인 필요</span><span class="sxs-lookup"><span data-stu-id="1a40d-227">Require email confirmation</span></span>

<span data-ttu-id="1a40d-228">새 사용자 등록의 전자 메일을 확인 하는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-228">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="1a40d-229">전자 메일 확인을 사용 하면 다른 사용자를 가장 하지 않는 것을 확인할 수 있습니다. 즉, 다른 사용자의 전자 메일을 사용 하 여 등록 하지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-229">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="1a40d-230">토론 포럼이 있고 ""을 ""로 등록 하지 못하도록 하는 경우를 가정해 보겠습니다 yli@example.com nolivetto@contoso.com .</span><span class="sxs-lookup"><span data-stu-id="1a40d-230">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="1a40d-231">전자 메일 확인이 없으면 ""이 (가) nolivetto@contoso.com 앱에서 원치 않는 전자 메일을 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-231">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="1a40d-232">""로 실수로 등록 한 사용자가 ylo@example.com "yli"의 맞춤법 오류를 발견 했다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-232">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="1a40d-233">앱에 올바른 전자 메일이 없기 때문에 암호 복구를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-233">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="1a40d-234">전자 메일 확인은 봇에서 제한 된 보호 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-234">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="1a40d-235">전자 메일 확인은 전자 메일 계정이 많은 악의적인 사용자 로부터 보호를 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-235">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="1a40d-236">일반적으로 새 사용자가 확인 된 전자 메일을 보내기 전에 데이터를 웹 사이트에 게시 하는 것을 방지 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-236">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="1a40d-237">`Startup.ConfigureServices`확인 된 전자 메일을 요구 하도록 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-237">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="1a40d-238">`config.SignIn.RequireConfirmedEmail = true;`등록 된 사용자가 전자 메일을 확인할 때까지 로그인 하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-238">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="1a40d-239">전자 메일 공급자 구성</span><span class="sxs-lookup"><span data-stu-id="1a40d-239">Configure email provider</span></span>

<span data-ttu-id="1a40d-240">이 자습서에서는 [SendGrid](https://sendgrid.com) 를 사용 하 여 전자 메일을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-240">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="1a40d-241">전자 메일을 보내려면 SendGrid 계정 및 키가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-241">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="1a40d-242">다른 전자 메일 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-242">You can use other email providers.</span></span> <span data-ttu-id="1a40d-243">ASP.NET Core 2.x에는 `System.Net.Mail` 앱에서 전자 메일을 보낼 수 있는가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-243">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="1a40d-244">SendGrid 또는 다른 전자 메일 서비스를 사용 하 여 전자 메일을 보내는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-244">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="1a40d-245">SMTP는 안전 하 게 보호 하 고 올바르게 설정 하기 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-245">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="1a40d-246">보안 전자 메일 키를 인출 하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-246">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="1a40d-247">이 샘플의 경우 *Services/AuthMessageSenderOptions*을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-247">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="1a40d-248">SendGrid 사용자 비밀 구성</span><span class="sxs-lookup"><span data-stu-id="1a40d-248">Configure SendGrid user secrets</span></span>

<span data-ttu-id="1a40d-249">`SendGridUser` `SendGridKey` [암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 및를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-249">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="1a40d-250">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-250">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="1a40d-251">Windows에서 Secret Manager는 디렉터리에 있는 *비밀. json* 파일의 키/값 쌍을 저장 합니다 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` .</span><span class="sxs-lookup"><span data-stu-id="1a40d-251">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="1a40d-252">*비밀. json* 파일의 내용이 암호화 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-252">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="1a40d-253">다음 태그는 *비밀. json* 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-253">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="1a40d-254">`SendGridKey`값이 제거 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-254">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="1a40d-255">자세한 내용은 [옵션 패턴](xref:fundamentals/configuration/options) 및 [구성](xref:fundamentals/configuration/index)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1a40d-255">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="1a40d-256">SendGrid 설치</span><span class="sxs-lookup"><span data-stu-id="1a40d-256">Install SendGrid</span></span>

<span data-ttu-id="1a40d-257">이 자습서에서는 [SendGrid](https://sendgrid.com/)를 통해 전자 메일 알림을 추가 하는 방법을 보여 주지만 SMTP 및 기타 메커니즘을 사용 하 여 전자 메일을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-257">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="1a40d-258">NuGet 패키지를 설치 합니다 `SendGrid` .</span><span class="sxs-lookup"><span data-stu-id="1a40d-258">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1a40d-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a40d-259">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1a40d-260">패키지 관리자 콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-260">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="1a40d-261">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1a40d-261">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1a40d-262">콘솔에서 다음 명령을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-262">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="1a40d-263">무료 SendGrid 계정에 등록 하려면 [무료로 SendGrid 시작](https://sendgrid.com/free/) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1a40d-263">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="1a40d-264">IEmailSender 구현</span><span class="sxs-lookup"><span data-stu-id="1a40d-264">Implement IEmailSender</span></span>

<span data-ttu-id="1a40d-265">를 구현 하려면 `IEmailSender` 다음과 유사한 코드를 사용 하 여 *서비스/emailsender .cs* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-265">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="1a40d-266">전자 메일을 지원 하도록 시작 구성</span><span class="sxs-lookup"><span data-stu-id="1a40d-266">Configure startup to support email</span></span>

<span data-ttu-id="1a40d-267">`ConfigureServices` *Startup.cs* 파일의 메서드에 다음 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-267">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="1a40d-268">을 `EmailSender` 임시 서비스로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-268">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="1a40d-269">`AuthMessageSenderOptions`구성 인스턴스를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-269">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="1a40d-270">계정 확인 및 암호 복구 사용</span><span class="sxs-lookup"><span data-stu-id="1a40d-270">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="1a40d-271">템플릿에는 계정 확인 및 암호 복구를 위한 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-271">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="1a40d-272">`OnPostAsync` *영역/ Identity /Pages/Account/Register.cshtml.cs*에서 메서드를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-272">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="1a40d-273">다음 줄을 주석으로 처리 하 여 새로 등록 된 사용자가 자동으로 로그인 하지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-273">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="1a40d-274">전체 메서드는 변경 된 줄이 강조 표시 된 상태로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-274">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="1a40d-275">등록, 전자 메일 확인 및 암호 재설정</span><span class="sxs-lookup"><span data-stu-id="1a40d-275">Register, confirm email, and reset password</span></span>

<span data-ttu-id="1a40d-276">웹 앱을 실행 하 고 계정 확인 및 암호 복구 흐름을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-276">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="1a40d-277">앱을 실행 하 고 새 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-277">Run the app and register a new user</span></span>
* <span data-ttu-id="1a40d-278">계정 확인 링크에 대 한 전자 메일을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-278">Check your email for the account confirmation link.</span></span> <span data-ttu-id="1a40d-279">전자 메일을 받을 수 없는 경우 [디버그 전자 메일](#debug) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1a40d-279">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="1a40d-280">링크를 클릭 하 여 전자 메일을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-280">Click the link to confirm your email.</span></span>
* <span data-ttu-id="1a40d-281">전자 메일 및 암호를 사용 하 여 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-281">Sign in with your email and password.</span></span>
* <span data-ttu-id="1a40d-282">로그아웃합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-282">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="1a40d-283">관리 페이지 보기</span><span class="sxs-lookup"><span data-stu-id="1a40d-283">View the manage page</span></span>

<span data-ttu-id="1a40d-284">브라우저: ![ 사용자 이름을 사용 하 여 브라우저 창에서 사용자 이름을 선택 합니다.](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="1a40d-284">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="1a40d-285">관리 페이지는 **프로필** 탭이 선택 된 상태로 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-285">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="1a40d-286">전자 **메일** 에는 전자 메일이 확인 되었다는 확인란이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-286">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="1a40d-287">암호 재설정 테스트</span><span class="sxs-lookup"><span data-stu-id="1a40d-287">Test password reset</span></span>

* <span data-ttu-id="1a40d-288">로그인 하는 경우 **로그 아웃**을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-288">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="1a40d-289">**로그인** 링크를 선택 하 고 암호를 **잊으셨나요?** 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-289">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="1a40d-290">계정을 등록 하는 데 사용한 전자 메일을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-290">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="1a40d-291">암호를 재설정 하는 링크가 포함 된 전자 메일이 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-291">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="1a40d-292">전자 메일을 확인 하 고 링크를 클릭 하 여 암호를 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-292">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="1a40d-293">암호를 성공적으로 재설정 한 후에는 전자 메일 및 새 암호로 로그인 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-293">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="1a40d-294">전자 메일 및 작업 시간 제한 변경</span><span class="sxs-lookup"><span data-stu-id="1a40d-294">Change email and activity timeout</span></span>

<span data-ttu-id="1a40d-295">기본 비활성 시간 제한은 14 일입니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-295">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="1a40d-296">다음 코드는 비활성 시간 제한을 5 일로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-296">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="1a40d-297">모든 데이터 보호 토큰 변경 lifespans</span><span class="sxs-lookup"><span data-stu-id="1a40d-297">Change all data protection token lifespans</span></span>

<span data-ttu-id="1a40d-298">다음 코드는 모든 데이터 보호 토큰 제한 시간을 3 시간으로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-298">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="1a40d-299">기본 제공 Identity 사용자 토큰 ( [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 참조)에는 [1 일 시간 제한이](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-299">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="1a40d-300">전자 메일 토큰 수명 변경</span><span class="sxs-lookup"><span data-stu-id="1a40d-300">Change the email token lifespan</span></span>

<span data-ttu-id="1a40d-301">[ Identity 사용자 토큰](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 의 기본 토큰 수명은 [1 일](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)입니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-301">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="1a40d-302">이 섹션에서는 전자 메일 토큰 수명을 변경 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-302">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="1a40d-303">사용자 지정 [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) 및를 추가 합니다 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> .</span><span class="sxs-lookup"><span data-stu-id="1a40d-303">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="1a40d-304">서비스 컨테이너에 사용자 지정 공급자를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-304">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="1a40d-305">전자 메일 다시 보내기 확인</span><span class="sxs-lookup"><span data-stu-id="1a40d-305">Resend email confirmation</span></span>

<span data-ttu-id="1a40d-306">이 [GitHub 문제](https://github.com/dotnet/AspNetCore/issues/5410)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1a40d-306">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="1a40d-307">디버그 전자 메일</span><span class="sxs-lookup"><span data-stu-id="1a40d-307">Debug email</span></span>

<span data-ttu-id="1a40d-308">전자 메일을 사용할 수 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="1a40d-308">If you can't get email working:</span></span>

* <span data-ttu-id="1a40d-309">`EmailSender.Execute`가 호출 되었는지 확인 하려면에서 중단점을 설정 `SendGridClient.SendEmailAsync` 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-309">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="1a40d-310">비슷한 코드를 사용 하 여 [전자 메일을 보내는 콘솔 앱](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) 을 만듭니다 `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="1a40d-310">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="1a40d-311">[전자 메일 작업](https://sendgrid.com/docs/User_Guide/email_activity.html) 페이지를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-311">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="1a40d-312">스팸 폴더를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-312">Check your spam folder.</span></span>
* <span data-ttu-id="1a40d-313">다른 전자 메일 공급자 (Microsoft, Yahoo, Gmail 등)에서 다른 전자 메일 별칭을 사용해 보세요.</span><span class="sxs-lookup"><span data-stu-id="1a40d-313">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="1a40d-314">다른 전자 메일 계정으로 전송 해 보세요.</span><span class="sxs-lookup"><span data-stu-id="1a40d-314">Try sending to different email accounts.</span></span>

<span data-ttu-id="1a40d-315">**보안 모범 사례** 는 테스트 및 개발에서 프로덕션 암호를 사용 **하지 않는** 것입니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-315">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="1a40d-316">Azure에 앱을 게시 하는 경우 Azure 웹 앱 포털에서 응용 프로그램 설정으로 SendGrid 암호를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-316">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="1a40d-317">구성 시스템은 환경 변수에서 키를 읽도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-317">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="1a40d-318">소셜 및 로컬 로그인 계정 결합</span><span class="sxs-lookup"><span data-stu-id="1a40d-318">Combine social and local login accounts</span></span>

<span data-ttu-id="1a40d-319">이 섹션을 완료 하려면 먼저 외부 인증 공급자를 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-319">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="1a40d-320">[Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1a40d-320">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="1a40d-321">전자 메일 링크를 클릭 하 여 로컬 및 소셜 계정을 결합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-321">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="1a40d-322">다음 시퀀스에서는 " RickAndMSFT@gmail.com "이 (가) 로컬 로그인으로 먼저 생성 되지만 계정을 소셜 로그인으로 먼저 만든 다음 로컬 로그인을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-322">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![웹 응용 프로그램: RickAndMSFT@gmail.com 사용자 인증 됨](accconfirm/_static/rick.png)

<span data-ttu-id="1a40d-324">**관리** 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-324">Click on the **Manage** link.</span></span> <span data-ttu-id="1a40d-325">이 계정과 연결 된 0 외부 (소셜 로그인)를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-325">Note the 0 external (social logins) associated with this account.</span></span>

![뷰 관리](accconfirm/_static/manage.png)

<span data-ttu-id="1a40d-327">다른 로그인 서비스에 대 한 링크를 클릭 하 고 앱 요청을 수락 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-327">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="1a40d-328">다음 이미지에서 Facebook은 외부 인증 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-328">In the following image, Facebook is the external authentication provider:</span></span>

![외부 로그인 보기 관리 Facebook 목록](accconfirm/_static/fb.png)

<span data-ttu-id="1a40d-330">두 계정을 결합 했습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-330">The two accounts have been combined.</span></span> <span data-ttu-id="1a40d-331">두 계정을 사용 하 여 로그인 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-331">You are able to sign in with either account.</span></span> <span data-ttu-id="1a40d-332">소셜 로그인 인증 서비스가 다운 되거나 소셜 계정에 대 한 액세스 권한이 손실 될 가능성이 있는 경우 사용자가 로컬 계정을 추가 하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-332">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="1a40d-333">사이트에 사용자가 있는 후 계정 확인 사용</span><span class="sxs-lookup"><span data-stu-id="1a40d-333">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="1a40d-334">사용자가 있는 사이트에서 계정 확인을 사용 하도록 설정 하면 모든 기존 사용자가 잠깁니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-334">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="1a40d-335">계정이 확인 되지 않았기 때문에 기존 사용자가 잠깁니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-335">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="1a40d-336">기존 사용자 잠금을 해결 하려면 다음 방법 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-336">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="1a40d-337">기존 사용자를 모두 확인 된 것으로 표시 하도록 데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-337">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="1a40d-338">기존 사용자를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-338">Confirm existing users.</span></span> <span data-ttu-id="1a40d-339">예를 들어, 확인 링크가 포함 된 메일을 일괄 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="1a40d-339">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
