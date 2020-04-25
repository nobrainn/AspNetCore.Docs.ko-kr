---
title: 인증 라이브러리를 Blazor 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 25aa7761b9c1acc72081653422e80cb004500573
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138524"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="3d08d-102">인증 라이브러리를 Blazor 사용 하 여 ASP.NET Core weasembomoma 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="3d08d-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="3d08d-103">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3d08d-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="3d08d-104">*AAD (Azure Active Directory) 및 Azure Active Directory B2C (AAD B2C)의 경우이 항목의 지침을 따르세요. 이 목차 노드의 AAD 및 AAD B2C 항목을 참조 하세요.*</span><span class="sxs-lookup"><span data-stu-id="3d08d-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="3d08d-105">라이브러리를 사용 Blazor `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 하는 weasembom독립 실행형 앱을 만들려면 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="3d08d-106">출력 위치를 지정 하려면 프로젝트 폴더 (없는 경우 `-o BlazorSample`)를 지정 하 고 명령에 출력 옵션을 포함 합니다 (예:).</span><span class="sxs-lookup"><span data-stu-id="3d08d-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="3d08d-107">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="3d08d-108">Visual Studio에서 [ Blazor weasembomapp을 만듭니다](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="3d08d-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="3d08d-109">**사용자 계정을 앱에 저장** 옵션을 사용 하 여 **개별 사용자 계정** 에 대 한 **인증** 을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="3d08d-110">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="3d08d-110">Authentication package</span></span>

<span data-ttu-id="3d08d-111">개별 사용자 계정을 사용 하도록 앱을 만들 때 앱은 앱의 프로젝트 파일에서 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지에 대 한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="3d08d-112">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="3d08d-113">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="3d08d-114">위의 `{VERSION}` 패키지 참조에서를 `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> 문서에 표시 된 패키지의 버전으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="3d08d-115">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="3d08d-115">Authentication service support</span></span>

<span data-ttu-id="3d08d-116">사용자 인증에 대 한 지원은 `AddOidcAuthentication` `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지에서 제공 하는 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="3d08d-117">이 메서드는 앱이 IP (Id 공급자)와 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="3d08d-118">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="3d08d-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="3d08d-119">구성은 *wwwroot/appsettings. json* 파일에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-119">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="3d08d-120">독립형 앱에 대 한 인증 지원은 OIDC (Open ID Connect)를 사용 하 여 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-120">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="3d08d-121">메서드 `AddOidcAuthentication` 는 oidc를 사용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 하는 콜백을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-121">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="3d08d-122">앱을 구성 하는 데 필요한 값은 OIDC 규격 IP에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-122">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="3d08d-123">앱을 등록 하면 일반적으로 온라인 포털에서 발생 하는 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-123">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="3d08d-124">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="3d08d-124">Access token scopes</span></span>

<span data-ttu-id="3d08d-125">Weasembomtemplate은 Blazor 보안 API에 대 한 액세스 토큰을 요청 하도록 앱을 자동으로 구성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-125">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="3d08d-126">액세스 토큰을 로그인 흐름의 일부로 프로 비전 하려면의 기본 토큰 범위에 범위를 추가 합니다 `OidcProviderOptions`.</span><span class="sxs-lookup"><span data-stu-id="3d08d-126">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="3d08d-127">Azure Portal에서 범위 URI를 제공 하 고 앱이 API에서 *401 권한 없음* 응답을 받을 때 **처리 되지 않은 예외를 throw** 하는 경우 스키마와 호스트를 포함 하지 않는 범위 uri를 사용해 보세요.</span><span class="sxs-lookup"><span data-stu-id="3d08d-127">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="3d08d-128">예를 들어 Azure Portal는 다음 범위 URI 형식 중 하나를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-128">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="3d08d-129">스키마 및 호스트 없이 범위 URI를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="3d08d-129">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="3d08d-130">자세한 내용은 *추가 시나리오* 문서의 다음 섹션을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="3d08d-130">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="3d08d-131">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="3d08d-131">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="3d08d-132">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="3d08d-132">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="3d08d-133">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="3d08d-133">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="3d08d-134">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="3d08d-134">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="3d08d-135">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="3d08d-135">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="3d08d-136">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="3d08d-136">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="3d08d-137">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="3d08d-137">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="3d08d-138">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="3d08d-138">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="3d08d-139">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3d08d-139">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
