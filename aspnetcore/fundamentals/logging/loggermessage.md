---
<span data-ttu-id="88320-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="88320-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="88320-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="88320-102">'Blazor'</span></span>
- <span data-ttu-id="88320-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="88320-103">'Identity'</span></span>
- <span data-ttu-id="88320-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="88320-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="88320-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="88320-105">'Razor'</span></span>
- <span data-ttu-id="88320-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="88320-106">'SignalR' uid:</span></span> 

---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="88320-107">ASP.NET Core에서 LoggerMessage를 사용한 고성능 로깅</span><span class="sxs-lookup"><span data-stu-id="88320-107">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="88320-108"><xref:Microsoft.Extensions.Logging.LoggerMessage> 기능은 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> 및 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>와 같은 [로거 확장 메서드](xref:Microsoft.Extensions.Logging.LoggerExtensions)에 비해 적은 개체 할당 및 감소된 계산 오버헤드를 필요로 하는 캐시 가능한 대리자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="88320-108"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="88320-109">고성능 로깅 시나리오의 경우 <xref:Microsoft.Extensions.Logging.LoggerMessage> 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-109">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="88320-110"><xref:Microsoft.Extensions.Logging.LoggerMessage>는 로거 확장 메서드에 비해 다음과 같은 성능 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="88320-111">로거 확장 메서드는 `object`에 대한 `int`와 같은 "boxing"(변환) 값 형식이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-111">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="88320-112"><xref:Microsoft.Extensions.Logging.LoggerMessage> 패턴은 정적 <xref:System.Action> 필드 및 강력한 형식의 매개 변수가 있는 확장 메서드를 사용하여 boxing을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-112">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="88320-113">로거 확장 메서드는 로그 메시지가 기록될 때마다 메시지 템플릿(명명된 형식 문자열)을 구문 분석해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-113">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="88320-114"><xref:Microsoft.Extensions.Logging.LoggerMessage>는 메시지가 정의될 때 템플릿 구문 분석이 한번만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-114"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="88320-115">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="88320-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="88320-116">샘플 앱은 기본 견적 추적 시스템으로 <xref:Microsoft.Extensions.Logging.LoggerMessage> 기능을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="88320-116">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="88320-117">앱은 메모리 내 데이터베이스를 사용하여 견적을 추가하고 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-117">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="88320-118">이러한 작업이 발생하는 대로 로그 메시지는 <xref:Microsoft.Extensions.Logging.LoggerMessage> 패턴을 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-118">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="88320-119">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="88320-119">LoggerMessage.Define</span></span>

<span data-ttu-id="88320-120">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)은 메시지 로깅을 위한 <xref:System.Action> 대리자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="88320-120">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="88320-121"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 오버로드는 명명된 형식 문자열(템플릿)로 최대 6개의 형식 매개 변수 전달을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-121"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="88320-122"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 메서드에 제공된 문자열은 템플릿이며 보간된 문자열이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="88320-122">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="88320-123">자리 표시자는 형식이 지정된 순서로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="88320-123">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="88320-124">템플릿의 자리 표시자 이름은 템플릿에서 알기 쉽고 일관되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-124">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="88320-125">구조적 로그 데이터 내에서 속성 이름으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-125">They serve as property names within structured log data.</span></span> <span data-ttu-id="88320-126">자리 표시자 이름으로 [파스칼식 대/소문자](/dotnet/standard/design-guidelines/capitalization-conventions)를 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-126">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="88320-127">예: `{Count}`, `{FirstName}`</span><span class="sxs-lookup"><span data-stu-id="88320-127">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="88320-128">각 로그 메시지는 [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)에서 만들어진 정적 필드에 보관된 <xref:System.Action>입니다.</span><span class="sxs-lookup"><span data-stu-id="88320-128">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="88320-129">예를 들어 샘플 앱은 인덱스 페이지의 GET 요청에 대한 로그 메시지를 설명하는 필드를 만듭니다(*Internal/LoggerExtensions.cs*).</span><span class="sxs-lookup"><span data-stu-id="88320-129">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="88320-130"><xref:System.Action>의 경우 다음을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-130">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="88320-131">로그 수준</span><span class="sxs-lookup"><span data-stu-id="88320-131">The log level.</span></span>
* <span data-ttu-id="88320-132">정적 확장 메서드의 이름이 있는 고유한 이벤트 식별자(<xref:Microsoft.Extensions.Logging.EventId>)입니다.</span><span class="sxs-lookup"><span data-stu-id="88320-132">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="88320-133">메시지 템플릿(명명된 형식 문자열)</span><span class="sxs-lookup"><span data-stu-id="88320-133">The message template (named format string).</span></span> 

