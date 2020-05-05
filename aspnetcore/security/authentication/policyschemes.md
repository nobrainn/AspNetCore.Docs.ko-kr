---
title: ASP.NET Core의 정책 스키마
author: rick-anderson
description: 인증 정책 스키마를 사용 하면 단일 논리 인증 체계를 보다 쉽게 수행할 수 있습니다.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/policyschemes
ms.openlocfilehash: ddedf62c5e8363bd93c9948fd2d3418abc566539
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767319"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="cc5a3-103">ASP.NET Core의 정책 스키마</span><span class="sxs-lookup"><span data-stu-id="cc5a3-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="cc5a3-104">인증 정책 스키마를 사용 하면 단일 논리 인증 체계에서 잠재적으로 여러 방법을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc5a3-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="cc5a3-105">예를 들어 정책 체계가 챌린지에 Google 인증을 사용 하 고 다른 모든 항목에 대 한 쿠키 인증을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc5a3-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="cc5a3-106">인증 정책 스키마는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="cc5a3-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="cc5a3-107">다른 체계에 인증 작업을 쉽게 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc5a3-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="cc5a3-108">요청에 따라 동적으로 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="cc5a3-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="cc5a3-109">파생 <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> 된 및 연결 된 [\<authenticationhandler toptions ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)를 사용 하는 모든 인증 스키마>:</span><span class="sxs-lookup"><span data-stu-id="cc5a3-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="cc5a3-110">는 ASP.NET Core 2.1 이상에서 자동으로 정책 스키마를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="cc5a3-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="cc5a3-111">구성표의 옵션을 구성 하 여 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc5a3-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="cc5a3-112">예</span><span class="sxs-lookup"><span data-stu-id="cc5a3-112">Examples</span></span>

<span data-ttu-id="cc5a3-113">다음 예에서는 하위 수준 스키마를 조합 하는 더 높은 수준의 스키마를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="cc5a3-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="cc5a3-114">Google 인증은 챌린지에 사용 되며 쿠키 인증은 다른 모든 항목에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cc5a3-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="cc5a3-115">다음 예에서는 요청당 스키마를 동적으로 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cc5a3-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="cc5a3-116">즉, 쿠키와 API 인증을 혼합 하는 방법:</span><span class="sxs-lookup"><span data-stu-id="cc5a3-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
