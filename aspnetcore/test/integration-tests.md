---
<span data-ttu-id="b8d88-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-102">'Blazor'</span></span>
- <span data-ttu-id="b8d88-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-103">'Identity'</span></span>
- <span data-ttu-id="b8d88-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-105">'Razor'</span></span>
- <span data-ttu-id="b8d88-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-106">'SignalR' uid:</span></span> 

---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="b8d88-107">ASP.NET Core의 통합 테스트</span><span class="sxs-lookup"><span data-stu-id="b8d88-107">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="b8d88-108">작성자: [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/) 및 [Jos van der Til](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="b8d88-108">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b8d88-109">통합 테스트는 앱의 구성 요소가 데이터베이스, 파일 시스템 및 네트워크를 비롯하여 앱의 지원 인프라를 포함하는 수준에서 올바르게 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-109">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="b8d88-110">ASP.NET Core는 테스트 웹 호스트 및 메모리 내 테스트 서버에서 단위 테스트 프레임워크를 사용하는 통합 테스트를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-110">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="b8d88-111">이 항목에서는 단위 테스트에 대한 기본적인 지식이 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-111">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="b8d88-112">테스트 개념을 잘 모르는 경우 .[.NET Core 및 .NET Standard의 단위 테스트](/dotnet/core/testing/) 항목 및 연결된 콘텐츠를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-112">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="b8d88-113">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b8d88-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b8d88-114">샘플 앱은 Razor Pages 앱이며 Razor Pages를 기본적으로 이해하고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-114">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="b8d88-115">Razor Pages에 익숙하지 않은 경우 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-115">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="b8d88-116">[Razor Pages 소개](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="b8d88-116">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="b8d88-117">[Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="b8d88-117">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="b8d88-118">[Razor Pages 단위 테스트](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="b8d88-118">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="b8d88-119">SPA 테스트의 경우 브라우저를 자동화할 수 있는 [Selenium](https://www.seleniumhq.org/)과 같은 도구가 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-119">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="b8d88-120">통합 테스트 소개</span><span class="sxs-lookup"><span data-stu-id="b8d88-120">Introduction to integration tests</span></span>

<span data-ttu-id="b8d88-121">통합 테스트는 [단위 테스트](/dotnet/core/testing/)보다 광범위한 수준에서 앱의 구성 요소를 평가합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-121">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="b8d88-122">단위 테스트는 개별 클래스 메서드와 같은 격리된 소프트웨어 구성 요소를 테스트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-122">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="b8d88-123">통합 테스트는 두 개 이상의 앱 구성 요소가 함께 작동하여 요청을 완전히 처리하는 데 필요한 모든 구성 요소를 포함하여 예상되는 결과를 생성하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-123">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="b8d88-124">이러한 광범위한 테스트는 다음과 같은 구성 요소를 포함하여 앱의 인프라 및 전체 프레임워크를 테스트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-124">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="b8d88-125">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="b8d88-125">Database</span></span>
* <span data-ttu-id="b8d88-126">파일 시스템</span><span class="sxs-lookup"><span data-stu-id="b8d88-126">File system</span></span>
* <span data-ttu-id="b8d88-127">네트워크 어플라이언스</span><span class="sxs-lookup"><span data-stu-id="b8d88-127">Network appliances</span></span>
* <span data-ttu-id="b8d88-128">요청-응답 파이프라인</span><span class="sxs-lookup"><span data-stu-id="b8d88-128">Request-response pipeline</span></span>

<span data-ttu-id="b8d88-129">단위 테스트는 인프라 구성 요소 대신 *가짜* 또는 *모의 개체*로 알려져 있는 제작된 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-129">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="b8d88-130">단위 테스트와 달리, 통합 테스트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-130">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="b8d88-131">앱이 프로덕션 환경에서 사용하는 실제 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-131">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="b8d88-132">더 많은 코드와 데이터 처리가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-132">Require more code and data processing.</span></span>
* <span data-ttu-id="b8d88-133">실행하는 데 시간이 더 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-133">Take longer to run.</span></span>

<span data-ttu-id="b8d88-134">따라서 통합 테스트 사용은 가장 중요한 인프라 시나리오로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-134">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="b8d88-135">단위 테스트 또는 통합 테스트를 사용하여 동작을 테스트할 수 있는 경우 단위 테스트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-135">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="b8d88-136">데이터베이스 및 파일 시스템을 사용하는 데이터 및 파일 액세스의 가능한 모든 데이터 순열에 대해 통합 테스트를 작성하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-136">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="b8d88-137">앱에서 데이터베이스 및 파일 시스템과 상호 작용하는 위치 수에 관계없이, 주요 읽기, 쓰기, 업데이트 및 삭제 통합 테스트 세트를 통해 일반적으로 데이터베이스 및 파일 시스템 구성 요소를 적절하게 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-137">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="b8d88-138">이러한 구성 요소와 상호 작용하는 메서드 논리의 루틴 테스트를 위해 단위 테스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-138">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="b8d88-139">단위 테스트에서 인프라 가짜/모의 시험을 사용하면 테스트 실행 속도가 더 빨라집니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-139">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="b8d88-140">통합 테스트를 논의할 때 테스트된 프로젝트를 주로 *테스트 중인 시스템* 또는 간단히 "SUT"라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-140">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="b8d88-141">*이 항목 전체에서 테스트된 ASP.NET Core 앱을 나타내기 위해 "SUT"를 사용합니다.*</span><span class="sxs-lookup"><span data-stu-id="b8d88-141">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="b8d88-142">ASP.NET Core의 통합 테스트</span><span class="sxs-lookup"><span data-stu-id="b8d88-142">ASP.NET Core integration tests</span></span>

<span data-ttu-id="b8d88-143">ASP.NET Core의 통합 테스트에는 다음이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-143">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="b8d88-144">테스트 프로젝트는 테스트를 포함하고 실행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-144">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="b8d88-145">테스트 프로젝트에는 SUT에 대한 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-145">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="b8d88-146">테스트 프로젝트는 SUT에 대한 테스트 웹 호스트를 만들고 테스트 서버 클라이언트를 사용하여 SUT에서 요청 및 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-146">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="b8d88-147">Test Runner는 테스트를 실행하고 테스트 결과를 보고하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-147">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="b8d88-148">통합 테스트는 일반적인 *정렬*, *실행*및 *어설션* 테스트 단계를 포함하는 이벤트 시퀀스를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-148">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="b8d88-149">SUT의 웹 호스트가 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-149">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="b8d88-150">앱에 요청을 제출하는 테스트 서버 클라이언트가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-150">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="b8d88-151">*정렬* 테스트 단계가 실행됩니다. 테스트 앱은 요청을 준비합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-151">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="b8d88-152">*실행* 테스트 단계가 실행됩니다. 클라이언트는 요청을 제출하고 응답을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-152">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="b8d88-153">*어설션* 테스트 단계가 실행됩니다. *실제* 응답은 *예상* 응답에 따라 *성공* 또는 *실패*로 검증됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-153">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="b8d88-154">모든 테스트가 실행될 때까지 프로세스가 계속됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-154">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="b8d88-155">테스트 결과가 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-155">The test results are reported.</span></span>

<span data-ttu-id="b8d88-156">일반적으로 테스트 웹 호스트는 테스트 실행을 위해 앱의 일반 웹 호스트와는 다르게 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-156">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="b8d88-157">예를 들어, 다른 데이터베이스 또는 다른 앱 설정을 테스트에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-157">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="b8d88-158">테스트 웹 호스트 및 메모리 내 테스트 서버([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver))와 같은 인프라 구성 요소는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지에서 제공 및 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-158">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="b8d88-159">이 패키지를 사용하면 테스트 생성 및 실행이 간소화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-159">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="b8d88-160">`Microsoft.AspNetCore.Mvc.Testing` 패키지는 다음 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-160">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="b8d88-161">SUT의 종속성 파일(*deps*)을 테스트 프로젝트의 *bin* 디렉터리로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-161">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="b8d88-162">테스트를 실행하면 고정 파일 및 페이지/뷰를 찾을 수 있도록 [콘텐츠 루트](xref:fundamentals/index#content-root)를 SUT의 프로젝트 루트로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-162">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="b8d88-163">`TestServer`에서 SUT 부트스트랩을 간소화하기 위해 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 클래스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-163">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="b8d88-164">[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 설명서에서는 테스트를 실행하는 방법에 대한 자세한 지침과 테스트 및 테스트 클래스의 이름을 설정하는 방법에 대한 권장 사항을 제공하고 테스트 프로젝트 및 Test Runner를 설정하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-164">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="b8d88-165">앱용 테스트 프로젝트를 만들 때 다른 프로젝트에 대한 통합 테스트에서 단위 테스트를 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-165">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="b8d88-166">이렇게 하면 인프라 테스트 구성 요소가 실수로 단위 테스트에 포함되지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-166">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="b8d88-167">또한 단위 및 통합 테스트를 분리하면 실행되는 테스트 세트를 제어할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-167">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="b8d88-168">Razor Pages 앱과 MVC 앱의 테스트 구성 간에는 차이가 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-168">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="b8d88-169">유일한 차이점은 테스트의 이름을 지정하는 방법 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-169">The only difference is in how the tests are named.</span></span> <span data-ttu-id="b8d88-170">Razor Pages 앱에서 페이지 엔드포인트의 테스트 이름은 일반적으로 페이지 모델 클래스의 이름을 따서 지정합니다(예: 인덱스 페이지에 대한 구성 요소 통합을 테스트하려는 경우 `IndexPageTests`).</span><span class="sxs-lookup"><span data-stu-id="b8d88-170">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="b8d88-171">MVC 앱에서 테스트는 일반적으로 컨트롤러 클래스로 구성되며 해당 이름은 테스트하는 컨트롤러의 이름을 따서 지정합니다(예: 홈 컨트롤러에 대한 구성 요소 통합을 테스트하려는 경우 `HomeControllerTests`).</span><span class="sxs-lookup"><span data-stu-id="b8d88-171">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="b8d88-172">테스트 앱 필수 조건</span><span class="sxs-lookup"><span data-stu-id="b8d88-172">Test app prerequisites</span></span>

<span data-ttu-id="b8d88-173">테스트 프로젝트는 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-173">The test project must:</span></span>

* <span data-ttu-id="b8d88-174">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-174">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="b8d88-175">프로젝트 파일(`<Project Sdk="Microsoft.NET.Sdk.Web">`)에 웹 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-175">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="b8d88-176">이러한 필수 구성 요소는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)에서 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-176">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="b8d88-177">*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-177">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="b8d88-178">샘플 앱은 [xUnit](https://xunit.github.io/) 테스트 프레임워크 및 [AngleSharp](https://anglesharp.github.io/) 파서 라이브러리를 사용하므로 샘플 앱은 다음을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-178">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="b8d88-179">xunit</span><span class="sxs-lookup"><span data-stu-id="b8d88-179">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="b8d88-180">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="b8d88-180">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="b8d88-181">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="b8d88-181">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="b8d88-182">Entity Framework Core도 테스트에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-182">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="b8d88-183">앱 참조:</span><span class="sxs-lookup"><span data-stu-id="b8d88-183">The app references:</span></span>

* [<span data-ttu-id="b8d88-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="b8d88-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* <span data-ttu-id="b8d88-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="b8d88-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span></span>
* [<span data-ttu-id="b8d88-186">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="b8d88-186">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="b8d88-187">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="b8d88-187">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="b8d88-188">Microsoft.EntityFrameworkCore.Tools</span><span class="sxs-lookup"><span data-stu-id="b8d88-188">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="b8d88-189">SUT 환경</span><span class="sxs-lookup"><span data-stu-id="b8d88-189">SUT environment</span></span>

<span data-ttu-id="b8d88-190">SUT의 [환경](xref:fundamentals/environments)이 설정되지 않은 경우 환경은 기본적으로 Development입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-190">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="b8d88-191">기본 WebApplicationFactory를 사용하는 기본 테스트</span><span class="sxs-lookup"><span data-stu-id="b8d88-191">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="b8d88-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)는 통합 테스트에 대한 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="b8d88-193">`TEntryPoint`는 SUT의 진입점 클래스로, 일반적으로 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-193">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="b8d88-194">테스트 클래스는 *클래스 픽스쳐* 인터페이스([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))를 구현하여 클래스가 테스트를 포함하고 클래스의 테스트에서 공유 개체 인스턴스를 제공함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-194">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="b8d88-195">다음 테스트 클래스 `BasicTests`에서는 `WebApplicationFactory`를 사용하여 SUT를 부트스트랩하고 [HttpClient](/dotnet/api/system.net.http.httpclient)를 테스트 메서드 `Get_EndpointsReturnSuccessAndCorrectContentType`에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-195">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="b8d88-196">메서드는 응답 상태 코드가 성공(200-299 범위의 상태 코드)인지 `Content-Type` 헤더가 여러 앱 페이지에 대해 `text/html; charset=utf-8`인지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-196">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="b8d88-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)는 자동으로 리디렉션을 따르고 쿠키를 처리하는 `HttpClient`의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="b8d88-198">기본적으로 [GDPR 동의 정책](xref:security/gdpr)을 사용하도록 설정한 경우에는 필요하지 않은 쿠키가 요청에서 유지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-198">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="b8d88-199">TempData 공급자가 사용하는 쿠키와 같이 필수가 아닌 쿠키를 유지하려면 테스트에서 필수로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-199">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="b8d88-200">쿠키를 필수로 표시하는 방법에 대한 지침은 [필수 쿠키](xref:security/gdpr#essential-cookies)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-200">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="b8d88-201">WebApplicationFactory 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="b8d88-201">Customize WebApplicationFactory</span></span>

<span data-ttu-id="b8d88-202">웹 호스트 구성은 하나 이상의 사용자 지정 팩터리를 만들기 위해 `WebApplicationFactory`에서 상속하여 테스트 클래스와는 별개로 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-202">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="b8d88-203">`WebApplicationFactory`에서 상속하고 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-203">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="b8d88-204">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)는 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)를 사용하여 서비스 컬렉션을 구성할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-204">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="b8d88-205">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)의 데이터베이스 시드는 `InitializeDbForTests` 메서드에서 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-205">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="b8d88-206">이 메서드는 [통합 테스트 샘플: 테스트 앱 조직](#test-app-organization) 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-206">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="b8d88-207">SUT의 데이터베이스 컨텍스트는 `Startup.ConfigureServices` 메서드에 등록되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-207">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b8d88-208">테스트 앱의 `builder.ConfigureServices` 콜백은 앱의 `Startup.ConfigureServices` 코드가 실행된 *후*에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-208">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="b8d88-209">실행 순서는 ASP.NET Core 3.0의 릴리스를 포함하는 [일반 호스트](xref:fundamentals/host/generic-host)의 호환성이 손상되는 변경에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-209">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="b8d88-210">앱의 데이터베이스와는 다른 데이터베이스를 테스트에 사용하려면 앱의 데이터베이스 컨텍스트를 `builder.ConfigureServices`에서 바꾸어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-210">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="b8d88-211">여전히 [웹 호스트](xref:fundamentals/host/web-host)를 사용하는 SUT의 경우, SUT의 `Startup.ConfigureServices` 코드 이전에 테스트 앱의 `builder.ConfigureServices` 콜백이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-211">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="b8d88-212">테스트 앱의 `builder.ConfigureTestServices` 콜백은 *이후에* 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-212">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

   <span data-ttu-id="b8d88-213">샘플 앱은 데이터베이스 컨텍스트에 대한 서비스 설명자를 찾고, 이 설명자를 사용하여 서비스 등록을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-213">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="b8d88-214">그런 다음, 팩터리는 테스트를 위해 메모리 내 데이터베이스를 사용하는 새 `ApplicationDbContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-214">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="b8d88-215">메모리 내 데이터베이스가 아닌 다른 데이터베이스에 연결하려면 `UseInMemoryDatabase` 호출을 변경하여 컨텍스트를 다른 데이터베이스에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-215">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="b8d88-216">SQL Server 테스트 데이터베이스를 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-216">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="b8d88-217">프로젝트 파일에서 [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-217">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="b8d88-218">데이터베이스에 대한 연결 문자열을 사용하여 `UseSqlServer`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-218">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="b8d88-219">테스트 클래스에서 사용자 지정 `CustomWebApplicationFactory`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-219">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="b8d88-220">다음 예제에서는 `IndexPageTests` 클래스에서 팩터리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-220">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="b8d88-221">샘플 앱 클라이언트는 `HttpClient`의 다음 리디렉션을 방지하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-221">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="b8d88-222">[모의 인증](#mock-authentication) 섹션 뒷부분에 설명된 것처럼 이를 통해 테스트는 앱의 첫 번째 응답 결과를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-222">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="b8d88-223">첫 번째 응답은 `Location` 헤더를 사용하는 이러한 테스트 대부분에서 리디렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-223">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="b8d88-224">일반적인 테스트는 `HttpClient` 및 도우미 메서드를 사용하여 요청 및 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-224">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="b8d88-225">SUT에 대한 POST 요청은 앱의 [데이터 보호 위조 방지 시스템](xref:security/data-protection/introduction)에서 자동으로 수행하는 위조 방지 검사를 충족해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-225">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="b8d88-226">테스트의 POST 요청을 정렬하기 위해 테스트 앱은 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-226">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="b8d88-227">페이지에 대한 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-227">Make a request for the page.</span></span>
1. <span data-ttu-id="b8d88-228">위조 방지 쿠키를 구문 분석하고 응답에서 유효성 검사 토큰을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-228">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="b8d88-229">위조 방지 쿠키 및 요청 유효성 검사 토큰을 사용하여 POST 요청을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-229">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="b8d88-230">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)의 `SendAsync` 도우미 확장 메서드(*Helpers/HttpClientExtensions.cs*) 및 `GetDocumentAsync` 도우미 메서드(*Helpers/HtmlHelpers.cs*)는 [AngleSharp](https://anglesharp.github.io/) 파서를 사용하여 다음 메서드를 사용한 위조 방지 확인을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-230">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="b8d88-231">`GetDocumentAsync`: [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)를 수신하고 `IHtmlDocument`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-231">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="b8d88-232">`GetDocumentAsync`는 원본 `HttpResponseMessage`에 따라 *가상 응답*을 준비하는 팩터리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-232">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="b8d88-233">자세한 내용은 [AngleSharp 설명서](https://github.com/AngleSharp/AngleSharp#documentation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-233">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="b8d88-234">`HttpClient`에 대한 `SendAsync` 확장 메서드는 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)를 작성하고 [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)를 호출하여 요청을 SUT에 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-234">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="b8d88-235">`SendAsync`에 대한 오버로드는 HTML 양식(`IHtmlFormElement`) 및 다음을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-235">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="b8d88-236">양식(`IHtmlElement`)의 제출 단추</span><span class="sxs-lookup"><span data-stu-id="b8d88-236">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="b8d88-237">양식 값 컬렉션(`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="b8d88-237">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="b8d88-238">제출 단추(`IHtmlElement`) 및 양식 값(`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="b8d88-238">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="b8d88-239">[AngleSharp](https://anglesharp.github.io/)는 이 항목 및 샘플 앱에서 데모용으로 사용되는 타사 구문 분석 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-239">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="b8d88-240">AngleSharp는 ASP.NET Core 앱의 통합 테스트에 대해 지원되지 않거나 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-240">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="b8d88-241">[HAP(Html Agility Pack)](https://html-agility-pack.net/)와 같은 기타 파서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-241">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="b8d88-242">또 다른 방법은 위조 방지 시스템의 요청 확인 토큰과 위조 방지 쿠키를 직접 처리하는 코드를 작성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-242">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="b8d88-243">WithWebHostBuilder를 사용하여 클라이언트 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="b8d88-243">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="b8d88-244">테스트 메서드 내에서 추가 구성이 필요한 경우 [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)는 구성에서 추가로 사용자 지정되는 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)를 사용하여 새 `WebApplicationFactory`를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-244">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="b8d88-245">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)의 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 테스트 메서드는 `WithWebHostBuilder`의 사용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-245">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="b8d88-246">이 테스트는 SUT에서 양식 제출을 트리거하여 데이터베이스에서 레코드 삭제를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-246">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="b8d88-247">`IndexPageTests` 클래스의 다른 테스트는 데이터베이스의 모든 레코드를 삭제하는 작업을 수행하고 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 메서드 이전에 실행될 수 있기 때문에, 이 테스트 메서드에서 데이터베이스가 다시 시드되어 SUT가 삭제할 레코드가 존재하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-247">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="b8d88-248">SUT에서 `messages` 양식의 첫 번째 삭제 단추를 선택하면 SUT에 대한 요청에서 시뮬레이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-248">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="b8d88-249">클라이언트 옵션</span><span class="sxs-lookup"><span data-stu-id="b8d88-249">Client options</span></span>

<span data-ttu-id="b8d88-250">다음 표에서는 `HttpClient` 인스턴스를 만들 때 사용할 수 있는 기본 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-250">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="b8d88-251">옵션</span><span class="sxs-lookup"><span data-stu-id="b8d88-251">Option</span></span> | <span data-ttu-id="b8d88-252">설명</span><span class="sxs-lookup"><span data-stu-id="b8d88-252">Description</span></span> | <span data-ttu-id="b8d88-253">기본값</span><span class="sxs-lookup"><span data-stu-id="b8d88-253">Default</span></span> |
| ---
<span data-ttu-id="b8d88-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-255">'Blazor'</span></span>
- <span data-ttu-id="b8d88-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-256">'Identity'</span></span>
- <span data-ttu-id="b8d88-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-258">'Razor'</span></span>
- <span data-ttu-id="b8d88-259">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-259">'SignalR' uid:</span></span> 

<span data-ttu-id="b8d88-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-261">'Blazor'</span></span>
- <span data-ttu-id="b8d88-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-262">'Identity'</span></span>
- <span data-ttu-id="b8d88-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-264">'Razor'</span></span>
- <span data-ttu-id="b8d88-265">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-267">'Blazor'</span></span>
- <span data-ttu-id="b8d88-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-268">'Identity'</span></span>
- <span data-ttu-id="b8d88-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-270">'Razor'</span></span>
- <span data-ttu-id="b8d88-271">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-273">'Blazor'</span></span>
- <span data-ttu-id="b8d88-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-274">'Identity'</span></span>
- <span data-ttu-id="b8d88-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-276">'Razor'</span></span>
- <span data-ttu-id="b8d88-277">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-277">'SignalR' uid:</span></span> 

<span data-ttu-id="b8d88-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-279">'Blazor'</span></span>
- <span data-ttu-id="b8d88-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-280">'Identity'</span></span>
- <span data-ttu-id="b8d88-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-282">'Razor'</span></span>
- <span data-ttu-id="b8d88-283">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-283">'SignalR' uid:</span></span> 

<span data-ttu-id="b8d88-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | `HttpClient` 인스턴스가 자동으로 리디렉션 응답을 따라야 하는지 여부를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="b8d88-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | `HttpClient` 인스턴스의 기준 주소를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="b8d88-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | `HttpClient` 인스턴스에서 쿠키를 처리할지 여부를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="b8d88-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | `HttpClient` 인스턴스에서 따라야 하는 리디렉션 응답의 최대 수를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="b8d88-288">| 7 |</span><span class="sxs-lookup"><span data-stu-id="b8d88-288">| 7 |</span></span>

<span data-ttu-id="b8d88-289">`WebApplicationFactoryClientOptions` 클래스를 만들고 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 메서드에 전달합니다(기본값은 코드 예제에 표시됨).</span><span class="sxs-lookup"><span data-stu-id="b8d88-289">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="b8d88-290">모의 서비스 주입</span><span class="sxs-lookup"><span data-stu-id="b8d88-290">Inject mock services</span></span>

<span data-ttu-id="b8d88-291">호스트 작성기에서 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)에 대한 호출을 사용하여 테스트에서 서비스를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-291">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="b8d88-292">**모의 서비스를 주입하려면 SUT에 `Startup.ConfigureServices` 메서드를 포함하는 `Startup` 클래스가 있어야 합니다.**</span><span class="sxs-lookup"><span data-stu-id="b8d88-292">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="b8d88-293">샘플 SUT에는 따옴표를 반환하는 범위 지정 서비스가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-293">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="b8d88-294">인덱스 페이지를 요청하면 따옴표가 인덱스 페이지의 숨겨진 필드에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-294">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="b8d88-295">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8d88-295">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="b8d88-296">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8d88-296">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="b8d88-297">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8d88-297">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="b8d88-298">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8d88-298">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="b8d88-299">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8d88-299">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="b8d88-300">다음 태그는 SUT 앱이 실행될 때 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-300">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="b8d88-301">통합 테스트에서 서비스 및 따옴표 주입을 테스트하기 위해, 테스트를 통해 모의 서비스가 SUT에 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-301">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="b8d88-302">모의 서비스는 앱의 `QuoteService`를 `TestQuoteService`이라는 테스트 앱에서 제공하는 서비스로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-302">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="b8d88-303">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8d88-303">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="b8d88-304">`ConfigureTestServices`가 호출되고 범위 지정 서비스가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-304">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="b8d88-305">테스트 실행 중에 생성된 태그는 `TestQuoteService`에서 제공하는 따옴표 텍스트를 반영하므로 어설션은 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-305">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="b8d88-306">모의 인증</span><span class="sxs-lookup"><span data-stu-id="b8d88-306">Mock authentication</span></span>

<span data-ttu-id="b8d88-307">`AuthTests` 클래스의 테스트는 보안 엔드포인트가 다음을 수행하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-307">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="b8d88-308">인증되지 않은 사용자를 앱의 로그인 페이지로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-308">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="b8d88-309">인증된 사용자에 대한 콘텐츠를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-309">Returns content for an authenticated user.</span></span>

<span data-ttu-id="b8d88-310">SUT에서 `/SecurePage` 페이지는 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 규칙을 사용하여 페이지에 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-310">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="b8d88-311">자세한 내용은 [Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-311">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="b8d88-312">`Get_SecurePageRedirectsAnUnauthenticatedUser` 테스트에서 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)는 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)를 `false`로 설정하여 리디렉션을 허용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-312">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="b8d88-313">클라이언트에서 리디렉션을 따르도록 허용하지 않으면 다음 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-313">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="b8d88-314">SUT에서 반환된 상태 코드는 로그인 페이지로 리디렉션된 후의 최종 상태 코드([HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode))가 아니라 예상된 [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) 결과에 따라 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-314">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="b8d88-315">응답 헤더의 `Location` 헤더 값을 검토하여 `Location` 헤더가 없는 마지막 로그인 페이지 응답이 아니라 `http://localhost/Identity/Account/Login`으로 시작하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-315">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="b8d88-316">테스트 앱은 인증 및 권한 부여의 측면을 테스트하기 위해 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)에서 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 모의로 시험할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-316">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="b8d88-317">최소 시나리오는 [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-317">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="b8d88-318">`TestAuthHandler`는 인증 체계가 `Test`로 설정된 사용자를 인증하도록 호출됩니다. 여기서는 `AddAuthentication`이 `ConfigureTestServices`에 등록되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-318">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="b8d88-319">`WebApplicationFactoryClientOptions`에 대한 자세한 내용은 [클라이언트 옵션](#client-options) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-319">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="b8d88-320">환경 설정</span><span class="sxs-lookup"><span data-stu-id="b8d88-320">Set the environment</span></span>

<span data-ttu-id="b8d88-321">기본적으로 SUT의 호스트 및 앱 환경은 개발 환경을 사용하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-321">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="b8d88-322">`IHostBuilder`를 사용하여 SUT의 환경을 재정의하는 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-322">To override the SUT's environment when using `IHostBuilder`:</span></span>

* <span data-ttu-id="b8d88-323">`ASPNETCORE_ENVIRONMENT` 환경 변수(예: `Staging`, `Production` 또는 `Testing`과 같은 기타 사용자 지정 값)를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-323">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="b8d88-324">`ASPNETCORE`를 접두사로 사용하는 환경 변수를 읽도록 테스트 앱의 `CreateHostBuilder`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-324">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

<span data-ttu-id="b8d88-325">SUT에서 웹 호스트(`IWebHostBuilder`)를 사용하는 경우 `CreateWebHostBuilder`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-325">If the SUT uses the Web Host (`IWebHostBuilder`), override `CreateWebHostBuilder`:</span></span>

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="b8d88-326">테스트 인프라가 앱 콘텐츠 루트 경로를 유추하는 방법</span><span class="sxs-lookup"><span data-stu-id="b8d88-326">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="b8d88-327">`WebApplicationFactory` 생성자는 `TEntryPoint` 어셈블리 `System.Reflection.Assembly.FullName`와 같은 키가 있는 통합 테스트를 포함하는 어셈블리에서 [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)를 검색하여 앱 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 유추합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-327">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="b8d88-328">올바른 키를 포함하는 특성을 찾을 수 없는 경우 `WebApplicationFactory`는 솔루션 파일( *.sln*) 검색으로 대체되고 `TEntryPoint` 어셈블리 이름을 솔루션 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-328">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="b8d88-329">앱 루트 디렉터리(콘텐츠 루트 경로)는 뷰와 콘텐츠 파일을 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-329">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="b8d88-330">섀도 복사 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="b8d88-330">Disable shadow copying</span></span>

<span data-ttu-id="b8d88-331">섀도 복사를 수행하면 테스트가 출력 디렉터리와는 다른 디렉터리에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-331">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="b8d88-332">테스트가 제대로 작동하려면 섀도 복사를 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-332">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="b8d88-333">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)은 xUnit을 사용하고 올바른 구성 설정에 *xunit.runner.json* 파일을 포함하여 xUnit에 대한 섀도 복사를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-333">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="b8d88-334">자세한 내용은 [JSON으로 xUnit 구성](https://xunit.github.io/docs/configuring-with-json.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-334">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="b8d88-335">다음 콘텐츠를 사용하여 테스트 프로젝트의 루트에 *xunit.runner.json* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-335">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="b8d88-336">개체 삭제</span><span class="sxs-lookup"><span data-stu-id="b8d88-336">Disposal of objects</span></span>

<span data-ttu-id="b8d88-337">`IClassFixture` 구현의 테스트를 실행한 후 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 및 [HttpClient](/dotnet/api/system.net.http.httpclient)는 xUnit이 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)을 삭제하면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-337">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="b8d88-338">개발자가 인스턴스화한 개체를 삭제해야 하는 경우 `IClassFixture` 구현에서 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-338">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="b8d88-339">자세한 내용은 [Dispose 메서드 구현](/dotnet/standard/garbage-collection/implementing-dispose)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-339">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="b8d88-340">통합 테스트 샘플</span><span class="sxs-lookup"><span data-stu-id="b8d88-340">Integration tests sample</span></span>

<span data-ttu-id="b8d88-341">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)은 다음 두 앱으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-341">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="b8d88-342">앱</span><span class="sxs-lookup"><span data-stu-id="b8d88-342">App</span></span> | <span data-ttu-id="b8d88-343">프로젝트 디렉터리</span><span class="sxs-lookup"><span data-stu-id="b8d88-343">Project directory</span></span> | <span data-ttu-id="b8d88-344">설명</span><span class="sxs-lookup"><span data-stu-id="b8d88-344">Description</span></span> |
| --- | ---
<span data-ttu-id="b8d88-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-346">'Blazor'</span></span>
- <span data-ttu-id="b8d88-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-347">'Identity'</span></span>
- <span data-ttu-id="b8d88-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-349">'Razor'</span></span>
- <span data-ttu-id="b8d88-350">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-352">'Blazor'</span></span>
- <span data-ttu-id="b8d88-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-353">'Identity'</span></span>
- <span data-ttu-id="b8d88-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-355">'Razor'</span></span>
- <span data-ttu-id="b8d88-356">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-358">'Blazor'</span></span>
- <span data-ttu-id="b8d88-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-359">'Identity'</span></span>
- <span data-ttu-id="b8d88-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-361">'Razor'</span></span>
- <span data-ttu-id="b8d88-362">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-364">'Blazor'</span></span>
- <span data-ttu-id="b8d88-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-365">'Identity'</span></span>
- <span data-ttu-id="b8d88-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-367">'Razor'</span></span>
- <span data-ttu-id="b8d88-368">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-370">'Blazor'</span></span>
- <span data-ttu-id="b8d88-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-371">'Identity'</span></span>
- <span data-ttu-id="b8d88-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-373">'Razor'</span></span>
- <span data-ttu-id="b8d88-374">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-376">'Blazor'</span></span>
- <span data-ttu-id="b8d88-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-377">'Identity'</span></span>
- <span data-ttu-id="b8d88-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-379">'Razor'</span></span>
- <span data-ttu-id="b8d88-380">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-380">'SignalR' uid:</span></span> 

