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
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET 코어에 대한 인증 샘플

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[ASP.NET 코어 리포지토리에는](https://github.com/dotnet/AspNetCore) *AspNetCore/src/Security/샘플* 폴더에 다음과 같은 인증 샘플이 포함되어 있습니다.

* [클레임 변환](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [쿠키 인증](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [사용자 지정 정책 공급자 - I권한 부여정책공급자](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [동적 인증 체계 및 옵션](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [외부 클레임](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [요청에 따라 쿠키와 다른 인증 체계 중 선택](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [정적 파일에 대한 액세스를 제한합니다.](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>샘플 실행

* [분기를](https://github.com/dotnet/AspNetCore)선택합니다. 예를 들어 `release/3.1`
* 복제 또는 [ASP.NET 코어 리포지토리를](https://github.com/dotnet/AspNetCore)다운로드합니다.
* ASP.NET 코어 리포지토리의 복제본과 일치하는 [.NET 코어 SDK](https://dotnet.microsoft.com/download/dotnet-core) 버전을 설치했는지 확인합니다.
* *AspNetCore/src/보안/샘플의 샘플로* 이동하여 을 사용하여 `dotnet run`샘플을 실행합니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[ASP.NET 코어 리포지토리에는](https://github.com/dotnet/AspNetCore) *AspNetCore/src/Security/샘플* 폴더에 다음과 같은 인증 샘플이 포함되어 있습니다.

* [클레임 변환](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [쿠키 인증](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [사용자 지정 정책 공급자 - I권한 부여정책공급자](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [동적 인증 체계 및 옵션](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [외부 클레임](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [요청에 따라 쿠키와 다른 인증 체계 중 선택](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [정적 파일에 대한 액세스를 제한합니다.](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>샘플 실행

* [분기를](https://github.com/dotnet/AspNetCore)선택합니다. 예를 들어 `release/2.2`
* 복제 또는 [ASP.NET 코어 리포지토리를](https://github.com/dotnet/AspNetCore)다운로드합니다.
* ASP.NET 코어 리포지토리의 복제본과 일치하는 [.NET 코어 SDK](https://dotnet.microsoft.com/download/dotnet-core) 버전을 설치했는지 확인합니다.
* *AspNetCore/src/보안/샘플의 샘플로* 이동하여 을 사용하여 `dotnet run`샘플을 실행합니다.

::: moniker-end
