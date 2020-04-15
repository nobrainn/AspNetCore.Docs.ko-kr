---
title: ASP.NET 코어에 대한 인증 샘플
author: rick-anderson
description: ASP.NET Core 리포지토리의 인증 샘플에 대한 링크를 제공합니다.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b33eaa5c1bda9e23b2815cd1663e02ae06fec856
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308217"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="f8f3a-103">ASP.NET 코어에 대한 인증 샘플</span><span class="sxs-lookup"><span data-stu-id="f8f3a-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="f8f3a-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f8f3a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f8f3a-105">[ASP.NET 코어 리포지토리에는](https://github.com/dotnet/AspNetCore) *AspNetCore/src/Security/샘플* 폴더에 다음과 같은 인증 샘플이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f8f3a-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="f8f3a-106">클레임 변환</span><span class="sxs-lookup"><span data-stu-id="f8f3a-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="f8f3a-107">쿠키 인증</span><span class="sxs-lookup"><span data-stu-id="f8f3a-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="f8f3a-108">사용자 지정 정책 공급자 - I권한 부여정책공급자</span><span class="sxs-lookup"><span data-stu-id="f8f3a-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="f8f3a-109">동적 인증 체계 및 옵션</span><span class="sxs-lookup"><span data-stu-id="f8f3a-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="f8f3a-110">외부 클레임</span><span class="sxs-lookup"><span data-stu-id="f8f3a-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="f8f3a-111">요청에 따라 쿠키와 다른 인증 체계 중 선택</span><span class="sxs-lookup"><span data-stu-id="f8f3a-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="f8f3a-112">정적 파일에 대한 액세스를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="f8f3a-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="f8f3a-113">샘플 실행</span><span class="sxs-lookup"><span data-stu-id="f8f3a-113">Run the samples</span></span>

* <span data-ttu-id="f8f3a-114">[분기를](https://github.com/dotnet/AspNetCore)선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8f3a-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="f8f3a-115">예를 들어 `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="f8f3a-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="f8f3a-116">복제 또는 [ASP.NET 코어 리포지토리를](https://github.com/dotnet/AspNetCore)다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f8f3a-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="f8f3a-117">ASP.NET 코어 리포지토리의 복제본과 일치하는 [.NET 코어 SDK](https://dotnet.microsoft.com/download/dotnet-core) 버전을 설치했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f8f3a-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="f8f3a-118">*AspNetCore/src/보안/샘플의 샘플로* 이동하여 을 사용하여 `dotnet run`샘플을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f8f3a-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f8f3a-119">[ASP.NET 코어 리포지토리에는](https://github.com/dotnet/AspNetCore) *AspNetCore/src/Security/샘플* 폴더에 다음과 같은 인증 샘플이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f8f3a-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="f8f3a-120">클레임 변환</span><span class="sxs-lookup"><span data-stu-id="f8f3a-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="f8f3a-121">쿠키 인증</span><span class="sxs-lookup"><span data-stu-id="f8f3a-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="f8f3a-122">사용자 지정 정책 공급자 - I권한 부여정책공급자</span><span class="sxs-lookup"><span data-stu-id="f8f3a-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="f8f3a-123">동적 인증 체계 및 옵션</span><span class="sxs-lookup"><span data-stu-id="f8f3a-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="f8f3a-124">외부 클레임</span><span class="sxs-lookup"><span data-stu-id="f8f3a-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="f8f3a-125">요청에 따라 쿠키와 다른 인증 체계 중 선택</span><span class="sxs-lookup"><span data-stu-id="f8f3a-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="f8f3a-126">정적 파일에 대한 액세스를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="f8f3a-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="f8f3a-127">샘플 실행</span><span class="sxs-lookup"><span data-stu-id="f8f3a-127">Run the samples</span></span>

* <span data-ttu-id="f8f3a-128">[분기를](https://github.com/dotnet/AspNetCore)선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f8f3a-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="f8f3a-129">예를 들어 `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="f8f3a-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="f8f3a-130">복제 또는 [ASP.NET 코어 리포지토리를](https://github.com/dotnet/AspNetCore)다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f8f3a-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="f8f3a-131">ASP.NET 코어 리포지토리의 복제본과 일치하는 [.NET 코어 SDK](https://dotnet.microsoft.com/download/dotnet-core) 버전을 설치했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f8f3a-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="f8f3a-132">*AspNetCore/src/보안/샘플의 샘플로* 이동하여 을 사용하여 `dotnet run`샘플을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f8f3a-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