<span data-ttu-id="b8d88-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-382">'Blazor'</span></span>
- <span data-ttu-id="b8d88-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-383">'Identity'</span></span>
- <span data-ttu-id="b8d88-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-385">'Razor'</span></span>
- <span data-ttu-id="b8d88-386">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-388">'Blazor'</span></span>
- <span data-ttu-id="b8d88-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-389">'Identity'</span></span>
- <span data-ttu-id="b8d88-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-391">'Razor'</span></span>
- <span data-ttu-id="b8d88-392">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-394">'Blazor'</span></span>
- <span data-ttu-id="b8d88-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-395">'Identity'</span></span>
- <span data-ttu-id="b8d88-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-397">'Razor'</span></span>
- <span data-ttu-id="b8d88-398">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-398">'SignalR' uid:</span></span> 

<span data-ttu-id="b8d88-399">------ | | 메시지 앱(SUT) | *src/RazorPagesProject* | 사용자가 메시지를 추가, 삭제, 모두 삭제 및 분석할 수 있도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-399">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="b8d88-400">| | 테스트 앱 | *tests/RazorPagesProject.Tests* | SUT를 통합 테스트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-400">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="b8d88-401">[Visual Studio](https://visualstudio.microsoft.com)와 같은 IDE의 기본 제공 테스트 기능을 사용하여 테스트를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-401">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="b8d88-402">[Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용하는 경우 *tests/RazorPagesProject.Tests* 디렉터리의 명령 프롬프트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-402">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="b8d88-403">메시지 앱(SUT) 조직</span><span class="sxs-lookup"><span data-stu-id="b8d88-403">Message app (SUT) organization</span></span>

<span data-ttu-id="b8d88-404">SUT는 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-404">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="b8d88-405">앱의 인덱스 페이지(*Pages/Index.cshtml* 및 *Pages/Index.cshtml.cs*)는 메시지의 추가, 삭제 및 분석을 제어하기 위한 UI 및 페이지 모델 메서드를 제공합니다(메시지당 평균 단어 수).</span><span class="sxs-lookup"><span data-stu-id="b8d88-405">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="b8d88-406">메시지는 `Id`(키) 및 `Text`(메시지)의 두 가지 속성을 사용하여 `Message` 클래스(*Data/Message.cs*)에서 설명됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-406">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="b8d88-407">`Text` 속성은 필수이며 200자로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-407">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="b8d88-408">메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)를 사용하여 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-408">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="b8d88-409">앱은 데이터베이스 컨텍스트 클래스 `AppDbContext`(*Data/AppDbContext*)에 DAL(데이터 액세스 계층)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-409">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="b8d88-410">앱 시작 시 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-410">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="b8d88-411">앱에는 인증된 사용자만 액세스할 수 있는 `/SecurePage`가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-411">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="b8d88-412">EF 항목 [InMemory로 테스트](/ef/core/miscellaneous/testing/in-memory)에서는 MSTest를 사용하여 테스트에 메모리 내 데이터베이스를 사용하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-412">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="b8d88-413">이 항목에서는 [xUnit](https://xunit.github.io/) 테스트 프레임워크를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-413">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="b8d88-414">여러 테스트 프레임워크의 테스트 개념 및 테스트 구현은 비슷하지만 동일하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-414">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="b8d88-415">앱은 리포지토리 패턴을 사용하지 않고 [UoW(작업 단위) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예가 아니지만 Razor Pages는 이러한 개발 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-415">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="b8d88-416">자세한 내용은 [인프라 지속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 [테스트 컨트롤러 논리](/aspnet/core/mvc/controllers/testing)(샘플에서 리포지토리 패턴 구현)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-416">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="b8d88-417">테스트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="b8d88-417">Test app organization</span></span>

<span data-ttu-id="b8d88-418">테스트 앱은 *tests/RazorPagesProject.Tests* 디렉터리 내에 있는 콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-418">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="b8d88-419">테스트 앱 디렉터리</span><span class="sxs-lookup"><span data-stu-id="b8d88-419">Test app directory</span></span> | <span data-ttu-id="b8d88-420">설명</span><span class="sxs-lookup"><span data-stu-id="b8d88-420">Description</span></span> |
| ---
<span data-ttu-id="b8d88-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-422">'Blazor'</span></span>
- <span data-ttu-id="b8d88-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-423">'Identity'</span></span>
- <span data-ttu-id="b8d88-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-425">'Razor'</span></span>
- <span data-ttu-id="b8d88-426">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-428">'Blazor'</span></span>
- <span data-ttu-id="b8d88-429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-429">'Identity'</span></span>
- <span data-ttu-id="b8d88-430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-430">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-431">'Razor'</span></span>
- <span data-ttu-id="b8d88-432">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-434">'Blazor'</span></span>
- <span data-ttu-id="b8d88-435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-435">'Identity'</span></span>
- <span data-ttu-id="b8d88-436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-436">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-437">'Razor'</span></span>
- <span data-ttu-id="b8d88-438">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-440">'Blazor'</span></span>
- <span data-ttu-id="b8d88-441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-441">'Identity'</span></span>
- <span data-ttu-id="b8d88-442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-442">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-443">'Razor'</span></span>
- <span data-ttu-id="b8d88-444">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-446">'Blazor'</span></span>
- <span data-ttu-id="b8d88-447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-447">'Identity'</span></span>
- <span data-ttu-id="b8d88-448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-448">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-449">'Razor'</span></span>
- <span data-ttu-id="b8d88-450">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-452">'Blazor'</span></span>
- <span data-ttu-id="b8d88-453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-453">'Identity'</span></span>
- <span data-ttu-id="b8d88-454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-454">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-455">'Razor'</span></span>
- <span data-ttu-id="b8d88-456">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-458">'Blazor'</span></span>
- <span data-ttu-id="b8d88-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-459">'Identity'</span></span>
- <span data-ttu-id="b8d88-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-461">'Razor'</span></span>
- <span data-ttu-id="b8d88-462">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-462">'SignalR' uid:</span></span> 

<span data-ttu-id="b8d88-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-464">'Blazor'</span></span>
- <span data-ttu-id="b8d88-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-465">'Identity'</span></span>
- <span data-ttu-id="b8d88-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-467">'Razor'</span></span>
- <span data-ttu-id="b8d88-468">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-470">'Blazor'</span></span>
- <span data-ttu-id="b8d88-471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-471">'Identity'</span></span>
- <span data-ttu-id="b8d88-472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-472">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-473">'Razor'</span></span>
- <span data-ttu-id="b8d88-474">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-476">'Blazor'</span></span>
- <span data-ttu-id="b8d88-477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-477">'Identity'</span></span>
- <span data-ttu-id="b8d88-478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-478">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-479">'Razor'</span></span>
- <span data-ttu-id="b8d88-480">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-480">'SignalR' uid:</span></span> 

<span data-ttu-id="b8d88-481">------ | | *AuthTests* | 다음에 대한 테스트 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-481">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="b8d88-482">인증되지 않은 사용자가 보안 페이지에 액세스</span><span class="sxs-lookup"><span data-stu-id="b8d88-482">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="b8d88-483">모의 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 사용하여 인증된 사용자가 보안 페이지에 액세스</span><span class="sxs-lookup"><span data-stu-id="b8d88-483">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="b8d88-484">GitHub 사용자 프로필을 가져오고 프로필의 사용자 로그인 확인</span><span class="sxs-lookup"><span data-stu-id="b8d88-484">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="b8d88-485">| | *BasicTests* | 라우팅 및 콘텐츠 형식에 대한 테스트 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-485">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="b8d88-486">| | *IntegrationTests* | 사용자 지정 `WebApplicationFactory` 클래스를 사용하여 인덱스 페이지에 대한 통합 테스트를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-486">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="b8d88-487">| | *Helpers/Utilities* | </span><span class="sxs-lookup"><span data-stu-id="b8d88-487">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="b8d88-488">*Utilities.cs*에는 테스트 데이터로 데이터베이스를 시드하는 데 사용되는 `InitializeDbForTests` 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-488">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="b8d88-489">*HtmlHelpers.cs*는 테스트 메서드에서 사용할 AngleSharp `IHtmlDocument`를 반환하기 위한 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-489">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="b8d88-490">*HttpClientExtensions.cs*는 `SendAsync`에 대한 오버로드를 제공하여 SUT에 요청을 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-490">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="b8d88-491">테스트 프레임워크는 [xUnit](https://xunit.github.io/)입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-491">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="b8d88-492">[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)를 포함하는 [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)를 사용하여 통합 테스트가 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-492">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="b8d88-493">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지를 사용하여 테스트 호스트와 테스트 서버를 구성하기 때문에 `TestHost` 및 `TestServer` 패키지에는 테스트 앱의 프로젝트 파일 또는 테스트 앱의 개발자 구성에서 직접 패키지 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-493">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="b8d88-494">**테스트를 위해 데이터베이스 시드**</span><span class="sxs-lookup"><span data-stu-id="b8d88-494">**Seeding the database for testing**</span></span>

<span data-ttu-id="b8d88-495">통합 테스트에서는 일반적으로 테스트 실행 전에 데이터베이스에 작은 데이터 세트가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-495">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="b8d88-496">예를 들어, 삭제 테스트는 데이터베이스 레코드 삭제를 요구하므로 삭제 요청이 성공하려면 데이터베이스에 적어도 하나 이상의 레코드가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-496">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="b8d88-497">샘플 앱은 테스트가 실행될 때 사용할 수 있는 *Utilities.cs*의 세 가지 메시지로 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-497">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="b8d88-498">SUT의 데이터베이스 컨텍스트는 `Startup.ConfigureServices` 메서드에 등록되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-498">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b8d88-499">테스트 앱의 `builder.ConfigureServices` 콜백은 앱의 `Startup.ConfigureServices` 코드가 실행된 *후*에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-499">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="b8d88-500">다른 데이터베이스를 테스트에 사용하려면 앱의 데이터베이스 컨텍스트를 `builder.ConfigureServices`에서 바꾸어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-500">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="b8d88-501">자세한 내용은 [WebApplicationFactory 사용자 지정](#customize-webapplicationfactory) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-501">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

<span data-ttu-id="b8d88-502">여전히 [웹 호스트](xref:fundamentals/host/web-host)를 사용하는 SUT의 경우, SUT의 `Startup.ConfigureServices` 코드 이전에 테스트 앱의 `builder.ConfigureServices` 콜백이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-502">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="b8d88-503">테스트 앱의 `builder.ConfigureTestServices` 콜백은 *이후에* 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-503">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b8d88-504">통합 테스트는 앱의 구성 요소가 데이터베이스, 파일 시스템 및 네트워크를 비롯하여 앱의 지원 인프라를 포함하는 수준에서 올바르게 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-504">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="b8d88-505">ASP.NET Core는 테스트 웹 호스트 및 메모리 내 테스트 서버에서 단위 테스트 프레임워크를 사용하는 통합 테스트를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-505">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="b8d88-506">이 항목에서는 단위 테스트에 대한 기본적인 지식이 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-506">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="b8d88-507">테스트 개념을 잘 모르는 경우 .[.NET Core 및 .NET Standard의 단위 테스트](/dotnet/core/testing/) 항목 및 연결된 콘텐츠를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-507">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="b8d88-508">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b8d88-508">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b8d88-509">샘플 앱은 Razor Pages 앱이며 Razor Pages를 기본적으로 이해하고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-509">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="b8d88-510">Razor Pages에 익숙하지 않은 경우 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-510">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="b8d88-511">[Razor Pages 소개](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="b8d88-511">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="b8d88-512">[Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="b8d88-512">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="b8d88-513">[Razor Pages 단위 테스트](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="b8d88-513">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="b8d88-514">SPA 테스트의 경우 브라우저를 자동화할 수 있는 [Selenium](https://www.seleniumhq.org/)과 같은 도구가 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-514">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="b8d88-515">통합 테스트 소개</span><span class="sxs-lookup"><span data-stu-id="b8d88-515">Introduction to integration tests</span></span>

<span data-ttu-id="b8d88-516">통합 테스트는 [단위 테스트](/dotnet/core/testing/)보다 광범위한 수준에서 앱의 구성 요소를 평가합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-516">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="b8d88-517">단위 테스트는 개별 클래스 메서드와 같은 격리된 소프트웨어 구성 요소를 테스트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-517">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="b8d88-518">통합 테스트는 두 개 이상의 앱 구성 요소가 함께 작동하여 요청을 완전히 처리하는 데 필요한 모든 구성 요소를 포함하여 예상되는 결과를 생성하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-518">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="b8d88-519">이러한 광범위한 테스트는 다음과 같은 구성 요소를 포함하여 앱의 인프라 및 전체 프레임워크를 테스트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-519">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="b8d88-520">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="b8d88-520">Database</span></span>
* <span data-ttu-id="b8d88-521">파일 시스템</span><span class="sxs-lookup"><span data-stu-id="b8d88-521">File system</span></span>
* <span data-ttu-id="b8d88-522">네트워크 어플라이언스</span><span class="sxs-lookup"><span data-stu-id="b8d88-522">Network appliances</span></span>
* <span data-ttu-id="b8d88-523">요청-응답 파이프라인</span><span class="sxs-lookup"><span data-stu-id="b8d88-523">Request-response pipeline</span></span>

<span data-ttu-id="b8d88-524">단위 테스트는 인프라 구성 요소 대신 *가짜* 또는 *모의 개체*로 알려져 있는 제작된 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-524">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="b8d88-525">단위 테스트와 달리, 통합 테스트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-525">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="b8d88-526">앱이 프로덕션 환경에서 사용하는 실제 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-526">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="b8d88-527">더 많은 코드와 데이터 처리가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-527">Require more code and data processing.</span></span>
* <span data-ttu-id="b8d88-528">실행하는 데 시간이 더 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-528">Take longer to run.</span></span>

<span data-ttu-id="b8d88-529">따라서 통합 테스트 사용은 가장 중요한 인프라 시나리오로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-529">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="b8d88-530">단위 테스트 또는 통합 테스트를 사용하여 동작을 테스트할 수 있는 경우 단위 테스트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-530">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="b8d88-531">데이터베이스 및 파일 시스템을 사용하는 데이터 및 파일 액세스의 가능한 모든 데이터 순열에 대해 통합 테스트를 작성하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-531">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="b8d88-532">앱에서 데이터베이스 및 파일 시스템과 상호 작용하는 위치 수에 관계없이, 주요 읽기, 쓰기, 업데이트 및 삭제 통합 테스트 세트를 통해 일반적으로 데이터베이스 및 파일 시스템 구성 요소를 적절하게 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-532">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="b8d88-533">이러한 구성 요소와 상호 작용하는 메서드 논리의 루틴 테스트를 위해 단위 테스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-533">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="b8d88-534">단위 테스트에서 인프라 가짜/모의 시험을 사용하면 테스트 실행 속도가 더 빨라집니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-534">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="b8d88-535">통합 테스트를 논의할 때 테스트된 프로젝트를 주로 *테스트 중인 시스템* 또는 간단히 "SUT"라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-535">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="b8d88-536">*이 항목 전체에서 테스트된 ASP.NET Core 앱을 나타내기 위해 "SUT"를 사용합니다.*</span><span class="sxs-lookup"><span data-stu-id="b8d88-536">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="b8d88-537">ASP.NET Core의 통합 테스트</span><span class="sxs-lookup"><span data-stu-id="b8d88-537">ASP.NET Core integration tests</span></span>

<span data-ttu-id="b8d88-538">ASP.NET Core의 통합 테스트에는 다음이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-538">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="b8d88-539">테스트 프로젝트는 테스트를 포함하고 실행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-539">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="b8d88-540">테스트 프로젝트에는 SUT에 대한 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-540">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="b8d88-541">테스트 프로젝트는 SUT에 대한 테스트 웹 호스트를 만들고 테스트 서버 클라이언트를 사용하여 SUT에서 요청 및 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-541">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="b8d88-542">Test Runner는 테스트를 실행하고 테스트 결과를 보고하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-542">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="b8d88-543">통합 테스트는 일반적인 *정렬*, *실행*및 *어설션* 테스트 단계를 포함하는 이벤트 시퀀스를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-543">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="b8d88-544">SUT의 웹 호스트가 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-544">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="b8d88-545">앱에 요청을 제출하는 테스트 서버 클라이언트가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-545">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="b8d88-546">*정렬* 테스트 단계가 실행됩니다. 테스트 앱은 요청을 준비합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-546">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="b8d88-547">*실행* 테스트 단계가 실행됩니다. 클라이언트는 요청을 제출하고 응답을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-547">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="b8d88-548">*어설션* 테스트 단계가 실행됩니다. *실제* 응답은 *예상* 응답에 따라 *성공* 또는 *실패*로 검증됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-548">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="b8d88-549">모든 테스트가 실행될 때까지 프로세스가 계속됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-549">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="b8d88-550">테스트 결과가 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-550">The test results are reported.</span></span>

<span data-ttu-id="b8d88-551">일반적으로 테스트 웹 호스트는 테스트 실행을 위해 앱의 일반 웹 호스트와는 다르게 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-551">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="b8d88-552">예를 들어, 다른 데이터베이스 또는 다른 앱 설정을 테스트에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-552">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="b8d88-553">테스트 웹 호스트 및 메모리 내 테스트 서버([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver))와 같은 인프라 구성 요소는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지에서 제공 및 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-553">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="b8d88-554">이 패키지를 사용하면 테스트 생성 및 실행이 간소화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-554">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="b8d88-555">`Microsoft.AspNetCore.Mvc.Testing` 패키지는 다음 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-555">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="b8d88-556">SUT의 종속성 파일(*deps*)을 테스트 프로젝트의 *bin* 디렉터리로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-556">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="b8d88-557">테스트를 실행하면 고정 파일 및 페이지/뷰를 찾을 수 있도록 [콘텐츠 루트](xref:fundamentals/index#content-root)를 SUT의 프로젝트 루트로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-557">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="b8d88-558">`TestServer`에서 SUT 부트스트랩을 간소화하기 위해 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 클래스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-558">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="b8d88-559">[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 설명서에서는 테스트를 실행하는 방법에 대한 자세한 지침과 테스트 및 테스트 클래스의 이름을 설정하는 방법에 대한 권장 사항을 제공하고 테스트 프로젝트 및 Test Runner를 설정하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-559">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="b8d88-560">앱용 테스트 프로젝트를 만들 때 다른 프로젝트에 대한 통합 테스트에서 단위 테스트를 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-560">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="b8d88-561">이렇게 하면 인프라 테스트 구성 요소가 실수로 단위 테스트에 포함되지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-561">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="b8d88-562">또한 단위 및 통합 테스트를 분리하면 실행되는 테스트 세트를 제어할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-562">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="b8d88-563">Razor Pages 앱과 MVC 앱의 테스트 구성 간에는 차이가 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-563">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="b8d88-564">유일한 차이점은 테스트의 이름을 지정하는 방법 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-564">The only difference is in how the tests are named.</span></span> <span data-ttu-id="b8d88-565">Razor Pages 앱에서 페이지 엔드포인트의 테스트 이름은 일반적으로 페이지 모델 클래스의 이름을 따서 지정합니다(예: 인덱스 페이지에 대한 구성 요소 통합을 테스트하려는 경우 `IndexPageTests`).</span><span class="sxs-lookup"><span data-stu-id="b8d88-565">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="b8d88-566">MVC 앱에서 테스트는 일반적으로 컨트롤러 클래스로 구성되며 해당 이름은 테스트하는 컨트롤러의 이름을 따서 지정합니다(예: 홈 컨트롤러에 대한 구성 요소 통합을 테스트하려는 경우 `HomeControllerTests`).</span><span class="sxs-lookup"><span data-stu-id="b8d88-566">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="b8d88-567">테스트 앱 필수 조건</span><span class="sxs-lookup"><span data-stu-id="b8d88-567">Test app prerequisites</span></span>

<span data-ttu-id="b8d88-568">테스트 프로젝트는 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-568">The test project must:</span></span>

* <span data-ttu-id="b8d88-569">다음 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-569">Reference the following packages:</span></span>
  * [<span data-ttu-id="b8d88-570">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="b8d88-570">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="b8d88-571">Microsoft.AspNetCore.Mvc.Testing</span><span class="sxs-lookup"><span data-stu-id="b8d88-571">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="b8d88-572">프로젝트 파일(`<Project Sdk="Microsoft.NET.Sdk.Web">`)에 웹 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-572">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="b8d88-573">웹 SDK는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-573">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b8d88-574">이러한 필수 구성 요소는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)에서 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-574">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="b8d88-575">*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-575">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="b8d88-576">샘플 앱은 [xUnit](https://xunit.github.io/) 테스트 프레임워크 및 [AngleSharp](https://anglesharp.github.io/) 파서 라이브러리를 사용하므로 샘플 앱은 다음을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-576">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="b8d88-577">xunit</span><span class="sxs-lookup"><span data-stu-id="b8d88-577">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="b8d88-578">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="b8d88-578">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="b8d88-579">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="b8d88-579">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="b8d88-580">SUT 환경</span><span class="sxs-lookup"><span data-stu-id="b8d88-580">SUT environment</span></span>

<span data-ttu-id="b8d88-581">SUT의 [환경](xref:fundamentals/environments)이 설정되지 않은 경우 환경은 기본적으로 Development입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-581">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="b8d88-582">기본 WebApplicationFactory를 사용하는 기본 테스트</span><span class="sxs-lookup"><span data-stu-id="b8d88-582">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="b8d88-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)는 통합 테스트에 대한 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="b8d88-584">`TEntryPoint`는 SUT의 진입점 클래스로, 일반적으로 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-584">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="b8d88-585">테스트 클래스는 *클래스 픽스쳐* 인터페이스([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))를 구현하여 클래스가 테스트를 포함하고 클래스의 테스트에서 공유 개체 인스턴스를 제공함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-585">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="b8d88-586">다음 테스트 클래스 `BasicTests`에서는 `WebApplicationFactory`를 사용하여 SUT를 부트스트랩하고 [HttpClient](/dotnet/api/system.net.http.httpclient)를 테스트 메서드 `Get_EndpointsReturnSuccessAndCorrectContentType`에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-586">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="b8d88-587">메서드는 응답 상태 코드가 성공(200-299 범위의 상태 코드)인지 `Content-Type` 헤더가 여러 앱 페이지에 대해 `text/html; charset=utf-8`인지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-587">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="b8d88-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)는 자동으로 리디렉션을 따르고 쿠키를 처리하는 `HttpClient`의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="b8d88-589">기본적으로 [GDPR 동의 정책](xref:security/gdpr)을 사용하도록 설정한 경우에는 필요하지 않은 쿠키가 요청에서 유지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-589">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="b8d88-590">TempData 공급자가 사용하는 쿠키와 같이 필수가 아닌 쿠키를 유지하려면 테스트에서 필수로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-590">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="b8d88-591">쿠키를 필수로 표시하는 방법에 대한 지침은 [필수 쿠키](xref:security/gdpr#essential-cookies)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-591">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="b8d88-592">WebApplicationFactory 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="b8d88-592">Customize WebApplicationFactory</span></span>

<span data-ttu-id="b8d88-593">웹 호스트 구성은 하나 이상의 사용자 지정 팩터리를 만들기 위해 `WebApplicationFactory`에서 상속하여 테스트 클래스와는 별개로 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-593">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="b8d88-594">`WebApplicationFactory`에서 상속하고 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-594">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="b8d88-595">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)를 사용하면 앱의 `Startup.ConfigureServices` 이전에 실행되는 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)를 사용하여 서비스 컬렉션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-595">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), which is executed before the app's `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b8d88-596">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)를 사용하면 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)를 사용하여 서비스 컬렉션에서 등록된 서비스를 재정의하고 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-596">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows overriding and modifying registered services in the service collection with [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="b8d88-597">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)의 데이터베이스 시드는 `InitializeDbForTests` 메서드에서 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-597">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="b8d88-598">이 메서드는 [통합 테스트 샘플: 테스트 앱 조직](#test-app-organization) 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-598">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="b8d88-599">테스트 클래스에서 사용자 지정 `CustomWebApplicationFactory`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-599">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="b8d88-600">다음 예제에서는 `IndexPageTests` 클래스에서 팩터리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-600">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="b8d88-601">샘플 앱 클라이언트는 `HttpClient`의 다음 리디렉션을 방지하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-601">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="b8d88-602">[모의 인증](#mock-authentication) 섹션 뒷부분에 설명된 것처럼 이를 통해 테스트는 앱의 첫 번째 응답 결과를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-602">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="b8d88-603">첫 번째 응답은 `Location` 헤더를 사용하는 이러한 테스트 대부분에서 리디렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-603">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="b8d88-604">일반적인 테스트는 `HttpClient` 및 도우미 메서드를 사용하여 요청 및 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-604">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="b8d88-605">SUT에 대한 POST 요청은 앱의 [데이터 보호 위조 방지 시스템](xref:security/data-protection/introduction)에서 자동으로 수행하는 위조 방지 검사를 충족해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-605">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="b8d88-606">테스트의 POST 요청을 정렬하기 위해 테스트 앱은 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-606">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="b8d88-607">페이지에 대한 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-607">Make a request for the page.</span></span>
1. <span data-ttu-id="b8d88-608">위조 방지 쿠키를 구문 분석하고 응답에서 유효성 검사 토큰을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-608">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="b8d88-609">위조 방지 쿠키 및 요청 유효성 검사 토큰을 사용하여 POST 요청을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-609">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="b8d88-610">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)의 `SendAsync` 도우미 확장 메서드(*Helpers/HttpClientExtensions.cs*) 및 `GetDocumentAsync` 도우미 메서드(*Helpers/HtmlHelpers.cs*)는 [AngleSharp](https://anglesharp.github.io/) 파서를 사용하여 다음 메서드를 사용한 위조 방지 확인을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-610">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="b8d88-611">`GetDocumentAsync`: [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)를 수신하고 `IHtmlDocument`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-611">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="b8d88-612">`GetDocumentAsync`는 원본 `HttpResponseMessage`에 따라 *가상 응답*을 준비하는 팩터리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-612">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="b8d88-613">자세한 내용은 [AngleSharp 설명서](https://github.com/AngleSharp/AngleSharp#documentation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-613">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="b8d88-614">`HttpClient`에 대한 `SendAsync` 확장 메서드는 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)를 작성하고 [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)를 호출하여 요청을 SUT에 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-614">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="b8d88-615">`SendAsync`에 대한 오버로드는 HTML 양식(`IHtmlFormElement`) 및 다음을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-615">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="b8d88-616">양식(`IHtmlElement`)의 제출 단추</span><span class="sxs-lookup"><span data-stu-id="b8d88-616">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="b8d88-617">양식 값 컬렉션(`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="b8d88-617">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="b8d88-618">제출 단추(`IHtmlElement`) 및 양식 값(`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="b8d88-618">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="b8d88-619">[AngleSharp](https://anglesharp.github.io/)는 이 항목 및 샘플 앱에서 데모용으로 사용되는 타사 구문 분석 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-619">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="b8d88-620">AngleSharp는 ASP.NET Core 앱의 통합 테스트에 대해 지원되지 않거나 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-620">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="b8d88-621">[HAP(Html Agility Pack)](https://html-agility-pack.net/)와 같은 기타 파서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-621">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="b8d88-622">또 다른 방법은 위조 방지 시스템의 요청 확인 토큰과 위조 방지 쿠키를 직접 처리하는 코드를 작성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-622">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="b8d88-623">WithWebHostBuilder를 사용하여 클라이언트 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="b8d88-623">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="b8d88-624">테스트 메서드 내에서 추가 구성이 필요한 경우 [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)는 구성에서 추가로 사용자 지정되는 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)를 사용하여 새 `WebApplicationFactory`를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-624">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="b8d88-625">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)의 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 테스트 메서드는 `WithWebHostBuilder`의 사용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-625">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="b8d88-626">이 테스트는 SUT에서 양식 제출을 트리거하여 데이터베이스에서 레코드 삭제를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-626">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="b8d88-627">`IndexPageTests` 클래스의 다른 테스트는 데이터베이스의 모든 레코드를 삭제하는 작업을 수행하고 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 메서드 이전에 실행될 수 있기 때문에, 이 테스트 메서드에서 데이터베이스가 다시 시드되어 SUT가 삭제할 레코드가 존재하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-627">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="b8d88-628">SUT에서 `messages` 양식의 첫 번째 삭제 단추를 선택하면 SUT에 대한 요청에서 시뮬레이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-628">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="b8d88-629">클라이언트 옵션</span><span class="sxs-lookup"><span data-stu-id="b8d88-629">Client options</span></span>

<span data-ttu-id="b8d88-630">다음 표에서는 `HttpClient` 인스턴스를 만들 때 사용할 수 있는 기본 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-630">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="b8d88-631">옵션</span><span class="sxs-lookup"><span data-stu-id="b8d88-631">Option</span></span> | <span data-ttu-id="b8d88-632">설명</span><span class="sxs-lookup"><span data-stu-id="b8d88-632">Description</span></span> | <span data-ttu-id="b8d88-633">기본값</span><span class="sxs-lookup"><span data-stu-id="b8d88-633">Default</span></span> |
| ---
<span data-ttu-id="b8d88-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-635">'Blazor'</span></span>
- <span data-ttu-id="b8d88-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-636">'Identity'</span></span>
- <span data-ttu-id="b8d88-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-638">'Razor'</span></span>
- <span data-ttu-id="b8d88-639">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-639">'SignalR' uid:</span></span> 

<span data-ttu-id="b8d88-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-641">'Blazor'</span></span>
- <span data-ttu-id="b8d88-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-642">'Identity'</span></span>
- <span data-ttu-id="b8d88-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-644">'Razor'</span></span>
- <span data-ttu-id="b8d88-645">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-645">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-647">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-647">'Blazor'</span></span>
- <span data-ttu-id="b8d88-648">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-648">'Identity'</span></span>
- <span data-ttu-id="b8d88-649">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-649">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-650">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-650">'Razor'</span></span>
- <span data-ttu-id="b8d88-651">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-651">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-653">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-653">'Blazor'</span></span>
- <span data-ttu-id="b8d88-654">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-654">'Identity'</span></span>
- <span data-ttu-id="b8d88-655">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-655">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-656">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-656">'Razor'</span></span>
- <span data-ttu-id="b8d88-657">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-657">'SignalR' uid:</span></span> 

<span data-ttu-id="b8d88-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-659">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-659">'Blazor'</span></span>
- <span data-ttu-id="b8d88-660">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-660">'Identity'</span></span>
- <span data-ttu-id="b8d88-661">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-661">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-662">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-662">'Razor'</span></span>
- <span data-ttu-id="b8d88-663">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-663">'SignalR' uid:</span></span> 

<span data-ttu-id="b8d88-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | `HttpClient` 인스턴스가 자동으로 리디렉션 응답을 따라야 하는지 여부를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="b8d88-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | `HttpClient` 인스턴스의 기준 주소를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="b8d88-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | `HttpClient` 인스턴스에서 쿠키를 처리할지 여부를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="b8d88-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | `HttpClient` 인스턴스에서 따라야 하는 리디렉션 응답의 최대 수를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="b8d88-668">| 7 |</span><span class="sxs-lookup"><span data-stu-id="b8d88-668">| 7 |</span></span>

<span data-ttu-id="b8d88-669">`WebApplicationFactoryClientOptions` 클래스를 만들고 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 메서드에 전달합니다(기본값은 코드 예제에 표시됨).</span><span class="sxs-lookup"><span data-stu-id="b8d88-669">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="b8d88-670">모의 서비스 주입</span><span class="sxs-lookup"><span data-stu-id="b8d88-670">Inject mock services</span></span>

<span data-ttu-id="b8d88-671">호스트 작성기에서 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)에 대한 호출을 사용하여 테스트에서 서비스를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-671">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="b8d88-672">**모의 서비스를 주입하려면 SUT에 `Startup.ConfigureServices` 메서드를 포함하는 `Startup` 클래스가 있어야 합니다.**</span><span class="sxs-lookup"><span data-stu-id="b8d88-672">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="b8d88-673">샘플 SUT에는 따옴표를 반환하는 범위 지정 서비스가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-673">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="b8d88-674">인덱스 페이지를 요청하면 따옴표가 인덱스 페이지의 숨겨진 필드에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-674">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="b8d88-675">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8d88-675">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="b8d88-676">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8d88-676">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="b8d88-677">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8d88-677">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="b8d88-678">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8d88-678">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="b8d88-679">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8d88-679">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="b8d88-680">다음 태그는 SUT 앱이 실행될 때 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-680">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="b8d88-681">통합 테스트에서 서비스 및 따옴표 주입을 테스트하기 위해, 테스트를 통해 모의 서비스가 SUT에 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-681">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="b8d88-682">모의 서비스는 앱의 `QuoteService`를 `TestQuoteService`이라는 테스트 앱에서 제공하는 서비스로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-682">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="b8d88-683">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="b8d88-683">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="b8d88-684">`ConfigureTestServices`가 호출되고 범위 지정 서비스가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-684">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="b8d88-685">테스트 실행 중에 생성된 태그는 `TestQuoteService`에서 제공하는 따옴표 텍스트를 반영하므로 어설션은 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-685">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="b8d88-686">모의 인증</span><span class="sxs-lookup"><span data-stu-id="b8d88-686">Mock authentication</span></span>

