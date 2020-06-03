---
<span data-ttu-id="35ef5-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="35ef5-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="35ef5-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="35ef5-102">'Blazor'</span></span>
- <span data-ttu-id="35ef5-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="35ef5-103">'Identity'</span></span>
- <span data-ttu-id="35ef5-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="35ef5-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="35ef5-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="35ef5-105">'Razor'</span></span>
- <span data-ttu-id="35ef5-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="35ef5-106">'SignalR' uid:</span></span> 

---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="35ef5-107">ASP.NET Core의 상태 검사</span><span class="sxs-lookup"><span data-stu-id="35ef5-107">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="35ef5-108">작성자: [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="35ef5-108">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="35ef5-109">ASP.NET Core는 앱 인프라 구성 요소의 상태를 보고하기 위해 상태 검사 미들웨어와 라이브러리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-109">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="35ef5-110">상태 검사는 앱에 의해 HTTP 엔드포인트로서 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-110">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="35ef5-111">상태 검사 엔드포인트는 다양한 실시간 모니터링 시나리오에 맞게 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-111">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="35ef5-112">상태 프로브는 컨테이너 오케스트레이터와 부하 분산 장치가 앱 상태를 검사하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-112">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="35ef5-113">예를 들어, 컨테이너 오케스트레이터는 롤링 배포를 중지하거나 컨테이너를 다시 시작하여 실패한 상태 검사에 응답할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-113">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="35ef5-114">부하 분산 장치는 장애가 발생한 상태 검사 인스턴스로부터 트래픽을 다른 곳으로 라우팅하여 비정상 앱에 대응할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-114">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="35ef5-115">메모리, 디스크 및 기타 물리적 서버 리소스의 사용이 정상적인 상태인지 모니터링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-115">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="35ef5-116">상태 검사는 데이터베이스 및 외부 서비스 엔드포인트와 같은 앱 종속성을 테스트하여 가용성 및 정상 작동 여부를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-116">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="35ef5-117">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="35ef5-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="35ef5-118">샘플 앱에는 이 항목에 설명된 시나리오의 예가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-118">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="35ef5-119">지정된 시나리오에 대해 샘플 앱을 실행하려면 명령 셸의 프로젝트 폴더에서 [dotnet run](/dotnet/core/tools/dotnet-run) 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-119">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="35ef5-120">샘플 앱의 사용 방법에 대한 자세한 내용은 샘플 앱의 *README.md* 파일과 이 항목의 시나리오 설명을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="35ef5-120">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="35ef5-121">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="35ef5-121">Prerequisites</span></span>

<span data-ttu-id="35ef5-122">상태 검사는 일반적으로 외부 모니터링 서비스 또는 컨테이너 오케스트레이터와 함께 사용되어 앱 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-122">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="35ef5-123">앱에 상태 검사를 추가하기 전에 사용할 모니터링 시스템을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-123">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="35ef5-124">모니터링 시스템은 어떤 유형의 상태 검사를 만들고 그 엔드포인트를 구성하는 방법을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-124">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="35ef5-125">[Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) 패키지는 ASP.NET Core 앱에 대해 암시적으로 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-125">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="35ef5-126">Entity Framework Core를 사용하여 상태 검사를 수행하려면 [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) 패키지에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-126">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="35ef5-127">샘플 앱은 여러 시나리오의 상태 검사를 보여주는 시작 코드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-127">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="35ef5-128">[데이터베이스 프로브](#database-probe) 시나리오는 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)를 사용하여 데이터베이스 연결의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-128">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="35ef5-129">[DbContext 프로브](#entity-framework-core-dbcontext-probe) 시나리오는 EF Core `DbContext`를 사용하여 데이터베이스를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-129">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="35ef5-130">데이터베이스 시나리오를 탐색하려면 샘플 앱:</span><span class="sxs-lookup"><span data-stu-id="35ef5-130">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="35ef5-131">데이터베이스를 만들고 *appsettings.json* 파일에서 연결 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-131">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="35ef5-132">해당 프로젝트 파일에 다음 패키지 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-132">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="35ef5-133">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="35ef5-133">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="35ef5-134">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="35ef5-134">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="35ef5-135">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-135">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="35ef5-136">다른 상태 검사 시나리오는 관리 포트에 대해 상태 검사를 필터링하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-136">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="35ef5-137">샘플 앱을 사용하려면 관리 URL과 관리 포트가 포함된 *Properties/launchSettings.json*  파일을 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-137">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="35ef5-138">자세한 내용은 [포트별 필터링](#filter-by-port) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="35ef5-138">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="35ef5-139">기본 상태 프로브</span><span class="sxs-lookup"><span data-stu-id="35ef5-139">Basic health probe</span></span>

<span data-ttu-id="35ef5-140">많은 앱의 경우, 요청을 처리할 수 있는 앱의 가용성(*활동성*)을 보고하는 기본 상태 검사 구성으로 앱 상태를 충분히 파악할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-140">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="35ef5-141">기본 구성은 상태 검사 서비스를 등록하고 상태 검사 미들웨어를 호출하여 URL 엔드포인트에서 상태 응답을 사용하여 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-141">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="35ef5-142">기본적으로 특정 종속성이나 하위 시스템을 테스트하기 위해 특정 상태 검사가 등록되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-142">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="35ef5-143">상태 엔드포인트 URL에서 응답할 수 있는 경우 앱 상태가 좋은 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-143">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="35ef5-144">기본 응답 기록기는 상태(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)를 일반 텍스트 응답으로 다시 클라이언트에 기록하여 [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 또는 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 상태를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-144">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="35ef5-145">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-145">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="35ef5-146">`Startup.Configure`에서 `MapHealthChecks`를 호출하여 상태 검사 엔드포인트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-146">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="35ef5-147">샘플 앱에서 상태 검사 엔드포인트는 `/health`(*BasicStartup.cs*)에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-147">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

<span data-ttu-id="35ef5-148">샘플 앱을 사용하여 기본 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-148">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="35ef5-149">Docker 예</span><span class="sxs-lookup"><span data-stu-id="35ef5-149">Docker example</span></span>

<span data-ttu-id="35ef5-150">[Docker](xref:host-and-deploy/docker/index)는 기본 상태 검사 구성을 사용하는 앱의 상태를 검사하는 데 사용할 수 있는 기본 제공 `HEALTHCHECK` 지시문을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-150">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="35ef5-151">상태 검사 만들기</span><span class="sxs-lookup"><span data-stu-id="35ef5-151">Create health checks</span></span>

<span data-ttu-id="35ef5-152">상태 검사는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 인터페이스를 구현하여 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-152">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="35ef5-153"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> 메서드는 상태를 `Healthy`, `Degraded` 또는 `Unhealthy`로 나타내는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-153">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="35ef5-154">결과는 구성 가능한 상태 코드가 있는 일반 텍스트 응답으로 기록됩니다(구성은 [상태 검사 옵션](#health-check-options) 섹션에 설명되어 있음).</span><span class="sxs-lookup"><span data-stu-id="35ef5-154">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="35ef5-155"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>는 선택 사항인 키-값 쌍을 반환할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-155"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="35ef5-156">다음 `ExampleHealthCheck` 클래스는 상태 검사의 레이아웃을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-156">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="35ef5-157">상태 검사 논리는 `CheckHealthAsync` 메서드에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-157">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="35ef5-158">다음 예제에서는 더미 변수 `healthCheckResultHealthy`를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-158">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="35ef5-159">`healthCheckResultHealthy` 값이 `false`로 설정되면 [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 상태가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-159">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a><span data-ttu-id="35ef5-160">상태 검사 서비스 등록</span><span class="sxs-lookup"><span data-stu-id="35ef5-160">Register health check services</span></span>

<span data-ttu-id="35ef5-161">`ExampleHealthCheck` 형식은 `Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>으로 상태 검사 서비스에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-161">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="35ef5-162">다음 예제에 표시된 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 오버로드는 상태 검사에서 오류가 보고되면 보고하도록 오류 상태(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-162">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="35ef5-163">오류 상태가 `null`(기본값)로 설정되면 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)가 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-163">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="35ef5-164">이 오버로드는 라이브러리 작성자에게 유용한 시나리오입니다. 이 라이브러리에서는 상태 검사 구현이 설정을 준수하는 경우 상태 검사 실패가 발생할 때 라이브러리가 나타내는 오류 상태는 앱에 의해 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-164">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="35ef5-165">*태그*를 사용하여 상태 검사를 필터링할 수 있습니다([필터 상태 검사](#filter-health-checks) 섹션에 자세히 설명되어 있음).</span><span class="sxs-lookup"><span data-stu-id="35ef5-165">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="35ef5-166"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>는 람다 함수를 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-166"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="35ef5-167">다음 예제에서 상태 검사 이름은 `Example`로 지정되고 검사는 항상 정상 상태를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-167">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="35ef5-168"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*>를 호출하여 상태 검사 구현에 인수를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-168">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="35ef5-169">다음 예제에서 `TestHealthCheckWithArgs`는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>가 호출될 때 사용할 정수 및 문자열을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-169">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

<span data-ttu-id="35ef5-170">`TestHealthCheckWithArgs`는 구현에 전달된 정수 및 문자열로 `AddTypeActivatedCheck`를 호출하여 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-170">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="35ef5-171">상태 검사 라우팅 사용</span><span class="sxs-lookup"><span data-stu-id="35ef5-171">Use Health Checks Routing</span></span>

<span data-ttu-id="35ef5-172">`Startup.Configure`에서 엔드포인트 URL 또는 상대 경로를 사용하여 엔드포인트 작성기에 `MapHealthChecks`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-172">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="35ef5-173">호스트 필요</span><span class="sxs-lookup"><span data-stu-id="35ef5-173">Require host</span></span>

<span data-ttu-id="35ef5-174">`RequireHost`를 호출하여 상태 검사 엔드포인트에 대해 허용된 호스트를 하나 이상 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-174">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="35ef5-175">호스트는 punycode가 아닌 유니코드여야 하고 포트를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-175">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="35ef5-176">컬렉션을 제공하지 않으면 모든 호스트가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-176">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="35ef5-177">자세한 내용은 [포트별 필터링](#filter-by-port) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="35ef5-177">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="35ef5-178">권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="35ef5-178">Require authorization</span></span>

<span data-ttu-id="35ef5-179">`RequireAuthorization`을 호출하여 상태 검사 요청 엔드포인트에서 권한 부여 미들웨어를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-179">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="35ef5-180">`RequireAuthorization` 오버로드는 하나 이상의 권한 부여 정책을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-180">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="35ef5-181">정책을 제공하지 않으면 기본 권한 부여 정책이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-181">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="35ef5-182">원본 간 요청(CORS) 사용</span><span class="sxs-lookup"><span data-stu-id="35ef5-182">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="35ef5-183">브라우저에서 수동으로 상태 검사를 수행하는 것이 일반적이지는 않지만, 상태 검사 엔드포인트에 `RequireCors`를 호출하여 CORS 미들웨어를 사용하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-183">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="35ef5-184">`RequireCors` 오버로드는 CORS 정책 작성기 대리자(`CorsPolicyBuilder`) 또는 정책 이름을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-184">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="35ef5-185">정책을 제공하지 않으면 기본 CORS 정책이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-185">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="35ef5-186">자세한 내용은 <xref:security/cors>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="35ef5-186">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="35ef5-187">상태 검사 옵션</span><span class="sxs-lookup"><span data-stu-id="35ef5-187">Health check options</span></span>

<span data-ttu-id="35ef5-188"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>는 다음과 같은 상태 검사 동작을 사용자 지정할 수 있는 기회를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-188"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="35ef5-189">상태 검사 필터링</span><span class="sxs-lookup"><span data-stu-id="35ef5-189">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="35ef5-190">HTTP 상태 코드 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="35ef5-190">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="35ef5-191">캐시 헤더 표시 안 함</span><span class="sxs-lookup"><span data-stu-id="35ef5-191">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="35ef5-192">출력 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="35ef5-192">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="35ef5-193">상태 검사 필터링</span><span class="sxs-lookup"><span data-stu-id="35ef5-193">Filter health checks</span></span>

<span data-ttu-id="35ef5-194">기본적으로 상태 검사 미들웨어는 등록된 모든 상태 검사를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-194">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="35ef5-195">상태 검사 하위 세트를 실행하려면 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 옵션에 부울 값을 반환하는 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-195">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="35ef5-196">다음 예제에서는 상태 검사의 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 속성이 `foo_tag` 또는 `baz_tag`와 일치하는 경우에만 `true`가 반환되는 함수의 조건문에서 태그(`bar_tag`)에 의해 `Bar` 상태 검사가 필터링됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-196">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="35ef5-197">`Startup.ConfigureServices`의 경우</span><span class="sxs-lookup"><span data-stu-id="35ef5-197">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="35ef5-198">`Startup.Configure`에서 `Predicate`는 'Bar' 상태 검사를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-198">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="35ef5-199">Foo 및 Baz만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-199">Only Foo and Baz execute.:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="35ef5-200">HTTP 상태 코드 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="35ef5-200">Customize the HTTP status code</span></span>

<span data-ttu-id="35ef5-201"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>를 사용하여 상태를 HTTP 상태 코드에 매핑하는 작업을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-201">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="35ef5-202">다음 <xref:Microsoft.AspNetCore.Http.StatusCodes> 할당은 미들웨어에서 사용되는 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-202">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="35ef5-203">요구 사항에 맞게 상태 코드 값을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-203">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="35ef5-204">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="35ef5-204">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="35ef5-205">캐시 헤더 표시 안 함</span><span class="sxs-lookup"><span data-stu-id="35ef5-205">Suppress cache headers</span></span>

<span data-ttu-id="35ef5-206"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>는 응답 캐싱을 방지하기 위해 상태 검사 미들웨어가 HTTP 헤더를 프로브 응답에 추가할지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-206"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="35ef5-207">값이 `false`(기본값)인 경우 미들웨어는 응답 캐싱을 방지하기 위해 `Cache-Control`, `Expires` 및 `Pragma` 헤더를 설정하거나 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-207">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="35ef5-208">값이 `true`인 경우 미들웨어는 응답 캐시 헤더를 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-208">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="35ef5-209">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="35ef5-209">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="35ef5-210">출력 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="35ef5-210">Customize output</span></span>

<span data-ttu-id="35ef5-211">`Startup.Configure`에서 [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) 옵션을 응답을 쓰기 위한 대리자로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-211">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="35ef5-212">기본 대리자는 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)의 문자열 값을 사용하여 최소 일반 텍스트 응답을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-212">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="35ef5-213">다음 사용자 지정 대리자는 사용자 지정 JSON 응답을 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-213">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="35ef5-214">샘플 앱의 첫 번째 예제에서는 <xref:System.Text.Json?displayProperty=fullName>를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-214">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="35ef5-215">두 번째 예제에서는 [Newtonsoft.json](https://www.nuget.org/packages/Newtonsoft.Json/)을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-215">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="35ef5-216">샘플 앱에서 `WriteResponse` 버전의 `Newtonsoft.Json`을 사용할 수 있도록 *CustomWriterStartup.cs*의 `SYSTEM_TEXT_JSON` [전처리기 지시문](xref:index#preprocessor-directives-in-sample-code)을 주석 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-216">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="35ef5-217">상태 검사 API는 사용자가 선택한 모니터링 시스템에만 적용되는 형식이므로 복합 JSON 반환 형식에 대한 기본 제공 지원을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-217">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="35ef5-218">앞의 예제에서 응답을 필요에 따라 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-218">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="35ef5-219">`System.Text.Json`을 사용한 역직렬화에 대한 자세한 내용은 [.NET에서 JSON을 직렬화 및 역직렬화하는 방법](/dotnet/standard/serialization/system-text-json-how-to)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="35ef5-219">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="35ef5-220">데이터베이스 프로브</span><span class="sxs-lookup"><span data-stu-id="35ef5-220">Database probe</span></span>

<span data-ttu-id="35ef5-221">상태 검사는 데이터베이스 쿼리를 지정하여 데이터베이스가 정상적으로 응답하는지를 나타내기 위해 부울 테스트로서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-221">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="35ef5-222">샘플 앱은 ASP.NET Core 앱의 상태 검사 라이브러리인 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)를 사용하여 SQL Server 데이터베이스에 대한 상태 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-222">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="35ef5-223">`AspNetCore.Diagnostics.HealthChecks`는 데이터베이스에 연결이 양호한지 확인하기 위해 데이터베이스에 대해 `SELECT 1` 쿼리를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-223">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="35ef5-224">쿼리로 데이터베이스 연결을 검사할 때 신속하게 반환되는 쿼리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-224">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="35ef5-225">쿼리 방식 실행은 데이터베이스의 오버로드와 성능 저하를 유발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-225">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="35ef5-226">대부분의 경우 테스트 쿼리를 실행할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-226">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="35ef5-227">간단히 데이터베이스에 연결하는 정도로도 충분합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-227">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="35ef5-228">쿼리를 실행해야 하는 경우 `SELECT 1`과 같은 간단한 SELECT 쿼리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-228">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="35ef5-229">[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)에 대한 패키지 참조를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-229">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="35ef5-230">샘플 앱의 *appsettings.json* 파일에서 유효한 데이터베이스 연결 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-230">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="35ef5-231">앱은 `HealthCheckSample`이라고 하는 SQL Server 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-231">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="35ef5-232">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-232">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="35ef5-233">샘플 앱은 데이터베이스의 연결 문자열(*DbHealthStartup.cs*)을 사용하여 `AddSqlServer` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-233">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="35ef5-234">상태 검사 엔드포인트는 `Startup.Configure`에서 `MapHealthChecks`를 호출하여 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-234">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="35ef5-235">샘플 앱을 사용하여 데이터베이스 프로브 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-235">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="35ef5-236">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-236">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="35ef5-237">Entity Framework Core DbContext 프로브</span><span class="sxs-lookup"><span data-stu-id="35ef5-237">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="35ef5-238">`DbContext` 검사는 앱이 EF Core `DbContext`에 대해 구성된 데이터베이스와 통신할 수 있음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-238">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="35ef5-239">`DbContext` 검사는 다음과 같은 앱에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-239">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="35ef5-240">[EF(Entity Framework) Core](/ef/core/)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-240">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="35ef5-241">[Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)에 대한 패키지 참조를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-241">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="35ef5-242">`AddDbContextCheck<TContext>`는 `DbContext`에 대한 상태 검사를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-242">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="35ef5-243">`DbContext`는 메서드에 `TContext`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-243">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="35ef5-244">오버로드는 오류 상태, 태그 및 사용자 지정 테스트 쿼리를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-244">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="35ef5-245">기본적으로 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-245">By default:</span></span>

* <span data-ttu-id="35ef5-246">`DbContextHealthCheck`는 EF Core의 `CanConnectAsync` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-246">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="35ef5-247">`AddDbContextCheck` 메서드 오버로드를 사용하여 상태를 검사할 때 실행되는 작업을 사용자 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-247">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="35ef5-248">상태 검사의 이름은 `TContext` 형식의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-248">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="35ef5-249">샘플 앱에서 `AppDbContext`는 `AddDbContextCheck`에 제공되고 `Startup.ConfigureServices`의 서비스로서 등록됩니다(*DbContextHealthStartup.cs*).</span><span class="sxs-lookup"><span data-stu-id="35ef5-249">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="35ef5-250">상태 검사 엔드포인트는 `Startup.Configure`에서 `MapHealthChecks`를 호출하여 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-250">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="35ef5-251">샘플 앱을 사용하여 `DbContext` 프로브 시나리오를 실행하려면 연결 문자열로 지정된 데이터베이스가 SQL Server 인스턴스에 있지 않은지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-251">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="35ef5-252">데이터베이스가 있으면 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-252">If the database exists, delete it.</span></span>

<span data-ttu-id="35ef5-253">명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-253">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="35ef5-254">앱을 실행한 후 브라우저의 `/health` 엔드포인트에 요청하여 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-254">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="35ef5-255">데이터베이스 및 `AppDbContext`가 존재하기 않기 때문에 앱은 다음 응답을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-255">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="35ef5-256">데이터베이스를 만드는 샘플 앱을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-256">Trigger the sample app to create the database.</span></span> <span data-ttu-id="35ef5-257">`/createdatabase`에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-257">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="35ef5-258">앱은 다음과 같이 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-258">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="35ef5-259">`/health` 엔드포인트에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-259">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="35ef5-260">데이터베이스와 컨텍스트가 존재하기 때문에 앱은 다음과 같이 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-260">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="35ef5-261">데이터베이스를 삭제하는 샘플 앱을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-261">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="35ef5-262">`/deletedatabase`에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-262">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="35ef5-263">앱은 다음과 같이 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-263">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="35ef5-264">`/health` 엔드포인트에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-264">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="35ef5-265">앱은 다음과 같이 비정상 응답을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-265">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="35ef5-266">별도의 준비 상태 및 활동성 프로브</span><span class="sxs-lookup"><span data-stu-id="35ef5-266">Separate readiness and liveness probes</span></span>

<span data-ttu-id="35ef5-267">일부 호스팅 시나리오에서 두 개의 앱 상태를 구분하는 한 쌍의 상태 검사가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-267">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="35ef5-268">앱은 작동하지만, 아직 요청을 받을 준비가 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-268">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="35ef5-269">이 상태는 앱의 *준비 상태*입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-269">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="35ef5-270">앱을 작동하고 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-270">The app is functioning and responding to requests.</span></span> <span data-ttu-id="35ef5-271">이 상태는 앱의 *활동성*입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-271">This state is the app's *liveness*.</span></span>

<span data-ttu-id="35ef5-272">준비 상태 검사는 대개 앱의 하위 시스템 및 리소스를 모두 사용할 수 있는지 확인하기 위해 보다 광범위하고 시간이 많이 소요되는 검사 세트를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-272">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="35ef5-273">활동성 검사는 앱이 요청을 처리할 수 있는지 여부를 확인하기 위해 빠른 검사만 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-273">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="35ef5-274">앱이 준비 상태 검사를 통과한 후에는 값 비싼 준비 상태 검사 세트로 앱에 추가로 부담 지울 필요가 없으며&mdash;이후 활동성에 대한 검사만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-274">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="35ef5-275">샘플 앱에는 [호스팅된 서비스](xref:fundamentals/host/hosted-services)에서 장기 실행 시작 작업 완료를 보고하는 상태 검사가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-275">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="35ef5-276">`StartupHostedServiceHealthCheck`는 장기 실행 작업이 완료되면(*StartupHostedServiceHealthCheck.cs*) 호스팅된 서비스를 `true`로 설정할 수 있는 속성 `StartupTaskCompleted`를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-276">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="35ef5-277">장기 실행 백그라운드 작업은 [호스팅된 서비스](xref:fundamentals/host/hosted-services)(*Services/StartupHostedService*)에 의해 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-277">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="35ef5-278">작업이 끝나면 `StartupHostedServiceHealthCheck.StartupTaskCompleted`는 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-278">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="35ef5-279">상태 검사는 호스팅된 서비스와 함께 `Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-279">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="35ef5-280">호스팅된 서비스가 상태 검사에서 속성을 설정해야 하기 때문에 상태 확인도 서비스 컨테이너(*LivenessProbeStartup.cs*)에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-280">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="35ef5-281">상태 검사 엔드포인트는 `Startup.Configure`에서 `MapHealthChecks`를 호출하여 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-281">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="35ef5-282">샘플 앱에서 상태 검사 엔드포인트는 다음 위치에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-282">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="35ef5-283">준비 상태 검사를 위해 `/health/ready`.</span><span class="sxs-lookup"><span data-stu-id="35ef5-283">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="35ef5-284">준비 상태 검사는 `ready` 태그가 있는 상태 검사에서 상태 검사를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-284">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="35ef5-285">활동성 검사를 위해 `/health/live`.</span><span class="sxs-lookup"><span data-stu-id="35ef5-285">`/health/live` for the liveness check.</span></span> <span data-ttu-id="35ef5-286">활동성 검사는 [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate)에서 `false`를 반환하여 `StartupHostedServiceHealthCheck`를 필터링합니다(자세한 내용은 [상태 검사 필터링](#filter-health-checks) 참조).</span><span class="sxs-lookup"><span data-stu-id="35ef5-286">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="35ef5-287">다음 예제 코드에서:</span><span class="sxs-lookup"><span data-stu-id="35ef5-287">In the following example code:</span></span>

* <span data-ttu-id="35ef5-288">준비 상태 검사는 'ready' 태그가 있는 등록된 검사를 모두 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-288">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="35ef5-289">`Predicate`는 모든 검사를 제외하고 200-Ok를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-289">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

<span data-ttu-id="35ef5-290">샘플 앱을 사용하여 준비 상태/활동성 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-290">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="35ef5-291">브라우저에서 15초가 경과할 때까지 `/health/ready`를 여러 번 방문합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-291">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="35ef5-292">상태 검사는 첫 15초 동안 *Unhealthy*를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-292">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="35ef5-293">15초 후, 엔드포인트는 *Healthy*를 보고하여 호스트된 서비스에 의해 장기 실행 작업이 완료되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-293">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="35ef5-294">이 예제에서는 2초 지연을 사용하여 첫 번째 준비 검사를 실행하는 상태 검사 게시자(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현)도 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-294">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="35ef5-295">자세한 내용은 [상태 검사 게시자](#health-check-publisher) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="35ef5-295">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="35ef5-296">Kubernetes 예제</span><span class="sxs-lookup"><span data-stu-id="35ef5-296">Kubernetes example</span></span>

<span data-ttu-id="35ef5-297">별도의 준비 상태 및 활동성 검사를 사용하는 것은 [Kubernetes](https://kubernetes.io/)와 같은 환경에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-297">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="35ef5-298">Kubernetes에서 앱은 기본 데이터베이스 가용성 테스트와 같이 요청을 수락하기 전에 시간이 많이 걸리는 시작 작업을 수행해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-298">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="35ef5-299">별도의 검사를 사용하면 오케스트레이터가 앱이 작동하고 있지만 아직 준비가 되지 않았는지 또는 앱이 시작되지 않았는지 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-299">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="35ef5-300">Kubernetes의 준비 상태 및 활동성 프로브에 대한 자세한 내용은 Kubernetes 설명서의 [활동성 및 준비 상태 프로브 구성](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="35ef5-300">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="35ef5-301">다음 예제는 Kubernetes 준비 상태 프로브 구성을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-301">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```yml
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="35ef5-302">사용자 지정 응답 기록기가 있는 메트릭 기반 프로브</span><span class="sxs-lookup"><span data-stu-id="35ef5-302">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="35ef5-303">샘플 앱은 사용자 지정 응답 기록기가 있는 메모리 상태를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-303">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="35ef5-304">앱이 일정한 메모리 임계값(샘플 앱의 경우 1GB)을 초과하여 사용하는 경우 `MemoryHealthCheck`는 성능이 저하된 상태를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-304">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="35ef5-305"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>에는 앱의 가비지 수집기(GC) 정보가 포함됩니다(*MemoryHealthCheck.cs*).</span><span class="sxs-lookup"><span data-stu-id="35ef5-305">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="35ef5-306">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-306">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="35ef5-307"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>에 전달하여 상태 검사를 사용하는 대신 `MemoryHealthCheck`는 서비스로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-307">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="35ef5-308">모든 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 등록된 서비스는 상태 검사 서비스 및 미들웨어에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-308">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="35ef5-309">상태 확인 서비스를 Singleton 서비스로 등록하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-309">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="35ef5-310">샘플 앱의 *CustomWriterStartup.cs*에서:</span><span class="sxs-lookup"><span data-stu-id="35ef5-310">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="35ef5-311">상태 검사 엔드포인트는 `Startup.Configure`에서 `MapHealthChecks`를 호출하여 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-311">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="35ef5-312">상태 검사를 실행하는 경우 `WriteResponse` 대리자가 사용자 지정 JSON 응답을 출력하는 출력에 대한 <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> 속성에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-312">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="35ef5-313">`WriteResponse` 대리자는 `CompositeHealthCheckResult`를 JSON 객체로 형식 지정하고 상태 검사 응답을 위해 JSON 출력을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-313">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="35ef5-314">자세한 내용은 [출력 사용자 지정](#customize-output) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="35ef5-314">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="35ef5-315">샘플 앱을 사용하여 사용자 지정 응답 기록기 출력으로 메트릭 기반 프로브를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-315">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="35ef5-316">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)에는 디스크 스토리지 및 최댓값 활동성 검사를 포함하여 메트릭 기반 상태 확인 시나리오가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-316">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="35ef5-317">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-317">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="35ef5-318">포트별 필터링</span><span class="sxs-lookup"><span data-stu-id="35ef5-318">Filter by port</span></span>

<span data-ttu-id="35ef5-319">지정된 포트에 대한 상태 검사 요청을 제한하는 포트를 지정하는 URL 패턴을 사용하여 `MapHealthChecks`에 `RequireHost`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-319">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="35ef5-320">이는 일반적으로 컨테이너 환경에서 서비스 모니터링을 위해 포트를 노출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-320">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="35ef5-321">샘플 앱은 [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)를 사용하여 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-321">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="35ef5-322">포트는 *launchSettings.json* 파일에서 설정되고 환경 변수를 통해 구성 공급자에게 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-322">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="35ef5-323">또한 관리 포트에서 요청을 수신하도록 서버를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-323">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="35ef5-324">샘플 앱을 사용하여 관리 포트 구성을 보여주려면 *launchSettings.json* 파일을 *Properties* 폴더에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-324">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="35ef5-325">샘플 앱의 다음 *Properties/launchSettings.json* 파일은 샘플 앱의 프로젝트 파일에 포함되지 않으며 수동으로 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-325">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="35ef5-326">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-326">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="35ef5-327">`Startup.Configure`에서 `MapHealthChecks`를 호출하여 상태 검사 엔드포인트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-327">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="35ef5-328">샘플 앱에서 `Startup.Configure`의 엔드포인트에 대한 `RequireHost` 호출은 구성의 관리 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-328">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="35ef5-329">샘플 앱에서 엔드포인트는 `Startup.Configure`에서 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-329">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="35ef5-330">다음 예제 코드에서:</span><span class="sxs-lookup"><span data-stu-id="35ef5-330">In the following example code:</span></span>

* <span data-ttu-id="35ef5-331">준비 상태 검사는 'ready' 태그가 있는 등록된 검사를 모두 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-331">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="35ef5-332">`Predicate`는 모든 검사를 제외하고 200-Ok를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-332">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> <span data-ttu-id="35ef5-333">코드에서 관리 포트를 명시적으로 설정하여 샘플 앱에 *launchSettings.json* 파일을 만들지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-333">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="35ef5-334"><xref:Microsoft.Extensions.Hosting.HostBuilder>가 만들어진 *Program.cs*에서 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*>에 대한 호출을 추가하고 앱의 관리 포트 엔드포인트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-334">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="35ef5-335">*ManagementPortStartup.cs*의 `Configure`에서 `RequireHost`로 관리 포트를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-335">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="35ef5-336">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="35ef5-336">*Program.cs*:</span></span>
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> <span data-ttu-id="35ef5-337">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="35ef5-337">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="35ef5-338">샘플 앱을 사용하여 관리 포트 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-338">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="35ef5-339">상태 검사 라이브러리 배포</span><span class="sxs-lookup"><span data-stu-id="35ef5-339">Distribute a health check library</span></span>

<span data-ttu-id="35ef5-340">상태 검사를 라이브러리로 배포하려면 다음과 같이 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-340">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="35ef5-341"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 인터페이스를 독립 실행형 클래스로 구현하는 상태 검사를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-341">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="35ef5-342">클래스는 [DI(종속성 주입)](xref:fundamentals/dependency-injection), 유형 활성화 및 [명명된 옵션](xref:fundamentals/configuration/options)에 의존하여 구성 데이터에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-342">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="35ef5-343">`CheckHealthAsync`의 상태 검사 논리에서:</span><span class="sxs-lookup"><span data-stu-id="35ef5-343">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="35ef5-344">`data1` 및 `data2`는 메서드에서 프로브의 상태 검사 논리를 실행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-344">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="35ef5-345">`AccessViolationException`이 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-345">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="35ef5-346"><xref:System.AccessViolationException>이 발생하는 경우 사용자가 상태 검사 실패 상태를 구성할 수 있도록 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus>가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>와 함께 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-346">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. <span data-ttu-id="35ef5-347">사용하는 앱이 `Startup.Configure` 메서드에서 호출하는 매개 변수를 사용하여 확장 메서드를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-347">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="35ef5-348">다음 예제에서는 다음 상태 검사 메서드 서명을 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-348">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="35ef5-349">이전 서명은 `ExampleHealthCheck`가 상태 검사 프로브 로직을 처리하기 위해 추가 데이터가 필요함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-349">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="35ef5-350">상태 검사가 확장 메서드를 사용하여 등록되면 데이터는 상태 검사 인스턴스를 만드는 데 사용된 대리자에게 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-350">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="35ef5-351">다음 예제에서 호출자는 선택 사항을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-351">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="35ef5-352">상태 검사 이름(`name`).</span><span class="sxs-lookup"><span data-stu-id="35ef5-352">health check name (`name`).</span></span> <span data-ttu-id="35ef5-353">`null`인 경우 `example_health_check`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-353">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="35ef5-354">상태 검사를 위한 문자열 데이터 요소입니다(`data1`).</span><span class="sxs-lookup"><span data-stu-id="35ef5-354">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="35ef5-355">상태 검사를 위한 정수 데이터 요소입니다(`data2`).</span><span class="sxs-lookup"><span data-stu-id="35ef5-355">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="35ef5-356">`null`인 경우 `1`이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-356">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="35ef5-357">실패 상태입니다(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="35ef5-357">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="35ef5-358">기본값은 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-358">The default is `null`.</span></span> <span data-ttu-id="35ef5-359">`null`인 경우 오류 상태는 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)로 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-359">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="35ef5-360">태그(`IEnumerable<string>`)입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-360">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="35ef5-361">상태 검사 게시자</span><span class="sxs-lookup"><span data-stu-id="35ef5-361">Health Check Publisher</span></span>

<span data-ttu-id="35ef5-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>가 서비스 컨테이너에 추가되면 상태 검사 시스템이 주기적으로 상태 검사를 실행하고 그 결과로 `PublishAsync`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-362">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="35ef5-363">이는 각 프로세스가 상태를 결정하기 위해 주기적으로 모니터링 시스템을 호출하도록 하는 푸시 기반 상태 모니터링 시스템 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-363">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="35ef5-364"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인터페이스는 단일 메서드를 가집니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-364">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="35ef5-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>를 사용하여 다음을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="35ef5-366"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: 앱이 시작된 후 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인스턴스를 시작하기 전에 적용되는 초기 지연입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-366"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="35ef5-367">지연은 시작 시 한 번 적용되며 후속 반복에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-367">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="35ef5-368">기본값은 5초입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-368">The default value is five seconds.</span></span>
* <span data-ttu-id="35ef5-369"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 실행 기간입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-369"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="35ef5-370">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-370">The default value is 30 seconds.</span></span>
* <span data-ttu-id="35ef5-371"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>가 `null`(기본값)인 경우 상태 검사 게시자 서비스는 등록된 상태 검사를 모두 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-371"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="35ef5-372">상태 검사 하위 집합을 실행하려면 검사 세트를 필터링하는 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-372">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="35ef5-373">조건자는 기간마다 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-373">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="35ef5-374"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: 모든 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인스턴스에 대한 상태 검사 실행의 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-374"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="35ef5-375">시간 제한 없이 실행하려면 <xref:System.Threading.Timeout.InfiniteTimeSpan>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-375">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="35ef5-376">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-376">The default value is 30 seconds.</span></span>

<span data-ttu-id="35ef5-377">샘플 앱에서 `ReadinessPublisher`는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-377">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="35ef5-378">상태 검사 상태는 다음의 로그 수준에서 각 검사에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-378">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="35ef5-379">상태 검사 상태가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>인 경우 정보(<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>)</span><span class="sxs-lookup"><span data-stu-id="35ef5-379">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="35ef5-380">상태가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> 또는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>인 경우 오류(<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>)</span><span class="sxs-lookup"><span data-stu-id="35ef5-380">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="35ef5-381">샘플 앱의 `LivenessProbeStartup` 예제에서 `StartupHostedService` 준비 검사에는 2초 시작 지연이 있고 30초마다 검사가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-381">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="35ef5-382"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현을 활성화하기 위해 샘플은 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에서 `ReadinessPublisher`를 싱글톤 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-382">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="35ef5-383">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 [Application Insights](/azure/application-insights/app-insights-overview)를 포함하여 몇몇 시스템에 대한 게시자를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-383">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="35ef5-384">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-384">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="35ef5-385">MapWhen으로 상태 검사 제한</span><span class="sxs-lookup"><span data-stu-id="35ef5-385">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="35ef5-386"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>을 사용하여 상태 검사 엔드포인트에 대한 요청 파이프라인을 조건부로 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-386">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="35ef5-387">다음 예제에서 `MapWhen`은 `api/HealthCheck` 엔드포인트에 대한 GET 요청을 받는 경우 상태 검사 미들웨어를 활성화하도록 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-387">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

<span data-ttu-id="35ef5-388">자세한 내용은 <xref:fundamentals/middleware/index#use-run-and-map>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="35ef5-388">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="35ef5-389">ASP.NET Core는 앱 인프라 구성 요소의 상태를 보고하기 위해 상태 검사 미들웨어와 라이브러리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-389">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="35ef5-390">상태 검사는 앱에 의해 HTTP 엔드포인트로서 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-390">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="35ef5-391">상태 검사 엔드포인트는 다양한 실시간 모니터링 시나리오에 맞게 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-391">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="35ef5-392">상태 프로브는 컨테이너 오케스트레이터와 부하 분산 장치가 앱 상태를 검사하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-392">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="35ef5-393">예를 들어, 컨테이너 오케스트레이터는 롤링 배포를 중지하거나 컨테이너를 다시 시작하여 실패한 상태 검사에 응답할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-393">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="35ef5-394">부하 분산 장치는 장애가 발생한 상태 검사 인스턴스로부터 트래픽을 다른 곳으로 라우팅하여 비정상 앱에 대응할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-394">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="35ef5-395">메모리, 디스크 및 기타 물리적 서버 리소스의 사용이 정상적인 상태인지 모니터링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-395">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="35ef5-396">상태 검사는 데이터베이스 및 외부 서비스 엔드포인트와 같은 앱 종속성을 테스트하여 가용성 및 정상 작동 여부를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-396">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="35ef5-397">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="35ef5-397">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="35ef5-398">샘플 앱에는 이 항목에 설명된 시나리오의 예가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-398">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="35ef5-399">지정된 시나리오에 대해 샘플 앱을 실행하려면 명령 셸의 프로젝트 폴더에서 [dotnet run](/dotnet/core/tools/dotnet-run) 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-399">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="35ef5-400">샘플 앱의 사용 방법에 대한 자세한 내용은 샘플 앱의 *README.md* 파일과 이 항목의 시나리오 설명을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="35ef5-400">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="35ef5-401">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="35ef5-401">Prerequisites</span></span>

<span data-ttu-id="35ef5-402">상태 검사는 일반적으로 외부 모니터링 서비스 또는 컨테이너 오케스트레이터와 함께 사용되어 앱 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-402">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="35ef5-403">앱에 상태 검사를 추가하기 전에 사용할 모니터링 시스템을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-403">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="35ef5-404">모니터링 시스템은 어떤 유형의 상태 검사를 만들고 그 엔드포인트를 구성하는 방법을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-404">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="35ef5-405">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하거나 [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-405">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="35ef5-406">샘플 앱은 여러 시나리오의 상태 검사를 보여주는 시작 코드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-406">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="35ef5-407">[데이터베이스 프로브](#database-probe) 시나리오는 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)를 사용하여 데이터베이스 연결의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-407">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="35ef5-408">[DbContext 프로브](#entity-framework-core-dbcontext-probe) 시나리오는 EF Core `DbContext`를 사용하여 데이터베이스를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-408">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="35ef5-409">데이터베이스 시나리오를 탐색하려면 샘플 앱:</span><span class="sxs-lookup"><span data-stu-id="35ef5-409">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="35ef5-410">데이터베이스를 만들고 *appsettings.json* 파일에서 연결 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-410">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="35ef5-411">해당 프로젝트 파일에 다음 패키지 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-411">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="35ef5-412">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="35ef5-412">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="35ef5-413">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="35ef5-413">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="35ef5-414">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-414">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="35ef5-415">다른 상태 검사 시나리오는 관리 포트에 대해 상태 검사를 필터링하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-415">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="35ef5-416">샘플 앱을 사용하려면 관리 URL과 관리 포트가 포함된 *Properties/launchSettings.json*  파일을 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-416">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="35ef5-417">자세한 내용은 [포트별 필터링](#filter-by-port) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="35ef5-417">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="35ef5-418">기본 상태 프로브</span><span class="sxs-lookup"><span data-stu-id="35ef5-418">Basic health probe</span></span>

<span data-ttu-id="35ef5-419">많은 앱의 경우, 요청을 처리할 수 있는 앱의 가용성(*활동성*)을 보고하는 기본 상태 검사 구성으로 앱 상태를 충분히 파악할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-419">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="35ef5-420">기본 구성은 상태 검사 서비스를 등록하고 상태 검사 미들웨어를 호출하여 URL 엔드포인트에서 상태 응답을 사용하여 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-420">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="35ef5-421">기본적으로 특정 종속성이나 하위 시스템을 테스트하기 위해 특정 상태 검사가 등록되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-421">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="35ef5-422">상태 엔드포인트 URL에서 응답할 수 있는 경우 앱 상태가 좋은 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-422">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="35ef5-423">기본 응답 기록기는 상태(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)를 일반 텍스트 응답으로 다시 클라이언트에 기록하여 [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 또는 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 상태를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-423">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="35ef5-424">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-424">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="35ef5-425">`Startup.Configure`의 요청 처리 파이프라인에 있는 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>로 상태 검사 미들웨어용 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-425">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="35ef5-426">샘플 앱에서 상태 검사 엔드포인트는 `/health`(*BasicStartup.cs*)에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-426">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

<span data-ttu-id="35ef5-427">샘플 앱을 사용하여 기본 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-427">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="35ef5-428">Docker 예</span><span class="sxs-lookup"><span data-stu-id="35ef5-428">Docker example</span></span>

<span data-ttu-id="35ef5-429">[Docker](xref:host-and-deploy/docker/index)는 기본 상태 검사 구성을 사용하는 앱의 상태를 검사하는 데 사용할 수 있는 기본 제공 `HEALTHCHECK` 지시문을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-429">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="35ef5-430">상태 검사 만들기</span><span class="sxs-lookup"><span data-stu-id="35ef5-430">Create health checks</span></span>

<span data-ttu-id="35ef5-431">상태 검사는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 인터페이스를 구현하여 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-431">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="35ef5-432"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> 메서드는 상태를 `Healthy`, `Degraded` 또는 `Unhealthy`로 나타내는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-432">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="35ef5-433">결과는 구성 가능한 상태 코드가 있는 일반 텍스트 응답으로 기록됩니다(구성은 [상태 검사 옵션](#health-check-options) 섹션에 설명되어 있음).</span><span class="sxs-lookup"><span data-stu-id="35ef5-433">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="35ef5-434"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>는 선택 사항인 키-값 쌍을 반환할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-434"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="35ef5-435">상태 검사 예</span><span class="sxs-lookup"><span data-stu-id="35ef5-435">Example health check</span></span>

<span data-ttu-id="35ef5-436">다음 `ExampleHealthCheck` 클래스는 상태 검사의 레이아웃을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-436">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="35ef5-437">상태 검사 논리는 `CheckHealthAsync` 메서드에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-437">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="35ef5-438">다음 예제에서는 더미 변수 `healthCheckResultHealthy`를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-438">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="35ef5-439">`healthCheckResultHealthy` 값이 `false`로 설정되면 [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 상태가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-439">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a><span data-ttu-id="35ef5-440">상태 검사 서비스 등록</span><span class="sxs-lookup"><span data-stu-id="35ef5-440">Register health check services</span></span>

<span data-ttu-id="35ef5-441">`ExampleHealthCheck` 형식은 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>로 `Startup.ConfigureServices`의 상태 검사 서비스에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-441">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="35ef5-442">다음 예제에 표시된 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 오버로드는 상태 검사에서 오류가 보고되면 보고하도록 오류 상태(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-442">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="35ef5-443">오류 상태가 `null`(기본값)로 설정되면 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)가 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-443">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="35ef5-444">이 오버로드는 라이브러리 작성자에게 유용한 시나리오입니다. 이 라이브러리에서는 상태 검사 구현이 설정을 준수하는 경우 상태 검사 실패가 발생할 때 라이브러리가 나타내는 오류 상태는 앱에 의해 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-444">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="35ef5-445">*태그*를 사용하여 상태 검사를 필터링할 수 있습니다([필터 상태 검사](#filter-health-checks) 섹션에 자세히 설명되어 있음).</span><span class="sxs-lookup"><span data-stu-id="35ef5-445">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="35ef5-446"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>는 람다 함수를 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-446"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="35ef5-447">다음 `Startup.ConfigureServices` 예제에서 상태 검사 이름은 `Example`로 지정되고 검사는 항상 정상 상태를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-447">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="35ef5-448">상태 검사 미들웨어 사용</span><span class="sxs-lookup"><span data-stu-id="35ef5-448">Use Health Checks Middleware</span></span>

<span data-ttu-id="35ef5-449">`Startup.Configure`에서 엔드포인트 URL 또는 상대 경로를 사용하여 처리 파이프라인에서 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-449">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="35ef5-450">상태 검사가 특정 포트에서 수신 대기해야 하는 경우 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>의 오버로드를 사용하여 포트를 설정합니다([포트별 필터링 ](#filter-by-port) 섹션에 자세히 설명되어 있음).</span><span class="sxs-lookup"><span data-stu-id="35ef5-450">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="35ef5-451">상태 검사 옵션</span><span class="sxs-lookup"><span data-stu-id="35ef5-451">Health check options</span></span>

<span data-ttu-id="35ef5-452"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>는 다음과 같은 상태 검사 동작을 사용자 지정할 수 있는 기회를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-452"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="35ef5-453">상태 검사 필터링</span><span class="sxs-lookup"><span data-stu-id="35ef5-453">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="35ef5-454">HTTP 상태 코드 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="35ef5-454">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="35ef5-455">캐시 헤더 표시 안 함</span><span class="sxs-lookup"><span data-stu-id="35ef5-455">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="35ef5-456">출력 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="35ef5-456">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="35ef5-457">상태 검사 필터링</span><span class="sxs-lookup"><span data-stu-id="35ef5-457">Filter health checks</span></span>

<span data-ttu-id="35ef5-458">기본적으로 상태 검사 미들웨어는 등록된 모든 상태 검사를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-458">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="35ef5-459">상태 검사 하위 세트를 실행하려면 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 옵션에 부울 값을 반환하는 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-459">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="35ef5-460">다음 예제에서는 상태 검사의 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 속성이 `foo_tag` 또는 `baz_tag`와 일치하는 경우에만 `true`가 반환되는 함수의 조건문에서 태그(`bar_tag`)에 의해 `Bar` 상태 검사가 필터링됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-460">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="35ef5-461">HTTP 상태 코드 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="35ef5-461">Customize the HTTP status code</span></span>

<span data-ttu-id="35ef5-462"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>를 사용하여 상태를 HTTP 상태 코드에 매핑하는 작업을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-462">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="35ef5-463">다음 <xref:Microsoft.AspNetCore.Http.StatusCodes> 할당은 미들웨어에서 사용되는 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-463">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="35ef5-464">요구 사항에 맞게 상태 코드 값을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-464">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="35ef5-465">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="35ef5-465">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="35ef5-466">캐시 헤더 표시 안 함</span><span class="sxs-lookup"><span data-stu-id="35ef5-466">Suppress cache headers</span></span>

<span data-ttu-id="35ef5-467"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>는 응답 캐싱을 방지하기 위해 상태 검사 미들웨어가 HTTP 헤더를 프로브 응답에 추가할지 여부를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-467"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="35ef5-468">값이 `false`(기본값)인 경우 미들웨어는 응답 캐싱을 방지하기 위해 `Cache-Control`, `Expires` 및 `Pragma` 헤더를 설정하거나 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-468">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="35ef5-469">값이 `true`인 경우 미들웨어는 응답 캐시 헤더를 수정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-469">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="35ef5-470">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="35ef5-470">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="35ef5-471">출력 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="35ef5-471">Customize output</span></span>

<span data-ttu-id="35ef5-472"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> 옵션은 응답을 기록하는 데 사용되는 대리자를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-472">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="35ef5-473">기본 대리자는 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)의 문자열 값을 사용하여 최소 일반 텍스트 응답을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-473">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="35ef5-474">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="35ef5-474">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="35ef5-475">기본 대리자는 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status)의 문자열 값을 사용하여 최소 일반 텍스트 응답을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-475">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="35ef5-476">다음 사용자 지정 대리자 `WriteResponse`는 사용자 지정 JSON 응답을 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-476">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

<span data-ttu-id="35ef5-477">상태 검사 시스템은 사용자가 선택한 모니터링 시스템에만 적용되는 형식이므로 복합 JSON 반환 형식에 대한 기본 제공 지원을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-477">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="35ef5-478">필요에 따라 앞의 예제에서 `JObject`를 자유롭게 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-478">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="35ef5-479">데이터베이스 프로브</span><span class="sxs-lookup"><span data-stu-id="35ef5-479">Database probe</span></span>

<span data-ttu-id="35ef5-480">상태 검사는 데이터베이스 쿼리를 지정하여 데이터베이스가 정상적으로 응답하는지를 나타내기 위해 부울 테스트로서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-480">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="35ef5-481">샘플 앱은 ASP.NET Core 앱의 상태 검사 라이브러리인 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)를 사용하여 SQL Server 데이터베이스에 대한 상태 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-481">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="35ef5-482">`AspNetCore.Diagnostics.HealthChecks`는 데이터베이스에 연결이 양호한지 확인하기 위해 데이터베이스에 대해 `SELECT 1` 쿼리를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-482">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="35ef5-483">쿼리로 데이터베이스 연결을 검사할 때 신속하게 반환되는 쿼리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-483">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="35ef5-484">쿼리 방식 실행은 데이터베이스의 오버로드와 성능 저하를 유발할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-484">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="35ef5-485">대부분의 경우 테스트 쿼리를 실행할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-485">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="35ef5-486">간단히 데이터베이스에 연결하는 정도로도 충분합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-486">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="35ef5-487">쿼리를 실행해야 하는 경우 `SELECT 1`과 같은 간단한 SELECT 쿼리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-487">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="35ef5-488">[AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)에 대한 패키지 참조를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-488">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="35ef5-489">샘플 앱의 *appsettings.json* 파일에서 유효한 데이터베이스 연결 문자열을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-489">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="35ef5-490">앱은 `HealthCheckSample`이라고 하는 SQL Server 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-490">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="35ef5-491">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-491">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="35ef5-492">샘플 앱은 데이터베이스의 연결 문자열(*DbHealthStartup.cs*)을 사용하여 `AddSqlServer` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-492">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="35ef5-493">`Startup.Configure`의 앱 처리 파이프라인에서 상태 검사 미들웨어를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-493">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="35ef5-494">샘플 앱을 사용하여 데이터베이스 프로브 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-494">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="35ef5-495">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-495">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="35ef5-496">Entity Framework Core DbContext 프로브</span><span class="sxs-lookup"><span data-stu-id="35ef5-496">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="35ef5-497">`DbContext` 검사는 앱이 EF Core `DbContext`에 대해 구성된 데이터베이스와 통신할 수 있음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-497">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="35ef5-498">`DbContext` 검사는 다음과 같은 앱에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-498">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="35ef5-499">[EF(Entity Framework) Core](/ef/core/)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-499">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="35ef5-500">[Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)에 대한 패키지 참조를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-500">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="35ef5-501">`AddDbContextCheck<TContext>`는 `DbContext`에 대한 상태 검사를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-501">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="35ef5-502">`DbContext`는 메서드에 `TContext`로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-502">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="35ef5-503">오버로드는 오류 상태, 태그 및 사용자 지정 테스트 쿼리를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-503">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="35ef5-504">기본적으로 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-504">By default:</span></span>

* <span data-ttu-id="35ef5-505">`DbContextHealthCheck`는 EF Core의 `CanConnectAsync` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-505">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="35ef5-506">`AddDbContextCheck` 메서드 오버로드를 사용하여 상태를 검사할 때 실행되는 작업을 사용자 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-506">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="35ef5-507">상태 검사의 이름은 `TContext` 형식의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-507">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="35ef5-508">샘플 앱에서 `AppDbContext`는 `AddDbContextCheck`에 제공되고 `Startup.ConfigureServices`의 서비스로서 등록됩니다(*DbContextHealthStartup.cs*).</span><span class="sxs-lookup"><span data-stu-id="35ef5-508">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="35ef5-509">샘플 앱에서 `UseHealthChecks`는 `Startup.Configure`에서 상태 검사 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-509">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="35ef5-510">샘플 앱을 사용하여 `DbContext` 프로브 시나리오를 실행하려면 연결 문자열로 지정된 데이터베이스가 SQL Server 인스턴스에 있지 않은지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-510">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="35ef5-511">데이터베이스가 있으면 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-511">If the database exists, delete it.</span></span>

<span data-ttu-id="35ef5-512">명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-512">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="35ef5-513">앱을 실행한 후 브라우저의 `/health` 엔드포인트에 요청하여 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-513">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="35ef5-514">데이터베이스 및 `AppDbContext`가 존재하기 않기 때문에 앱은 다음 응답을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-514">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="35ef5-515">데이터베이스를 만드는 샘플 앱을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-515">Trigger the sample app to create the database.</span></span> <span data-ttu-id="35ef5-516">`/createdatabase`에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-516">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="35ef5-517">앱은 다음과 같이 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-517">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="35ef5-518">`/health` 엔드포인트에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-518">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="35ef5-519">데이터베이스와 컨텍스트가 존재하기 때문에 앱은 다음과 같이 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-519">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="35ef5-520">데이터베이스를 삭제하는 샘플 앱을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-520">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="35ef5-521">`/deletedatabase`에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-521">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="35ef5-522">앱은 다음과 같이 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-522">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="35ef5-523">`/health` 엔드포인트에 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-523">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="35ef5-524">앱은 다음과 같이 비정상 응답을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-524">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="35ef5-525">별도의 준비 상태 및 활동성 프로브</span><span class="sxs-lookup"><span data-stu-id="35ef5-525">Separate readiness and liveness probes</span></span>

<span data-ttu-id="35ef5-526">일부 호스팅 시나리오에서 두 개의 앱 상태를 구분하는 한 쌍의 상태 검사가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-526">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="35ef5-527">앱은 작동하지만, 아직 요청을 받을 준비가 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-527">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="35ef5-528">이 상태는 앱의 *준비 상태*입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-528">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="35ef5-529">앱을 작동하고 요청에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-529">The app is functioning and responding to requests.</span></span> <span data-ttu-id="35ef5-530">이 상태는 앱의 *활동성*입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-530">This state is the app's *liveness*.</span></span>

<span data-ttu-id="35ef5-531">준비 상태 검사는 대개 앱의 하위 시스템 및 리소스를 모두 사용할 수 있는지 확인하기 위해 보다 광범위하고 시간이 많이 소요되는 검사 세트를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-531">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="35ef5-532">활동성 검사는 앱이 요청을 처리할 수 있는지 여부를 확인하기 위해 빠른 검사만 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-532">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="35ef5-533">앱이 준비 상태 검사를 통과한 후에는 값 비싼 준비 상태 검사 세트로 앱에 추가로 부담 지울 필요가 없으며&mdash;이후 활동성에 대한 검사만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-533">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="35ef5-534">샘플 앱에는 [호스팅된 서비스](xref:fundamentals/host/hosted-services)에서 장기 실행 시작 작업 완료를 보고하는 상태 검사가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-534">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="35ef5-535">`StartupHostedServiceHealthCheck`는 장기 실행 작업이 완료되면(*StartupHostedServiceHealthCheck.cs*) 호스팅된 서비스를 `true`로 설정할 수 있는 속성 `StartupTaskCompleted`를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-535">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="35ef5-536">장기 실행 백그라운드 작업은 [호스팅된 서비스](xref:fundamentals/host/hosted-services)(*Services/StartupHostedService*)에 의해 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-536">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="35ef5-537">작업이 끝나면 `StartupHostedServiceHealthCheck.StartupTaskCompleted`는 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-537">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="35ef5-538">상태 검사는 호스팅된 서비스와 함께 `Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-538">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="35ef5-539">호스팅된 서비스가 상태 검사에서 속성을 설정해야 하기 때문에 상태 확인도 서비스 컨테이너(*LivenessProbeStartup.cs*)에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-539">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="35ef5-540">`Startup.Configure`의 앱 처리 파이프라인에서 상태 검사 미들웨어를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-540">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="35ef5-541">샘플 앱에서 상태 검사 엔드포인트는 활동성 검사의 경우 `/health/ready`에, 준비 상태 검사의 경우 `/health/live`에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-541">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="35ef5-542">준비 상태 검사는 `ready` 태그가 있는 상태 검사에서 상태 검사를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-542">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="35ef5-543">활동성 검사는 [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate)에서 `false`를 반환하여 `StartupHostedServiceHealthCheck`를 필터링합니다(자세한 내용은 [상태 검사 필터링](#filter-health-checks) 참조).</span><span class="sxs-lookup"><span data-stu-id="35ef5-543">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

<span data-ttu-id="35ef5-544">샘플 앱을 사용하여 준비 상태/활동성 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-544">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="35ef5-545">브라우저에서 15초가 경과할 때까지 `/health/ready`를 여러 번 방문합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-545">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="35ef5-546">상태 검사는 첫 15초 동안 *Unhealthy*를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-546">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="35ef5-547">15초 후, 엔드포인트는 *Healthy*를 보고하여 호스트된 서비스에 의해 장기 실행 작업이 완료되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-547">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="35ef5-548">이 예제에서는 2초 지연을 사용하여 첫 번째 준비 검사를 실행하는 상태 검사 게시자(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현)도 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-548">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="35ef5-549">자세한 내용은 [상태 검사 게시자](#health-check-publisher) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="35ef5-549">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="35ef5-550">Kubernetes 예제</span><span class="sxs-lookup"><span data-stu-id="35ef5-550">Kubernetes example</span></span>

<span data-ttu-id="35ef5-551">별도의 준비 상태 및 활동성 검사를 사용하는 것은 [Kubernetes](https://kubernetes.io/)와 같은 환경에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-551">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="35ef5-552">Kubernetes에서 앱은 기본 데이터베이스 가용성 테스트와 같이 요청을 수락하기 전에 시간이 많이 걸리는 시작 작업을 수행해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-552">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="35ef5-553">별도의 검사를 사용하면 오케스트레이터가 앱이 작동하고 있지만 아직 준비가 되지 않았는지 또는 앱이 시작되지 않았는지 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-553">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="35ef5-554">Kubernetes의 준비 상태 및 활동성 프로브에 대한 자세한 내용은 Kubernetes 설명서의 [활동성 및 준비 상태 프로브 구성](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="35ef5-554">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="35ef5-555">다음 예제는 Kubernetes 준비 상태 프로브 구성을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-555">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="35ef5-556">사용자 지정 응답 기록기가 있는 메트릭 기반 프로브</span><span class="sxs-lookup"><span data-stu-id="35ef5-556">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="35ef5-557">샘플 앱은 사용자 지정 응답 기록기가 있는 메모리 상태를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-557">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="35ef5-558">앱이 일정한 메모리 임계값(샘플 앱의 경우 1GB)을 초과하여 사용하는 경우 `MemoryHealthCheck`는 비정상적인 상태를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-558">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="35ef5-559"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>에는 앱의 가비지 수집기(GC) 정보가 포함됩니다(*MemoryHealthCheck.cs*).</span><span class="sxs-lookup"><span data-stu-id="35ef5-559">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="35ef5-560">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-560">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="35ef5-561"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>에 전달하여 상태 검사를 사용하는 대신 `MemoryHealthCheck`는 서비스로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-561">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="35ef5-562">모든 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 등록된 서비스는 상태 검사 서비스 및 미들웨어에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-562">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="35ef5-563">상태 확인 서비스를 Singleton 서비스로 등록하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-563">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="35ef5-564">샘플 앱(*CustomWriterStartup.cs*)에서:</span><span class="sxs-lookup"><span data-stu-id="35ef5-564">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="35ef5-565">`Startup.Configure`의 앱 처리 파이프라인에서 상태 검사 미들웨어를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-565">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="35ef5-566">상태 검사가 실행될 때 사용자 정의 JSON 응답을 출력하기 위해 `ResponseWriter` 속성에 `WriteResponse` 대리자가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-566">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="35ef5-567">`WriteResponse` 메서드는 `CompositeHealthCheckResult`를 JSON 객체로 형식 지정하고 상태 검사 응답을 위해 JSON 출력을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-567">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="35ef5-568">샘플 앱을 사용하여 사용자 지정 응답 기록기 출력으로 메트릭 기반 프로브를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-568">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="35ef5-569">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)에는 디스크 스토리지 및 최댓값 활동성 검사를 포함하여 메트릭 기반 상태 확인 시나리오가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-569">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="35ef5-570">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-570">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="35ef5-571">포트별 필터링</span><span class="sxs-lookup"><span data-stu-id="35ef5-571">Filter by port</span></span>

<span data-ttu-id="35ef5-572">포트와 함께 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>를 호출하면 지정된 포트에 대한 상태 검사 요청을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-572">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="35ef5-573">이는 일반적으로 컨테이너 환경에서 서비스 모니터링을 위해 포트를 노출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-573">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="35ef5-574">샘플 앱은 [환경 변수 구성 공급자](xref:fundamentals/configuration/index#environment-variables-configuration-provider)를 사용하여 포트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-574">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="35ef5-575">포트는 *launchSettings.json* 파일에서 설정되고 환경 변수를 통해 구성 공급자에게 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-575">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="35ef5-576">또한 관리 포트에서 요청을 수신하도록 서버를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-576">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="35ef5-577">샘플 앱을 사용하여 관리 포트 구성을 보여주려면 *launchSettings.json* 파일을 *Properties* 폴더에 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-577">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="35ef5-578">샘플 앱의 다음 *Properties/launchSettings.json* 파일은 샘플 앱의 프로젝트 파일에 포함되지 않으며 수동으로 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-578">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="35ef5-579">`Startup.ConfigureServices`의 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*>에 상태 검사 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-579">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="35ef5-580"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>에 대한 호출은 관리 포트를 지정합니다(*ManagementPortStartup.cs*).</span><span class="sxs-lookup"><span data-stu-id="35ef5-580">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="35ef5-581">코드에서 URL과 관리 포트를 명시적으로 설정하여 샘플 앱에 *launchSettings.json* 파일을 만들지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-581">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="35ef5-582"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>가 만들어진 *Program.cs*에서 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>에 호출을 추가하고 앱의 정상적인 응답 엔드포인트와 관리 포트 엔드포인트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-582">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="35ef5-583"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>가 호출되는 *ManagementPortStartup.cs*에서 관리 포트를 명시적으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-583">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="35ef5-584">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="35ef5-584">*Program.cs*:</span></span>
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> <span data-ttu-id="35ef5-585">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="35ef5-585">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="35ef5-586">샘플 앱을 사용하여 관리 포트 구성 시나리오를 실행하려면 명령 셸의 프로젝트 폴더에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-586">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="35ef5-587">상태 검사 라이브러리 배포</span><span class="sxs-lookup"><span data-stu-id="35ef5-587">Distribute a health check library</span></span>

<span data-ttu-id="35ef5-588">상태 검사를 라이브러리로 배포하려면 다음과 같이 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-588">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="35ef5-589"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 인터페이스를 독립 실행형 클래스로 구현하는 상태 검사를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-589">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="35ef5-590">클래스는 [DI(종속성 주입)](xref:fundamentals/dependency-injection), 유형 활성화 및 [명명된 옵션](xref:fundamentals/configuration/options)에 의존하여 구성 데이터에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-590">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="35ef5-591">`CheckHealthAsync`의 상태 검사 논리에서:</span><span class="sxs-lookup"><span data-stu-id="35ef5-591">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="35ef5-592">`data1` 및 `data2`는 메서드에서 프로브의 상태 검사 논리를 실행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-592">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="35ef5-593">`AccessViolationException`이 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-593">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="35ef5-594"><xref:System.AccessViolationException>이 발생하는 경우 사용자가 상태 검사 실패 상태를 구성할 수 있도록 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus>가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>와 함께 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-594">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. <span data-ttu-id="35ef5-595">사용하는 앱이 `Startup.Configure` 메서드에서 호출하는 매개 변수를 사용하여 확장 메서드를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-595">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="35ef5-596">다음 예제에서는 다음 상태 검사 메서드 서명을 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-596">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="35ef5-597">이전 서명은 `ExampleHealthCheck`가 상태 검사 프로브 로직을 처리하기 위해 추가 데이터가 필요함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-597">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="35ef5-598">상태 검사가 확장 메서드를 사용하여 등록되면 데이터는 상태 검사 인스턴스를 만드는 데 사용된 대리자에게 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-598">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="35ef5-599">다음 예제에서 호출자는 선택 사항을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-599">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="35ef5-600">상태 검사 이름(`name`).</span><span class="sxs-lookup"><span data-stu-id="35ef5-600">health check name (`name`).</span></span> <span data-ttu-id="35ef5-601">`null`인 경우 `example_health_check`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-601">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="35ef5-602">상태 검사를 위한 문자열 데이터 요소입니다(`data1`).</span><span class="sxs-lookup"><span data-stu-id="35ef5-602">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="35ef5-603">상태 검사를 위한 정수 데이터 요소입니다(`data2`).</span><span class="sxs-lookup"><span data-stu-id="35ef5-603">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="35ef5-604">`null`인 경우 `1`이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-604">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="35ef5-605">실패 상태입니다(<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="35ef5-605">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="35ef5-606">기본값은 `null`입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-606">The default is `null`.</span></span> <span data-ttu-id="35ef5-607">`null`인 경우 오류 상태는 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)로 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-607">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="35ef5-608">태그(`IEnumerable<string>`)입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-608">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="35ef5-609">상태 검사 게시자</span><span class="sxs-lookup"><span data-stu-id="35ef5-609">Health Check Publisher</span></span>

<span data-ttu-id="35ef5-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>가 서비스 컨테이너에 추가되면 상태 검사 시스템이 주기적으로 상태 검사를 실행하고 그 결과로 `PublishAsync`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-610">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="35ef5-611">이는 각 프로세스가 상태를 결정하기 위해 주기적으로 모니터링 시스템을 호출하도록 하는 푸시 기반 상태 모니터링 시스템 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-611">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="35ef5-612"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인터페이스는 단일 메서드를 가집니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-612">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="35ef5-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>를 사용하여 다음을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="35ef5-614"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: 앱이 시작된 후 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인스턴스를 시작하기 전에 적용되는 초기 지연입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-614"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="35ef5-615">지연은 시작 시 한 번 적용되며 후속 반복에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-615">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="35ef5-616">기본값은 5초입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-616">The default value is five seconds.</span></span>
* <span data-ttu-id="35ef5-617"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 실행 기간입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-617"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="35ef5-618">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-618">The default value is 30 seconds.</span></span>
* <span data-ttu-id="35ef5-619"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>가 `null`(기본값)인 경우 상태 검사 게시자 서비스는 등록된 상태 검사를 모두 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-619"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="35ef5-620">상태 검사 하위 집합을 실행하려면 검사 세트를 필터링하는 함수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-620">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="35ef5-621">조건자는 기간마다 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-621">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="35ef5-622"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: 모든 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인스턴스에 대한 상태 검사 실행의 시간 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-622"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="35ef5-623">시간 제한 없이 실행하려면 <xref:System.Threading.Timeout.InfiniteTimeSpan>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-623">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="35ef5-624">기본값은 30초입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-624">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="35ef5-625">ASP.NET Core 2.2 릴리스에서 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현은 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> 설정을 적용하지 않으며, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-625">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="35ef5-626">이 문제는 ASP.NET Core 3.0에서 해결되었습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-626">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="35ef5-627">샘플 앱에서 `ReadinessPublisher`는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-627">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="35ef5-628">상태 검사 상태는 다음의 로그 수준에서 각 검사에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-628">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="35ef5-629">상태 검사 상태가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>인 경우 정보(<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>)</span><span class="sxs-lookup"><span data-stu-id="35ef5-629">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="35ef5-630">상태가 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> 또는 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>인 경우 오류(<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>)</span><span class="sxs-lookup"><span data-stu-id="35ef5-630">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="35ef5-631">샘플 앱의 `LivenessProbeStartup` 예제에서 `StartupHostedService` 준비 검사에는 2초 시작 지연이 있고 30초마다 검사가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-631">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="35ef5-632"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 구현을 활성화하기 위해 샘플은 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에서 `ReadinessPublisher`를 싱글톤 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-632">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="35ef5-633">다음 해결 방법에서는 하나 이상의 다른 호스트 서비스가 이미 앱에 추가되었을 때 서비스 컨테이너에 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 인스턴스를 추가하는 것을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-633">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="35ef5-634">ASP.NET Core 3.0에는 이 해결 방법이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-634">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> <span data-ttu-id="35ef5-635">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 [Application Insights](/azure/application-insights/app-insights-overview)를 포함하여 몇몇 시스템에 대한 게시자를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-635">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="35ef5-636">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)는 Microsoft에서 유지 관리하거나 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-636">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="35ef5-637">MapWhen으로 상태 검사 제한</span><span class="sxs-lookup"><span data-stu-id="35ef5-637">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="35ef5-638"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>을 사용하여 상태 검사 엔드포인트에 대한 요청 파이프라인을 조건부로 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-638">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="35ef5-639">다음 예제에서 `MapWhen`은 `api/HealthCheck` 엔드포인트에 대한 GET 요청을 받는 경우 상태 검사 미들웨어를 활성화하도록 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="35ef5-639">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="35ef5-640">자세한 내용은 <xref:fundamentals/middleware/index#use-run-and-map>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="35ef5-640">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
