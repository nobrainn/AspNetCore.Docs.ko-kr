---
title: ASP.NET Core를 사용하는 Microsoft ID 플랫폼 및 Azure Active Directory
author: rick-anderson
description: ASP.NET Core의 웹앱 및 API에 대한 Microsoft ID 플랫폼 Azure Active Directory를 사용한 인증과 관련된 토픽을 알아보세요.
ms.author: riande
ms.date: 01/21/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/azure-active-directory/index
ms.openlocfilehash: d89bf0bdfa9b10378d78c7fcedaa80eb68debe12
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399935"
---
# <a name="azure-active-directory-with-aspnet-core"></a><span data-ttu-id="8172d-103">ASP.NET Core와 Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="8172d-103">Azure Active Directory with ASP.NET Core</span></span>

<span data-ttu-id="8172d-104">해당 자습서 및 샘플에서는 Microsoft ID 플랫폼과 Azure Active Directory를 사용하여 ASP.NET Core에서의 인증을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="8172d-104">These tutorials and samples demonstrate authentication in ASP.NET Core using Microsoft identity platform and Azure Active Directory.</span></span> <span data-ttu-id="8172d-105">Azure AD와 함께 ASP.NET Core를 사용하는 추가 자습서 및 샘플은 [Microsoft ID 플랫폼](/azure/active-directory/develop/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8172d-105">For additional tutorials and samples using ASP.NET Core with Azure AD, see [Microsoft identity platform](/azure/active-directory/develop/).</span></span>

## <a name="application-scenarios"></a><span data-ttu-id="8172d-106">응용 프로그램 시나리오</span><span class="sxs-lookup"><span data-stu-id="8172d-106">Application Scenarios</span></span>

* [<span data-ttu-id="8172d-107">빠른 시작: ASP.NET Core 웹앱에 Microsoft로 로그인 추가</span><span class="sxs-lookup"><span data-stu-id="8172d-107">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="8172d-108">사용자를 로그인 하는 웹앱</span><span class="sxs-lookup"><span data-stu-id="8172d-108">Web app that signs in users</span></span>](/azure/active-directory/develop/scenario-web-app-sign-user-overview?tabs=aspnetcore)
* [<span data-ttu-id="8172d-109">웹 API를 호출하는 웹앱</span><span class="sxs-lookup"><span data-stu-id="8172d-109">Web app that calls web APIs</span></span>](/azure/active-directory/develop/scenario-web-app-call-api-overview)
* [<span data-ttu-id="8172d-110">보호된 웹 API</span><span class="sxs-lookup"><span data-stu-id="8172d-110">Protected web API</span></span>](/azure/active-directory/develop/scenario-protected-web-api-overview)
* [<span data-ttu-id="8172d-111">다른 웹 API를 호출하는 웹 API</span><span class="sxs-lookup"><span data-stu-id="8172d-111">Web API that calls other web APIs</span></span>](/azure/active-directory/develop/scenario-web-api-call-api-overview)
* [<span data-ttu-id="8172d-112">Azure AD B2C로 사용자를 로그인하는 웹앱</span><span class="sxs-lookup"><span data-stu-id="8172d-112">Web app that signs in users with Azure AD B2C</span></span>](xref:security/authentication/azure-ad-b2c)

## <a name="samples"></a><span data-ttu-id="8172d-113">샘플</span><span class="sxs-lookup"><span data-stu-id="8172d-113">Samples</span></span>

* <span data-ttu-id="8172d-114">[Azure AD V2를 사용하여 ASP.NET Core 앱이 사용자를 로그인하고 웹 API를 호출할 수 있도록 설정하세요.](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/)</span><span class="sxs-lookup"><span data-stu-id="8172d-114">[Enable your ASP.NET Core app to sign-in users and call web APIs using Azure AD V2](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span></span> 
  * <span data-ttu-id="8172d-115">[이 관련 비디오](https://channel9.msdn.com/Events/Build/2018/THR5001)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8172d-115">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5001)</span></span>

* <span data-ttu-id="8172d-116">[Azure AD V2를 사용하여 WPF 애플리케이션에서 ASP.NET Core 2.0 Web API 호출](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span><span class="sxs-lookup"><span data-stu-id="8172d-116">[Calling an ASP.NET Core 2.0 Web API from a WPF application using Azure AD V2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span></span> 
  * <span data-ttu-id="8172d-117">[이 관련 비디오](https://channel9.msdn.com/Events/Build/2018/THR5000)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8172d-117">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5000)</span></span>

* [<span data-ttu-id="8172d-118">Azure AD B2C를 사용하여 ASP.NET Core 웹 API</span><span class="sxs-lookup"><span data-stu-id="8172d-118">An ASP.NET Core web API with Azure AD B2C</span></span>](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/)
