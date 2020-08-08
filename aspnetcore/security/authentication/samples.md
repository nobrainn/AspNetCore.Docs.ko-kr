---
title: ASP.NET Core에 대 한 인증 샘플
author: rick-anderson
description: ASP.NET Core 리포지토리의 인증 샘플에 대 한 링크를 제공 합니다.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 71e614eb3d91e8425ec430d775b8a0163dc7258f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017794"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="cb120-103">ASP.NET Core에 대 한 인증 샘플</span><span class="sxs-lookup"><span data-stu-id="cb120-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="cb120-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cb120-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cb120-105">[ASP.NET Core 리포지토리에](https://github.com/dotnet/AspNetCore) 는 *AspNetCore/src/Security/samples* 폴더에 다음 인증 샘플이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb120-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="cb120-106">클레임 변환</span><span class="sxs-lookup"><span data-stu-id="cb120-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="cb120-107">[Cookie인증은](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="cb120-107">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="cb120-108">사용자 지정 정책 공급자-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="cb120-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="cb120-109">동적 인증 스키마 및 옵션</span><span class="sxs-lookup"><span data-stu-id="cb120-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="cb120-110">[외부 클레임](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="cb120-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="cb120-111">cookie요청에 따라 및 다른 인증 체계 중에서 선택</span><span class="sxs-lookup"><span data-stu-id="cb120-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="cb120-112">정적 파일에 대 한 액세스를 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb120-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="cb120-113">샘플 실행</span><span class="sxs-lookup"><span data-stu-id="cb120-113">Run the samples</span></span>

* <span data-ttu-id="cb120-114">[분기](https://github.com/dotnet/AspNetCore)를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb120-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="cb120-115">예, `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="cb120-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="cb120-116">[ASP.NET Core 리포지토리](https://github.com/dotnet/AspNetCore)를 복제 하거나 다운로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb120-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="cb120-117">ASP.NET Core 리포지토리의 복제본과 일치 하는 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 버전을 설치 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb120-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="cb120-118">*AspNetCore/src/Security/samples* 에서 샘플로 이동 하 여로 샘플을 실행 `dotnet run` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb120-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cb120-119">[ASP.NET Core 리포지토리에](https://github.com/dotnet/AspNetCore) 는 *AspNetCore/src/Security/samples* 폴더에 다음 인증 샘플이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb120-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="cb120-120">클레임 변환</span><span class="sxs-lookup"><span data-stu-id="cb120-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="cb120-121">[Cookie인증은](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="cb120-121">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="cb120-122">사용자 지정 정책 공급자-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="cb120-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="cb120-123">동적 인증 스키마 및 옵션</span><span class="sxs-lookup"><span data-stu-id="cb120-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="cb120-124">[외부 클레임](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="cb120-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="cb120-125">cookie요청에 따라 및 다른 인증 체계 중에서 선택</span><span class="sxs-lookup"><span data-stu-id="cb120-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="cb120-126">정적 파일에 대 한 액세스를 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb120-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="cb120-127">샘플 실행</span><span class="sxs-lookup"><span data-stu-id="cb120-127">Run the samples</span></span>

* <span data-ttu-id="cb120-128">[분기](https://github.com/dotnet/AspNetCore)를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb120-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="cb120-129">예, `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="cb120-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="cb120-130">[ASP.NET Core 리포지토리](https://github.com/dotnet/AspNetCore)를 복제 하거나 다운로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb120-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="cb120-131">ASP.NET Core 리포지토리의 복제본과 일치 하는 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 버전을 설치 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb120-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="cb120-132">*AspNetCore/src/Security/samples* 에서 샘플로 이동 하 여로 샘플을 실행 `dotnet run` 합니다.</span><span class="sxs-lookup"><span data-stu-id="cb120-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
