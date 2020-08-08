---
title: ASP.NET Core 분산 캐싱
author: rick-anderson
description: 특히 클라우드 또는 서버 팜 환경에서 ASP.NET Core 분산 캐시를 사용 하 여 앱 성능 및 확장성을 개선 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/distributed
ms.openlocfilehash: 64a4b6f606a4f5f8e73ef08f53cbb6e4003245aa
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020680"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="5b68a-103">ASP.NET Core 분산 캐싱</span><span class="sxs-lookup"><span data-stu-id="5b68a-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="5b68a-104">작성자- [Mohsin Nasir](https://github.com/mohsinnasir) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5b68a-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5b68a-105">분산 캐시는 여러 앱 서버에서 공유 하는 캐시로, 일반적으로 액세스 하는 앱 서버에 외부 서비스로 유지 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="5b68a-106">분산 캐시는 특히 앱이 클라우드 서비스 또는 서버 팜에서 호스트 되는 경우 ASP.NET Core 앱의 성능과 확장성을 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="5b68a-107">분산 캐시는 개별 앱 서버에 캐시 된 데이터가 저장 되는 다른 캐싱 시나리오에 비해 여러 가지 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="5b68a-108">캐시 된 데이터를 배포 하는 경우 데이터는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="5b68a-109">는 여러 서버에 대 한 여러 요청 *에서 일관 되* 고 일관 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="5b68a-110">서버를 다시 시작 하 고 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="5b68a-111">로컬 메모리를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-111">Doesn't use local memory.</span></span>

<span data-ttu-id="5b68a-112">분산 캐시 구성은 구현 별로 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="5b68a-113">이 문서에서는 SQL Server 및 Redis 분산 캐시를 구성 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="5b68a-114">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))와 같은 타사 구현도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="5b68a-115">선택한 구현과 관계 없이 앱은 인터페이스를 사용 하 여 캐시와 상호 작용 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="5b68a-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="5b68a-116">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5b68a-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5b68a-117">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="5b68a-117">Prerequisites</span></span>

<span data-ttu-id="5b68a-118">SQL Server 분산 캐시를 사용 하려면 패키지 참조를 [Microsoft 확장명이](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) . i n t. i n t. i n t 패키지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="5b68a-119">Redis 분산 캐시를 사용 하려면 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 패키지에 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="5b68a-120">NCache 분산 캐시를 사용 하려면 [NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 패키지에 대 한 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="5b68a-121">IDistributedCache 인터페이스</span><span class="sxs-lookup"><span data-stu-id="5b68a-121">IDistributedCache interface</span></span>

<span data-ttu-id="5b68a-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>인터페이스는 분산 캐시 구현에서 항목을 조작 하는 다음과 같은 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="5b68a-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : 문자열 키를 허용 하 고 `byte[]` 캐시 된 항목을 캐시에 있는 경우 배열로 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="5b68a-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` 문자열 키를 사용 하 여 캐시에 항목 (배열)을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="5b68a-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : 해당 키에 따라 캐시에서 항목을 새로 고쳐 슬라이딩 만료 시간 제한 (있는 경우)을 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="5b68a-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : 문자열 키를 기준으로 캐시 항목을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="5b68a-127">분산 캐싱 서비스 설정</span><span class="sxs-lookup"><span data-stu-id="5b68a-127">Establish distributed caching services</span></span>

<span data-ttu-id="5b68a-128">에서의 구현을 등록 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5b68a-129">이 항목에서 설명 하는 프레임 워크 제공 구현에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="5b68a-130">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="5b68a-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="5b68a-131">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="5b68a-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="5b68a-132">Distributed Redis cache</span><span class="sxs-lookup"><span data-stu-id="5b68a-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="5b68a-133">Distributed NCache cache</span><span class="sxs-lookup"><span data-stu-id="5b68a-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="5b68a-134">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="5b68a-134">Distributed Memory Cache</span></span>

<span data-ttu-id="5b68a-135">분산 메모리 캐시 ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> )는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 메모리에 항목을 저장 하는 프레임 워크에서 제공 하는 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="5b68a-136">분산 메모리 캐시는 실제 분산 된 캐시가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="5b68a-137">캐시 된 항목은 앱이 실행 되 고 있는 서버의 앱 인스턴스에 의해 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="5b68a-138">분산 메모리 캐시는 다음과 같은 유용한 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="5b68a-139">개발 및 테스트 시나리오에서</span><span class="sxs-lookup"><span data-stu-id="5b68a-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="5b68a-140">프로덕션 환경에서 단일 서버를 사용 하는 경우 메모리 소비가 문제가 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="5b68a-141">분산 메모리 캐시를 구현 하면 캐시 된 데이터 저장소를 추상화 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="5b68a-142">이를 통해 여러 노드 또는 내결함성이 필요한 경우 나중에 진정한 분산 캐싱 솔루션을 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="5b68a-143">샘플 앱은 앱이의 개발 환경에서 실행 될 때 분산 메모리 캐시를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="5b68a-144">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="5b68a-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="5b68a-145">분산 SQL Server 캐시 구현 ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> )을 사용 하면 분산 캐시에서 SQL Server 데이터베이스를 백업 저장소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="5b68a-146">SQL Server 인스턴스에서 캐시 된 SQL Server 항목 테이블을 만들려면 도구를 사용할 수 있습니다 `sql-cache` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="5b68a-147">이 도구는 사용자가 지정한 이름 및 스키마를 사용 하 여 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="5b68a-148">명령을 실행 하 여 SQL Server에서 테이블을 만듭니다 `sql-cache create` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="5b68a-149">SQL Server 인스턴스 ( `Data Source` ), 데이터베이스 ( `Initial Catalog` ), 스키마 (예 `dbo` :) 및 테이블 이름 (예 `TestCache` :)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="5b68a-150">도구가 성공 했음을 나타내는 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="5b68a-151">도구에서 만든 테이블에는 `sql-cache` 다음 스키마가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="5b68a-153">앱은가 아닌 인스턴스를 사용 하 여 캐시 값을 조작 해야 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="5b68a-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="5b68a-154">샘플 앱은 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 다음과 같은 개발 이외의 환경에서을 구현 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="5b68a-155"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(및 필요에 따라 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 및 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> )는 일반적으로 소스 제어 외부에 저장 됩니다. 예를 들어, [Secret Manager](xref:security/app-secrets) 또는 appsettings *의appsettings.js*에 저장 됩니다 / *. 환경}. json* 파일).</span><span class="sxs-lookup"><span data-stu-id="5b68a-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="5b68a-156">연결 문자열에는 원본 제어 시스템에서 유지 되어야 하는 자격 증명이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="5b68a-157">분산 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5b68a-157">Distributed Redis Cache</span></span>