<span data-ttu-id="b8d88-687">`AuthTests` 클래스의 테스트는 보안 엔드포인트가 다음을 수행하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-687">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="b8d88-688">인증되지 않은 사용자를 앱의 로그인 페이지로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-688">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="b8d88-689">인증된 사용자에 대한 콘텐츠를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-689">Returns content for an authenticated user.</span></span>

<span data-ttu-id="b8d88-690">SUT에서 `/SecurePage` 페이지는 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 규칙을 사용하여 페이지에 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-690">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="b8d88-691">자세한 내용은 [Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-691">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="b8d88-692">`Get_SecurePageRedirectsAnUnauthenticatedUser` 테스트에서 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)는 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)를 `false`로 설정하여 리디렉션을 허용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-692">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="b8d88-693">클라이언트에서 리디렉션을 따르도록 허용하지 않으면 다음 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-693">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="b8d88-694">SUT에서 반환된 상태 코드는 로그인 페이지로 리디렉션된 후의 최종 상태 코드([HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode))가 아니라 예상된 [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) 결과에 따라 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-694">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="b8d88-695">응답 헤더의 `Location` 헤더 값을 검토하여 `Location` 헤더가 없는 마지막 로그인 페이지 응답이 아니라 `http://localhost/Identity/Account/Login`으로 시작하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-695">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="b8d88-696">테스트 앱은 인증 및 권한 부여의 측면을 테스트하기 위해 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)에서 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 모의로 시험할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-696">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="b8d88-697">최소 시나리오는 [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-697">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="b8d88-698">`TestAuthHandler`는 인증 체계가 `Test`로 설정된 사용자를 인증하도록 호출됩니다. 여기서는 `AddAuthentication`이 `ConfigureTestServices`에 등록되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-698">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="b8d88-699">`WebApplicationFactoryClientOptions`에 대한 자세한 내용은 [클라이언트 옵션](#client-options) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-699">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="b8d88-700">환경 설정</span><span class="sxs-lookup"><span data-stu-id="b8d88-700">Set the environment</span></span>

