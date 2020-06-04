---
<span data-ttu-id="87694-101">제목: ' SignalR author: bradygaster description: ' ASP.NET Core 사용자 및 그룹 관리의 개요 '에서 사용자 및 그룹을 관리 SignalR 합니다.</span><span class="sxs-lookup"><span data-stu-id="87694-101">title: 'Manage users and groups in SignalR' author: bradygaster description: 'Overview of ASP.NET Core SignalR User and Group management.'</span></span>
<span data-ttu-id="87694-102">monikerRange: ' >= aspnetcore-2.1 ' ms author: bradyg ms. custom: mvc ms. date: 05/17/2020 no loc:</span><span class="sxs-lookup"><span data-stu-id="87694-102">monikerRange: '>= aspnetcore-2.1' ms.author: bradyg ms.custom: mvc ms.date: 05/17/2020 no-loc:</span></span>
- <span data-ttu-id="87694-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="87694-103">'Blazor'</span></span>
- <span data-ttu-id="87694-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="87694-104">'Identity'</span></span>
- <span data-ttu-id="87694-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="87694-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="87694-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="87694-106">'Razor'</span></span>
- <span data-ttu-id="87694-107">' SignalR ' uid: signalr/groups</span><span class="sxs-lookup"><span data-stu-id="87694-107">'SignalR' uid: signalr/groups</span></span>

---

# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="87694-108">에서 사용자 및 그룹 관리SignalR</span><span class="sxs-lookup"><span data-stu-id="87694-108">Manage users and groups in SignalR</span></span>

<span data-ttu-id="87694-109">만든 사람 [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="87694-109">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

SignalR<span data-ttu-id="87694-110">지정 된 연결 그룹 뿐만 아니라 특정 사용자와 연결 된 모든 연결에 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87694-110"> allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="87694-111">[샘플 코드 보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/)[(다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="87694-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="87694-112">사용자의SignalR</span><span class="sxs-lookup"><span data-stu-id="87694-112">Users in SignalR</span></span>

<span data-ttu-id="87694-113">의 단일 사용자는 SignalR 앱에 대 한 여러 연결을 가질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87694-113">A single user in SignalR can have multiple connections to an app.</span></span> <span data-ttu-id="87694-114">예를 들어 사용자는 자신의 데스크톱 및 휴대폰에 연결 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87694-114">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="87694-115">각 장치에는 별도의 SignalR 연결이 있지만 모두 동일한 사용자와 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="87694-115">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="87694-116">사용자에 게 메시지를 보내는 경우 해당 사용자와 연결 된 모든 연결에서 메시지를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="87694-116">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="87694-117">허브의 속성을 사용 하 여 연결에 대 한 사용자 id에 액세스할 수 있습니다 `Context.UserIdentifier` .</span><span class="sxs-lookup"><span data-stu-id="87694-117">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in the hub.</span></span>

<span data-ttu-id="87694-118">기본적으로는 SignalR 연결과 관련 된의을 `ClaimTypes.NameIdentifier` `ClaimsPrincipal` 사용자 식별자로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="87694-118">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="87694-119">이 동작을 사용자 지정 하려면 [클레임을 사용 하 여 id 처리 사용자 지정](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="87694-119">To customize this behavior, see [Use claims to customize identity handling](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span></span>

<span data-ttu-id="87694-120">`User`다음 예제와 같이 허브 메서드의 함수에 사용자 id를 전달 하 여 특정 사용자에 게 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="87694-120">Send a message to a specific user by passing the user identifier to the `User` function in a hub method, as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="87694-121">사용자 id는 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="87694-121">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="87694-122">그룹SignalR</span><span class="sxs-lookup"><span data-stu-id="87694-122">Groups in SignalR</span></span>

<span data-ttu-id="87694-123">그룹은 이름과 연결 된 연결의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="87694-123">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="87694-124">그룹의 모든 연결에 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87694-124">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="87694-125">그룹은 응용 프로그램에서 관리 되기 때문에 연결 또는 다중 연결에 전송 하는 데 권장 되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="87694-125">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="87694-126">연결은 여러 그룹의 멤버일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87694-126">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="87694-127">그룹은 각 공간을 그룹으로 표시할 수 있는 채팅 응용 프로그램과 같은 항목에 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="87694-127">Groups are ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="87694-128">연결은 및 메서드를 통해 그룹에 추가 되거나 제거 `AddToGroupAsync` 됩니다 `RemoveFromGroupAsync` .</span><span class="sxs-lookup"><span data-stu-id="87694-128">Connections are added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

<span data-ttu-id="87694-129">연결이 다시 연결 되 면 그룹 멤버 자격은 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="87694-129">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="87694-130">다시 설정 된 후에는 연결에서 그룹에 다시 조인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87694-130">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="87694-131">응용 프로그램을 여러 서버로 확장 하는 경우에는이 정보를 사용할 수 없으므로 그룹의 멤버 수를 계산할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="87694-131">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="87694-132">그룹을 사용 하는 동안 리소스에 대 한 액세스를 보호 하려면 ASP.NET Core의 [인증 및 권한 부여](xref:signalr/authn-and-authz) 기능을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="87694-132">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="87694-133">해당 그룹에 대 한 자격 증명이 유효한 경우에만 사용자를 그룹에 추가 하면 해당 그룹으로 전송 된 메시지는 권한 있는 사용자로만 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="87694-133">If a user is added to a group only when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="87694-134">그러나 그룹은 보안 기능이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="87694-134">However, groups are not a security feature.</span></span> <span data-ttu-id="87694-135">인증 클레임에는 만료 및 해지와 같이 그룹에서 제공 하지 않는 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="87694-135">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="87694-136">사용자의 그룹 액세스 권한이 해지 된 경우 앱은 해당 사용자를 그룹에서 명시적으로 제거 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="87694-136">If a user's permission to access the group is revoked, the app must remove the user from the group explicitly.</span></span>

> [!NOTE]
> <span data-ttu-id="87694-137">그룹 이름은 대/소문자를 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="87694-137">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="87694-138">관련 참고 자료</span><span class="sxs-lookup"><span data-stu-id="87694-138">Related resources</span></span>

* [<span data-ttu-id="87694-139">시작</span><span class="sxs-lookup"><span data-stu-id="87694-139">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="87694-140">허브</span><span class="sxs-lookup"><span data-stu-id="87694-140">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="87694-141">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="87694-141">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
