---
<span data-ttu-id="3679e-101">제목: pakrym-2.1에서 2.2 또는 3.0 author로 마이그레이션: description: 2.1에서 2.2 또는 3.0을 사용 하는 non-ASP.NET Core 응용 프로그램을 마이그레이션하는 방법에 대해 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="3679e-101">title: Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0 author: pakrym description: Learn how to migrate a non-ASP.NET Core application that uses Microsoft.Extensions.Logging from 2.1 to 2.2 or 3.0.</span></span>
<span data-ttu-id="3679e-102">pakrym: 사용자 지정: mvc ms. date: 01/04/2019 no loc:</span><span class="sxs-lookup"><span data-stu-id="3679e-102">ms.author: pakrym ms.custom: mvc ms.date: 01/04/2019 no-loc:</span></span>
- <span data-ttu-id="3679e-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3679e-103">'Blazor'</span></span>
- <span data-ttu-id="3679e-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3679e-104">'Identity'</span></span>
- <span data-ttu-id="3679e-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3679e-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="3679e-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3679e-106">'Razor'</span></span>
- <span data-ttu-id="3679e-107">' SignalR ' uid: 마이그레이션/로깅-nonaspnetcore</span><span class="sxs-lookup"><span data-stu-id="3679e-107">'SignalR' uid: migration/logging-nonaspnetcore</span></span>

---

# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="3679e-108">Microsoft Extensions. 로깅 2.1에서 2.2 또는 3.0로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="3679e-108">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="3679e-109">이 문서에서는 `Microsoft.Extensions.Logging` 2.1에서 2.2 또는 3.0로를 사용 하는 Non-ASP.NET Core 응용 프로그램을 마이그레이션하는 일반적인 단계를 간략하게 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="3679e-109">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="3679e-110">2.1-2.2</span><span class="sxs-lookup"><span data-stu-id="3679e-110">2.1 to 2.2</span></span>

<span data-ttu-id="3679e-111">수동으로 `ServiceCollection` 를 만들고 호출 `AddLogging` 합니다.</span><span class="sxs-lookup"><span data-stu-id="3679e-111">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="3679e-112">2.1 예제:</span><span class="sxs-lookup"><span data-stu-id="3679e-112">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="3679e-113">2.2 예제:</span><span class="sxs-lookup"><span data-stu-id="3679e-113">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="3679e-114">2.1 ~ 3.0</span><span class="sxs-lookup"><span data-stu-id="3679e-114">2.1 to 3.0</span></span>

<span data-ttu-id="3679e-115">3.0에서는을 사용 `LoggingFactory.Create` 합니다.</span><span class="sxs-lookup"><span data-stu-id="3679e-115">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="3679e-116">2.1 예제:</span><span class="sxs-lookup"><span data-stu-id="3679e-116">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="3679e-117">3.0 예제:</span><span class="sxs-lookup"><span data-stu-id="3679e-117">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="3679e-118">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3679e-118">Additional resources</span></span>

* <span data-ttu-id="3679e-119">[Microsoft. extension. Console NuGet 패키지](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="3679e-119">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
