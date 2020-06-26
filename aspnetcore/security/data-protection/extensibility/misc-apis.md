---
title: 기타 ASP.NET Core 데이터 보호 Api
author: rick-anderson
description: ASP.NET Core Data Protection ISecret 인터페이스에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: e9de92233468e9e07791df608b1c37ffb3b29949
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408502"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="db859-103">기타 ASP.NET Core 데이터 보호 Api</span><span class="sxs-lookup"><span data-stu-id="db859-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="db859-104">다음 인터페이스 중 하나를 구현 하는 형식은 여러 호출자에 대해 스레드로부터 안전 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db859-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="db859-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="db859-105">ISecret</span></span>

<span data-ttu-id="db859-106">`ISecret`인터페이스는 암호화 키 자료와 같은 비밀 값을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="db859-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="db859-107">다음 API 화면을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="db859-107">It contains the following API surface:</span></span>

* <span data-ttu-id="db859-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="db859-108">`Length`: `int`</span></span>

* <span data-ttu-id="db859-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="db859-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="db859-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="db859-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="db859-111">`WriteSecretIntoBuffer`메서드는 제공 된 버퍼를 원시 비밀 값으로 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="db859-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="db859-112">이 API는을 직접 반환 하지 않고 버퍼를 매개 변수로 사용 하는 이유는 `byte[]` 호출자에 게 버퍼 개체를 고정 하 여 관리 되는 가비지 수집기에 대 한 비밀 노출을 제한 하는 기회를 제공 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="db859-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="db859-113">`Secret`이 형식은 `ISecret` 비밀 값이 in-process 메모리에 저장 되는의 구체적 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="db859-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="db859-114">Windows 플랫폼에서 비밀 값은 [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)를 통해 암호화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db859-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