<span data-ttu-id="88320-134">샘플 앱의 인덱스 페이지에 대한 요청은 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-134">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="88320-135">로그 수준을 `Information`으로</span><span class="sxs-lookup"><span data-stu-id="88320-135">Log level to `Information`.</span></span>
* <span data-ttu-id="88320-136">이벤트 ID를 `IndexPageRequested` 메서드의 이름이 있는 `1`로</span><span class="sxs-lookup"><span data-stu-id="88320-136">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="88320-137">메시지 템플릿(명명된 형식 문자열)을 문자열로</span><span class="sxs-lookup"><span data-stu-id="88320-137">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="88320-138">구조적 로깅 저장소는 로깅을 보강하도록 이벤트 ID로 제공될 경우 이벤트 이름을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88320-138">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="88320-139">예를 들어 [Serilog](https://github.com/serilog/serilog-extensions-logging)는 이벤트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-139">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="88320-140"><xref:System.Action>은 강력한 형식의 확장 메서드를 통해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-140">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="88320-141">`IndexPageRequested` 메서드는 샘플 앱에서 인덱스 페이지 GET 요청에 대한 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-141">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="88320-142">`IndexPageRequested`는 *Pages/Index.cshtml.cs*에서 `OnGetAsync` 메서드의 로거에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-142">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="88320-143">앱의 콘솔 출력을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-143">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="88320-144">로그 메시지에 매개 변수를 전달하려면 정적 필드를 만들 때 최대 6개의 형식을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-144">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="88320-145">샘플 앱은 <xref:System.Action> 필드에 대한 `string` 형식을 정의하여 견적을 추가할 때 문자열을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-145">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="88320-146">대리자의 로그 메시지 템플릿은 제공되는 형식에서 해당 자리 표시자 값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="88320-146">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="88320-147">샘플 앱은 견적 매개 변수가 `string`인 견적을 추가하기 위한 대리자를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-147">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="88320-148">견적을 추가하기 위한 정적 확장 메서드, `QuoteAdded`는 견적 인수 값을 받고 <xref:System.Action> 대리자에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-148">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="88320-149">인덱스 페이지의 페이지 모델(*Pages/Index.cshtml.cs*)에서 `QuoteAdded`는 메시지를 기록하기 위해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-149">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="88320-150">앱의 콘솔 출력을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-150">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="88320-151">샘플 앱은 견적 삭제를 위한 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 패턴을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-151">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="88320-152">성공적인 삭제 작업에 대한 정보 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-152">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="88320-153">예외가 throw될 경우 삭제 작업에 대한 오류 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-153">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="88320-154">실패한 삭제 작업에 대한 로그 메시지는 예외 스택 추적(*Internal/LoggerExtensions.cs*)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-154">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="88320-155">`QuoteDeleteFailed`에서 예외가 대리자에 전달되는 방식을 참고합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-155">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="88320-156">인덱스 페이지에 대한 페이지 모델에서 성공적인 견적 삭제는 로거에서 `QuoteDeleted` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-156">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="88320-157">삭제에 대한 견적을 찾을 수 없는 경우 <xref:System.ArgumentNullException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-157">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="88320-158">예외는 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문에 의해 트래핑되고 [catch](/dotnet/csharp/language-reference/keywords/try-catch) 블록의 로거에서 `QuoteDeleteFailed` 메서드를 호출하여 기록됩니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="88320-158">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="88320-159">견적이 성공적으로 삭제되면 앱의 콘솔 출력을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-159">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="88320-160">견적 삭제가 실패하면 앱의 콘솔 출력을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-160">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="88320-161">예외는 로그 메시지에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88320-161">Note that the exception is included in the log message:</span></span>