<span data-ttu-id="5b68a-158">[Redis](https://redis.io/) 는 분산 캐시로 자주 사용 되는 오픈 소스 메모리 내 데이터 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="5b68a-159">Redis를 로컬로 사용할 수 있으며, Azure에서 호스트 되는 ASP.NET Core 앱에 대 한 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) 를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-159">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="5b68a-160">앱은 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> 다음과 같은 개발 이외의 환경에서 인스턴스 ()를 사용 하 여 캐시 구현을 구성 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="5b68a-161">로컬 컴퓨터에 Redis를 설치 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-161">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="5b68a-162">[Chocolatey Redis 패키지](https://chocolatey.org/packages/redis-64/)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-162">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="5b68a-163">`redis-server`명령 프롬프트에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-163">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="5b68a-164">Distributed NCache Cache</span><span class="sxs-lookup"><span data-stu-id="5b68a-164">Distributed NCache Cache</span></span>

<span data-ttu-id="5b68a-165">[NCache](https://github.com/Alachisoft/NCache) 는 .NET 및 .net Core에서 기본적으로 개발 된 오픈 소스 메모리 내 분산 캐시입니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-165">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="5b68a-166">NCache는 로컬에서 작동 하 고 Azure 또는 다른 호스팅 플랫폼에서 실행 되는 ASP.NET Core 앱에 대 한 분산 캐시 클러스터로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-166">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="5b68a-167">로컬 컴퓨터에 NCache를 설치 및 구성 하려면 [NCache 시작 가이드 For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="5b68a-167">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="5b68a-168">NCache를 구성 하려면:</span><span class="sxs-lookup"><span data-stu-id="5b68a-168">To configure NCache:</span></span>

1. <span data-ttu-id="5b68a-169">[NCache 오픈 소스 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-169">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="5b68a-170">[Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)에서 캐시 클러스터를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-170">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="5b68a-171">다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-171">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="5b68a-172">분산 캐시 사용</span><span class="sxs-lookup"><span data-stu-id="5b68a-172">Use the distributed cache</span></span>

<span data-ttu-id="5b68a-173">인터페이스를 사용 하려면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 앱의 모든 생성자에서의 인스턴스를 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-173">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="5b68a-174">인스턴스는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-174">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5b68a-175">샘플 앱이 시작 되 면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 가에 삽입 됩니다 `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-175">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="5b68a-176">현재 시간은를 사용 하 여 캐시 됩니다. <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> 자세한 내용은 [제네릭 호스트: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5b68a-176">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="5b68a-177">샘플 앱은 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` 인덱스 페이지에서 사용 하기 위해를에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-177">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="5b68a-178">인덱스 페이지가 로드 될 때마다에서 캐시 된 시간에 대 한 캐시를 확인 합니다 `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-178">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="5b68a-179">캐시 된 시간이 만료 되지 않은 경우에는 시간이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-179">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="5b68a-180">캐시 된 시간에 마지막으로 액세스 한 후 20 초가 경과한 경우 (이 페이지가 마지막으로 로드 된 시간) 페이지에는 *캐시 된 시간 만료*가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-180">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="5b68a-181">캐시 된 시간 **다시 설정** 단추를 선택 하 여 캐시 된 시간을 현재 시간으로 즉시 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-181">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="5b68a-182">단추는 `OnPostResetCachedTime` 처리기 메서드를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-182">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="5b68a-183">인스턴스에 대해 Singleton 또는 범위 수명을 사용할 필요는 없습니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (최소한 기본 제공 구현의 경우).</span><span class="sxs-lookup"><span data-stu-id="5b68a-183">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="5b68a-184"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>DI를 사용 하는 대신 인스턴스를 만들 수도 있지만, 코드에서 인스턴스를 만들면 코드를 테스트 하는 것이 어렵고 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 위반 하 게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-184">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="5b68a-185">권장 사항</span><span class="sxs-lookup"><span data-stu-id="5b68a-185">Recommendations</span></span>

<span data-ttu-id="5b68a-186"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>앱에 가장 적합 한 구현을 결정할 때 다음 사항을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-186">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="5b68a-187">기존 인프라</span><span class="sxs-lookup"><span data-stu-id="5b68a-187">Existing infrastructure</span></span>
* <span data-ttu-id="5b68a-188">성능 요구 사항</span><span class="sxs-lookup"><span data-stu-id="5b68a-188">Performance requirements</span></span>
* <span data-ttu-id="5b68a-189">비용</span><span class="sxs-lookup"><span data-stu-id="5b68a-189">Cost</span></span>
* <span data-ttu-id="5b68a-190">팀 환경</span><span class="sxs-lookup"><span data-stu-id="5b68a-190">Team experience</span></span>

<span data-ttu-id="5b68a-191">캐싱 솔루션은 일반적으로 메모리 내 저장소를 사용 하 여 캐시 된 데이터를 신속 하 게 검색 하지만 메모리는 제한 된 리소스 이며 확장 하는 데 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-191">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="5b68a-192">일반적으로 사용 되는 데이터를 캐시에만 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-192">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="5b68a-193">일반적으로 Redis cache는 SQL Server 캐시 보다 높은 처리량과 짧은 대기 시간을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-193">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="5b68a-194">그러나 일반적으로 벤치마킹은 캐싱 전략의 성능 특성을 결정 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-194">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="5b68a-195">SQL Server를 분산 캐시 백업 저장소로 사용 하는 경우 캐시에 대해 동일한 데이터베이스를 사용 하 고 앱의 일반 데이터 저장 및 검색을 사용 하면 두 성능에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-195">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="5b68a-196">분산 캐시 백업 저장소에 전용 SQL Server 인스턴스를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-196">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5b68a-197">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="5b68a-197">Additional resources</span></span>

* [<span data-ttu-id="5b68a-198">Azure의 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5b68a-198">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="5b68a-199">Azure의 SQL Database</span><span class="sxs-lookup"><span data-stu-id="5b68a-199">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="5b68a-200">[웹 팜의 ASP.NET Core IDistributedCache Provider For NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="5b68a-200">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="5b68a-201">분산 캐시는 여러 앱 서버에서 공유 하는 캐시로, 일반적으로 액세스 하는 앱 서버에 외부 서비스로 유지 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-201">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="5b68a-202">분산 캐시는 특히 앱이 클라우드 서비스 또는 서버 팜에서 호스트 되는 경우 ASP.NET Core 앱의 성능과 확장성을 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-202">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="5b68a-203">분산 캐시는 개별 앱 서버에 캐시 된 데이터가 저장 되는 다른 캐싱 시나리오에 비해 여러 가지 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-203">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="5b68a-204">캐시 된 데이터를 배포 하는 경우 데이터는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-204">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="5b68a-205">는 여러 서버에 대 한 여러 요청 *에서 일관 되* 고 일관 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-205">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="5b68a-206">서버를 다시 시작 하 고 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-206">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="5b68a-207">로컬 메모리를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-207">Doesn't use local memory.</span></span>

<span data-ttu-id="5b68a-208">분산 캐시 구성은 구현 별로 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-208">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="5b68a-209">이 문서에서는 SQL Server 및 Redis 분산 캐시를 구성 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-209">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="5b68a-210">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))와 같은 타사 구현도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-210">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="5b68a-211">선택한 구현과 관계 없이 앱은 인터페이스를 사용 하 여 캐시와 상호 작용 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="5b68a-211">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="5b68a-212">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5b68a-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5b68a-213">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="5b68a-213">Prerequisites</span></span>

<span data-ttu-id="5b68a-214">SQL Server 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하거나 패키지 참조를 [패키지에 추가 합니다.](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="5b68a-214">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="5b68a-215">Redis 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 패키지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-215">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="5b68a-216">Redis 패키지는 패키지에 포함 되지 `Microsoft.AspNetCore.App` 않으므로 프로젝트 파일에서 개별적으로 Redis 패키지를 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-216">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="5b68a-217">NCache 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 에 추가 합니다..</span><span class="sxs-lookup"><span data-stu-id="5b68a-217">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="5b68a-218">NCache 패키지는 패키지에 포함 되지 `Microsoft.AspNetCore.App` 않으므로 프로젝트 파일에서 개별적으로 NCache 패키지를 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-218">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="5b68a-219">IDistributedCache 인터페이스</span><span class="sxs-lookup"><span data-stu-id="5b68a-219">IDistributedCache interface</span></span>

<span data-ttu-id="5b68a-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>인터페이스는 분산 캐시 구현에서 항목을 조작 하는 다음과 같은 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-220">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="5b68a-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : 문자열 키를 허용 하 고 `byte[]` 캐시 된 항목을 캐시에 있는 경우 배열로 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="5b68a-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` 문자열 키를 사용 하 여 캐시에 항목 (배열)을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="5b68a-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : 해당 키에 따라 캐시에서 항목을 새로 고쳐 슬라이딩 만료 시간 제한 (있는 경우)을 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="5b68a-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : 문자열 키를 기준으로 캐시 항목을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="5b68a-225">분산 캐싱 서비스 설정</span><span class="sxs-lookup"><span data-stu-id="5b68a-225">Establish distributed caching services</span></span>

<span data-ttu-id="5b68a-226">에서의 구현을 등록 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-226">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5b68a-227">이 항목에서 설명 하는 프레임 워크 제공 구현에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-227">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="5b68a-228">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="5b68a-228">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="5b68a-229">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="5b68a-229">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="5b68a-230">Distributed Redis cache</span><span class="sxs-lookup"><span data-stu-id="5b68a-230">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="5b68a-231">Distributed NCache cache</span><span class="sxs-lookup"><span data-stu-id="5b68a-231">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="5b68a-232">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="5b68a-232">Distributed Memory Cache</span></span>

<span data-ttu-id="5b68a-233">분산 메모리 캐시 ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> )는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 메모리에 항목을 저장 하는 프레임 워크에서 제공 하는 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-233">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="5b68a-234">분산 메모리 캐시는 실제 분산 된 캐시가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-234">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="5b68a-235">캐시 된 항목은 앱이 실행 되 고 있는 서버의 앱 인스턴스에 의해 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-235">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="5b68a-236">분산 메모리 캐시는 다음과 같은 유용한 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-236">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="5b68a-237">개발 및 테스트 시나리오에서</span><span class="sxs-lookup"><span data-stu-id="5b68a-237">In development and testing scenarios.</span></span>
* <span data-ttu-id="5b68a-238">프로덕션 환경에서 단일 서버를 사용 하는 경우 메모리 소비가 문제가 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-238">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="5b68a-239">분산 메모리 캐시를 구현 하면 캐시 된 데이터 저장소를 추상화 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-239">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="5b68a-240">이를 통해 여러 노드 또는 내결함성이 필요한 경우 나중에 진정한 분산 캐싱 솔루션을 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-240">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="5b68a-241">샘플 앱은 앱이의 개발 환경에서 실행 될 때 분산 메모리 캐시를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-241">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="5b68a-242">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="5b68a-242">Distributed SQL Server Cache</span></span>

<span data-ttu-id="5b68a-243">분산 SQL Server 캐시 구현 ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> )을 사용 하면 분산 캐시에서 SQL Server 데이터베이스를 백업 저장소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-243">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="5b68a-244">SQL Server 인스턴스에서 캐시 된 SQL Server 항목 테이블을 만들려면 도구를 사용할 수 있습니다 `sql-cache` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-244">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="5b68a-245">이 도구는 사용자가 지정한 이름 및 스키마를 사용 하 여 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-245">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="5b68a-246">명령을 실행 하 여 SQL Server에서 테이블을 만듭니다 `sql-cache create` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-246">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="5b68a-247">SQL Server 인스턴스 ( `Data Source` ), 데이터베이스 ( `Initial Catalog` ), 스키마 (예 `dbo` :) 및 테이블 이름 (예 `TestCache` :)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-247">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="5b68a-248">도구가 성공 했음을 나타내는 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-248">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="5b68a-249">도구에서 만든 테이블에는 `sql-cache` 다음 스키마가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-249">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="5b68a-251">앱은가 아닌 인스턴스를 사용 하 여 캐시 값을 조작 해야 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="5b68a-251">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="5b68a-252">샘플 앱은 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 다음과 같은 개발 이외의 환경에서을 구현 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-252">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="5b68a-253"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(및 필요에 따라 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 및 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> )는 일반적으로 소스 제어 외부에 저장 됩니다. 예를 들어, [Secret Manager](xref:security/app-secrets) 또는 appsettings *의appsettings.js*에 저장 됩니다 / *. 환경}. json* 파일).</span><span class="sxs-lookup"><span data-stu-id="5b68a-253">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="5b68a-254">연결 문자열에는 원본 제어 시스템에서 유지 되어야 하는 자격 증명이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-254">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="5b68a-255">분산 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5b68a-255">Distributed Redis Cache</span></span>

