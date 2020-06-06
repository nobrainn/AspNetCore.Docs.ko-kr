---
title: BlazorAzure Active Directory 그룹 및 역할을 사용 하 여 ASP.NET Core weasembmbse
author: guardrex
description: BlazorAzure Active Directory 그룹 및 역할을 사용 하도록 weasemboma를 구성 하는 방법을 알아봅니다.
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
uid: security/blazor/webassembly/aad-groups-roles
ms.openlocfilehash: 3ed06cca7e20da381b870e642a6c616b2578cd0a
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451877"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="f1601-103">Azure AD 그룹, 관리 역할 및 사용자 정의 역할</span><span class="sxs-lookup"><span data-stu-id="f1601-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="f1601-104">By [Luke Latham](https://github.com/guardrex) 및 [Javier Calvarro e](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="f1601-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="f1601-105">AAD (Azure Active Directory)는 ASP.NET Core와 결합할 수 있는 몇 가지 권한 부여 방법을 제공 합니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="f1601-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="f1601-106">사용자 정의 그룹</span><span class="sxs-lookup"><span data-stu-id="f1601-106">User-defined groups</span></span>
  * <span data-ttu-id="f1601-107">보안</span><span class="sxs-lookup"><span data-stu-id="f1601-107">Security</span></span>
  * <span data-ttu-id="f1601-108">O365</span><span class="sxs-lookup"><span data-stu-id="f1601-108">O365</span></span>
  * <span data-ttu-id="f1601-109">배포</span><span class="sxs-lookup"><span data-stu-id="f1601-109">Distribution</span></span>
* <span data-ttu-id="f1601-110">역할</span><span class="sxs-lookup"><span data-stu-id="f1601-110">Roles</span></span>
  * <span data-ttu-id="f1601-111">기본 제공 관리 역할</span><span class="sxs-lookup"><span data-stu-id="f1601-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="f1601-112">사용자 정의 역할</span><span class="sxs-lookup"><span data-stu-id="f1601-112">User-defined roles</span></span>

<span data-ttu-id="f1601-113">이 문서의 지침은 Blazor 다음 항목에서 설명 하는 weasembommbsembomsemboma에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="f1601-114">Microsoft 계정을 사용하는 독립 실행형</span><span class="sxs-lookup"><span data-stu-id="f1601-114">Standalone with Microsoft Accounts</span></span>](xref:security/blazor/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="f1601-115">AAD를 사용하는 독립 실행형</span><span class="sxs-lookup"><span data-stu-id="f1601-115">Standalone with AAD</span></span>](xref:security/blazor/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="f1601-116">AAD를 사용하는 호스트형</span><span class="sxs-lookup"><span data-stu-id="f1601-116">Hosted with AAD</span></span>](xref:security/blazor/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="f1601-117">사용자 정의 그룹 및 기본 제공 관리 역할</span><span class="sxs-lookup"><span data-stu-id="f1601-117">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="f1601-118">멤버 자격 클레임을 제공 하도록 Azure Portal에서 앱을 구성 하려면 `groups` 다음 Azure 문서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f1601-118">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="f1601-119">사용자를 사용자 정의 AAD 그룹 및 기본 제공 관리 역할에 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-119">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="f1601-120">Azure AD 보안 그룹을 사용하는 역할</span><span class="sxs-lookup"><span data-stu-id="f1601-120">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="f1601-121">groupMembershipClaims 특성</span><span class="sxs-lookup"><span data-stu-id="f1601-121">groupMembershipClaims attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="f1601-122">다음 예에서는 사용자가 AAD 기본 제공 *청구 관리자* 역할에 할당 된 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-122">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="f1601-123">`groups`AAD에서 전송 하는 단일 클레임은 사용자의 그룹 및 역할을 JSON 배열의 개체 id (guid)로 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-123">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="f1601-124">앱은 그룹 및 역할의 JSON 배열을 `group` 앱이 [정책을](xref:security/authorization/policies) 구성할 수 있는 개별 클레임으로 변환 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-124">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="f1601-125"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>그룹 및 역할에 대 한 배열 속성을 포함 하도록 확장 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-125">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span>

<span data-ttu-id="f1601-126">*CustomUserAccount.cs*:</span><span class="sxs-lookup"><span data-stu-id="f1601-126">*CustomUserAccount.cs*:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; }

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; }
}
```

<span data-ttu-id="f1601-127">호스팅된 솔루션의 독립 실행형 앱 또는 클라이언트 앱에서 사용자 지정 사용자 팩터리를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-127">Create a custom user factory in the standalone app or Client app of a Hosted solution.</span></span> <span data-ttu-id="f1601-128">다음 팩터리는 `roles` [사용자 정의 역할](#user-defined-roles) 섹션에서 설명 하는 클레임 배열을 처리 하도록 구성 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-128">The following factory is also configured to handle `roles` claim arrays, which are covered in the [User-defined roles](#user-defined-roles) section:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomUserFactory(NavigationManager navigationManager,
        IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            foreach (var group in account.Groups)
            {
                userIdentity.AddClaim(new Claim("group", group));
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="f1601-129">원본 `groups` 클레임은 프레임 워크에 의해 자동으로 제거 되므로이를 제거 하는 코드를 제공할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-129">There's no need to provide code to remove the original `groups` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="f1601-130">`Program.Main`호스팅된 솔루션의 독립 실행형 앱 또는 클라이언트 앱의 (*Program.cs*)에 팩터리를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-130">Register the factory in `Program.Main` (*Program.cs*) of the standalone app or Client app of a Hosted solution:</span></span>

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");
    
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

<span data-ttu-id="f1601-131">에서 각 그룹 또는 역할에 대 한 [정책을](xref:security/authorization/policies) 만듭니다 `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="f1601-131">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="f1601-132">다음 예에서는 AAD 기본 제공 *청구 관리자* 역할에 대 한 정책을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-132">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="f1601-133">AAD 역할 개체 Id의 전체 목록은 [aad 관리 역할 그룹 id](#aad-adminstrative-role-group-ids) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f1601-133">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="f1601-134">다음 예제에서 앱은 위의 정책을 사용 하 여 사용자에 게 권한을 부여 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-134">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="f1601-135">[AuthorizeView 구성 요소](xref:security/blazor/index#authorizeview-component) 는 정책과 함께 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-135">The [AuthorizeView component](xref:security/blazor/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="f1601-136">전체 구성 요소에 대 한 액세스는 [ `[Authorize]` ] 특성 지시어] (f: security/blazor/index #) ()를 사용 하는 정책을 기반으로 할 수 있습니다 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> .</span><span class="sxs-lookup"><span data-stu-id="f1601-136">Access to an entire component can be based on the policy using [`[Authorize]`] attribute directive](xref:security/blazor/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="f1601-137">사용자가 로그인 하지 않은 경우 AAD 로그인 페이지로 리디렉션되고 로그인 한 후 구성 요소로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-137">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="f1601-138">[프로시저 논리를 사용 하 여 코드에서](xref:security/blazor/index#procedural-logic)정책 검사를 수행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-138">A policy check can also be [performed in code with procedural logic](xref:security/blazor/index#procedural-logic):</span></span>

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

### <a name="user-defined-roles"></a><span data-ttu-id="f1601-139">사용자 정의 역할</span><span class="sxs-lookup"><span data-stu-id="f1601-139">User-defined roles</span></span>

<span data-ttu-id="f1601-140">AAD에 등록 된 앱은 사용자 정의 역할을 사용 하도록 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-140">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="f1601-141">멤버 자격 클레임을 제공 하도록 Azure Portal에서 앱을 구성 하려면 `roles` [방법: 응용 프로그램에 앱 역할 추가 및 Azure 설명서의 토큰에서 수신](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f1601-141">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="f1601-142">다음 예제에서는 앱이 두 개의 역할로 구성 되어 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-142">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="f1601-143">Azure AD Premium 계정을 사용 하지 않고 보안 그룹에 역할을 할당할 수는 없지만 사용자를 역할에 할당 하 고 `roles` 표준 Azure 계정으로 사용자에 대 한 클레임을 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-143">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="f1601-144">이 섹션의 지침에는 Azure AD Premium 계정이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-144">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="f1601-145">각 추가 역할 할당에 대해 **_사용자를 다시 추가_** 하 여 Azure Portal에서 여러 역할을 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-145">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="f1601-146">`roles`AAD에서 전송 하는 단일 클레임은 사용자 정의 역할을 `appRoles` `value` JSON 배열의 s로 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-146">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="f1601-147">앱은 역할의 JSON 배열을 개별 클레임으로 변환 해야 합니다 `role` .</span><span class="sxs-lookup"><span data-stu-id="f1601-147">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="f1601-148">`CustomUserFactory` [사용자 정의 그룹 및 AAD 기본 제공 관리 역할](#user-defined-groups-and-built-in-administrative-roles) 섹션에 표시 된는 JSON 배열 값을 사용 하는 클레임에 대해 작동 하도록 설정 됩니다 `roles` .</span><span class="sxs-lookup"><span data-stu-id="f1601-148">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="f1601-149">`CustomUserFactory` [사용자 정의 그룹 및 AAD 기본 제공 관리 역할](#user-defined-groups-and-built-in-administrative-roles) 섹션에 표시 된 대로 호스팅된 솔루션의 독립 실행형 앱 또는 클라이언트 앱에를 추가 하 고 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-149">Add and register the `CustomUserFactory` in the standalone app or Client app of a Hosted solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="f1601-150">원본 `roles` 클레임은 프레임 워크에 의해 자동으로 제거 되므로이를 제거 하는 코드를 제공할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-150">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="f1601-151">`Program.Main`호스팅된 솔루션의 독립 실행형 앱 또는 클라이언트 앱에서 역할 클레임으로 이름이 "" 인 클레임을 지정 합니다 `role` .</span><span class="sxs-lookup"><span data-stu-id="f1601-151">In `Program.Main` of the standalone app or Client app of a Hosted solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="f1601-152">이 시점에서 구성 요소 권한 부여 방법이 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-152">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="f1601-153">구성 요소의 권한 부여 메커니즘은 역할을 사용 `admin` 하 여 사용자에 게 권한을 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-153">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="f1601-154">[AuthorizeView 구성 요소](xref:security/blazor/index#authorizeview-component) (예: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="f1601-154">[AuthorizeView component](xref:security/blazor/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="f1601-155">[ `[Authorize]` ] 특성 지시문] (f: security/blazor/index # 권한 부여-특성) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (예: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="f1601-155">[`[Authorize]`] attribute directive](xref:security/blazor/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="f1601-156">[절차적 논리](xref:security/blazor/index#procedural-logic) (예: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="f1601-156">[Procedural logic](xref:security/blazor/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="f1601-157">여러 역할 테스트가 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-157">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="f1601-158">AAD 관리 역할 그룹 Id</span><span class="sxs-lookup"><span data-stu-id="f1601-158">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="f1601-159">다음 표에 제공 된 개체 Id는 클레임에 대 한 [정책을](xref:security/authorization/policies) 만드는 데 사용 됩니다 `group` .</span><span class="sxs-lookup"><span data-stu-id="f1601-159">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="f1601-160">정책을 통해 앱은 앱의 다양 한 작업에 대 한 사용자 권한을 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-160">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="f1601-161">자세한 내용은 [사용자 정의 그룹 및 AAD 기본 제공 관리 역할](#user-defined-groups-and-built-in-administrative-roles) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f1601-161">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="f1601-162">AAD 관리 역할</span><span class="sxs-lookup"><span data-stu-id="f1601-162">AAD Administrative Role</span></span> | <span data-ttu-id="f1601-163">개체 ID입니다.</span><span class="sxs-lookup"><span data-stu-id="f1601-163">Object ID</span></span>
--- | ---
<span data-ttu-id="f1601-164">애플리케이션 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-164">Application administrator</span></span> | <span data-ttu-id="f1601-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="f1601-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="f1601-166">애플리케이션 개발자</span><span class="sxs-lookup"><span data-stu-id="f1601-166">Application developer</span></span> | <span data-ttu-id="f1601-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="f1601-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="f1601-168">인증 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-168">Authentication administrator</span></span> | <span data-ttu-id="f1601-169">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="f1601-169">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="f1601-170">Azure DevOps 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-170">Azure DevOps administrator</span></span> | <span data-ttu-id="f1601-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="f1601-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="f1601-172">Azure Information Protection 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-172">Azure Information Protection administrator</span></span> | <span data-ttu-id="f1601-173">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="f1601-173">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="f1601-174">B2C IEF 키 집합 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-174">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="f1601-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="f1601-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="f1601-176">B2C IEF 정책 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-176">B2C IEF Policy administrator</span></span> | <span data-ttu-id="f1601-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="f1601-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="f1601-178">B2C 사용자 흐름 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-178">B2C user flow administrator</span></span> | <span data-ttu-id="f1601-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="f1601-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="f1601-180">B2C 사용자 흐름 특성 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-180">B2C user flow attribute administrator</span></span> | <span data-ttu-id="f1601-181">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="f1601-181">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="f1601-182">대금 청구 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-182">Billing administrator</span></span> | <span data-ttu-id="f1601-183">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="f1601-183">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="f1601-184">클라우드 애플리케이션 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-184">Cloud application administrator</span></span> | <span data-ttu-id="f1601-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="f1601-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="f1601-186">클라우드 디바이스 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-186">Cloud device administrator</span></span> | <span data-ttu-id="f1601-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="f1601-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="f1601-188">규정 준수 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-188">Compliance administrator</span></span> | <span data-ttu-id="f1601-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="f1601-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="f1601-190">준수 데이터 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-190">Compliance data administrator</span></span> | <span data-ttu-id="f1601-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="f1601-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="f1601-192">조건부 액세스 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-192">Conditional Access administrator</span></span> | <span data-ttu-id="f1601-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="f1601-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="f1601-194">고객 LockBox 액세스 승인자</span><span class="sxs-lookup"><span data-stu-id="f1601-194">Customer LockBox access approver</span></span> | <span data-ttu-id="f1601-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="f1601-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="f1601-196">데스크톱 분석 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-196">Desktop Analytics administrator</span></span> | <span data-ttu-id="f1601-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="f1601-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="f1601-198">디렉터리 읽기 권한자</span><span class="sxs-lookup"><span data-stu-id="f1601-198">Directory readers</span></span> | <span data-ttu-id="f1601-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="f1601-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="f1601-200">Dynamics 365 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-200">Dynamics 365 administrator</span></span> | <span data-ttu-id="f1601-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="f1601-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="f1601-202">Exchange 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-202">Exchange administrator</span></span> | <span data-ttu-id="f1601-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="f1601-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="f1601-204">외부 Identity 공급자 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-204">External Identity Provider administrator</span></span> | <span data-ttu-id="f1601-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="f1601-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="f1601-206">전역 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-206">Global administrator</span></span> | <span data-ttu-id="f1601-207">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="f1601-207">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="f1601-208">전역 판독기</span><span class="sxs-lookup"><span data-stu-id="f1601-208">Global reader</span></span> | <span data-ttu-id="f1601-209">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="f1601-209">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="f1601-210">그룹 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-210">Groups administrator</span></span> | <span data-ttu-id="f1601-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="f1601-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="f1601-212">게스트 초대자</span><span class="sxs-lookup"><span data-stu-id="f1601-212">Guest inviter</span></span> | <span data-ttu-id="f1601-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="f1601-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="f1601-214">기술 지원팀 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-214">Helpdesk administrator</span></span> | <span data-ttu-id="f1601-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="f1601-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="f1601-216">Intune 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-216">Intune administrator</span></span> | <span data-ttu-id="f1601-217">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="f1601-217">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="f1601-218">Kaizala 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-218">Kaizala administrator</span></span> | <span data-ttu-id="f1601-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="f1601-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="f1601-220">라이선스 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-220">License administrator</span></span> | <span data-ttu-id="f1601-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="f1601-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="f1601-222">메시지 센터 개인 정보 읽기 권한자</span><span class="sxs-lookup"><span data-stu-id="f1601-222">Message center privacy reader</span></span> | <span data-ttu-id="f1601-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="f1601-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="f1601-224">메시지 센터 판독기</span><span class="sxs-lookup"><span data-stu-id="f1601-224">Message center reader</span></span> | <span data-ttu-id="f1601-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="f1601-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="f1601-226">Office 앱 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-226">Office apps administrator</span></span> | <span data-ttu-id="f1601-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="f1601-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="f1601-228">암호 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-228">Password administrator</span></span> | <span data-ttu-id="f1601-229">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="f1601-229">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="f1601-230">Power BI 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-230">Power BI administrator</span></span> | <span data-ttu-id="f1601-231">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="f1601-231">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="f1601-232">Power 플랫폼 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-232">Power platform administrator</span></span> | <span data-ttu-id="f1601-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="f1601-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="f1601-234">권한 있는 인증 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-234">Privileged authentication administrator</span></span> | <span data-ttu-id="f1601-235">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="f1601-235">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="f1601-236">권한 있는 역할 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-236">Privileged role administrator</span></span> | <span data-ttu-id="f1601-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="f1601-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="f1601-238">보고서 읽기 권한자</span><span class="sxs-lookup"><span data-stu-id="f1601-238">Reports reader</span></span> | <span data-ttu-id="f1601-239">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="f1601-239">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="f1601-240">관리자 검색</span><span class="sxs-lookup"><span data-stu-id="f1601-240">Search administrator</span></span> | <span data-ttu-id="f1601-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="f1601-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="f1601-242">검색 편집기</span><span class="sxs-lookup"><span data-stu-id="f1601-242">Search editor</span></span> | <span data-ttu-id="f1601-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="f1601-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="f1601-244">보안 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-244">Security administrator</span></span> | <span data-ttu-id="f1601-245">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="f1601-245">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="f1601-246">보안 운영자</span><span class="sxs-lookup"><span data-stu-id="f1601-246">Security operator</span></span> | <span data-ttu-id="f1601-247">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="f1601-247">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="f1601-248">보안 판독기</span><span class="sxs-lookup"><span data-stu-id="f1601-248">Security reader</span></span> | <span data-ttu-id="f1601-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="f1601-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="f1601-250">서비스 지원 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-250">Service support administrator</span></span> | <span data-ttu-id="f1601-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="f1601-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="f1601-252">SharePoint 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-252">SharePoint administrator</span></span> | <span data-ttu-id="f1601-253">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="f1601-253">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="f1601-254">비즈니스용 Skype 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-254">Skype for Business administrator</span></span> | <span data-ttu-id="f1601-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="f1601-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="f1601-256">Teams 통신 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-256">Teams Communications Administrator</span></span> | <span data-ttu-id="f1601-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="f1601-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="f1601-258">Teams 통신 지원 엔지니어</span><span class="sxs-lookup"><span data-stu-id="f1601-258">Teams Communications Support Engineer</span></span> | <span data-ttu-id="f1601-259">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="f1601-259">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="f1601-260">팀 통신 전문가</span><span class="sxs-lookup"><span data-stu-id="f1601-260">Teams Communications Specialist</span></span> | <span data-ttu-id="f1601-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="f1601-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="f1601-262">Teams 서비스 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-262">Teams Service Administrator</span></span> | <span data-ttu-id="f1601-263">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="f1601-263">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="f1601-264">사용자 관리자</span><span class="sxs-lookup"><span data-stu-id="f1601-264">User administrator</span></span> | <span data-ttu-id="f1601-265">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="f1601-265">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f1601-266">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f1601-266">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:security/blazor/index>
