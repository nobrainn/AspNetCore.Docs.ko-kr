---
<span data-ttu-id="7b9d4-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7b9d4-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-102">'Blazor'</span></span>
- <span data-ttu-id="7b9d4-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-103">'Identity'</span></span>
- <span data-ttu-id="7b9d4-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="7b9d4-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-105">'Razor'</span></span>
- <span data-ttu-id="7b9d4-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-in-a-web-farm"></a><span data-ttu-id="7b9d4-107">웹 팜에 ASP.NET Core 호스트</span><span class="sxs-lookup"><span data-stu-id="7b9d4-107">Host ASP.NET Core in a web farm</span></span>

<span data-ttu-id="7b9d4-108">작성자: [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="7b9d4-108">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="7b9d4-109">‘웹 팜’은 여러 앱 인스턴스를 호스트하는 둘 이상의 웹 서버(또는 ‘노드’)의 그룹입니다. </span><span class="sxs-lookup"><span data-stu-id="7b9d4-109">A *web farm* is a group of two or more web servers (or *nodes*) that host multiple instances of an app.</span></span> <span data-ttu-id="7b9d4-110">사용자의 요청이 웹 팜에 도착하면 ‘부하 분산 장치’가 요청을 웹 팜의 노드에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-110">When requests from users arrive to a web farm, a *load balancer* distributes the requests to the web farm's nodes.</span></span> <span data-ttu-id="7b9d4-111">웹 팜은 다음을 개선합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-111">Web farms improve:</span></span>

* <span data-ttu-id="7b9d4-112">**안정성/가용성**: 하나 이상의 노드가 실패하면 부하 분산 장치는 요청을 다른 작동 노드로 라우팅하여 요청 처리를 계속할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-112">**Reliability/availability**: When one or more nodes fail, the load balancer can route requests to other functioning nodes to continue processing requests.</span></span>
* <span data-ttu-id="7b9d4-113">**용량/성능**: 여러 노드에서 단일 서버보다 많은 요청을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-113">**Capacity/performance**: Multiple nodes can process more requests than a single server.</span></span> <span data-ttu-id="7b9d4-114">부하 분산 장치는 노드에 요청을 배포하여 워크로드를 분산합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-114">The load balancer balances the workload by distributing requests to the nodes.</span></span>
* <span data-ttu-id="7b9d4-115">**확장성**: 더 많거나 더 적은 용량이 필요할 경우 워크로드에 맞게 활성 노드 수가 증가하거나 감소할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-115">**Scalability**: When more or less capacity is required, the number of active nodes can be increased or decreased to match the workload.</span></span> <span data-ttu-id="7b9d4-116">[Azure App Service](https://azure.microsoft.com/services/app-service/)와 같은 웹 팜 플랫폼 기술은 시스템 관리자의 요청 시 또는 사용자 개입 없이 자동으로 노드를 자동으로 추가하거나 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-116">Web farm platform technologies, such as [Azure App Service](https://azure.microsoft.com/services/app-service/), can automatically add or remove nodes at the request of the system administrator or automatically without human intervention.</span></span>
* <span data-ttu-id="7b9d4-117">**유지 관리**: 웹 팜의 노드는 공유 서비스 집합을 사용하므로 시스템을 더 쉽게 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-117">**Maintainability**: Nodes of a web farm can rely on a set of shared services, which results in easier system management.</span></span> <span data-ttu-id="7b9d4-118">예를 들어 웹 팜의 노드는 단일 데이터베이스 서버 및 정적 리소스(예: 이미지 및 다운로드 가능한 파일)의 일반 네트워크 위치를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-118">For example, the nodes of a web farm can rely upon a single database server and a common network location for static resources, such as images and downloadable files.</span></span>

<span data-ttu-id="7b9d4-119">이 항목에서는 공유 리소스를 사용하는 웹 팜에 호스트된 ASP.NET Core 앱의 구성 및 종속성을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-119">This topic describes configuration and dependencies for ASP.NET core apps hosted in a web farm that rely upon shared resources.</span></span>

## <a name="general-configuration"></a><span data-ttu-id="7b9d4-120">일반 구성</span><span class="sxs-lookup"><span data-stu-id="7b9d4-120">General configuration</span></span>

<xref:host-and-deploy/index>  
<span data-ttu-id="7b9d4-121">호스팅 환경을 설정하고 ASP.NET Core 앱을 배포하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-121">Learn how to set up hosting environments and deploy ASP.NET Core apps.</span></span> <span data-ttu-id="7b9d4-122">웹 팜의 각 노드에서 프로세스 관리자를 구성하여 앱 시작 및 다시 시작을 자동화합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-122">Configure a process manager on each node of the web farm to automate app starts and restarts.</span></span> <span data-ttu-id="7b9d4-123">각 노드에는 ASP.NET Core 런타임이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-123">Each node requires the ASP.NET Core runtime.</span></span> <span data-ttu-id="7b9d4-124">자세한 내용은 문서의 [호스트 및 배포](xref:host-and-deploy/index) 영역에 있는 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-124">For more information, see the topics in the [Host and deploy](xref:host-and-deploy/index) area of the documentation.</span></span>

<xref:host-and-deploy/proxy-load-balancer>  
<span data-ttu-id="7b9d4-125">중요한 요청 정보를 종종 숨기는 프록시 서버 및 부하 분산 장치 뒤에 호스트되는 앱의 구성에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-125">Learn about configuration for apps hosted behind proxy servers and load balancers, which often obscure important request information.</span></span>

<xref:host-and-deploy/azure-apps/index>  
<span data-ttu-id="7b9d4-126">[Azure App Service](https://azure.microsoft.com/services/app-service/)는 ASP.NET Core를 비롯한 웹앱을 호스트하기 위한 [Microsoft 클라우드 컴퓨팅 플랫폼 서비스](https://azure.microsoft.com/)입니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-126">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span> <span data-ttu-id="7b9d4-127">App Service는 자동 크기 조정, 부하 분산, 패치 적용 및 지속적인 배포를 제공하는 완전히 관리되는 플랫폼입니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-127">App Service is a fully managed platform that provides automatic scaling, load balancing, patching, and continuous deployment.</span></span>

## <a name="app-data"></a><span data-ttu-id="7b9d4-128">앱 데이터</span><span class="sxs-lookup"><span data-stu-id="7b9d4-128">App data</span></span>

<span data-ttu-id="7b9d4-129">앱이 여러 인스턴스로 확장되는 경우 노드 간에 앱 상태를 공유해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-129">When an app is scaled to multiple instances, there might be app state that requires sharing across nodes.</span></span> <span data-ttu-id="7b9d4-130">일시적 상태인 경우 [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache)를 공유하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-130">If the state is transient, consider sharing an [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache).</span></span> <span data-ttu-id="7b9d4-131">공유 상태에 지속성이 필요한 경우 데이터베이스에 공유 상태를 저장하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-131">If the shared state requires persistence, consider storing the shared state in a database.</span></span>

## <a name="required-configuration"></a><span data-ttu-id="7b9d4-132">필수 구성</span><span class="sxs-lookup"><span data-stu-id="7b9d4-132">Required configuration</span></span>

<span data-ttu-id="7b9d4-133">데이터 보호 및 캐싱에는 웹 팜에 배포된 앱에 대한 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-133">Data Protection and Caching require configuration for apps deployed to a web farm.</span></span>

### <a name="data-protection"></a><span data-ttu-id="7b9d4-134">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="7b9d4-134">Data Protection</span></span>

<span data-ttu-id="7b9d4-135">[ASP.NET Core 데이터 보호 시스템](xref:security/data-protection/introduction)은 앱에서 데이터를 보호하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-135">The [ASP.NET Core Data Protection system](xref:security/data-protection/introduction) is used by apps to protect data.</span></span> <span data-ttu-id="7b9d4-136">데이터 보호에는 ‘키 링’에 저장된 암호화 키 집합을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-136">Data Protection relies upon a set of cryptographic keys stored in a *key ring*.</span></span> <span data-ttu-id="7b9d4-137">데이터 보호 시스템이 초기화되면 키 링을 로컬로 저장하는 [기본 설정](xref:security/data-protection/configuration/default-settings)을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-137">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) that store the key ring locally.</span></span> <span data-ttu-id="7b9d4-138">기본 구성에서는 고유 키 링이 웹 팜의 각 노드에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-138">Under the default configuration, a unique key ring is stored on each node of the web farm.</span></span> <span data-ttu-id="7b9d4-139">따라서 각 웹 팜 노드는 다른 노드의 앱으로 암호화된 데이터를 암호 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-139">Consequently, each web farm node can't decrypt data that's encrypted by an app on any other node.</span></span> <span data-ttu-id="7b9d4-140">일반적으로 웹 팜에서 앱을 호스트하는 경우에는 기본 구성이 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-140">The default configuration isn't generally appropriate for hosting apps in a web farm.</span></span> <span data-ttu-id="7b9d4-141">공유 키 링을 구현하는 대신 항상 사용자 요청을 동일한 노드로 라우팅할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-141">An alternative to implementing a shared key ring is to always route user requests to the same node.</span></span> <span data-ttu-id="7b9d4-142">웹 팜 배포의 데이터 보호 시스템 구성에 대한 자세한 내용은 <xref:security/data-protection/configuration/overview>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-142">For more information on Data Protection system configuration for web farm deployments, see <xref:security/data-protection/configuration/overview>.</span></span>

### <a name="caching"></a><span data-ttu-id="7b9d4-143">캐싱</span><span class="sxs-lookup"><span data-stu-id="7b9d4-143">Caching</span></span>

<span data-ttu-id="7b9d4-144">웹 팜 환경에서 캐싱 메커니즘은 웹 팜의 노드에서 캐시된 항목을 공유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-144">In a web farm environment, the caching mechanism must share cached items across the web farm's nodes.</span></span> <span data-ttu-id="7b9d4-145">캐싱은 일반적인 Redis 캐시, 공유 SQL Server 데이터베이스 또는 웹 팜에서 캐시된 항목을 공유하는 사용자 지정 캐싱 구현을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-145">Caching must either rely upon a common Redis cache, a shared SQL Server database, or a custom caching implementation that shares cached items across the web farm.</span></span> <span data-ttu-id="7b9d4-146">자세한 내용은 <xref:performance/caching/distributed>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-146">For more information, see <xref:performance/caching/distributed>.</span></span>

## <a name="dependent-components"></a><span data-ttu-id="7b9d4-147">종속 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7b9d4-147">Dependent components</span></span>

<span data-ttu-id="7b9d4-148">다음 시나리오에서는 추가 구성이 필요하지 않지만 웹 팜 구성이 필요한 기술을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-148">The following scenarios don't require additional configuration, but they depend on technologies that require configuration for web farms.</span></span>

| <span data-ttu-id="7b9d4-149">시나리오</span><span class="sxs-lookup"><span data-stu-id="7b9d4-149">Scenario</span></span> | <span data-ttu-id="7b9d4-150">&hellip; 사용</span><span class="sxs-lookup"><span data-stu-id="7b9d4-150">Depends on &hellip;</span></span> |
| ---
<span data-ttu-id="7b9d4-151">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-151">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7b9d4-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-152">'Blazor'</span></span>
- <span data-ttu-id="7b9d4-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-153">'Identity'</span></span>
- <span data-ttu-id="7b9d4-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="7b9d4-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-155">'Razor'</span></span>
- <span data-ttu-id="7b9d4-156">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7b9d4-157">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-157">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7b9d4-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-158">'Blazor'</span></span>
- <span data-ttu-id="7b9d4-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-159">'Identity'</span></span>
- <span data-ttu-id="7b9d4-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="7b9d4-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-161">'Razor'</span></span>
- <span data-ttu-id="7b9d4-162">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-162">'SignalR' uid:</span></span> 

<span data-ttu-id="7b9d4-163">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-163">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7b9d4-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-164">'Blazor'</span></span>
- <span data-ttu-id="7b9d4-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-165">'Identity'</span></span>
- <span data-ttu-id="7b9d4-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="7b9d4-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-167">'Razor'</span></span>
- <span data-ttu-id="7b9d4-168">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7b9d4-169">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-169">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7b9d4-170">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-170">'Blazor'</span></span>
- <span data-ttu-id="7b9d4-171">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-171">'Identity'</span></span>
- <span data-ttu-id="7b9d4-172">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-172">'Let's Encrypt'</span></span>
- <span data-ttu-id="7b9d4-173">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-173">'Razor'</span></span>
- <span data-ttu-id="7b9d4-174">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-174">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7b9d4-175">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-175">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7b9d4-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-176">'Blazor'</span></span>
- <span data-ttu-id="7b9d4-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-177">'Identity'</span></span>
- <span data-ttu-id="7b9d4-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="7b9d4-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-179">'Razor'</span></span>
- <span data-ttu-id="7b9d4-180">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7b9d4-181">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-181">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7b9d4-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-182">'Blazor'</span></span>
- <span data-ttu-id="7b9d4-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-183">'Identity'</span></span>
- <span data-ttu-id="7b9d4-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="7b9d4-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-185">'Razor'</span></span>
- <span data-ttu-id="7b9d4-186">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7b9d4-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7b9d4-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-188">'Blazor'</span></span>
- <span data-ttu-id="7b9d4-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-189">'Identity'</span></span>
- <span data-ttu-id="7b9d4-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="7b9d4-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-191">'Razor'</span></span>
- <span data-ttu-id="7b9d4-192">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7b9d4-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7b9d4-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-194">'Blazor'</span></span>
- <span data-ttu-id="7b9d4-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-195">'Identity'</span></span>
- <span data-ttu-id="7b9d4-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="7b9d4-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-197">'Razor'</span></span>
- <span data-ttu-id="7b9d4-198">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7b9d4-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7b9d4-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-200">'Blazor'</span></span>
- <span data-ttu-id="7b9d4-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-201">'Identity'</span></span>
- <span data-ttu-id="7b9d4-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="7b9d4-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7b9d4-203">'Razor'</span></span>
- <span data-ttu-id="7b9d4-204">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="7b9d4-204">'SignalR' uid:</span></span> 

<span data-ttu-id="7b9d4-205">---------- | | 인증 | 데이터 보호(<xref:security/data-protection/configuration/overview>참조).</span><span class="sxs-lookup"><span data-stu-id="7b9d4-205">---------- | | Authentication | Data Protection (see <xref:security/data-protection/configuration/overview>).</span></span><br><br><span data-ttu-id="7b9d4-206">자세한 내용은 <xref:security/authentication/cookie> 및 <xref:security/cookie-sharing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-206">For more information, see <xref:security/authentication/cookie> and <xref:security/cookie-sharing>.</span></span> <span data-ttu-id="7b9d4-207">| | Identity | 인증 및 데이터베이스 구성.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-207">| | Identity | Authentication and database configuration.</span></span><br><br><span data-ttu-id="7b9d4-208">자세한 내용은 <xref:security/authentication/identity>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-208">For more information, see <xref:security/authentication/identity>.</span></span> <span data-ttu-id="7b9d4-209">| | 세션 | 데이터 보호(암호화된 쿠키)(<xref:security/data-protection/configuration/overview> 참조) 및 캐싱(<xref:performance/caching/distributed> 참조).</span><span class="sxs-lookup"><span data-stu-id="7b9d4-209">| | Session | Data Protection (encrypted cookies) (see <xref:security/data-protection/configuration/overview>) and Caching (see <xref:performance/caching/distributed>).</span></span><br><br><span data-ttu-id="7b9d4-210">자세한 내용은 [세션 및 앱 상태: 세션 상태](xref:fundamentals/app-state#session-state)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-210">For more information, see [Session and state management: Session state](xref:fundamentals/app-state#session-state).</span></span> <span data-ttu-id="7b9d4-211">| | TempData | 데이터 보호(암호화된 쿠키)(<xref:security/data-protection/configuration/overview> 참조) 또는 세션([세션 및 앱 상태: 세션 상태](xref:fundamentals/app-state#session-state) 참조).</span><span class="sxs-lookup"><span data-stu-id="7b9d4-211">| | TempData | Data Protection (encrypted cookies) (see <xref:security/data-protection/configuration/overview>) or Session (see [Session and state management: Session state](xref:fundamentals/app-state#session-state)).</span></span><br><br><span data-ttu-id="7b9d4-212">자세한 내용은 [세션 및 앱 상태: TempData](xref:fundamentals/app-state#tempdata)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-212">For more information, see [Session and state management: TempData](xref:fundamentals/app-state#tempdata).</span></span> <span data-ttu-id="7b9d4-213">| | 위조 방지 | 데이터 보호(<xref:security/data-protection/configuration/overview> 참조)</span><span class="sxs-lookup"><span data-stu-id="7b9d4-213">| | Anti-forgery | Data Protection (see <xref:security/data-protection/configuration/overview>).</span></span><br><br><span data-ttu-id="7b9d4-214">자세한 내용은 <xref:security/anti-request-forgery>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-214">For more information, see <xref:security/anti-request-forgery>.</span></span> |

## <a name="troubleshoot"></a><span data-ttu-id="7b9d4-215">문제 해결</span><span class="sxs-lookup"><span data-stu-id="7b9d4-215">Troubleshoot</span></span>

### <a name="data-protection-and-caching"></a><span data-ttu-id="7b9d4-216">데이터 보호 및 캐싱</span><span class="sxs-lookup"><span data-stu-id="7b9d4-216">Data Protection and caching</span></span>

<span data-ttu-id="7b9d4-217">데이터 보호 또는 캐싱이 웹 팜 환경에 맞게 구성되어 있지 않으면 요청을 처리할 때 간헐적인 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-217">When Data Protection or caching isn't configured for a web farm environment, intermittent errors occur when requests are processed.</span></span> <span data-ttu-id="7b9d4-218">이 오류의 원인은 노드가 동일한 리소스를 공유하지 않고 사용자 요청이 항상 동일한 노드로 다시 라우팅되지는 않기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-218">This occurs because nodes don't share the same resources and user requests aren't always routed back to the same node.</span></span>

<span data-ttu-id="7b9d4-219">쿠키 인증을 사용하여 앱에 로그인하는 사용자를 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-219">Consider a user who signs into the app using cookie authentication.</span></span> <span data-ttu-id="7b9d4-220">사용자는 하나의 웹 팜 노드에서 앱에 로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-220">The user signs into the app on one web farm node.</span></span> <span data-ttu-id="7b9d4-221">다음 요청이 사용자가 로그인한 동일한 노드에 도착하면 앱은 인증 쿠키를 암호 해독할 수 있고 앱의 리소스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-221">If their next request arrives at the same node where they signed in, the app is able to decrypt the authentication cookie and allows access to the app's resource.</span></span> <span data-ttu-id="7b9d4-222">다음 요청이 다른 노드에 도착하면 앱은 사용자가 로그인한 노드의 인증 쿠키를 암호 해독할 수 없으며 요청된 리소스에 대한 권한 부여가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-222">If their next request arrives at a different node, the app can't decrypt the authentication cookie from the node where the user signed in, and authorization for the requested resource fails.</span></span>

<span data-ttu-id="7b9d4-223">다음 증상 중 하나라도 **간헐적**으로 발생할 경우 일반적으로 웹 팜 환경에 대한 부적절한 데이터 보호 또는 캐싱 구성 문제로 추적됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-223">When any of the following symptoms occur **intermittently**, the problem is usually traced to improper Data Protection or caching configuration for a web farm environment:</span></span>

* <span data-ttu-id="7b9d4-224">인증 중단: 인증 쿠키가 잘못 구성되었거나 암호를 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-224">Authentication breaks: The authentication cookie is misconfigured or can't be decrypted.</span></span> <span data-ttu-id="7b9d4-225">OAuth(Facebook, Microsoft, Twitter) 또는 OpenIdConnect 로그인이 “상관 관계 실패” 오류로 인해 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-225">OAuth (Facebook, Microsoft, Twitter) or OpenIdConnect logins fail with the error "Correlation failed."</span></span>
* <span data-ttu-id="7b9d4-226">권한 부여 중단: Identity가 손실되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-226">Authorization breaks: Identity is lost.</span></span>
* <span data-ttu-id="7b9d4-227">세션 상태에서 데이터가 손실됩니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-227">Session state loses data.</span></span>
* <span data-ttu-id="7b9d4-228">캐시된 항목이 사라집니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-228">Cached items disappear.</span></span>
* <span data-ttu-id="7b9d4-229">TempData가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-229">TempData fails.</span></span>
* <span data-ttu-id="7b9d4-230">POST 실패: 위조 방지 검사가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-230">POSTs fail: The anti-forgery check fails.</span></span>

<span data-ttu-id="7b9d4-231">웹 팜 배포의 데이터 보호 구성에 대한 자세한 내용은 <xref:security/data-protection/configuration/overview>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-231">For more information on Data Protection configuration for web farm deployments, see <xref:security/data-protection/configuration/overview>.</span></span> <span data-ttu-id="7b9d4-232">웹 팜 배포의 캐싱 구성에 대한 자세한 내용은 <xref:performance/caching/distributed>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-232">For more information on caching configuration for web farm deployments, see <xref:performance/caching/distributed>.</span></span>

## <a name="obtain-data-from-apps"></a><span data-ttu-id="7b9d4-233">앱에서 데이터 얻기</span><span class="sxs-lookup"><span data-stu-id="7b9d4-233">Obtain data from apps</span></span>

<span data-ttu-id="7b9d4-234">웹 팜 앱이 요청에 응답할 수 있는 경우 터미널 인라인 미들웨어를 사용하여 앱에서 요청, 연결 및 추가 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-234">If the web farm apps are capable of responding to requests, obtain request, connection, and additional data from the apps using terminal inline middleware.</span></span> <span data-ttu-id="7b9d4-235">자세한 내용과 샘플 코드는 <xref:test/troubleshoot#obtain-data-from-an-app>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-235">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7b9d4-236">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7b9d4-236">Additional resources</span></span>

* <span data-ttu-id="7b9d4-237">[Windows용 사용자 지정 스크립트 확장](/azure/virtual-machines/extensions/custom-script-windows): Azure 가상 머신에서 스크립트를 다운로드하여 실행합니다. 이는 배포 후 구성 및 소프트웨어 설치에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="7b9d4-237">[Custom Script Extension for Windows](/azure/virtual-machines/extensions/custom-script-windows): Downloads and executes scripts on Azure virtual machines, which is useful for post-deployment configuration and software installation.</span></span>
* <xref:host-and-deploy/proxy-load-balancer>
 