---
title: BlazorAzure Active Directory B2C를 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 059947888653c05a062ec5e5849d8087cd01f475
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153605"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="eb2b3-102">BlazorAzure Active Directory B2C를 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="eb2b3-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="eb2b3-103">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eb2b3-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="eb2b3-104">Blazor인증을 위해 [AAD (AZURE ACTIVE DIRECTORY) B2C](/azure/active-directory-b2c/overview) 를 사용 하는 weasembom독립형 앱을 만들려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="eb2b3-105">다음 항목의 지침에 따라 테 넌 트를 만들고 Azure Portal에서 웹 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="eb2b3-106">[AAD B2C 테 넌 트 만들기](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; 다음 정보를 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="eb2b3-107">1\.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-107">1\.</span></span> <span data-ttu-id="eb2b3-108">AAD B2C 인스턴스 (예: `https://contoso.b2clogin.com/` 후행 슬래시를 포함 하는)</span><span class="sxs-lookup"><span data-stu-id="eb2b3-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="eb2b3-109">2\.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-109">2\.</span></span> <span data-ttu-id="eb2b3-110">AAD B2C 테 넌 트 도메인 (예: `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="eb2b3-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="eb2b3-111">[웹 응용 프로그램 등록](/azure/active-directory-b2c/tutorial-register-applications) &ndash; 앱을 등록 하는 동안 다음을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="eb2b3-112">1\.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-112">1\.</span></span> <span data-ttu-id="eb2b3-113">**웹 앱/웹 API** 를 **예**로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="eb2b3-114">2\.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-114">2\.</span></span> <span data-ttu-id="eb2b3-115">**암시적 흐름 허용** 을 **예**로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="eb2b3-116">3.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-116">3\.</span></span> <span data-ttu-id="eb2b3-117">**회신 URL** 을 추가 `https://localhost:5001/authentication/login-callback` 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="eb2b3-118">응용 프로그램 ID (클라이언트 ID)를 기록 합니다 (예: `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="eb2b3-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="eb2b3-119">[사용자 흐름 만들기](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; 등록 및 로그인 사용자 흐름을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="eb2b3-120">최소한 **응용 프로그램 클레임**  >  **표시 이름** 사용자 특성을 선택 하 여 `context.User.Identity.Name` `LoginDisplay` 구성 요소 (*Shared/LoginDisplay*)에를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="eb2b3-121">앱에 대해 만든 등록 및 로그인 사용자 흐름 이름을 기록 합니다 (예: `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="eb2b3-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="eb2b3-122">다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="eb2b3-123">출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예: `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="eb2b3-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="eb2b3-124">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="eb2b3-125">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="eb2b3-125">Authentication package</span></span>

<span data-ttu-id="eb2b3-126">개별 B2C 계정 ()을 사용 하도록 앱을 만들면 `IndividualB2C` 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) ()에 대 한 패키지 참조를 자동으로 받습니다 `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="eb2b3-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="eb2b3-127">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="eb2b3-128">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="eb2b3-129">`{VERSION}`위의 패키지 참조에서를 `Microsoft.AspNetCore.Blazor.Templates` 문서에 표시 된 패키지의 버전으로 바꿉니다 <xref:blazor/get-started> .</span><span class="sxs-lookup"><span data-stu-id="eb2b3-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="eb2b3-130">`Microsoft.Authentication.WebAssembly.Msal`패키지는 앱에 패키지를 전이적으로 추가 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="eb2b3-131">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="eb2b3-131">Authentication service support</span></span>

<span data-ttu-id="eb2b3-132">사용자 인증에 대 한 지원은 패키지에서 제공 하는 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="eb2b3-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="eb2b3-133">이 메서드는 앱이 공급자 (IP)와 상호 작용 하는 데 필요한 모든 서비스를 설정 Identity 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="eb2b3-134">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="eb2b3-134">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="eb2b3-135">`AddMsalAuthentication`메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="eb2b3-136">앱을 구성 하는 데 필요한 값은 앱을 등록할 때 Microsoft 계정 구성에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-136">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="eb2b3-137">구성은 *wwwroot/appsettings. json* 파일에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-137">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="eb2b3-138">예제:</span><span class="sxs-lookup"><span data-stu-id="eb2b3-138">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="eb2b3-139">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="eb2b3-139">Access token scopes</span></span>

<span data-ttu-id="eb2b3-140">BlazorWeasembomtemplate은 보안 API에 대 한 액세스 토큰을 요청 하도록 앱을 자동으로 구성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-140">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="eb2b3-141">액세스 토큰을 로그인 흐름의 일부로 프로 비전 하려면의 기본 액세스 토큰 범위에 범위를 추가 합니다 `MsalProviderOptions` .</span><span class="sxs-lookup"><span data-stu-id="eb2b3-141">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="eb2b3-142">Azure Portal에서 범위 URI를 제공 하 고 앱이 API에서 *401 권한 없음* 응답을 받을 때 **처리 되지 않은 예외를 throw** 하는 경우 스키마와 호스트를 포함 하지 않는 범위 uri를 사용해 보세요.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-142">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="eb2b3-143">예를 들어 Azure Portal는 다음 범위 URI 형식 중 하나를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-143">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="eb2b3-144">스키마 및 호스트 없이 범위 URI를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-144">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="eb2b3-145">자세한 내용은 *추가 시나리오* 문서의 다음 섹션을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="eb2b3-145">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="eb2b3-146">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="eb2b3-146">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="eb2b3-147">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="eb2b3-147">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="eb2b3-148">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="eb2b3-148">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="eb2b3-149">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="eb2b3-149">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="eb2b3-150">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="eb2b3-150">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="eb2b3-151">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="eb2b3-151">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="eb2b3-152">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="eb2b3-152">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="eb2b3-153">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="eb2b3-153">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="eb2b3-154">추가 자료</span><span class="sxs-lookup"><span data-stu-id="eb2b3-154">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="eb2b3-155">보안 기본 클라이언트를 사용 하는 앱의 인증 되지 않은 또는 무단 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="eb2b3-155">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="eb2b3-156">자습서: Azure Active Directory B2C 테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="eb2b3-156">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="eb2b3-157">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="eb2b3-157">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
