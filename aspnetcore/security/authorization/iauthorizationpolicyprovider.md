---
title: ASP.NET 코어의 사용자 지정 권한 부여 정책 공급자
author: mjrousos
description: ASP.NET 핵심 앱에서 사용자 지정 IAuthorizationPolicyProvider를 사용하여 권한 부여 정책을 동적으로 생성하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2c67e25ff73bc8c3a5f3af4730a509b2385fc1cf
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661778"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>ASP.NET 코어에서 i권한 부여정책 공급자를 사용하는 사용자 지정 권한 부여 정책 공급자 

[Mike Rousos](https://github.com/mjrousos) 작성

일반적으로 [정책 기반 권한 부여를](xref:security/authorization/policies)사용하는 `AuthorizationOptions.AddPolicy` 경우 정책은 권한 부여 서비스 구성의 일부로 호출하여 등록됩니다. 일부 시나리오에서는 이러한 방식으로 모든 권한 부여 정책을 등록하는 것이 불가능하거나 바람직하지 않을 수 있습니다. 이러한 경우 사용자 지정을 `IAuthorizationPolicyProvider` 사용하여 권한 부여 정책이 제공되는 방법을 제어할 수 있습니다.

사용자 지정 [I권한 부여Policy공급자가](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) 유용할 수 있는 시나리오의 예는 다음과 같습니다.

* 외부 서비스를 사용하여 정책 평가를 제공합니다.
* 다양한 정책(예: 객실 번호 또는 연령에 따라)을 사용하면 각 개별 권한 부여 정책을 `AuthorizationOptions.AddPolicy` 호출과 함께 추가하는 것은 의미가 없습니다.
* 외부 데이터 원본(예: 데이터베이스)의 정보를 기반으로 런타임에 정책을 만들거나 다른 메커니즘을 통해 권한 부여 요구 사항을 동적으로 결정합니다.

[AspNetCore GitHub 리포지토리에서](https://github.com/dotnet/AspNetCore) [샘플 코드를 보거나 다운로드합니다.](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) 도트넷/AspNetCore 리포지토리 ZIP 파일을 다운로드합니다. 파일 의 압축을 해제합니다. *SRC/보안/샘플/사용자 지정정책공급자* 프로젝트 폴더로 이동합니다.

## <a name="customize-policy-retrieval"></a>정책 검색 사용자 지정

ASP.NET Core 앱은 인터페이스 `IAuthorizationPolicyProvider` 구현을 사용하여 권한 부여 정책을 검색합니다. 기본적으로 [Default권한 부여정책공급자가](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) 등록되어 사용됩니다. `DefaultAuthorizationPolicyProvider``AuthorizationOptions` 을 클릭합니다. `IServiceCollection.AddAuthorization`

앱의 `IAuthorizationPolicyProvider` [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 다른 구현을 등록하여 이 동작을 사용자 지정합니다. 

인터페이스에는 다음 세 개의 API가 `IAuthorizationPolicyProvider` 포함되어 있습니다.

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) 는 지정된 이름에 대한 권한 부여 정책을 반환합니다.
* [GetDefaultPolicyAsync는](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) 기본 권한 부여 정책(지정된 정책이 없는 특성에 `[Authorize]` 사용되는 정책)을 반환합니다. 
* [GetFallbackPolicyAsync는](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) 대체 권한 부여 정책(정책을 지정하지 않은 경우 권한 부여 미들웨어에서 사용하는 정책)을 반환합니다. 

이러한 API를 구현하면 권한 부여 정책이 제공되는 방법을 사용자 지정할 수 있습니다.

## <a name="parameterized-authorize-attribute-example"></a>매개 변수화된 권한 부여 특성 예제

유용한 한 `IAuthorizationPolicyProvider` 가지 시나리오는 `[Authorize]` 요구 사항이 매개 변수에 종속된 사용자 지정 특성을 사용하도록 설정하는 것입니다. 예를 들어 [정책 기반 권한 부여](xref:security/authorization/policies) 문서에서 연령 기반("AtLeast21") 정책이 샘플로 사용되었습니다. 연령대가 다른 사용자가 앱에서 서로 다른 *different* 컨트롤러 작업을 사용할 수 있도록 해야 하는 경우 여러 가지 연령 기반 정책을 사용할 수 있습니다. 응용 프로그램에서 필요한 모든 다른 연령 기반 정책을 등록하는 대신 `AuthorizationOptions`사용자 지정을 `IAuthorizationPolicyProvider`사용 하 여 동적으로 정책을 생성할 수 있습니다. 정책을 보다 쉽게 사용할 수 `[MinimumAgeAuthorize(20)]`있도록.

## <a name="custom-authorization-attributes"></a>사용자 지정 권한 부여 특성

권한 부여 정책은 이름으로 식별됩니다. 앞서 `MinimumAgeAuthorizeAttribute` 설명한 사용자 지정은 인수를 해당 권한 부여 정책을 검색하는 데 사용할 수 있는 문자열로 매핑해야 합니다. 속성을 `AuthorizeAttribute` 래핑하여 속성을 파생하여 `Age` `AuthorizeAttribute.Policy` 수행할 수 있습니다.

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

이 특성 형식에는 하드 코딩된 접두사() `Policy` `"MinimumAge"`및 생성자통해 전달되는 정수를 기반으로 하는 문자열이 있습니다.

정수를 매개 변수로 사용 한다는 `Authorize` 점을 제외하여 다른 특성과 동일한 방식으로 작업에 적용할 수 있습니다.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>사용자 지정 I권한 부여정책공급자

사용자 `MinimumAgeAuthorizeAttribute` 지정을 통해 원하는 최소 연령에 대해 권한 부여 정책을 쉽게 요청할 수 있습니다. 해결해야 할 다음 문제는 권한 부여 정책을 모든 연령대에 사용할 수 있도록 하는 것입니다. 이것은 유용한 `IAuthorizationPolicyProvider` 곳입니다.

을 `MinimumAgeAuthorizationAttribute`사용할 때 권한 부여 정책 `"MinimumAge" + Age`이름은 패턴을 `IAuthorizationPolicyProvider` 따르므로 사용자 지정은 다음을 통해 권한 부여 정책을 생성해야 합니다.

* 정책 이름에서 나이를 구문 분석합니다.
* 새 `AuthorizationPolicyBuilder` 새 를 만드는 데 사용`AuthorizationPolicy`
* 이 예제및 다음 예제에서는 사용자가 쿠키를 통해 인증되었다고 가정합니다. 하나 `AuthorizationPolicyBuilder` 이상의 권한 부여 체계 이름으로 구성하거나 항상 성공해야 합니다. 그렇지 않으면 사용자에게 챌린지를 제공하는 방법에 대한 정보가 없으며 예외가 throw됩니다.
* `AuthorizationPolicyBuilder.AddRequirements`의 연령을 기준으로 정책에 요구 사항을 추가합니다. 다른 시나리오에서는 을 `RequireClaim` `RequireRole`사용하거나 `RequireUserName` 대신 사용할 수 있습니다.

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a>여러 권한 부여 정책 공급자

사용자 지정 `IAuthorizationPolicyProvider` 구현을 사용하는 경우 ASP.NET Core는 `IAuthorizationPolicyProvider`의 한 인스턴스만 사용한다는 점에 유의하십시오. 사용자 지정 공급자가 사용할 모든 정책 이름에 대한 권한 부여 정책을 제공할 수 없는 경우 백업 공급자로 연기해야 합니다. 

예를 들어 사용자 지정 연령 정책과 보다 전통적인 역할 기반 정책 검색이 모두 필요한 응용 프로그램을 고려해 보겠습니다. 이러한 앱은 다음과 같은 사용자 지정 권한 부여 정책 공급자를 사용할 수 있습니다.

* 정책 이름을 구문 분석하려고 시도합니다. 
* 정책 이름에 연령이 포함되지 `DefaultAuthorizationPolicyProvider`않은 경우 다른 정책 공급자(예:)를 호출합니다.

위에 `IAuthorizationPolicyProvider` 표시된 예제 구현은 생성자에서 백업 정책 공급자를 만들어 사용하도록 `DefaultAuthorizationPolicyProvider` 업데이트할 수 있습니다(정책 이름이 'MinimumAge' + age의 예상 패턴과 일치하지 않는 경우 사용).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

그런 다음 `GetPolicyAsync` null을 반환하는 `BackupPolicyProvider` 대신 메서드를 업데이트하여 사용할 수 있습니다.

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>기본 정책

명명된 권한 부여 정책을 제공하는 `IAuthorizationPolicyProvider` 것 `GetDefaultPolicyAsync` 외에도 사용자 지정은 `[Authorize]` 정책 이름을 지정하지 않고 특성에 대한 권한 부여 정책을 제공하기 위해 구현해야 합니다.

대부분의 경우 이 권한 부여 특성에는 인증된 사용자만 필요하므로 `RequireAuthenticatedUser`다음을 호출하여 필요한 정책을 만들 수 있습니다.

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

사용자 지정의 `IAuthorizationPolicyProvider`모든 측면과 마찬가지로 필요에 따라 이 것을 사용자 지정할 수 있습니다. 경우에 따라 대체에서 기본 정책을 검색하는 것이 바람직할 수 `IAuthorizationPolicyProvider`있습니다.

## <a name="fallback-policy"></a>대체 정책

사용자 `IAuthorizationPolicyProvider` 지정은 정책을 `GetFallbackPolicyAsync` [결합할](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) 때와 정책을 지정하지 않은 경우 사용되는 정책을 제공하기 위해 선택적으로 구현할 수 있습니다. null이 아닌 정책을 반환하는 경우 `GetFallbackPolicyAsync` 요청에 대해 정책이 지정되지 않은 경우 권한 부여 미들웨어에서 반환된 정책을 사용합니다.

대체 정책이 필요하지 않은 경우 공급자는 `null` 대체 공급자를 반환하거나 연기할 수 있습니다.

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>사용자 지정 I권한 부여 정책 공급자 사용

`IAuthorizationPolicyProvider`에서 사용자 지정 정책을 사용하려면 다음을 수행해야 합니다.

* 모든 정책 `AuthorizationHandler` 기반 권한 부여 시나리오와 마찬가지로 종속성 주입(정책 [기반 권한 부여에](xref:security/authorization/policies#authorization-handlers)설명됨)을 사용 하 여 적절 한 형식을 등록 합니다.
* 기본 정책 `IAuthorizationPolicyProvider` 공급자를 대체 하려면 앱의 종속성 `Startup.ConfigureServices`주입 서비스 컬렉션(in)에 사용자 지정 형식을 등록합니다.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

전체 사용자 `IAuthorizationPolicyProvider` 지정 샘플은 [도트넷/aspnetcore GitHub 리포지토리에서](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)사용할 수 있습니다.