```console
LoggerMessageSample.Pages.IndexModel: Error: Quote delete failed (Id = 999)

System.NullReferenceException: Object reference not set to an instance of an object.
   at lambda_method(Closure , ValueBuffer )
   at System.Linq.Enumerable.SelectEnumerableIterator`2.MoveNext()
   at Microsoft.EntityFrameworkCore.InMemory.Query.Internal.InMemoryShapedQueryCompilingExpressionVisitor.AsyncQueryingEnumerable`1.AsyncEnumerator.MoveNextAsync()
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at LoggerMessageSample.Pages.IndexModel.OnPostDeleteQuoteAsync(Int32 id) in c:\Users\guard\Documents\GitHub\Docs\aspnetcore\fundamentals\logging\loggermessage\samples\3.x\LoggerMessageSample\Pages\Index.cshtml.cs:line 77
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="88320-162">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="88320-162">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="88320-163">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*)는 [로그 범위](xref:fundamentals/logging/index#log-scopes) 정의를 위한 <xref:System.Func%601> 대리자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="88320-163">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="88320-164"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 오버로드는 명명된 형식 문자열(템플릿)로 최대 3개의 형식 매개 변수 전달을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-164"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="88320-165"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 메서드의 경우와 마찬가지로 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 메서드에 제공된 문자열은 템플릿이며 보간된 문자열이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="88320-165">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="88320-166">자리 표시자는 형식이 지정된 순서로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="88320-166">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="88320-167">템플릿의 자리 표시자 이름은 템플릿에서 알기 쉽고 일관되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-167">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="88320-168">구조적 로그 데이터 내에서 속성 이름으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-168">They serve as property names within structured log data.</span></span> <span data-ttu-id="88320-169">자리 표시자 이름으로 [파스칼식 대/소문자](/dotnet/standard/design-guidelines/capitalization-conventions)를 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-169">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="88320-170">예: `{Count}`, `{FirstName}`</span><span class="sxs-lookup"><span data-stu-id="88320-170">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="88320-171"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 메서드를 사용하여 일련의 로그 메시지에 적용하도록 [로그 범위](xref:fundamentals/logging/index#log-scopes)를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-171">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="88320-172">샘플 앱에는 데이터베이스에서 모든 견적을 삭제하기 위한 **모두 지우기** 단추가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88320-172">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="88320-173">견적은 한 번에 하나를 제거하여 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-173">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="88320-174">견적이 삭제될 때마다 로거에서 `QuoteDeleted` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-174">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="88320-175">로그 범위가 이러한 로그 메시지에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-175">A log scope is added to these log messages.</span></span>

<span data-ttu-id="88320-176">*appsettings.json*의 콘솔 로거 섹션에서 `IncludeScopes`를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-176">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="88320-177">로그 범위를 만들려면 범위에 대한 <xref:System.Func%601> 대리자를 보유하는 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-177">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="88320-178">샘플 앱은 `_allQuotesDeletedScope`라는 필드를 만듭니다(*Internal/LoggerExtensions.cs*).</span><span class="sxs-lookup"><span data-stu-id="88320-178">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="88320-179"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>를 사용하여 대리자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="88320-179">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="88320-180">대리자가 호출되는 경우 템플릿 인수로 사용하기 위해 최대 세 개의 형식을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88320-180">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="88320-181">샘플 앱은 삭제된 견적의 수를 포함하는 메시지 템플릿을 사용합니다(`int` 형식).</span><span class="sxs-lookup"><span data-stu-id="88320-181">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="88320-182">로그 메시지에 대한 정적 확장 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-182">Provide a static extension method for the log message.</span></span> <span data-ttu-id="88320-183">메시지 템플릿에 표시되는 명명된 속성에 대한 모든 형식 매개 변수를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-183">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="88320-184">샘플 앱은 `_allQuotesDeletedScope`를 삭제하고 반환하는 데 `count`의 견적을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-184">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="88320-185">범위는 [using](/dotnet/csharp/language-reference/keywords/using-statement) 블록에서 로깅 확장 호출을 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-185">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="88320-186">앱의 콘솔 출력에서 로그 메시지를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-186">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="88320-187">다음 결과는 포함된 로그 범위 메시지와 함께 삭제된 세 가지 견적을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="88320-187">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="88320-188"><xref:Microsoft.Extensions.Logging.LoggerMessage> 기능은 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> 및 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>와 같은 [로거 확장 메서드](xref:Microsoft.Extensions.Logging.LoggerExtensions)에 비해 적은 개체 할당 및 감소된 계산 오버헤드를 필요로 하는 캐시 가능한 대리자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="88320-188"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="88320-189">고성능 로깅 시나리오의 경우 <xref:Microsoft.Extensions.Logging.LoggerMessage> 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-189">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="88320-190"><xref:Microsoft.Extensions.Logging.LoggerMessage>는 로거 확장 메서드에 비해 다음과 같은 성능 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="88320-191">로거 확장 메서드는 `object`에 대한 `int`와 같은 "boxing"(변환) 값 형식이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-191">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="88320-192"><xref:Microsoft.Extensions.Logging.LoggerMessage> 패턴은 정적 <xref:System.Action> 필드 및 강력한 형식의 매개 변수가 있는 확장 메서드를 사용하여 boxing을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-192">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="88320-193">로거 확장 메서드는 로그 메시지가 기록될 때마다 메시지 템플릿(명명된 형식 문자열)을 구문 분석해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-193">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="88320-194"><xref:Microsoft.Extensions.Logging.LoggerMessage>는 메시지가 정의될 때 템플릿 구문 분석이 한번만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-194"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="88320-195">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="88320-195">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="88320-196">샘플 앱은 기본 견적 추적 시스템으로 <xref:Microsoft.Extensions.Logging.LoggerMessage> 기능을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="88320-196">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="88320-197">앱은 메모리 내 데이터베이스를 사용하여 견적을 추가하고 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-197">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="88320-198">이러한 작업이 발생하는 대로 로그 메시지는 <xref:Microsoft.Extensions.Logging.LoggerMessage> 패턴을 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-198">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="88320-199">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="88320-199">LoggerMessage.Define</span></span>

<span data-ttu-id="88320-200">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)은 메시지 로깅을 위한 <xref:System.Action> 대리자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="88320-200">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="88320-201"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 오버로드는 명명된 형식 문자열(템플릿)로 최대 6개의 형식 매개 변수 전달을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-201"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="88320-202"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 메서드에 제공된 문자열은 템플릿이며 보간된 문자열이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="88320-202">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="88320-203">자리 표시자는 형식이 지정된 순서로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="88320-203">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="88320-204">템플릿의 자리 표시자 이름은 템플릿에서 알기 쉽고 일관되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-204">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="88320-205">구조적 로그 데이터 내에서 속성 이름으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-205">They serve as property names within structured log data.</span></span> <span data-ttu-id="88320-206">자리 표시자 이름으로 [파스칼식 대/소문자](/dotnet/standard/design-guidelines/capitalization-conventions)를 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-206">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="88320-207">예: `{Count}`, `{FirstName}`</span><span class="sxs-lookup"><span data-stu-id="88320-207">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="88320-208">각 로그 메시지는 [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)에서 만들어진 정적 필드에 보관된 <xref:System.Action>입니다.</span><span class="sxs-lookup"><span data-stu-id="88320-208">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="88320-209">예를 들어 샘플 앱은 인덱스 페이지의 GET 요청에 대한 로그 메시지를 설명하는 필드를 만듭니다(*Internal/LoggerExtensions.cs*).</span><span class="sxs-lookup"><span data-stu-id="88320-209">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="88320-210"><xref:System.Action>의 경우 다음을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-210">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="88320-211">로그 수준</span><span class="sxs-lookup"><span data-stu-id="88320-211">The log level.</span></span>
* <span data-ttu-id="88320-212">정적 확장 메서드의 이름이 있는 고유한 이벤트 식별자(<xref:Microsoft.Extensions.Logging.EventId>)입니다.</span><span class="sxs-lookup"><span data-stu-id="88320-212">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="88320-213">메시지 템플릿(명명된 형식 문자열)</span><span class="sxs-lookup"><span data-stu-id="88320-213">The message template (named format string).</span></span> 

<span data-ttu-id="88320-214">샘플 앱의 인덱스 페이지에 대한 요청은 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-214">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="88320-215">로그 수준을 `Information`으로</span><span class="sxs-lookup"><span data-stu-id="88320-215">Log level to `Information`.</span></span>
* <span data-ttu-id="88320-216">이벤트 ID를 `IndexPageRequested` 메서드의 이름이 있는 `1`로</span><span class="sxs-lookup"><span data-stu-id="88320-216">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="88320-217">메시지 템플릿(명명된 형식 문자열)을 문자열로</span><span class="sxs-lookup"><span data-stu-id="88320-217">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="88320-218">구조적 로깅 저장소는 로깅을 보강하도록 이벤트 ID로 제공될 경우 이벤트 이름을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88320-218">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="88320-219">예를 들어 [Serilog](https://github.com/serilog/serilog-extensions-logging)는 이벤트 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-219">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="88320-220"><xref:System.Action>은 강력한 형식의 확장 메서드를 통해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-220">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="88320-221">`IndexPageRequested` 메서드는 샘플 앱에서 인덱스 페이지 GET 요청에 대한 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-221">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="88320-222">`IndexPageRequested`는 *Pages/Index.cshtml.cs*에서 `OnGetAsync` 메서드의 로거에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-222">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="88320-223">앱의 콘솔 출력을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-223">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="88320-224">로그 메시지에 매개 변수를 전달하려면 정적 필드를 만들 때 최대 6개의 형식을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-224">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="88320-225">샘플 앱은 <xref:System.Action> 필드에 대한 `string` 형식을 정의하여 견적을 추가할 때 문자열을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-225">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="88320-226">대리자의 로그 메시지 템플릿은 제공되는 형식에서 해당 자리 표시자 값을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="88320-226">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="88320-227">샘플 앱은 견적 매개 변수가 `string`인 견적을 추가하기 위한 대리자를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-227">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="88320-228">견적을 추가하기 위한 정적 확장 메서드, `QuoteAdded`는 견적 인수 값을 받고 <xref:System.Action> 대리자에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-228">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="88320-229">인덱스 페이지의 페이지 모델(*Pages/Index.cshtml.cs*)에서 `QuoteAdded`는 메시지를 기록하기 위해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-229">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="88320-230">앱의 콘솔 출력을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-230">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="88320-231">샘플 앱은 견적 삭제를 위한 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 패턴을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-231">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="88320-232">성공적인 삭제 작업에 대한 정보 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-232">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="88320-233">예외가 throw될 경우 삭제 작업에 대한 오류 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-233">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="88320-234">실패한 삭제 작업에 대한 로그 메시지는 예외 스택 추적(*Internal/LoggerExtensions.cs*)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-234">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="88320-235">`QuoteDeleteFailed`에서 예외가 대리자에 전달되는 방식을 참고합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-235">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="88320-236">인덱스 페이지에 대한 페이지 모델에서 성공적인 견적 삭제는 로거에서 `QuoteDeleted` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-236">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="88320-237">삭제에 대한 견적을 찾을 수 없는 경우 <xref:System.ArgumentNullException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-237">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="88320-238">예외는 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문에 의해 트래핑되고 [catch](/dotnet/csharp/language-reference/keywords/try-catch) 블록의 로거에서 `QuoteDeleteFailed` 메서드를 호출하여 기록됩니다(*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="88320-238">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="88320-239">견적이 성공적으로 삭제되면 앱의 콘솔 출력을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-239">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="88320-240">견적 삭제가 실패하면 앱의 콘솔 출력을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-240">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="88320-241">예외는 로그 메시지에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88320-241">Note that the exception is included in the log message:</span></span>

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T]
       (T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() 
      in <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="88320-242">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="88320-242">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="88320-243">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*)는 [로그 범위](xref:fundamentals/logging/index#log-scopes) 정의를 위한 <xref:System.Func%601> 대리자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="88320-243">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="88320-244"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 오버로드는 명명된 형식 문자열(템플릿)로 최대 3개의 형식 매개 변수 전달을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-244"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="88320-245"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 메서드의 경우와 마찬가지로 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 메서드에 제공된 문자열은 템플릿이며 보간된 문자열이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="88320-245">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="88320-246">자리 표시자는 형식이 지정된 순서로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="88320-246">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="88320-247">템플릿의 자리 표시자 이름은 템플릿에서 알기 쉽고 일관되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-247">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="88320-248">구조적 로그 데이터 내에서 속성 이름으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-248">They serve as property names within structured log data.</span></span> <span data-ttu-id="88320-249">자리 표시자 이름으로 [파스칼식 대/소문자](/dotnet/standard/design-guidelines/capitalization-conventions)를 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-249">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="88320-250">예: `{Count}`, `{FirstName}`</span><span class="sxs-lookup"><span data-stu-id="88320-250">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="88320-251"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 메서드를 사용하여 일련의 로그 메시지에 적용하도록 [로그 범위](xref:fundamentals/logging/index#log-scopes)를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-251">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="88320-252">샘플 앱에는 데이터베이스에서 모든 견적을 삭제하기 위한 **모두 지우기** 단추가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88320-252">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="88320-253">견적은 한 번에 하나를 제거하여 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-253">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="88320-254">견적이 삭제될 때마다 로거에서 `QuoteDeleted` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-254">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="88320-255">로그 범위가 이러한 로그 메시지에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="88320-255">A log scope is added to these log messages.</span></span>

<span data-ttu-id="88320-256">*appsettings.json*의 콘솔 로거 섹션에서 `IncludeScopes`를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-256">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="88320-257">로그 범위를 만들려면 범위에 대한 <xref:System.Func%601> 대리자를 보유하는 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-257">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="88320-258">샘플 앱은 `_allQuotesDeletedScope`라는 필드를 만듭니다(*Internal/LoggerExtensions.cs*).</span><span class="sxs-lookup"><span data-stu-id="88320-258">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="88320-259"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>를 사용하여 대리자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="88320-259">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="88320-260">대리자가 호출되는 경우 템플릿 인수로 사용하기 위해 최대 세 개의 형식을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88320-260">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="88320-261">샘플 앱은 삭제된 견적의 수를 포함하는 메시지 템플릿을 사용합니다(`int` 형식).</span><span class="sxs-lookup"><span data-stu-id="88320-261">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="88320-262">로그 메시지에 대한 정적 확장 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-262">Provide a static extension method for the log message.</span></span> <span data-ttu-id="88320-263">메시지 템플릿에 표시되는 명명된 속성에 대한 모든 형식 매개 변수를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-263">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="88320-264">샘플 앱은 `_allQuotesDeletedScope`를 삭제하고 반환하는 데 `count`의 견적을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-264">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="88320-265">범위는 [using](/dotnet/csharp/language-reference/keywords/using-statement) 블록에서 로깅 확장 호출을 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-265">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="88320-266">앱의 콘솔 출력에서 로그 메시지를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="88320-266">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="88320-267">다음 결과는 포함된 로그 범위 메시지와 함께 삭제된 세 가지 견적을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="88320-267">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="88320-268">추가 자료</span><span class="sxs-lookup"><span data-stu-id="88320-268">Additional resources</span></span>

* [<span data-ttu-id="88320-269">로깅</span><span class="sxs-lookup"><span data-stu-id="88320-269">Logging</span></span>](xref:fundamentals/logging/index)
