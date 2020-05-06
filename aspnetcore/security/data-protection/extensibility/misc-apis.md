---
title: 기타 ASP.NET Core 데이터 보호 Api
author: rick-anderson
description: ASP.NET Core Data Protection ISecret 인터페이스에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: a07ccc3645a9a8132fd5290e7c43f353f74aca05
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776983"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="e02da-103">기타 ASP.NET Core 데이터 보호 Api</span><span class="sxs-lookup"><span data-stu-id="e02da-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="e02da-104">다음 인터페이스 중 하나를 구현 하는 형식은 여러 호출자에 대해 스레드로부터 안전 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e02da-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="e02da-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="e02da-105">ISecret</span></span>

<span data-ttu-id="e02da-106">인터페이스 `ISecret` 는 암호화 키 자료와 같은 비밀 값을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e02da-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="e02da-107">다음 API 화면을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="e02da-107">It contains the following API surface:</span></span>

* <span data-ttu-id="e02da-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="e02da-108">`Length`: `int`</span></span>

* <span data-ttu-id="e02da-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="e02da-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="e02da-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="e02da-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="e02da-111">메서드 `WriteSecretIntoBuffer` 는 제공 된 버퍼를 원시 비밀 값으로 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="e02da-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="e02da-112">이 API는을 `byte[]` 직접 반환 하지 않고 버퍼를 매개 변수로 사용 하는 이유는 호출자에 게 버퍼 개체를 고정 하 여 관리 되는 가비지 수집기에 대 한 비밀 노출을 제한 하는 기회를 제공 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e02da-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="e02da-113">이 `Secret` 형식은 비밀 값이 in-process 메모리 `ISecret` 에 저장 되는의 구체적 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="e02da-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="e02da-114">Windows 플랫폼에서 비밀 값은 [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)를 통해 암호화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e02da-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
