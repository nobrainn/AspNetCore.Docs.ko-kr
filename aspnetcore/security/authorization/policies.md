---
title: ASP.NET 코어의 정책 기반 권한 부여
author: rick-anderson
description: ASP.NET Core 앱에서 권한 부여 요구 사항을 적용하기 위해 권한 부여 정책 처리기를 만들고 사용하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
uid: security/authorization/policies
ms.openlocfilehash: 66412a6020ea8f12427c8c5b466e1b2eedccf0f9
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488763"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>ASP.NET 코어의 정책 기반 권한 부여

::: moniker range=">= aspnetcore-3.0"

역할 기반 [권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여는](xref:security/authorization/claims) 요구 사항, 요구 사항 처리기 및 미리 구성된 정책을 사용합니다. 이러한 구성 요소는 코드에서 권한 부여 평가의 식을 지원합니다. 그 결과 더 풍부하고 재사용 가능하며 테스트 가능한 권한 부여 구조가 생성됩니다.

권한 부여 정책은 하나 이상의 요구 사항으로 구성됩니다. `Startup.ConfigureServices` 메서드에서 권한 부여 서비스 구성의 일부로 등록됩니다.

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

앞의 예제에서는 "AtLeast21" 정책이 만들어집니다. 요구 사항에 대한&mdash;매개 변수로 제공되는 최소 연령의 단일 요구 사항이 있습니다.

## <a name="iauthorizationservice"></a>I권한 부여 서비스 

권한 부여가 성공했는지 여부를 결정하는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>기본 서비스는 다음과 같은 것입니다.

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