<span data-ttu-id="b8d88-701">기본적으로 SUT의 호스트 및 앱 환경은 개발 환경을 사용하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-701">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="b8d88-702">SUT의 환경을 재정의하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-702">To override the SUT's environment:</span></span>

* <span data-ttu-id="b8d88-703">`ASPNETCORE_ENVIRONMENT` 환경 변수(예: `Staging`, `Production` 또는 `Testing`과 같은 기타 사용자 지정 값)를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-703">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="b8d88-704">테스트 앱의 `CreateWebHostBuilder`를 재정의하여 `ASPNETCORE_ENVIRONMENT` 환경 변수를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-704">Override `CreateWebHostBuilder` in the test app to read the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

<span data-ttu-id="b8d88-705">사용자 지정 <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>에서 호스트 작성기에 대해 직접 환경을 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-705">The environment can also be set directly on the host builder in a custom <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>:</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="b8d88-706">테스트 인프라가 앱 콘텐츠 루트 경로를 유추하는 방법</span><span class="sxs-lookup"><span data-stu-id="b8d88-706">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="b8d88-707">`WebApplicationFactory` 생성자는 `TEntryPoint` 어셈블리 `System.Reflection.Assembly.FullName`와 같은 키가 있는 통합 테스트를 포함하는 어셈블리에서 [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)를 검색하여 앱 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 유추합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-707">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="b8d88-708">올바른 키를 포함하는 특성을 찾을 수 없는 경우 `WebApplicationFactory`는 솔루션 파일( *.sln*) 검색으로 대체되고 `TEntryPoint` 어셈블리 이름을 솔루션 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-708">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="b8d88-709">앱 루트 디렉터리(콘텐츠 루트 경로)는 뷰와 콘텐츠 파일을 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-709">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="b8d88-710">섀도 복사 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="b8d88-710">Disable shadow copying</span></span>

