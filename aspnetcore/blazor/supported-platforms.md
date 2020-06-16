---
title: ASP.NET Core Blazor 지원 플랫폼
author: guardrex
description: ASP.NET Core Blazor 지원 플랫폼에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: c10132c87c93346af89c548363e786967609f3da
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/12/2020
ms.locfileid: "83607981"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="80c34-103">ASP.NET Core Blazor 지원 플랫폼</span><span class="sxs-lookup"><span data-stu-id="80c34-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="80c34-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="80c34-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="80c34-105">브라우저 요구 사항</span><span class="sxs-lookup"><span data-stu-id="80c34-105">Browser requirements</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="80c34-106"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="80c34-106"> WebAssembly</span></span>

| <span data-ttu-id="80c34-107">브라우저</span><span class="sxs-lookup"><span data-stu-id="80c34-107">Browser</span></span>                          | <span data-ttu-id="80c34-108">버전</span><span class="sxs-lookup"><span data-stu-id="80c34-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="80c34-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="80c34-109">Microsoft Edge</span></span>                   | <span data-ttu-id="80c34-110">현재</span><span class="sxs-lookup"><span data-stu-id="80c34-110">Current</span></span>               |
| <span data-ttu-id="80c34-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="80c34-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="80c34-112">현재</span><span class="sxs-lookup"><span data-stu-id="80c34-112">Current</span></span>               |
| <span data-ttu-id="80c34-113">Google Chrome(Android 포함)</span><span class="sxs-lookup"><span data-stu-id="80c34-113">Google Chrome, including Android</span></span> | <span data-ttu-id="80c34-114">현재</span><span class="sxs-lookup"><span data-stu-id="80c34-114">Current</span></span>               |
| <span data-ttu-id="80c34-115">Safari(iOS 포함)</span><span class="sxs-lookup"><span data-stu-id="80c34-115">Safari, including iOS</span></span>            | <span data-ttu-id="80c34-116">현재</span><span class="sxs-lookup"><span data-stu-id="80c34-116">Current</span></span>               |
| <span data-ttu-id="80c34-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="80c34-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="80c34-118">지원되지 않음&dagger;</span><span class="sxs-lookup"><span data-stu-id="80c34-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="80c34-119">&dagger;Microsoft Internet Explorer는 [WebAssembly](https://webassembly.org)를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="80c34-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="blazor-server"></a>Blazor<span data-ttu-id="80c34-120"> 서버</span><span class="sxs-lookup"><span data-stu-id="80c34-120"> Server</span></span>

| <span data-ttu-id="80c34-121">브라우저</span><span class="sxs-lookup"><span data-stu-id="80c34-121">Browser</span></span>                          | <span data-ttu-id="80c34-122">버전</span><span class="sxs-lookup"><span data-stu-id="80c34-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="80c34-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="80c34-123">Microsoft Edge</span></span>                   | <span data-ttu-id="80c34-124">현재</span><span class="sxs-lookup"><span data-stu-id="80c34-124">Current</span></span>    |
| <span data-ttu-id="80c34-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="80c34-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="80c34-126">현재</span><span class="sxs-lookup"><span data-stu-id="80c34-126">Current</span></span>    |
| <span data-ttu-id="80c34-127">Google Chrome(Android 포함)</span><span class="sxs-lookup"><span data-stu-id="80c34-127">Google Chrome, including Android</span></span> | <span data-ttu-id="80c34-128">현재</span><span class="sxs-lookup"><span data-stu-id="80c34-128">Current</span></span>    |
| <span data-ttu-id="80c34-129">Safari(iOS 포함)</span><span class="sxs-lookup"><span data-stu-id="80c34-129">Safari, including iOS</span></span>            | <span data-ttu-id="80c34-130">현재</span><span class="sxs-lookup"><span data-stu-id="80c34-130">Current</span></span>    |
| <span data-ttu-id="80c34-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="80c34-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="80c34-132">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="80c34-132">11&dagger;</span></span> |

<span data-ttu-id="80c34-133">&dagger;추가 보충 기능이 필요합니다(예: [Polyfill.io](https://polyfill.io/v3/) 번들을 통해 프라미스를 추가할 수 있음).</span><span class="sxs-lookup"><span data-stu-id="80c34-133">&dagger;Additional polyfills are required (for example, promises can be added via a [Polyfill.io](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="80c34-134">추가 자료</span><span class="sxs-lookup"><span data-stu-id="80c34-134">Additional resources</span></span>

* <xref:blazor/hosting-models>
