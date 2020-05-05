---
title: ASP.NET Core의 해시 암호
author: rick-anderson
description: ASP.NET Core 데이터 보호 Api를 사용 하 여 암호를 해시 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 6a5e0e4378241671905f2a759aad88372901e7d2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769782"
---
# <a name="hash-passwords-in-aspnet-core"></a>ASP.NET Core의 해시 암호

데이터 보호 코드 베이스에는 암호화 키 파생 함수를 포함 하는 *AspNetCore* 패키지가 포함 되어 있습니다. 이 패키지는 독립 실행형 구성 요소 이며 나머지 데이터 보호 시스템에 대 한 종속성이 없습니다. 완전히 독립적으로 사용할 수 있습니다. 원본은 데이터 보호 코드 베이스와 함께 편의를 위해 존재 합니다.

패키지는 현재 [PBKDF2 알고리즘](https://tools.ietf.org/html/rfc2898#section-5.2)을 `KeyDerivation.Pbkdf2` 사용 하 여 암호를 해시할 수 있는 메서드를 제공 합니다. 이 API는 .NET Framework의 기존 [Rfc2898DeriveBytes 형식과](/dotnet/api/system.security.cryptography.rfc2898derivebytes)매우 유사 하지만 세 가지 중요 한 차이점이 있습니다.

1. 메서드 `KeyDerivation.Pbkdf2` `HMACSHA1`는 여러 prfs (현재 `HMACSHA256`, `HMACSHA512`및)를 사용 하도록 지원 하지만 `Rfc2898DeriveBytes` 형식은를 지원 `HMACSHA1`합니다.

2. 메서드 `KeyDerivation.Pbkdf2` 는 현재 운영 체제를 검색 하 고 가장 최적화 된 루틴 구현을 선택 하려고 시도 하 여 특정 경우에 훨씬 더 나은 성능을 제공 합니다. Windows 8에서는의 `Rfc2898DeriveBytes`처리량을 10 배 하는 것을 제공 합니다.

3. 메서드에 `KeyDerivation.Pbkdf2` 는 호출자가 모든 매개 변수 (솔트, PRF 및 반복 횟수)를 지정 해야 합니다. `Rfc2898DeriveBytes` 형식은 이러한에 대 한 기본값을 제공 합니다.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

실제 사용 사례는 ASP.NET Core Identity `PasswordHasher` 형식에 대 한 [소스 코드](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) 를 참조 하세요.
