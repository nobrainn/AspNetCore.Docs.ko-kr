---
title: ASP.NET Core에 대 한 인증 샘플
author: rick-anderson
description: ASP.NET Core 리포지토리의 인증 샘플에 대 한 링크를 제공 합니다.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 7cd0fe60d7917abda7d8ac0e071deca13a4136ce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776554"
---
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET Core에 대 한 인증 샘플

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[ASP.NET Core 리포지토리에](https://github.com/dotnet/AspNetCore) 는 *AspNetCore/src/Security/samples* 폴더에 다음 인증 샘플이 포함 되어 있습니다.

* [클레임 변환](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [쿠키 인증](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [사용자 지정 정책 공급자-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [동적 인증 스키마 및 옵션](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [외부 클레임](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [요청에 따라 쿠키와 다른 인증 체계를 선택 합니다.](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [정적 파일에 대 한 액세스를 제한 합니다.](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>샘플 실행

* [분기](https://github.com/dotnet/AspNetCore)를 선택 합니다. 예를 들어 `release/3.1`
* [ASP.NET Core 리포지토리](https://github.com/dotnet/AspNetCore)를 복제 하거나 다운로드 합니다.
* ASP.NET Core 리포지토리의 복제본과 일치 하는 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 버전을 설치 했는지 확인 합니다.
* *AspNetCore/src/Security/samples* 에서 샘플로 이동 하 여로 `dotnet run`샘플을 실행 합니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[ASP.NET Core 리포지토리에](https://github.com/dotnet/AspNetCore) 는 *AspNetCore/src/Security/samples* 폴더에 다음 인증 샘플이 포함 되어 있습니다.

* [클레임 변환](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [쿠키 인증](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [사용자 지정 정책 공급자-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [동적 인증 스키마 및 옵션](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [외부 클레임](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [요청에 따라 쿠키와 다른 인증 체계를 선택 합니다.](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [정적 파일에 대 한 액세스를 제한 합니다.](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>샘플 실행

* [분기](https://github.com/dotnet/AspNetCore)를 선택 합니다. 예를 들어 `release/2.2`
* [ASP.NET Core 리포지토리](https://github.com/dotnet/AspNetCore)를 복제 하거나 다운로드 합니다.
* ASP.NET Core 리포지토리의 복제본과 일치 하는 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 버전을 설치 했는지 확인 합니다.
* *AspNetCore/src/Security/samples* 에서 샘플로 이동 하 여로 `dotnet run`샘플을 실행 합니다.

::: moniker-end