<span data-ttu-id="b8d88-711">섀도 복사를 수행하면 테스트가 출력 디렉터리와는 다른 디렉터리에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-711">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="b8d88-712">테스트가 제대로 작동하려면 섀도 복사를 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-712">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="b8d88-713">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)은 xUnit을 사용하고 올바른 구성 설정에 *xunit.runner.json* 파일을 포함하여 xUnit에 대한 섀도 복사를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-713">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="b8d88-714">자세한 내용은 [JSON으로 xUnit 구성](https://xunit.github.io/docs/configuring-with-json.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-714">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="b8d88-715">다음 콘텐츠를 사용하여 테스트 프로젝트의 루트에 *xunit.runner.json* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-715">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="b8d88-716">Visual Studio를 사용하는 경우 파일의 **출력 디렉터리로 복사** 속성을 **항상 복사**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-716">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="b8d88-717">Visual Studio를 사용하지 않는 경우 테스트 앱의 프로젝트 파일에 `Content` 대상을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-717">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="b8d88-718">개체 삭제</span><span class="sxs-lookup"><span data-stu-id="b8d88-718">Disposal of objects</span></span>

<span data-ttu-id="b8d88-719">`IClassFixture` 구현의 테스트를 실행한 후 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 및 [HttpClient](/dotnet/api/system.net.http.httpclient)는 xUnit이 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)을 삭제하면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-719">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="b8d88-720">개발자가 인스턴스화한 개체를 삭제해야 하는 경우 `IClassFixture` 구현에서 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-720">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="b8d88-721">자세한 내용은 [Dispose 메서드 구현](/dotnet/standard/garbage-collection/implementing-dispose)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-721">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="b8d88-722">통합 테스트 샘플</span><span class="sxs-lookup"><span data-stu-id="b8d88-722">Integration tests sample</span></span>