앞의 코드는 [I권한 부여 서비스](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 가지 메서드를 강조 표시됩니다.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>는 메서드가 없는 마커 서비스이며 권한 부여가 성공했는지 여부를 추적하는 메커니즘입니다.

각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 요구 사항이 충족되었는지 확인하는 책임은 각
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

클래스는 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 처리기가 요구 사항이 충족되었는지 여부를 표시하는 데 사용하는 것입니다.

```csharp
 context.Succeed(requirement)
```

다음 코드는 권한 부여 서비스의 단순화된(주석으로 주석이 추가된) 기본 구현을 보여 주며 다음과 같은 코드입니다.

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

다음 코드는 일반적인 `ConfigureServices`다음 을 보여 주며 다음과 같은

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

사용 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` 또는 권한 부여를 위해.

## <a name="applying-policies-to-mvc-controllers"></a>MVC 컨트롤러에 정책 적용

Razor 페이지를 사용하는 경우 이 문서의 [Razor 페이지에 정책 적용을 참조하세요.](#applying-policies-to-razor-pages)

정책은 정책 이름과 함께 특성을 `[Authorize]` 사용하여 컨트롤러에 적용됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Razor 페이지에 정책 적용

정책은 정책 이름과 함께 특성을 `[Authorize]` 사용하여 Razor 페이지에 적용됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

[권한 부여 규칙을](xref:security/authorization/razor-pages-authorization)사용하여 Razor 페이지에 정책을 적용할 수도 있습니다.

## <a name="requirements"></a>요구 사항

권한 부여 요구 사항은 정책이 현재 사용자 주체를 평가하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다. "Least21" 정책에서 요구 사항은 최소 연령의 단일 매개 변수입니다.&mdash; 요구 사항은 빈 마커 인터페이스인 [IAuthorizationRequirements를](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)구현합니다. 매개 변수화된 최소 연령 요구 사항은 다음과 같이 구현할 수 있습니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

권한 부여 정책에 여러 권한 부여 요구 사항이 포함된 경우 정책 평가가 성공하려면 모든 요구 사항을 통과해야 합니다. 즉, 단일 권한 부여 정책에 추가된 여러 권한 부여 요구 사항은 **AND** 기준으로 처리됩니다.

> [!NOTE]
> 요구 사항에는 데이터 또는 속성이 필요하지 않습니다.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>권한 부여 처리기

권한 부여 처리기는 요구 사항의 속성 평가를 담당합니다. 권한 부여 처리기는 제공된 [AuthorizationHandlerContext에](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 대한 요구 사항을 평가하여 액세스가 허용되는지 확인합니다.

요구 사항에는 [여러 처리기가](#security-authorization-policies-based-multiple-handlers)있을 수 있습니다. 처리기는 권한을 상속할 수 `TRequirement` 있습니다 [.,\<>, ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)여기서 처리 해야 하는 요구 사항입니다. 또는 처리기는 [IAuthorizationHandler를](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 구현하여 두 가지 이상의 요구 사항을 처리할 수 있습니다.

### <a name="use-a-handler-for-one-requirement"></a>하나의 요구 사항에 대해 처리기 사용

<a name="security-authorization-handler-example"></a>

다음은 최소 연령 처리기가 단일 요구 사항을 사용하는 일대일 관계의 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

앞의 코드는 현재 사용자 주체가 알려진 신뢰할 수 있는 발급자가 발급한 생년월일 클레임이 있는지 여부를 결정합니다. 클레임이 누락된 경우 권한 부여가 발생할 수 없으며, 이 경우 완료된 작업이 반환됩니다. 클레임이 있으면 사용자의 나이가 계산됩니다. 사용자가 요구 사항에 정의된 최소 연령을 충족하는 경우 권한 부여는 성공한 것으로 간주됩니다. 권한 부여가 `context.Succeed` 성공하면 만족스러운 요구 사항을 유일한 매개 변수로 호출합니다.

### <a name="use-a-handler-for-multiple-requirements"></a>여러 요구 사항에 대한 처리기 사용

다음은 권한 처리기가 세 가지 다른 유형의 요구 사항을 처리할 수 있는 일대다 관계의 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

앞의 코드는 [Pending요구사항](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;통과요구 사항으로 표시되지 않은 요구 사항을 포함하는 속성입니다. 요구 `ReadPermission` 사항의 경우 사용자는 요청된 리소스에 액세스하려면 소유자 또는 스폰서여야 합니다. `EditPermission` 또는 `DeletePermission` 요구 사항의 경우 요청된 리소스에 액세스하려면 소유자여야 합니다.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>처리기 등록

처리기는 구성 하는 동안 서비스 컬렉션에 등록 됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

앞의 코드는 `MinimumAgeHandler` 호출하여 단일 톤으로 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`등록합니다. 처리기는 기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 한 가지를 사용하여 등록할 수 있습니다.

## <a name="what-should-a-handler-return"></a>처리기는 무엇을 반환해야 합니까?

`Handle` [처리기 예제의](#security-authorization-handler-example) 메서드는 값을 반환하지 않습니다. 성공 또는 실패 의 상태는 어떻게 표시되어 있습니까?

* 처리기는 성공적으로 `context.Succeed(IAuthorizationRequirement requirement)`유효성을 검사한 요구 사항을 전달하여 호출하여 성공을 나타냅니다.

* 동일한 요구 사항에 대한 다른 처리기가 성공할 수 있기 때문에 처리기는 일반적으로 오류를 처리할 필요가 없습니다.

* 실패를 보장하려면 다른 요구 사항 처리기가 성공하더라도 을 호출합니다. `context.Fail`

처리기가 `context.Succeed` 호출하거나 `context.Fail`다른 모든 처리기가 계속 호출됩니다. 이렇게 하면 다른 처리기가 요구 사항의 유효성을 검사하거나 실패한 경우에도 발생하는 로깅과 같은 부작용을 생성할 수 있습니다. 로 설정하면 `false` [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성(ASP.NET 코어 1.1 이상에서 사용 가능)으로 설정하면 `context.Fail` 호출될 때 처리기의 실행이 단락됩니다. `InvokeHandlersAfterFailure`기본값은 `true`의 모든 처리기가 호출됩니다.

> [!NOTE]
> 인증에 실패한 경우에도 권한 부여 처리기가 호출됩니다.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>요구 사항에 대해 여러 처리기를 원하는 이유는 무엇입니까?

**평가를 OR** 기준으로 하려는 경우 단일 요구 사항에 대해 여러 처리기를 구현합니다. 예를 들어 Microsoft에는 키 카드로만 열리는 문이 있습니다. 열쇠 카드를 집에 두면 접수원이 임시 스티커를 인쇄하여 문을 엽니다. 이 시나리오에서는 단일 요구 사항인 *BuildingEntry*, 그러나 여러 처리기, 각각 단일 요구 사항을 검사 하는 것.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

두 처리기가 [모두 등록되어](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)있는지 확인합니다. 정책이 `BuildingEntryRequirement`을 평가할 때 둘 중 하나가 성공하면 정책 평가가 성공합니다.

## <a name="using-a-func-to-fulfill-a-policy"></a>func를 사용하여 정책 이행

정책을 이행하는 것이 코드에서 표현하기 가 간단한 상황이 있을 수 있습니다. 정책 빌더와 정책을 `Func<AuthorizationHandlerContext, bool>` 구성할 때 를 제공할 수 있습니다. `RequireAssertion`

예를 들어 이전 `BadgeEntryHandler` 을 다음과 같이 다시 작성할 수 있습니다.

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>처리기에서 MVC 요청 컨텍스트 액세스

권한 `HandleRequirementAsync` 부여 처리기에서 구현하는 메서드에는 두 `AuthorizationHandlerContext` `TRequirement` 가지 매개 변수가 있습니다. MVC 또는 Jabbr과 같은 프레임워크는 추가 정보를 `Resource` 전달하기 `AuthorizationHandlerContext` 위해 속성에 개체를 자유롭게 추가할 수 있습니다.

예를 들어 MVC는 속성에서 [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) 의 인스턴스를 전달합니다. `Resource` 이 속성은 `HttpContext`에 `RouteData`대한 액세스 권한을 제공합니다.

`Resource` 속성의 사용은 프레임워크에 따라 다릅니다. 속성에서 `Resource` 정보를 사용하면 권한 부여 정책이 특정 프레임워크로 제한됩니다. 키워드를 `Resource` 사용하여 `is` 속성을 캐스팅한 다음 캐스트가 다른 프레임워크에서 실행할 `InvalidCastException` 때 코드가 충돌하지 않도록 성공했는지 확인해야 합니다.

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

역할 기반 [권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여는](xref:security/authorization/claims) 요구 사항, 요구 사항 처리기 및 미리 구성된 정책을 사용합니다. 이러한 구성 요소는 코드에서 권한 부여 평가의 식을 지원합니다. 그 결과 더 풍부하고 재사용 가능하며 테스트 가능한 권한 부여 구조가 생성됩니다.

권한 부여 정책은 하나 이상의 요구 사항으로 구성됩니다. `Startup.ConfigureServices` 메서드에서 권한 부여 서비스 구성의 일부로 등록됩니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

앞의 예제에서는 "AtLeast21" 정책이 만들어집니다. 요구 사항에 대한&mdash;매개 변수로 제공되는 최소 연령의 단일 요구 사항이 있습니다.

## <a name="iauthorizationservice"></a>I권한 부여 서비스 

권한 부여가 성공했는지 여부를 결정하는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>기본 서비스는 다음과 같은 것입니다.

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

앞의 코드는 [I권한 부여 서비스](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 가지 메서드를 강조 표시됩니다.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>는 메서드가 없는 마커 서비스이며 권한 부여가 성공했는지 여부를 추적하는 메커니즘입니다.

각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 요구 사항이 충족되었는지 확인하는 책임은 각
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

클래스는 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 처리기가 요구 사항이 충족되었는지 여부를 표시하는 데 사용하는 것입니다.

```csharp
 context.Succeed(requirement)
```

다음 코드는 권한 부여 서비스의 단순화된(주석으로 주석이 추가된) 기본 구현을 보여 주며 다음과 같은 코드입니다.

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

다음 코드는 일반적인 `ConfigureServices`다음 을 보여 주며 다음과 같은

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

사용 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` 또는 권한 부여를 위해.

## <a name="applying-policies-to-mvc-controllers"></a>MVC 컨트롤러에 정책 적용

Razor 페이지를 사용하는 경우 이 문서의 [Razor 페이지에 정책 적용을 참조하세요.](#applying-policies-to-razor-pages)

정책은 정책 이름과 함께 특성을 `[Authorize]` 사용하여 컨트롤러에 적용됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Razor 페이지에 정책 적용

정책은 정책 이름과 함께 특성을 `[Authorize]` 사용하여 Razor 페이지에 적용됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

[권한 부여 규칙을](xref:security/authorization/razor-pages-authorization)사용하여 Razor 페이지에 정책을 적용할 수도 있습니다.

## <a name="requirements"></a>요구 사항

권한 부여 요구 사항은 정책이 현재 사용자 주체를 평가하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다. "Least21" 정책에서 요구 사항은 최소 연령의 단일 매개 변수입니다.&mdash; 요구 사항은 빈 마커 인터페이스인 [IAuthorizationRequirements를](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)구현합니다. 매개 변수화된 최소 연령 요구 사항은 다음과 같이 구현할 수 있습니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

권한 부여 정책에 여러 권한 부여 요구 사항이 포함된 경우 정책 평가가 성공하려면 모든 요구 사항을 통과해야 합니다. 즉, 단일 권한 부여 정책에 추가된 여러 권한 부여 요구 사항은 **AND** 기준으로 처리됩니다.

> [!NOTE]
> 요구 사항에는 데이터 또는 속성이 필요하지 않습니다.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>권한 부여 처리기

권한 부여 처리기는 요구 사항의 속성 평가를 담당합니다. 권한 부여 처리기는 제공된 [AuthorizationHandlerContext에](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 대한 요구 사항을 평가하여 액세스가 허용되는지 확인합니다.

요구 사항에는 [여러 처리기가](#security-authorization-policies-based-multiple-handlers)있을 수 있습니다. 처리기는 권한을 상속할 수 `TRequirement` 있습니다 [.,\<>, ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)여기서 처리 해야 하는 요구 사항입니다. 또는 처리기는 [IAuthorizationHandler를](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 구현하여 두 가지 이상의 요구 사항을 처리할 수 있습니다.

### <a name="use-a-handler-for-one-requirement"></a>하나의 요구 사항에 대해 처리기 사용

<a name="security-authorization-handler-example"></a>

다음은 최소 연령 처리기가 단일 요구 사항을 사용하는 일대일 관계의 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

앞의 코드는 현재 사용자 주체가 알려진 신뢰할 수 있는 발급자가 발급한 생년월일 클레임이 있는지 여부를 결정합니다. 클레임이 누락된 경우 권한 부여가 발생할 수 없으며, 이 경우 완료된 작업이 반환됩니다. 클레임이 있으면 사용자의 나이가 계산됩니다. 사용자가 요구 사항에 정의된 최소 연령을 충족하는 경우 권한 부여는 성공한 것으로 간주됩니다. 권한 부여가 `context.Succeed` 성공하면 만족스러운 요구 사항을 유일한 매개 변수로 호출합니다.

### <a name="use-a-handler-for-multiple-requirements"></a>여러 요구 사항에 대한 처리기 사용

다음은 권한 처리기가 세 가지 다른 유형의 요구 사항을 처리할 수 있는 일대다 관계의 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

앞의 코드는 [Pending요구사항](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;통과요구 사항으로 표시되지 않은 요구 사항을 포함하는 속성입니다. 요구 `ReadPermission` 사항의 경우 사용자는 요청된 리소스에 액세스하려면 소유자 또는 스폰서여야 합니다. `EditPermission` 또는 `DeletePermission` 요구 사항의 경우 요청된 리소스에 액세스하려면 소유자여야 합니다.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>처리기 등록

처리기는 구성 하는 동안 서비스 컬렉션에 등록 됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

앞의 코드는 `MinimumAgeHandler` 호출하여 단일 톤으로 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`등록합니다. 처리기는 기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 한 가지를 사용하여 등록할 수 있습니다.

## <a name="what-should-a-handler-return"></a>처리기는 무엇을 반환해야 합니까?

`Handle` [처리기 예제의](#security-authorization-handler-example) 메서드는 값을 반환하지 않습니다. 성공 또는 실패 의 상태는 어떻게 표시되어 있습니까?

* 처리기는 성공적으로 `context.Succeed(IAuthorizationRequirement requirement)`유효성을 검사한 요구 사항을 전달하여 호출하여 성공을 나타냅니다.

* 동일한 요구 사항에 대한 다른 처리기가 성공할 수 있기 때문에 처리기는 일반적으로 오류를 처리할 필요가 없습니다.

* 실패를 보장하려면 다른 요구 사항 처리기가 성공하더라도 을 호출합니다. `context.Fail`

처리기가 `context.Succeed` 호출하거나 `context.Fail`다른 모든 처리기가 계속 호출됩니다. 이렇게 하면 다른 처리기가 요구 사항의 유효성을 검사하거나 실패한 경우에도 발생하는 로깅과 같은 부작용을 생성할 수 있습니다. 로 설정하면 `false` [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성(ASP.NET 코어 1.1 이상에서 사용 가능)으로 설정하면 `context.Fail` 호출될 때 처리기의 실행이 단락됩니다. `InvokeHandlersAfterFailure`기본값은 `true`의 모든 처리기가 호출됩니다.

> [!NOTE]
> 인증에 실패한 경우에도 권한 부여 처리기가 호출됩니다.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>요구 사항에 대해 여러 처리기를 원하는 이유는 무엇입니까?

**평가를 OR** 기준으로 하려는 경우 단일 요구 사항에 대해 여러 처리기를 구현합니다. 예를 들어 Microsoft에는 키 카드로만 열리는 문이 있습니다. 열쇠 카드를 집에 두면 접수원이 임시 스티커를 인쇄하여 문을 엽니다. 이 시나리오에서는 단일 요구 사항인 *BuildingEntry*, 그러나 여러 처리기, 각각 단일 요구 사항을 검사 하는 것.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

두 처리기가 [모두 등록되어](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)있는지 확인합니다. 정책이 `BuildingEntryRequirement`을 평가할 때 둘 중 하나가 성공하면 정책 평가가 성공합니다.

## <a name="using-a-func-to-fulfill-a-policy"></a>func를 사용하여 정책 이행

정책을 이행하는 것이 코드에서 표현하기 가 간단한 상황이 있을 수 있습니다. 정책 빌더와 정책을 `Func<AuthorizationHandlerContext, bool>` 구성할 때 를 제공할 수 있습니다. `RequireAssertion`

예를 들어 이전 `BadgeEntryHandler` 을 다음과 같이 다시 작성할 수 있습니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>처리기에서 MVC 요청 컨텍스트 액세스

권한 `HandleRequirementAsync` 부여 처리기에서 구현하는 메서드에는 두 `AuthorizationHandlerContext` `TRequirement` 가지 매개 변수가 있습니다. MVC 또는 SignalR과 같은 프레임워크는 추가 정보를 `Resource` 전달하기 `AuthorizationHandlerContext` 위해 속성에 개체를 자유롭게 추가할 수 있습니다.

끝점 라우팅을 사용하는 경우 권한 부여는 일반적으로 권한 부여 미들웨어에서 처리합니다. 이 경우 `Resource` 속성은 의 <xref:Microsoft.AspNetCore.Http.Endpoint>인스턴스입니다. 끝점을 사용하여 라우팅중인 리소스의 기본을 조사할 수 있습니다. 다음은 그 예입니다.

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

기존 라우팅을 사용하면 또는 MVC의 권한 부여 필터의 일부로 `Resource` 권한 <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> 부여가 발생하는 경우 의 값은 인스턴스입니다. 이 속성은 `HttpContext`에 `RouteData`대한 액세스 권한을 제공합니다.

`Resource` 속성의 사용은 프레임워크에 따라 다릅니다. 속성에서 `Resource` 정보를 사용하면 권한 부여 정책이 특정 프레임워크로 제한됩니다. 키워드를 `Resource` 사용하여 `is` 속성을 캐스팅한 다음 캐스트가 다른 프레임워크에서 실행할 `InvalidCastException` 때 코드가 충돌하지 않도록 성공했는지 확인해야 합니다.

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
