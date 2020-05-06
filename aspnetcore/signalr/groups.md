---
title: 에서 사용자 및 그룹 관리SignalR
author: bradygaster
description: ASP.NET Core SignalR 사용자 및 그룹 관리 개요
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: af498575899fcfa407aba9f9f49c0bfeabadc7a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776300"
---
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="1beb2-103">에서 사용자 및 그룹 관리SignalR</span><span class="sxs-lookup"><span data-stu-id="1beb2-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="1beb2-104">만든 사람 [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="1beb2-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

SignalR<span data-ttu-id="1beb2-105">지정 된 연결 그룹 뿐만 아니라 특정 사용자와 연결 된 모든 연결에 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-105"> allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="1beb2-106">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="1beb2-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="1beb2-107">사용자의SignalR</span><span class="sxs-lookup"><span data-stu-id="1beb2-107">Users in SignalR</span></span>

SignalR<span data-ttu-id="1beb2-108">특정 사용자와 연결 된 모든 연결에 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-108"> allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="1beb2-109">기본적으로는 SignalR 연결과 `ClaimsPrincipal` 관련 `ClaimTypes.NameIdentifier` 된의을 사용자 식별자로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="1beb2-110">단일 사용자가 SignalR 앱에 대 한 여러 연결을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="1beb2-111">예를 들어 사용자는 자신의 데스크톱 및 휴대폰에 연결 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="1beb2-112">각 장치에는 별도 SignalR 의 연결이 있지만 모두 동일한 사용자와 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="1beb2-113">사용자에 게 메시지를 보내는 경우 해당 사용자와 연결 된 모든 연결에서 메시지를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="1beb2-114">허브의 `Context.UserIdentifier` 속성을 사용 하 여 연결에 대 한 사용자 id에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="1beb2-115">다음 예제와 같이 허브 메서드의 `User` 함수에 사용자 id를 전달 하 여 특정 사용자에 게 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="1beb2-116">사용자 id는 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="1beb2-117">그룹SignalR</span><span class="sxs-lookup"><span data-stu-id="1beb2-117">Groups in SignalR</span></span>

<span data-ttu-id="1beb2-118">그룹은 이름과 연결 된 연결의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="1beb2-119">그룹의 모든 연결에 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="1beb2-120">그룹은 응용 프로그램에서 관리 되기 때문에 연결 또는 다중 연결에 전송 하는 데 권장 되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="1beb2-121">연결은 여러 그룹의 멤버일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="1beb2-122">이를 통해 그룹은 채팅 응용 프로그램과 같은 항목에 이상적으로 사용할 수 있습니다. 여기서 각 공간은 그룹으로 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="1beb2-123">및 `AddToGroupAsync` `RemoveFromGroupAsync` 메서드를 통해 그룹에서 연결을 추가 하거나 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="1beb2-124">연결이 다시 연결 되 면 그룹 멤버 자격은 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="1beb2-125">다시 설정 된 후에는 연결에서 그룹에 다시 조인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="1beb2-126">응용 프로그램을 여러 서버로 확장 하는 경우에는이 정보를 사용할 수 없으므로 그룹의 멤버 수를 계산할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="1beb2-127">그룹을 사용 하는 동안 리소스에 대 한 액세스를 보호 하려면 ASP.NET Core의 [인증 및 권한 부여](xref:signalr/authn-and-authz) 기능을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="1beb2-128">해당 그룹에 대 한 자격 증명이 유효한 경우에만 그룹에 사용자를 추가 하는 경우 해당 그룹에 전송 된 메시지는 권한 있는 사용자로만 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="1beb2-129">그러나 그룹은 보안 기능이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-129">However, groups are not a security feature.</span></span> <span data-ttu-id="1beb2-130">인증 클레임에는 만료 및 해지와 같이 그룹에서 제공 하지 않는 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="1beb2-131">사용자의 그룹 액세스 권한이 해지 된 경우 해당 사용자를 수동으로 검색 하 여 그룹에서 제거 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="1beb2-132">그룹 이름은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="1beb2-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="1beb2-133">관련 리소스</span><span class="sxs-lookup"><span data-stu-id="1beb2-133">Related resources</span></span>

* [<span data-ttu-id="1beb2-134">시작</span><span class="sxs-lookup"><span data-stu-id="1beb2-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="1beb2-135">허브</span><span class="sxs-lookup"><span data-stu-id="1beb2-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="1beb2-136">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="1beb2-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