<span data-ttu-id="b8d88-723">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)은 다음 두 앱으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-723">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="b8d88-724">앱</span><span class="sxs-lookup"><span data-stu-id="b8d88-724">App</span></span> | <span data-ttu-id="b8d88-725">프로젝트 디렉터리</span><span class="sxs-lookup"><span data-stu-id="b8d88-725">Project directory</span></span> | <span data-ttu-id="b8d88-726">설명</span><span class="sxs-lookup"><span data-stu-id="b8d88-726">Description</span></span> |
| --- | ---
<span data-ttu-id="b8d88-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-728">'Blazor'</span></span>
- <span data-ttu-id="b8d88-729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-729">'Identity'</span></span>
- <span data-ttu-id="b8d88-730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-730">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-731">'Razor'</span></span>
- <span data-ttu-id="b8d88-732">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-734">'Blazor'</span></span>
- <span data-ttu-id="b8d88-735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-735">'Identity'</span></span>
- <span data-ttu-id="b8d88-736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-736">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-737">'Razor'</span></span>
- <span data-ttu-id="b8d88-738">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-740">'Blazor'</span></span>
- <span data-ttu-id="b8d88-741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-741">'Identity'</span></span>
- <span data-ttu-id="b8d88-742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-742">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-743">'Razor'</span></span>
- <span data-ttu-id="b8d88-744">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-746">'Blazor'</span></span>
- <span data-ttu-id="b8d88-747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-747">'Identity'</span></span>
- <span data-ttu-id="b8d88-748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-748">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-749">'Razor'</span></span>
- <span data-ttu-id="b8d88-750">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-752">'Blazor'</span></span>
- <span data-ttu-id="b8d88-753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-753">'Identity'</span></span>
- <span data-ttu-id="b8d88-754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-754">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-755">'Razor'</span></span>
- <span data-ttu-id="b8d88-756">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-758">'Blazor'</span></span>
- <span data-ttu-id="b8d88-759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-759">'Identity'</span></span>
- <span data-ttu-id="b8d88-760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-760">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-761">'Razor'</span></span>
- <span data-ttu-id="b8d88-762">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-762">'SignalR' uid:</span></span> 