<span data-ttu-id="5b68a-256">[Redis](https://redis.io/) 는 분산 캐시로 자주 사용 되는 오픈 소스 메모리 내 데이터 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-256">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="5b68a-257">Redis를 로컬로 사용할 수 있으며, Azure에서 호스트 되는 ASP.NET Core 앱에 대 한 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) 를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-257">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="5b68a-258">앱은 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> 다음과 같은 개발 이외의 환경에서 인스턴스 ()를 사용 하 여 캐시 구현을 구성 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-258">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="5b68a-259">로컬 컴퓨터에 Redis를 설치 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-259">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="5b68a-260">[Chocolatey Redis 패키지](https://chocolatey.org/packages/redis-64/)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-260">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="5b68a-261">`redis-server`명령 프롬프트에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-261">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="5b68a-262">Distributed NCache Cache</span><span class="sxs-lookup"><span data-stu-id="5b68a-262">Distributed NCache Cache</span></span>

<span data-ttu-id="5b68a-263">[NCache](https://github.com/Alachisoft/NCache) 는 .NET 및 .net Core에서 기본적으로 개발 된 오픈 소스 메모리 내 분산 캐시입니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-263">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="5b68a-264">NCache는 로컬에서 작동 하 고 Azure 또는 다른 호스팅 플랫폼에서 실행 되는 ASP.NET Core 앱에 대 한 분산 캐시 클러스터로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-264">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="5b68a-265">로컬 컴퓨터에 NCache를 설치 및 구성 하려면 [NCache 시작 가이드 For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="5b68a-265">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="5b68a-266">NCache를 구성 하려면:</span><span class="sxs-lookup"><span data-stu-id="5b68a-266">To configure NCache:</span></span>

1. <span data-ttu-id="5b68a-267">[NCache 오픈 소스 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-267">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="5b68a-268">[Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)에서 캐시 클러스터를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-268">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="5b68a-269">다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-269">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="5b68a-270">분산 캐시 사용</span><span class="sxs-lookup"><span data-stu-id="5b68a-270">Use the distributed cache</span></span>

<span data-ttu-id="5b68a-271">인터페이스를 사용 하려면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 앱의 모든 생성자에서의 인스턴스를 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-271">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="5b68a-272">인스턴스는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-272">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5b68a-273">샘플 앱이 시작 되 면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 가에 삽입 됩니다 `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-273">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="5b68a-274">현재 시간은를 사용 하 여 캐시 됩니다. <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> 자세한 내용은 [웹 호스트: IApplicationLifetime 인터페이스](xref:fundamentals/host/web-host#iapplicationlifetime-interface)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5b68a-274">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="5b68a-275">샘플 앱은 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` 인덱스 페이지에서 사용 하기 위해를에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-275">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="5b68a-276">인덱스 페이지가 로드 될 때마다에서 캐시 된 시간에 대 한 캐시를 확인 합니다 `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-276">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="5b68a-277">캐시 된 시간이 만료 되지 않은 경우에는 시간이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-277">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="5b68a-278">캐시 된 시간에 마지막으로 액세스 한 후 20 초가 경과한 경우 (이 페이지가 마지막으로 로드 된 시간) 페이지에는 *캐시 된 시간 만료*가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-278">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="5b68a-279">캐시 된 시간 **다시 설정** 단추를 선택 하 여 캐시 된 시간을 현재 시간으로 즉시 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-279">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="5b68a-280">단추는 `OnPostResetCachedTime` 처리기 메서드를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-280">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="5b68a-281">인스턴스에 대해 Singleton 또는 범위 수명을 사용할 필요는 없습니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (최소한 기본 제공 구현의 경우).</span><span class="sxs-lookup"><span data-stu-id="5b68a-281">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="5b68a-282"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>DI를 사용 하는 대신 인스턴스를 만들 수도 있지만, 코드에서 인스턴스를 만들면 코드를 테스트 하는 것이 어렵고 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 위반 하 게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-282">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="5b68a-283">권장 사항</span><span class="sxs-lookup"><span data-stu-id="5b68a-283">Recommendations</span></span>

<span data-ttu-id="5b68a-284"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>앱에 가장 적합 한 구현을 결정할 때 다음 사항을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-284">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="5b68a-285">기존 인프라</span><span class="sxs-lookup"><span data-stu-id="5b68a-285">Existing infrastructure</span></span>
* <span data-ttu-id="5b68a-286">성능 요구 사항</span><span class="sxs-lookup"><span data-stu-id="5b68a-286">Performance requirements</span></span>
* <span data-ttu-id="5b68a-287">비용</span><span class="sxs-lookup"><span data-stu-id="5b68a-287">Cost</span></span>
* <span data-ttu-id="5b68a-288">팀 환경</span><span class="sxs-lookup"><span data-stu-id="5b68a-288">Team experience</span></span>

<span data-ttu-id="5b68a-289">캐싱 솔루션은 일반적으로 메모리 내 저장소를 사용 하 여 캐시 된 데이터를 신속 하 게 검색 하지만 메모리는 제한 된 리소스 이며 확장 하는 데 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-289">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="5b68a-290">일반적으로 사용 되는 데이터를 캐시에만 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-290">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="5b68a-291">일반적으로 Redis cache는 SQL Server 캐시 보다 높은 처리량과 짧은 대기 시간을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-291">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="5b68a-292">그러나 일반적으로 벤치마킹은 캐싱 전략의 성능 특성을 결정 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-292">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="5b68a-293">SQL Server를 분산 캐시 백업 저장소로 사용 하는 경우 캐시에 대해 동일한 데이터베이스를 사용 하 고 앱의 일반 데이터 저장 및 검색을 사용 하면 두 성능에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-293">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="5b68a-294">분산 캐시 백업 저장소에 전용 SQL Server 인스턴스를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-294">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5b68a-295">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="5b68a-295">Additional resources</span></span>

* [<span data-ttu-id="5b68a-296">Azure의 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5b68a-296">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="5b68a-297">Azure의 SQL Database</span><span class="sxs-lookup"><span data-stu-id="5b68a-297">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="5b68a-298">[웹 팜의 ASP.NET Core IDistributedCache Provider For NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="5b68a-298">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5b68a-299">분산 캐시는 여러 앱 서버에서 공유 하는 캐시로, 일반적으로 액세스 하는 앱 서버에 외부 서비스로 유지 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-299">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="5b68a-300">분산 캐시는 특히 앱이 클라우드 서비스 또는 서버 팜에서 호스트 되는 경우 ASP.NET Core 앱의 성능과 확장성을 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-300">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="5b68a-301">분산 캐시는 개별 앱 서버에 캐시 된 데이터가 저장 되는 다른 캐싱 시나리오에 비해 여러 가지 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-301">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="5b68a-302">캐시 된 데이터를 배포 하는 경우 데이터는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-302">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="5b68a-303">는 여러 서버에 대 한 여러 요청 *에서 일관 되* 고 일관 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-303">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="5b68a-304">서버를 다시 시작 하 고 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-304">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="5b68a-305">로컬 메모리를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-305">Doesn't use local memory.</span></span>

<span data-ttu-id="5b68a-306">분산 캐시 구성은 구현 별로 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-306">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="5b68a-307">이 문서에서는 SQL Server 및 Redis 분산 캐시를 구성 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-307">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="5b68a-308">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))와 같은 타사 구현도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-308">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="5b68a-309">선택한 구현과 관계 없이 앱은 인터페이스를 사용 하 여 캐시와 상호 작용 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="5b68a-309">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="5b68a-310">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5b68a-310">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5b68a-311">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="5b68a-311">Prerequisites</span></span>

<span data-ttu-id="5b68a-312">SQL Server 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하거나 패키지 참조를 [패키지에 추가 합니다.](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="5b68a-312">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="5b68a-313">Redis 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) 패키지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-313">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="5b68a-314">Redis 패키지는 패키지에 포함 되지 `Microsoft.AspNetCore.App` 않으므로 프로젝트 파일에서 개별적으로 Redis 패키지를 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-314">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="5b68a-315">NCache 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 에 추가 합니다..</span><span class="sxs-lookup"><span data-stu-id="5b68a-315">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="5b68a-316">NCache 패키지는 패키지에 포함 되지 `Microsoft.AspNetCore.App` 않으므로 프로젝트 파일에서 개별적으로 NCache 패키지를 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-316">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="5b68a-317">IDistributedCache 인터페이스</span><span class="sxs-lookup"><span data-stu-id="5b68a-317">IDistributedCache interface</span></span>

<span data-ttu-id="5b68a-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>인터페이스는 분산 캐시 구현에서 항목을 조작 하는 다음과 같은 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-318">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="5b68a-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : 문자열 키를 허용 하 고 `byte[]` 캐시 된 항목을 캐시에 있는 경우 배열로 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="5b68a-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` 문자열 키를 사용 하 여 캐시에 항목 (배열)을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="5b68a-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : 해당 키에 따라 캐시에서 항목을 새로 고쳐 슬라이딩 만료 시간 제한 (있는 경우)을 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="5b68a-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : 문자열 키를 기준으로 캐시 항목을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="5b68a-323">분산 캐싱 서비스 설정</span><span class="sxs-lookup"><span data-stu-id="5b68a-323">Establish distributed caching services</span></span>

<span data-ttu-id="5b68a-324">에서의 구현을 등록 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-324">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5b68a-325">이 항목에서 설명 하는 프레임 워크 제공 구현에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-325">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="5b68a-326">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="5b68a-326">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="5b68a-327">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="5b68a-327">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="5b68a-328">Distributed Redis cache</span><span class="sxs-lookup"><span data-stu-id="5b68a-328">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="5b68a-329">Distributed NCache cache</span><span class="sxs-lookup"><span data-stu-id="5b68a-329">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="5b68a-330">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="5b68a-330">Distributed Memory Cache</span></span>

<span data-ttu-id="5b68a-331">분산 메모리 캐시 ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> )는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 메모리에 항목을 저장 하는 프레임 워크에서 제공 하는 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-331">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="5b68a-332">분산 메모리 캐시는 실제 분산 된 캐시가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-332">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="5b68a-333">캐시 된 항목은 앱이 실행 되 고 있는 서버의 앱 인스턴스에 의해 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-333">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="5b68a-334">분산 메모리 캐시는 다음과 같은 유용한 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-334">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="5b68a-335">개발 및 테스트 시나리오에서</span><span class="sxs-lookup"><span data-stu-id="5b68a-335">In development and testing scenarios.</span></span>
* <span data-ttu-id="5b68a-336">프로덕션 환경에서 단일 서버를 사용 하는 경우 메모리 소비가 문제가 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-336">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="5b68a-337">분산 메모리 캐시를 구현 하면 캐시 된 데이터 저장소를 추상화 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-337">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="5b68a-338">이를 통해 여러 노드 또는 내결함성이 필요한 경우 나중에 진정한 분산 캐싱 솔루션을 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-338">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="5b68a-339">샘플 앱은 앱이의 개발 환경에서 실행 될 때 분산 메모리 캐시를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-339">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="5b68a-340">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="5b68a-340">Distributed SQL Server Cache</span></span>

<span data-ttu-id="5b68a-341">분산 SQL Server 캐시 구현 ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> )을 사용 하면 분산 캐시에서 SQL Server 데이터베이스를 백업 저장소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-341">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="5b68a-342">SQL Server 인스턴스에서 캐시 된 SQL Server 항목 테이블을 만들려면 도구를 사용할 수 있습니다 `sql-cache` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-342">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="5b68a-343">이 도구는 사용자가 지정한 이름 및 스키마를 사용 하 여 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-343">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="5b68a-344">명령을 실행 하 여 SQL Server에서 테이블을 만듭니다 `sql-cache create` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-344">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="5b68a-345">SQL Server 인스턴스 ( `Data Source` ), 데이터베이스 ( `Initial Catalog` ), 스키마 (예 `dbo` :) 및 테이블 이름 (예 `TestCache` :)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-345">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="5b68a-346">도구가 성공 했음을 나타내는 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-346">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="5b68a-347">도구에서 만든 테이블에는 `sql-cache` 다음 스키마가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-347">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="5b68a-349">앱은가 아닌 인스턴스를 사용 하 여 캐시 값을 조작 해야 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="5b68a-349">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="5b68a-350">샘플 앱은 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 다음과 같은 개발 이외의 환경에서을 구현 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-350">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="5b68a-351"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(및 필요에 따라 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 및 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> )는 일반적으로 소스 제어 외부에 저장 됩니다. 예를 들어, [Secret Manager](xref:security/app-secrets) 또는 appsettings *의appsettings.js*에 저장 됩니다 / *. 환경}. json* 파일).</span><span class="sxs-lookup"><span data-stu-id="5b68a-351">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="5b68a-352">연결 문자열에는 원본 제어 시스템에서 유지 되어야 하는 자격 증명이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-352">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="5b68a-353">분산 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5b68a-353">Distributed Redis Cache</span></span>

<span data-ttu-id="5b68a-354">[Redis](https://redis.io/) 는 분산 캐시로 자주 사용 되는 오픈 소스 메모리 내 데이터 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-354">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="5b68a-355">Redis를 로컬로 사용할 수 있으며, Azure에서 호스트 되는 ASP.NET Core 앱에 대 한 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) 를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-355">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="5b68a-356">앱은 인스턴스 ()를 사용 하 여 캐시 구현을 구성 합니다 <xref:Microsoft.Extensions.Caching.Redis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> .</span><span class="sxs-lookup"><span data-stu-id="5b68a-356">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="5b68a-357">로컬 컴퓨터에 Redis를 설치 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-357">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="5b68a-358">[Chocolatey Redis 패키지](https://chocolatey.org/packages/redis-64/)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-358">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="5b68a-359">`redis-server`명령 프롬프트에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-359">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="5b68a-360">Distributed NCache Cache</span><span class="sxs-lookup"><span data-stu-id="5b68a-360">Distributed NCache Cache</span></span>

<span data-ttu-id="5b68a-361">[NCache](https://github.com/Alachisoft/NCache) 는 .NET 및 .net Core에서 기본적으로 개발 된 오픈 소스 메모리 내 분산 캐시입니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-361">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="5b68a-362">NCache는 로컬에서 작동 하 고 Azure 또는 다른 호스팅 플랫폼에서 실행 되는 ASP.NET Core 앱에 대 한 분산 캐시 클러스터로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-362">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="5b68a-363">로컬 컴퓨터에 NCache를 설치 및 구성 하려면 [NCache 시작 가이드 For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)를 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="5b68a-363">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="5b68a-364">NCache를 구성 하려면:</span><span class="sxs-lookup"><span data-stu-id="5b68a-364">To configure NCache:</span></span>

1. <span data-ttu-id="5b68a-365">[NCache 오픈 소스 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-365">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="5b68a-366">[Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)에서 캐시 클러스터를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-366">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="5b68a-367">다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-367">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="5b68a-368">분산 캐시 사용</span><span class="sxs-lookup"><span data-stu-id="5b68a-368">Use the distributed cache</span></span>

<span data-ttu-id="5b68a-369">인터페이스를 사용 하려면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 앱의 모든 생성자에서의 인스턴스를 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-369">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="5b68a-370">인스턴스는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-370">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5b68a-371">샘플 앱이 시작 되 면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 가에 삽입 됩니다 `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-371">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="5b68a-372">현재 시간은를 사용 하 여 캐시 됩니다. <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> 자세한 내용은 [웹 호스트: IApplicationLifetime 인터페이스](xref:fundamentals/host/web-host#iapplicationlifetime-interface)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5b68a-372">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="5b68a-373">샘플 앱은 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` 인덱스 페이지에서 사용 하기 위해를에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-373">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="5b68a-374">인덱스 페이지가 로드 될 때마다에서 캐시 된 시간에 대 한 캐시를 확인 합니다 `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="5b68a-374">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="5b68a-375">캐시 된 시간이 만료 되지 않은 경우에는 시간이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-375">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="5b68a-376">캐시 된 시간에 마지막으로 액세스 한 후 20 초가 경과한 경우 (이 페이지가 마지막으로 로드 된 시간) 페이지에는 *캐시 된 시간 만료*가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-376">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="5b68a-377">캐시 된 시간 **다시 설정** 단추를 선택 하 여 캐시 된 시간을 현재 시간으로 즉시 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-377">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="5b68a-378">단추는 `OnPostResetCachedTime` 처리기 메서드를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-378">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="5b68a-379">인스턴스에 대해 Singleton 또는 범위 수명을 사용할 필요는 없습니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (최소한 기본 제공 구현의 경우).</span><span class="sxs-lookup"><span data-stu-id="5b68a-379">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="5b68a-380"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>DI를 사용 하는 대신 인스턴스를 만들 수도 있지만, 코드에서 인스턴스를 만들면 코드를 테스트 하는 것이 어렵고 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 위반 하 게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-380">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="5b68a-381">권장 사항</span><span class="sxs-lookup"><span data-stu-id="5b68a-381">Recommendations</span></span>

<span data-ttu-id="5b68a-382"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>앱에 가장 적합 한 구현을 결정할 때 다음 사항을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-382">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="5b68a-383">기존 인프라</span><span class="sxs-lookup"><span data-stu-id="5b68a-383">Existing infrastructure</span></span>
* <span data-ttu-id="5b68a-384">성능 요구 사항</span><span class="sxs-lookup"><span data-stu-id="5b68a-384">Performance requirements</span></span>
* <span data-ttu-id="5b68a-385">비용</span><span class="sxs-lookup"><span data-stu-id="5b68a-385">Cost</span></span>
* <span data-ttu-id="5b68a-386">팀 환경</span><span class="sxs-lookup"><span data-stu-id="5b68a-386">Team experience</span></span>

<span data-ttu-id="5b68a-387">캐싱 솔루션은 일반적으로 메모리 내 저장소를 사용 하 여 캐시 된 데이터를 신속 하 게 검색 하지만 메모리는 제한 된 리소스 이며 확장 하는 데 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-387">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="5b68a-388">일반적으로 사용 되는 데이터를 캐시에만 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-388">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="5b68a-389">일반적으로 Redis cache는 SQL Server 캐시 보다 높은 처리량과 짧은 대기 시간을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-389">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="5b68a-390">그러나 일반적으로 벤치마킹은 캐싱 전략의 성능 특성을 결정 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-390">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="5b68a-391">SQL Server를 분산 캐시 백업 저장소로 사용 하는 경우 캐시에 대해 동일한 데이터베이스를 사용 하 고 앱의 일반 데이터 저장 및 검색을 사용 하면 두 성능에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-391">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="5b68a-392">분산 캐시 백업 저장소에 전용 SQL Server 인스턴스를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5b68a-392">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5b68a-393">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="5b68a-393">Additional resources</span></span>

* [<span data-ttu-id="5b68a-394">Azure의 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5b68a-394">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="5b68a-395">Azure의 SQL Database</span><span class="sxs-lookup"><span data-stu-id="5b68a-395">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="5b68a-396">[웹 팜의 ASP.NET Core IDistributedCache Provider For NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="5b68a-396">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
 