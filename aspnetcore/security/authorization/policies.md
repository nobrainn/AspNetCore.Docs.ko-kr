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
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="302a6-103">ASP.NET 코어의 정책 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="302a6-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="302a6-104">역할 기반 [권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여는](xref:security/authorization/claims) 요구 사항, 요구 사항 처리기 및 미리 구성된 정책을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="302a6-105">이러한 구성 요소는 코드에서 권한 부여 평가의 식을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="302a6-106">그 결과 더 풍부하고 재사용 가능하며 테스트 가능한 권한 부여 구조가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="302a6-107">권한 부여 정책은 하나 이상의 요구 사항으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="302a6-108">`Startup.ConfigureServices` 메서드에서 권한 부여 서비스 구성의 일부로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="302a6-109">앞의 예제에서는 "AtLeast21" 정책이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="302a6-110">요구 사항에 대한&mdash;매개 변수로 제공되는 최소 연령의 단일 요구 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="302a6-111">I권한 부여 서비스</span><span class="sxs-lookup"><span data-stu-id="302a6-111">IAuthorizationService</span></span> 

<span data-ttu-id="302a6-112">권한 부여가 성공했는지 여부를 결정하는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>기본 서비스는 다음과 같은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="302a6-113">앞의 코드는 [I권한 부여 서비스](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 가지 메서드를 강조 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="302a6-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>는 메서드가 없는 마커 서비스이며 권한 부여가 성공했는지 여부를 추적하는 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="302a6-115">각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 요구 사항이 충족되었는지 확인하는 책임은 각</span><span class="sxs-lookup"><span data-stu-id="302a6-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="302a6-116">클래스는 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 처리기가 요구 사항이 충족되었는지 여부를 표시하는 데 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="302a6-117">다음 코드는 권한 부여 서비스의 단순화된(주석으로 주석이 추가된) 기본 구현을 보여 주며 다음과 같은 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="302a6-118">다음 코드는 일반적인 `ConfigureServices`다음 을 보여 주며 다음과 같은</span><span class="sxs-lookup"><span data-stu-id="302a6-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="302a6-119">사용 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` 또는 권한 부여를 위해.</span><span class="sxs-lookup"><span data-stu-id="302a6-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="302a6-120">MVC 컨트롤러에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="302a6-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="302a6-121">Razor 페이지를 사용하는 경우 이 문서의 [Razor 페이지에 정책 적용을 참조하세요.](#applying-policies-to-razor-pages)</span><span class="sxs-lookup"><span data-stu-id="302a6-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="302a6-122">정책은 정책 이름과 함께 특성을 `[Authorize]` 사용하여 컨트롤러에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="302a6-123">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="302a6-124">Razor 페이지에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="302a6-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="302a6-125">정책은 정책 이름과 함께 특성을 `[Authorize]` 사용하여 Razor 페이지에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="302a6-126">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="302a6-127">[권한 부여 규칙을](xref:security/authorization/razor-pages-authorization)사용하여 Razor 페이지에 정책을 적용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="302a6-128">요구 사항</span><span class="sxs-lookup"><span data-stu-id="302a6-128">Requirements</span></span>

<span data-ttu-id="302a6-129">권한 부여 요구 사항은 정책이 현재 사용자 주체를 평가하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="302a6-130">"Least21" 정책에서 요구 사항은 최소 연령의 단일 매개 변수입니다.&mdash;</span><span class="sxs-lookup"><span data-stu-id="302a6-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="302a6-131">요구 사항은 빈 마커 인터페이스인 [IAuthorizationRequirements를](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)구현합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="302a6-132">매개 변수화된 최소 연령 요구 사항은 다음과 같이 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="302a6-133">권한 부여 정책에 여러 권한 부여 요구 사항이 포함된 경우 정책 평가가 성공하려면 모든 요구 사항을 통과해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="302a6-134">즉, 단일 권한 부여 정책에 추가된 여러 권한 부여 요구 사항은 **AND** 기준으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="302a6-135">요구 사항에는 데이터 또는 속성이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="302a6-136">권한 부여 처리기</span><span class="sxs-lookup"><span data-stu-id="302a6-136">Authorization handlers</span></span>

<span data-ttu-id="302a6-137">권한 부여 처리기는 요구 사항의 속성 평가를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="302a6-138">권한 부여 처리기는 제공된 [AuthorizationHandlerContext에](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 대한 요구 사항을 평가하여 액세스가 허용되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="302a6-139">요구 사항에는 [여러 처리기가](#security-authorization-policies-based-multiple-handlers)있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="302a6-140">처리기는 권한을 상속할 수 `TRequirement` 있습니다 [.,\<>, ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)여기서 처리 해야 하는 요구 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="302a6-141">또는 처리기는 [IAuthorizationHandler를](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 구현하여 두 가지 이상의 요구 사항을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="302a6-142">하나의 요구 사항에 대해 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="302a6-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="302a6-143">다음은 최소 연령 처리기가 단일 요구 사항을 사용하는 일대일 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="302a6-144">앞의 코드는 현재 사용자 주체가 알려진 신뢰할 수 있는 발급자가 발급한 생년월일 클레임이 있는지 여부를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="302a6-145">클레임이 누락된 경우 권한 부여가 발생할 수 없으며, 이 경우 완료된 작업이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="302a6-146">클레임이 있으면 사용자의 나이가 계산됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="302a6-147">사용자가 요구 사항에 정의된 최소 연령을 충족하는 경우 권한 부여는 성공한 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="302a6-148">권한 부여가 `context.Succeed` 성공하면 만족스러운 요구 사항을 유일한 매개 변수로 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="302a6-149">여러 요구 사항에 대한 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="302a6-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="302a6-150">다음은 권한 처리기가 세 가지 다른 유형의 요구 사항을 처리할 수 있는 일대다 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="302a6-151">앞의 코드는 [Pending요구사항](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;통과요구 사항으로 표시되지 않은 요구 사항을 포함하는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="302a6-152">요구 `ReadPermission` 사항의 경우 사용자는 요청된 리소스에 액세스하려면 소유자 또는 스폰서여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="302a6-153">`EditPermission` 또는 `DeletePermission` 요구 사항의 경우 요청된 리소스에 액세스하려면 소유자여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="302a6-154">처리기 등록</span><span class="sxs-lookup"><span data-stu-id="302a6-154">Handler registration</span></span>

<span data-ttu-id="302a6-155">처리기는 구성 하는 동안 서비스 컬렉션에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="302a6-156">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="302a6-157">앞의 코드는 `MinimumAgeHandler` 호출하여 단일 톤으로 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`등록합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="302a6-158">처리기는 기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 한 가지를 사용하여 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="302a6-159">처리기는 무엇을 반환해야 합니까?</span><span class="sxs-lookup"><span data-stu-id="302a6-159">What should a handler return?</span></span>

<span data-ttu-id="302a6-160">`Handle` [처리기 예제의](#security-authorization-handler-example) 메서드는 값을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="302a6-161">성공 또는 실패 의 상태는 어떻게 표시되어 있습니까?</span><span class="sxs-lookup"><span data-stu-id="302a6-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="302a6-162">처리기는 성공적으로 `context.Succeed(IAuthorizationRequirement requirement)`유효성을 검사한 요구 사항을 전달하여 호출하여 성공을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="302a6-163">동일한 요구 사항에 대한 다른 처리기가 성공할 수 있기 때문에 처리기는 일반적으로 오류를 처리할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="302a6-164">실패를 보장하려면 다른 요구 사항 처리기가 성공하더라도 을 호출합니다. `context.Fail`</span><span class="sxs-lookup"><span data-stu-id="302a6-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="302a6-165">처리기가 `context.Succeed` 호출하거나 `context.Fail`다른 모든 처리기가 계속 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="302a6-166">이렇게 하면 다른 처리기가 요구 사항의 유효성을 검사하거나 실패한 경우에도 발생하는 로깅과 같은 부작용을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="302a6-167">로 설정하면 `false` [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성(ASP.NET 코어 1.1 이상에서 사용 가능)으로 설정하면 `context.Fail` 호출될 때 처리기의 실행이 단락됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="302a6-168">`InvokeHandlersAfterFailure`기본값은 `true`의 모든 처리기가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="302a6-169">인증에 실패한 경우에도 권한 부여 처리기가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="302a6-170">요구 사항에 대해 여러 처리기를 원하는 이유는 무엇입니까?</span><span class="sxs-lookup"><span data-stu-id="302a6-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="302a6-171">**평가를 OR** 기준으로 하려는 경우 단일 요구 사항에 대해 여러 처리기를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="302a6-172">예를 들어 Microsoft에는 키 카드로만 열리는 문이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="302a6-173">열쇠 카드를 집에 두면 접수원이 임시 스티커를 인쇄하여 문을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="302a6-174">이 시나리오에서는 단일 요구 사항인 *BuildingEntry*, 그러나 여러 처리기, 각각 단일 요구 사항을 검사 하는 것.</span><span class="sxs-lookup"><span data-stu-id="302a6-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="302a6-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="302a6-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="302a6-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="302a6-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="302a6-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="302a6-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="302a6-178">두 처리기가 [모두 등록되어](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="302a6-179">정책이 `BuildingEntryRequirement`을 평가할 때 둘 중 하나가 성공하면 정책 평가가 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="302a6-180">func를 사용하여 정책 이행</span><span class="sxs-lookup"><span data-stu-id="302a6-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="302a6-181">정책을 이행하는 것이 코드에서 표현하기 가 간단한 상황이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="302a6-182">정책 빌더와 정책을 `Func<AuthorizationHandlerContext, bool>` 구성할 때 를 제공할 수 있습니다. `RequireAssertion`</span><span class="sxs-lookup"><span data-stu-id="302a6-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="302a6-183">예를 들어 이전 `BadgeEntryHandler` 을 다음과 같이 다시 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="302a6-184">처리기에서 MVC 요청 컨텍스트 액세스</span><span class="sxs-lookup"><span data-stu-id="302a6-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="302a6-185">권한 `HandleRequirementAsync` 부여 처리기에서 구현하는 메서드에는 두 `AuthorizationHandlerContext` `TRequirement` 가지 매개 변수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="302a6-186">MVC 또는 Jabbr과 같은 프레임워크는 추가 정보를 `Resource` 전달하기 `AuthorizationHandlerContext` 위해 속성에 개체를 자유롭게 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="302a6-187">예를 들어 MVC는 속성에서 [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) 의 인스턴스를 전달합니다. `Resource`</span><span class="sxs-lookup"><span data-stu-id="302a6-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="302a6-188">이 속성은 `HttpContext`에 `RouteData`대한 액세스 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="302a6-189">`Resource` 속성의 사용은 프레임워크에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="302a6-190">속성에서 `Resource` 정보를 사용하면 권한 부여 정책이 특정 프레임워크로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="302a6-191">키워드를 `Resource` 사용하여 `is` 속성을 캐스팅한 다음 캐스트가 다른 프레임워크에서 실행할 `InvalidCastException` 때 코드가 충돌하지 않도록 성공했는지 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="302a6-192">역할 기반 [권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여는](xref:security/authorization/claims) 요구 사항, 요구 사항 처리기 및 미리 구성된 정책을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="302a6-193">이러한 구성 요소는 코드에서 권한 부여 평가의 식을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="302a6-194">그 결과 더 풍부하고 재사용 가능하며 테스트 가능한 권한 부여 구조가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="302a6-195">권한 부여 정책은 하나 이상의 요구 사항으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="302a6-196">`Startup.ConfigureServices` 메서드에서 권한 부여 서비스 구성의 일부로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="302a6-197">앞의 예제에서는 "AtLeast21" 정책이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="302a6-198">요구 사항에 대한&mdash;매개 변수로 제공되는 최소 연령의 단일 요구 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="302a6-199">I권한 부여 서비스</span><span class="sxs-lookup"><span data-stu-id="302a6-199">IAuthorizationService</span></span> 

<span data-ttu-id="302a6-200">권한 부여가 성공했는지 여부를 결정하는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>기본 서비스는 다음과 같은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="302a6-201">앞의 코드는 [I권한 부여 서비스](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 가지 메서드를 강조 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="302a6-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>는 메서드가 없는 마커 서비스이며 권한 부여가 성공했는지 여부를 추적하는 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="302a6-203">각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 요구 사항이 충족되었는지 확인하는 책임은 각</span><span class="sxs-lookup"><span data-stu-id="302a6-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="302a6-204">클래스는 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 처리기가 요구 사항이 충족되었는지 여부를 표시하는 데 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="302a6-205">다음 코드는 권한 부여 서비스의 단순화된(주석으로 주석이 추가된) 기본 구현을 보여 주며 다음과 같은 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="302a6-206">다음 코드는 일반적인 `ConfigureServices`다음 을 보여 주며 다음과 같은</span><span class="sxs-lookup"><span data-stu-id="302a6-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="302a6-207">사용 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` 또는 권한 부여를 위해.</span><span class="sxs-lookup"><span data-stu-id="302a6-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="302a6-208">MVC 컨트롤러에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="302a6-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="302a6-209">Razor 페이지를 사용하는 경우 이 문서의 [Razor 페이지에 정책 적용을 참조하세요.](#applying-policies-to-razor-pages)</span><span class="sxs-lookup"><span data-stu-id="302a6-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="302a6-210">정책은 정책 이름과 함께 특성을 `[Authorize]` 사용하여 컨트롤러에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="302a6-211">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="302a6-212">Razor 페이지에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="302a6-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="302a6-213">정책은 정책 이름과 함께 특성을 `[Authorize]` 사용하여 Razor 페이지에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="302a6-214">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="302a6-215">[권한 부여 규칙을](xref:security/authorization/razor-pages-authorization)사용하여 Razor 페이지에 정책을 적용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="302a6-216">요구 사항</span><span class="sxs-lookup"><span data-stu-id="302a6-216">Requirements</span></span>

<span data-ttu-id="302a6-217">권한 부여 요구 사항은 정책이 현재 사용자 주체를 평가하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="302a6-218">"Least21" 정책에서 요구 사항은 최소 연령의 단일 매개 변수입니다.&mdash;</span><span class="sxs-lookup"><span data-stu-id="302a6-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="302a6-219">요구 사항은 빈 마커 인터페이스인 [IAuthorizationRequirements를](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)구현합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="302a6-220">매개 변수화된 최소 연령 요구 사항은 다음과 같이 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="302a6-221">권한 부여 정책에 여러 권한 부여 요구 사항이 포함된 경우 정책 평가가 성공하려면 모든 요구 사항을 통과해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="302a6-222">즉, 단일 권한 부여 정책에 추가된 여러 권한 부여 요구 사항은 **AND** 기준으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="302a6-223">요구 사항에는 데이터 또는 속성이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="302a6-224">권한 부여 처리기</span><span class="sxs-lookup"><span data-stu-id="302a6-224">Authorization handlers</span></span>

<span data-ttu-id="302a6-225">권한 부여 처리기는 요구 사항의 속성 평가를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="302a6-226">권한 부여 처리기는 제공된 [AuthorizationHandlerContext에](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 대한 요구 사항을 평가하여 액세스가 허용되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="302a6-227">요구 사항에는 [여러 처리기가](#security-authorization-policies-based-multiple-handlers)있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="302a6-228">처리기는 권한을 상속할 수 `TRequirement` 있습니다 [.,\<>, ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)여기서 처리 해야 하는 요구 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="302a6-229">또는 처리기는 [IAuthorizationHandler를](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 구현하여 두 가지 이상의 요구 사항을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="302a6-230">하나의 요구 사항에 대해 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="302a6-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="302a6-231">다음은 최소 연령 처리기가 단일 요구 사항을 사용하는 일대일 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="302a6-232">앞의 코드는 현재 사용자 주체가 알려진 신뢰할 수 있는 발급자가 발급한 생년월일 클레임이 있는지 여부를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="302a6-233">클레임이 누락된 경우 권한 부여가 발생할 수 없으며, 이 경우 완료된 작업이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="302a6-234">클레임이 있으면 사용자의 나이가 계산됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="302a6-235">사용자가 요구 사항에 정의된 최소 연령을 충족하는 경우 권한 부여는 성공한 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="302a6-236">권한 부여가 `context.Succeed` 성공하면 만족스러운 요구 사항을 유일한 매개 변수로 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="302a6-237">여러 요구 사항에 대한 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="302a6-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="302a6-238">다음은 권한 처리기가 세 가지 다른 유형의 요구 사항을 처리할 수 있는 일대다 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="302a6-239">앞의 코드는 [Pending요구사항](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;통과요구 사항으로 표시되지 않은 요구 사항을 포함하는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="302a6-240">요구 `ReadPermission` 사항의 경우 사용자는 요청된 리소스에 액세스하려면 소유자 또는 스폰서여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="302a6-241">`EditPermission` 또는 `DeletePermission` 요구 사항의 경우 요청된 리소스에 액세스하려면 소유자여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="302a6-242">처리기 등록</span><span class="sxs-lookup"><span data-stu-id="302a6-242">Handler registration</span></span>

<span data-ttu-id="302a6-243">처리기는 구성 하는 동안 서비스 컬렉션에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="302a6-244">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="302a6-245">앞의 코드는 `MinimumAgeHandler` 호출하여 단일 톤으로 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`등록합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="302a6-246">처리기는 기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 한 가지를 사용하여 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="302a6-247">처리기는 무엇을 반환해야 합니까?</span><span class="sxs-lookup"><span data-stu-id="302a6-247">What should a handler return?</span></span>

<span data-ttu-id="302a6-248">`Handle` [처리기 예제의](#security-authorization-handler-example) 메서드는 값을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="302a6-249">성공 또는 실패 의 상태는 어떻게 표시되어 있습니까?</span><span class="sxs-lookup"><span data-stu-id="302a6-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="302a6-250">처리기는 성공적으로 `context.Succeed(IAuthorizationRequirement requirement)`유효성을 검사한 요구 사항을 전달하여 호출하여 성공을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="302a6-251">동일한 요구 사항에 대한 다른 처리기가 성공할 수 있기 때문에 처리기는 일반적으로 오류를 처리할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="302a6-252">실패를 보장하려면 다른 요구 사항 처리기가 성공하더라도 을 호출합니다. `context.Fail`</span><span class="sxs-lookup"><span data-stu-id="302a6-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="302a6-253">처리기가 `context.Succeed` 호출하거나 `context.Fail`다른 모든 처리기가 계속 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="302a6-254">이렇게 하면 다른 처리기가 요구 사항의 유효성을 검사하거나 실패한 경우에도 발생하는 로깅과 같은 부작용을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="302a6-255">로 설정하면 `false` [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성(ASP.NET 코어 1.1 이상에서 사용 가능)으로 설정하면 `context.Fail` 호출될 때 처리기의 실행이 단락됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="302a6-256">`InvokeHandlersAfterFailure`기본값은 `true`의 모든 처리기가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="302a6-257">인증에 실패한 경우에도 권한 부여 처리기가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="302a6-258">요구 사항에 대해 여러 처리기를 원하는 이유는 무엇입니까?</span><span class="sxs-lookup"><span data-stu-id="302a6-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="302a6-259">**평가를 OR** 기준으로 하려는 경우 단일 요구 사항에 대해 여러 처리기를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="302a6-260">예를 들어 Microsoft에는 키 카드로만 열리는 문이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="302a6-261">열쇠 카드를 집에 두면 접수원이 임시 스티커를 인쇄하여 문을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="302a6-262">이 시나리오에서는 단일 요구 사항인 *BuildingEntry*, 그러나 여러 처리기, 각각 단일 요구 사항을 검사 하는 것.</span><span class="sxs-lookup"><span data-stu-id="302a6-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="302a6-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="302a6-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="302a6-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="302a6-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="302a6-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="302a6-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="302a6-266">두 처리기가 [모두 등록되어](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="302a6-267">정책이 `BuildingEntryRequirement`을 평가할 때 둘 중 하나가 성공하면 정책 평가가 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="302a6-268">func를 사용하여 정책 이행</span><span class="sxs-lookup"><span data-stu-id="302a6-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="302a6-269">정책을 이행하는 것이 코드에서 표현하기 가 간단한 상황이 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="302a6-270">정책 빌더와 정책을 `Func<AuthorizationHandlerContext, bool>` 구성할 때 를 제공할 수 있습니다. `RequireAssertion`</span><span class="sxs-lookup"><span data-stu-id="302a6-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="302a6-271">예를 들어 이전 `BadgeEntryHandler` 을 다음과 같이 다시 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="302a6-272">처리기에서 MVC 요청 컨텍스트 액세스</span><span class="sxs-lookup"><span data-stu-id="302a6-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="302a6-273">권한 `HandleRequirementAsync` 부여 처리기에서 구현하는 메서드에는 두 `AuthorizationHandlerContext` `TRequirement` 가지 매개 변수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="302a6-274">MVC 또는 SignalR과 같은 프레임워크는 추가 정보를 `Resource` 전달하기 `AuthorizationHandlerContext` 위해 속성에 개체를 자유롭게 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="302a6-275">끝점 라우팅을 사용하는 경우 권한 부여는 일반적으로 권한 부여 미들웨어에서 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="302a6-276">이 경우 `Resource` 속성은 의 <xref:Microsoft.AspNetCore.Http.Endpoint>인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="302a6-277">끝점을 사용하여 라우팅중인 리소스의 기본을 조사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="302a6-278">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="302a6-279">기존 라우팅을 사용하면 또는 MVC의 권한 부여 필터의 일부로 `Resource` 권한 <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> 부여가 발생하는 경우 의 값은 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="302a6-280">이 속성은 `HttpContext`에 `RouteData`대한 액세스 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="302a6-281">`Resource` 속성의 사용은 프레임워크에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="302a6-282">속성에서 `Resource` 정보를 사용하면 권한 부여 정책이 특정 프레임워크로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="302a6-283">키워드를 `Resource` 사용하여 `is` 속성을 캐스팅한 다음 캐스트가 다른 프레임워크에서 실행할 `InvalidCastException` 때 코드가 충돌하지 않도록 성공했는지 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="302a6-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