<span data-ttu-id="b8d88-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-764">'Blazor'</span></span>
- <span data-ttu-id="b8d88-765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-765">'Identity'</span></span>
- <span data-ttu-id="b8d88-766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-766">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-767">'Razor'</span></span>
- <span data-ttu-id="b8d88-768">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-768">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-770">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-770">'Blazor'</span></span>
- <span data-ttu-id="b8d88-771">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-771">'Identity'</span></span>
- <span data-ttu-id="b8d88-772">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-772">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-773">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-773">'Razor'</span></span>
- <span data-ttu-id="b8d88-774">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-774">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-776">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-776">'Blazor'</span></span>
- <span data-ttu-id="b8d88-777">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-777">'Identity'</span></span>
- <span data-ttu-id="b8d88-778">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-778">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-779">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-779">'Razor'</span></span>
- <span data-ttu-id="b8d88-780">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-780">'SignalR' uid:</span></span> 

<span data-ttu-id="b8d88-781">------ | | 메시지 앱(SUT) | *src/RazorPagesProject* | 사용자가 메시지를 추가, 삭제, 모두 삭제 및 분석할 수 있도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-781">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="b8d88-782">| | 테스트 앱 | *tests/RazorPagesProject.Tests* | SUT를 통합 테스트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-782">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="b8d88-783">[Visual Studio](https://visualstudio.microsoft.com)와 같은 IDE의 기본 제공 테스트 기능을 사용하여 테스트를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-783">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="b8d88-784">[Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용하는 경우 *tests/RazorPagesProject.Tests* 디렉터리의 명령 프롬프트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-784">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="b8d88-785">메시지 앱(SUT) 조직</span><span class="sxs-lookup"><span data-stu-id="b8d88-785">Message app (SUT) organization</span></span>

<span data-ttu-id="b8d88-786">SUT는 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-786">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="b8d88-787">앱의 인덱스 페이지(*Pages/Index.cshtml* 및 *Pages/Index.cshtml.cs*)는 메시지의 추가, 삭제 및 분석을 제어하기 위한 UI 및 페이지 모델 메서드를 제공합니다(메시지당 평균 단어 수).</span><span class="sxs-lookup"><span data-stu-id="b8d88-787">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="b8d88-788">메시지는 `Id`(키) 및 `Text`(메시지)의 두 가지 속성을 사용하여 `Message` 클래스(*Data/Message.cs*)에서 설명됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-788">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="b8d88-789">`Text` 속성은 필수이며 200자로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-789">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="b8d88-790">메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)를 사용하여 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-790">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="b8d88-791">앱은 데이터베이스 컨텍스트 클래스 `AppDbContext`(*Data/AppDbContext*)에 DAL(데이터 액세스 계층)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-791">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="b8d88-792">앱 시작 시 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-792">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="b8d88-793">앱에는 인증된 사용자만 액세스할 수 있는 `/SecurePage`가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-793">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="b8d88-794">EF 항목 [InMemory로 테스트](/ef/core/miscellaneous/testing/in-memory)에서는 MSTest를 사용하여 테스트에 메모리 내 데이터베이스를 사용하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-794">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="b8d88-795">이 항목에서는 [xUnit](https://xunit.github.io/) 테스트 프레임워크를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-795">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="b8d88-796">여러 테스트 프레임워크의 테스트 개념 및 테스트 구현은 비슷하지만 동일하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-796">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="b8d88-797">앱은 리포지토리 패턴을 사용하지 않고 [UoW(작업 단위) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예가 아니지만 Razor Pages는 이러한 개발 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-797">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="b8d88-798">자세한 내용은 [인프라 지속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 [테스트 컨트롤러 논리](/aspnet/core/mvc/controllers/testing)(샘플에서 리포지토리 패턴 구현)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b8d88-798">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="b8d88-799">테스트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="b8d88-799">Test app organization</span></span>

<span data-ttu-id="b8d88-800">테스트 앱은 *tests/RazorPagesProject.Tests* 디렉터리 내에 있는 콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-800">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="b8d88-801">테스트 앱 디렉터리</span><span class="sxs-lookup"><span data-stu-id="b8d88-801">Test app directory</span></span> | <span data-ttu-id="b8d88-802">설명</span><span class="sxs-lookup"><span data-stu-id="b8d88-802">Description</span></span> |
| ---
<span data-ttu-id="b8d88-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-804">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-804">'Blazor'</span></span>
- <span data-ttu-id="b8d88-805">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-805">'Identity'</span></span>
- <span data-ttu-id="b8d88-806">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-806">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-807">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-807">'Razor'</span></span>
- <span data-ttu-id="b8d88-808">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-808">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-810">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-810">'Blazor'</span></span>
- <span data-ttu-id="b8d88-811">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-811">'Identity'</span></span>
- <span data-ttu-id="b8d88-812">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-812">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-813">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-813">'Razor'</span></span>
- <span data-ttu-id="b8d88-814">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-814">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-816">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-816">'Blazor'</span></span>
- <span data-ttu-id="b8d88-817">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-817">'Identity'</span></span>
- <span data-ttu-id="b8d88-818">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-818">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-819">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-819">'Razor'</span></span>
- <span data-ttu-id="b8d88-820">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-820">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-822">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-822">'Blazor'</span></span>
- <span data-ttu-id="b8d88-823">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-823">'Identity'</span></span>
- <span data-ttu-id="b8d88-824">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-824">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-825">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-825">'Razor'</span></span>
- <span data-ttu-id="b8d88-826">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-826">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-828">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-828">'Blazor'</span></span>
- <span data-ttu-id="b8d88-829">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-829">'Identity'</span></span>
- <span data-ttu-id="b8d88-830">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-830">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-831">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-831">'Razor'</span></span>
- <span data-ttu-id="b8d88-832">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-832">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-834">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-834">'Blazor'</span></span>
- <span data-ttu-id="b8d88-835">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-835">'Identity'</span></span>
- <span data-ttu-id="b8d88-836">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-836">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-837">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-837">'Razor'</span></span>
- <span data-ttu-id="b8d88-838">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-838">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-840">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-840">'Blazor'</span></span>
- <span data-ttu-id="b8d88-841">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-841">'Identity'</span></span>
- <span data-ttu-id="b8d88-842">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-842">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-843">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-843">'Razor'</span></span>
- <span data-ttu-id="b8d88-844">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-844">'SignalR' uid:</span></span> 

<span data-ttu-id="b8d88-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-846">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-846">'Blazor'</span></span>
- <span data-ttu-id="b8d88-847">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-847">'Identity'</span></span>
- <span data-ttu-id="b8d88-848">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-848">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-849">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-849">'Razor'</span></span>
- <span data-ttu-id="b8d88-850">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-850">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-852">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-852">'Blazor'</span></span>
- <span data-ttu-id="b8d88-853">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-853">'Identity'</span></span>
- <span data-ttu-id="b8d88-854">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-854">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-855">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-855">'Razor'</span></span>
- <span data-ttu-id="b8d88-856">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-856">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8d88-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b8d88-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8d88-858">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-858">'Blazor'</span></span>
- <span data-ttu-id="b8d88-859">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8d88-859">'Identity'</span></span>
- <span data-ttu-id="b8d88-860">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8d88-860">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8d88-861">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8d88-861">'Razor'</span></span>
- <span data-ttu-id="b8d88-862">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="b8d88-862">'SignalR' uid:</span></span> 

<span data-ttu-id="b8d88-863">------ | | *AuthTests* | 다음에 대한 테스트 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-863">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="b8d88-864">인증되지 않은 사용자가 보안 페이지에 액세스</span><span class="sxs-lookup"><span data-stu-id="b8d88-864">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="b8d88-865">모의 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 사용하여 인증된 사용자가 보안 페이지에 액세스</span><span class="sxs-lookup"><span data-stu-id="b8d88-865">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="b8d88-866">GitHub 사용자 프로필을 가져오고 프로필의 사용자 로그인 확인</span><span class="sxs-lookup"><span data-stu-id="b8d88-866">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="b8d88-867">| | *BasicTests* | 라우팅 및 콘텐츠 형식에 대한 테스트 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-867">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="b8d88-868">| | *IntegrationTests* | 사용자 지정 `WebApplicationFactory` 클래스를 사용하여 인덱스 페이지에 대한 통합 테스트를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-868">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="b8d88-869">| | *Helpers/Utilities* | </span><span class="sxs-lookup"><span data-stu-id="b8d88-869">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="b8d88-870">*Utilities.cs*에는 테스트 데이터로 데이터베이스를 시드하는 데 사용되는 `InitializeDbForTests` 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-870">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="b8d88-871">*HtmlHelpers.cs*는 테스트 메서드에서 사용할 AngleSharp `IHtmlDocument`를 반환하기 위한 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-871">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="b8d88-872">*HttpClientExtensions.cs*는 `SendAsync`에 대한 오버로드를 제공하여 SUT에 요청을 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-872">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="b8d88-873">테스트 프레임워크는 [xUnit](https://xunit.github.io/)입니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-873">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="b8d88-874">[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)를 포함하는 [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)를 사용하여 통합 테스트가 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-874">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="b8d88-875">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지를 사용하여 테스트 호스트와 테스트 서버를 구성하기 때문에 `TestHost` 및 `TestServer` 패키지에는 테스트 앱의 프로젝트 파일 또는 테스트 앱의 개발자 구성에서 직접 패키지 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-875">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="b8d88-876">**테스트를 위해 데이터베이스 시드**</span><span class="sxs-lookup"><span data-stu-id="b8d88-876">**Seeding the database for testing**</span></span>

<span data-ttu-id="b8d88-877">통합 테스트에서는 일반적으로 테스트 실행 전에 데이터베이스에 작은 데이터 세트가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-877">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="b8d88-878">예를 들어, 삭제 테스트는 데이터베이스 레코드 삭제를 요구하므로 삭제 요청이 성공하려면 데이터베이스에 적어도 하나 이상의 레코드가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-878">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="b8d88-879">샘플 앱은 테스트가 실행될 때 사용할 수 있는 *Utilities.cs*의 세 가지 메시지로 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="b8d88-879">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b8d88-880">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b8d88-880">Additional resources</span></span>

* [<span data-ttu-id="b8d88-881">단위 테스트</span><span class="sxs-lookup"><span data-stu-id="b8d88-881">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
