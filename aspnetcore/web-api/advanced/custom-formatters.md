---
title: ASP.NET Core Web API에서 포맷터 사용자 지정
author: rick-anderson
description: ASP.NET Core의 웹 API에서 사용자 지정 포맷터를 만들고 사용하는 방법을 알아봅니다.
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 27819f77cf86c946ab0415d3583dfbab80a24cf5
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408866"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="9775c-103">ASP.NET Core Web API에서 포맷터 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="9775c-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="9775c-104">[Kirk Larkin](https://twitter.com/serpent5) 및 [Tom Dykstra](https://github.com/tdykstra)를 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Tom Dykstra](https://github.com/tdykstra).</span></span>

<span data-ttu-id="9775c-105">ASP.NET Core MVC는 입력 및 출력 포맷터를 사용하여 Web API에서 데이터 교환을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="9775c-106">입력 포맷터는 [모델 바인딩](xref:mvc/models/model-binding)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="9775c-107">출력 포맷터는 [응답 형식을 지정](xref:web-api/advanced/formatting)하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="9775c-108">프레임워크는 JSON 및 XML에 대한 기본 제공 입력 및 출력 포맷터를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="9775c-109">일반 텍스트에 대한 기본 제공 출력 포맷터는 제공하지만 일반 텍스트에 대한 입력 포맷터는 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="9775c-110">이 문서에서는 사용자 지정 포맷터를 만들어 추가 형식에 대한 지원을 추가하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="9775c-111">사용자 지정 일반 텍스트 입력 포맷터의 예제는 GitHub의 [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="9775c-111">For an example of a custom plain text input formatter, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="9775c-112">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9775c-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="9775c-113">사용자 지정 포맷터를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="9775c-113">When to use custom formatters</span></span>

<span data-ttu-id="9775c-114">사용자 지정 포맷터를 사용 하 여 bult 포맷터에서 처리 하지 않는 콘텐츠 형식에 대 한 지원을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-114">Use a custom formatter to add support for a content type that isn't handled by the bult-in formatters.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="9775c-115">사용자 지정 포맷터를 사용하는 방법 개요</span><span class="sxs-lookup"><span data-stu-id="9775c-115">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="9775c-116">사용자 지정 포맷터를 만들려면:</span><span class="sxs-lookup"><span data-stu-id="9775c-116">To create a custom formatter:</span></span>

* <span data-ttu-id="9775c-117">클라이언트로 전송 된 데이터를 serialize 하려면 출력 포맷터 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-117">For serializing data sent to the client, create an output formatter class.</span></span>
* <span data-ttu-id="9775c-118">클라이언트에서 받은 deserialzing 데이터의 경우 입력 포맷터 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-118">For deserialzing data received from the client, create an input formatter class.</span></span>
* <span data-ttu-id="9775c-119">포맷터 클래스의 인스턴스를 `InputFormatters` `OutputFormatters` [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions)의 및 컬렉션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-119">Add instances of formatter classes to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="9775c-120">사용자 지정 포맷터 클래스를 만드는 방법</span><span class="sxs-lookup"><span data-stu-id="9775c-120">How to create a custom formatter class</span></span>

<span data-ttu-id="9775c-121">포맷터를 만들려면:</span><span class="sxs-lookup"><span data-stu-id="9775c-121">To create a formatter:</span></span>

* <span data-ttu-id="9775c-122">적절한 기본 클래스에서 클래스를 파생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-122">Derive the class from the appropriate base class.</span></span> <span data-ttu-id="9775c-123">샘플 앱은 및에서 <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> 파생 <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-123">The sample app derives from <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> and <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter>.</span></span>
* <span data-ttu-id="9775c-124">생성자에서 유효한 미디어 형식 및 인코딩을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-124">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="9775c-125"><xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> 및 <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> 메서드를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-125">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> and <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> methods.</span></span>
* <span data-ttu-id="9775c-126"><xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> 및 `WriteResponseBodyAsync` 메서드를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-126">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> and `WriteResponseBodyAsync` methods.</span></span>

<span data-ttu-id="9775c-127">다음 코드는 샘플의 `VcardOutputFormatter` 클래스를 보여 [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample)줍니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-127">The following code shows the `VcardOutputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=snippet)]
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="9775c-128">적절한 기본 클래스에서 파생</span><span class="sxs-lookup"><span data-stu-id="9775c-128">Derive from the appropriate base class</span></span>

<span data-ttu-id="9775c-129">텍스트 미디어 형식(예: vCard)의 경우 [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) 또는 [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) 기본 클래스에서 파생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-129">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="9775c-130">이진 형식의 경우 [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) 또는 [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) 기본 클래스에서 파생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-130">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="9775c-131">유효한 미디어 형식 및 인코딩 지정</span><span class="sxs-lookup"><span data-stu-id="9775c-131">Specify valid media types and encodings</span></span>

<span data-ttu-id="9775c-132">생성자에서 `SupportedMediaTypes` 및 `SupportedEncodings` 컬렉션에 추가하여 유효한 미디어 형식 및 인코딩을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-132">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=ctor)]

<span data-ttu-id="9775c-133">포맷터 클래스는 해당 종속성에 대 한 생성자 삽입을 사용할 수 **없습니다** .</span><span class="sxs-lookup"><span data-stu-id="9775c-133">A formatter class can **not** use constructor injection for its dependencies.</span></span> <span data-ttu-id="9775c-134">예를 들어는 `ILogger<VcardOutputFormatter>` 생성자에 매개 변수로 추가할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-134">For example, `ILogger<VcardOutputFormatter>` cannot be added as a parameter to the constructor.</span></span> <span data-ttu-id="9775c-135">서비스에 액세스 하려면 메서드에 전달 되는 컨텍스트 개체를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-135">To access services, use the context object that gets passed in to the methods.</span></span> <span data-ttu-id="9775c-136">이 문서의 코드 예제 및 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample) 은이 작업을 수행 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-136">A code example in this article and the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample) show how to do this.</span></span>

### <a name="override-canreadtype-and-canwritetype"></a><span data-ttu-id="9775c-137">CanReadType 및 CanWriteType 재정의</span><span class="sxs-lookup"><span data-stu-id="9775c-137">Override CanReadType and CanWriteType</span></span>

<span data-ttu-id="9775c-138">또는 메서드를 재정의 하 여 deserialize 하거나 serialize 할 형식을 지정 `CanReadType` 합니다 `CanWriteType` .</span><span class="sxs-lookup"><span data-stu-id="9775c-138">Specify the type to deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="9775c-139">예를 들어 형식에서 vCard 텍스트를 만들거나 `Contact` 그 반대의 경우도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-139">For example, creating vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="9775c-140">CanWriteResult 메서드</span><span class="sxs-lookup"><span data-stu-id="9775c-140">The CanWriteResult method</span></span>

<span data-ttu-id="9775c-141">일부 시나리오에서는가 대신를 `CanWriteResult` 재정의 해야 합니다 `CanWriteType` .</span><span class="sxs-lookup"><span data-stu-id="9775c-141">In some scenarios, `CanWriteResult` must be overridden rather than `CanWriteType`.</span></span> <span data-ttu-id="9775c-142">다음 조건이 true인 경우 `CanWriteResult`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-142">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="9775c-143">동작 메서드는 모델 클래스를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-143">The action method returns a model class.</span></span>
* <span data-ttu-id="9775c-144">런타임 시 반환될 수 있는 파생 클래스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-144">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="9775c-145">작업에서 반환 된 파생 클래스는 런타임에 알고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-145">The derived class returned by the action must be known at runtime.</span></span>

<span data-ttu-id="9775c-146">예를 들어, 작업 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-146">For example, suppose the action method:</span></span>

* <span data-ttu-id="9775c-147">Signature는 `Person` 형식을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-147">Signature returns a `Person` type.</span></span>
* <span data-ttu-id="9775c-148">는 `Student` `Instructor` 에서 파생 된 또는 형식을 반환할 수 있습니다 `Person` .</span><span class="sxs-lookup"><span data-stu-id="9775c-148">Can return a `Student` or `Instructor` type that derives from `Person`.</span></span> 

<span data-ttu-id="9775c-149">포맷터가 개체만 처리 하려면 `Student` 메서드에 제공 된 컨텍스트 개체에서 [개체](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) 의 형식을 확인 합니다 `CanWriteResult` .</span><span class="sxs-lookup"><span data-stu-id="9775c-149">For the formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="9775c-150">동작 메서드가 반환 하는 경우 `IActionResult` :</span><span class="sxs-lookup"><span data-stu-id="9775c-150">When the action method returns `IActionResult`:</span></span>

* <span data-ttu-id="9775c-151">를 반드시 사용할 필요는 없습니다 `CanWriteResult` .</span><span class="sxs-lookup"><span data-stu-id="9775c-151">It's not necessary to use `CanWriteResult`.</span></span>
* <span data-ttu-id="9775c-152">`CanWriteType`메서드는 런타임 형식을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-152">The `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a><span data-ttu-id="9775c-153">ReadRequestBodyAsync 및 WriteResponseBodyAsync 재정의</span><span class="sxs-lookup"><span data-stu-id="9775c-153">Override ReadRequestBodyAsync and WriteResponseBodyAsync</span></span>

<span data-ttu-id="9775c-154">Deserialization 또는 serialization은 또는에서 수행 됩니다 `ReadRequestBodyAsync` `WriteResponseBodyAsync` .</span><span class="sxs-lookup"><span data-stu-id="9775c-154">Deserialization or serialization is performed in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="9775c-155">다음 예제에서는 종속성 주입 컨테이너에서 서비스를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-155">The following example shows how to get services from the dependency injection container.</span></span> <span data-ttu-id="9775c-156">생성자 매개 변수에서 서비스를 가져올 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-156">Services can't be obtained from constructor parameters.</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardOutputFormatter.cs?name=writeresponse)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="9775c-157">사용자 지정 포맷터를 사용하도록 MVC를 구성하는 방법</span><span class="sxs-lookup"><span data-stu-id="9775c-157">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="9775c-158">사용자 지정 포맷터를 사용하려면 `InputFormatters` 또는 `OutputFormatters` 컬렉션에 포맷터 클래스의 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-158">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/3.1sample/Startup.cs?name=mvcoptions)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

<span data-ttu-id="9775c-159">포맷터는 삽입한 순서 대로 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-159">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="9775c-160">첫 번째 포맷터가 우선됩니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-160">The first one takes precedence.</span></span>

## <a name="the-completed-vcardinputformatter-class"></a><span data-ttu-id="9775c-161">Completed `VcardInputFormatter` 클래스</span><span class="sxs-lookup"><span data-stu-id="9775c-161">The completed `VcardInputFormatter` class</span></span>

<span data-ttu-id="9775c-162">다음 코드는 샘플의 `VcardInputFormatter` 클래스를 보여 [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample)줍니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-162">The following code shows the `VcardInputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/3.1sample):</span></span>

[!code-csharp[](custom-formatters/3.1sample/Formatters/VcardInputFormatter.cs?name=snippet)]

## <a name="test-the-app"></a><span data-ttu-id="9775c-163">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="9775c-163">Test the app</span></span>

<span data-ttu-id="9775c-164">기본 vCard 입력 및 출력 포맷터를 구현 하는 [이 문서에 대 한 샘플 앱을 실행](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-164">[Run the sample app for this article](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements basic vCard input and output formatters.</span></span> <span data-ttu-id="9775c-165">앱은 다음과 유사 하 게 Vcard를 읽고 씁니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-165">The app reads and writes vCards similar to the following:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

<span data-ttu-id="9775c-166">VCard 출력을 보려면 응용 프로그램을 실행 하 고 Accept 헤더가 포함 된 Get 요청 `text/vcard` 을에 보냅니다 `https://localhost:5001/api/contacts` .</span><span class="sxs-lookup"><span data-stu-id="9775c-166">To see vCard output, run the app and send a Get request with Accept header `text/vcard` to `https://localhost:5001/api/contacts`.</span></span>

<span data-ttu-id="9775c-167">VCard를 메모리 내 연락처 컬렉션에 추가 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-167">To add a vCard to the in-memory collection of contacts:</span></span>

* <span data-ttu-id="9775c-168">`Post` `/api/contacts` Postman과 같은 도구를 사용 하 여 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-168">Send a `Post` request to `/api/contacts` with a tool like Postman.</span></span>
* <span data-ttu-id="9775c-169">`Content-Type` 헤더를 `text/vcard`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-169">Set the `Content-Type` header to `text/vcard`.</span></span>
* <span data-ttu-id="9775c-170">`vCard`앞의 예제와 같이 서식이 지정 된 본문에 텍스트를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="9775c-170">Set `vCard` text in the body, formatted like the preceding example.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9775c-171">추가 자료</span><span class="sxs-lookup"><span data-stu-id="9775c-171">Additional resources</span></span>

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
