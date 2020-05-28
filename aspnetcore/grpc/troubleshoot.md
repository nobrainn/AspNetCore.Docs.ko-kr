---
<span data-ttu-id="25fb3-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25fb3-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25fb3-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25fb3-102">'Blazor'</span></span>
- <span data-ttu-id="25fb3-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25fb3-103">'Identity'</span></span>
- <span data-ttu-id="25fb3-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25fb3-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="25fb3-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25fb3-105">'Razor'</span></span>
- <span data-ttu-id="25fb3-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="25fb3-106">'SignalR' uid:</span></span> 

---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="25fb3-107">.NET Core에서 gRPC 문제 해결</span><span class="sxs-lookup"><span data-stu-id="25fb3-107">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="25fb3-108">작성자: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="25fb3-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="25fb3-109">이 문서에서는 .NET에서 gRPC 앱을 개발할 때 발생하는 일반적인 문제를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-109">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="25fb3-110">클라이언트와 서비스의 SSL/TLS 구성이 일치하지 않음</span><span class="sxs-lookup"><span data-stu-id="25fb3-110">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="25fb3-111">gRPC 템플릿과 샘플은 기본적으로 [TLS](https://tools.ietf.org/html/rfc5246)(전송 계층 보안)를 사용하여 gRPC 서비스를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-111">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="25fb3-112">gRPC 클라이언트는 보안 연결을 사용하여 안전한 gRPC 서비스를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-112">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="25fb3-113">앱 시작 시 작성된 로그에서 ASP.NET Core gRPC 서비스가 TLS를 사용하고 있는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-113">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="25fb3-114">서비스는 HTTPS 엔드포인트에서 수신 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-114">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="25fb3-115">.NET Core 클라이언트는 서버 주소에 `https`를 사용하여 보안 연결을 통해 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-115">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="25fb3-116">모든 gRPC 클라이언트 구현에서 TLS를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-116">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="25fb3-117">다른 언어의 gRPC 클라이언트에는 일반적으로 `SslCredentials`로 구성된 채널이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-117">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="25fb3-118">`SslCredentials`는 클라이언트에서 사용할 인증서를 지정하며, 안전하지 않은 자격 증명 대신 이 인증서를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-118">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="25fb3-119">TLS를 사용하도록 여러 gRPC 클라이언트 구현을 구성하는 예제는 [gRPC 인증](https://www.grpc.io/docs/guides/auth/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="25fb3-119">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="25fb3-120">신뢰할 수 없는/잘못된 인증서를 사용하여 gRPC 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="25fb3-120">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="25fb3-121">.NET gRPC 클라이언트를 사용하려면 서비스에 신뢰할 수 있는 인증서가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-121">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="25fb3-122">신뢰할 수 있는 인증서 없이 gRPC 서비스를 호출할 경우 다음 오류 메시지가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-122">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="25fb3-123">처리되지 않은 예외가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-123">Unhandled exception.</span></span> <span data-ttu-id="25fb3-124">System.Net.Http.HttpRequestException: SSL 연결을 설정할 수 없습니다. 내부 예외를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="25fb3-124">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="25fb3-125">---> System.Security.Authentication.AuthenticationException: 유효성 검사 절차에 따르면 원격 인증서가 잘못되었습니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-125">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="25fb3-126">로컬에서 앱을 테스트 중이며 ASP.NET Core HTTPS 개발 인증서를 신뢰할 수 없는 경우 이 오류가 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-126">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="25fb3-127">이 문제의 해결 지침은 [Windows 및 macOS에서 ASP.NET Core HTTPS 개발 인증서 신뢰](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="25fb3-127">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="25fb3-128">다른 머신에서 gRPC 서비스를 호출 중이며 인증서를 신뢰할 수 없는 경우 잘못된 인증서를 무시하도록 gRPC 클라이언트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-128">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="25fb3-129">다음 코드는 [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback)을 사용하여 신뢰할 수 있는 인증서 없이 호출을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-129">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="25fb3-130">신뢰할 수 없는 인증서는 앱 개발 중에만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-130">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="25fb3-131">프로덕션 앱은 항상 유효한 인증서를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-131">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="25fb3-132">.NET Core 클라이언트를 사용하여 안전하지 않은 gRPC 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="25fb3-132">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="25fb3-133">.NET Core 클라이언트를 사용하여 안전하지 않은 gRPC 서비스를 호출하려면 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-133">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="25fb3-134">gRPC 클라이언트는 `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` 스위치를 `true`로 설정하고 서버 주소에 `http`를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-134">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="25fb3-135">macOS에서 ASP.NET Core gRPC 앱을 시작할 수 없음</span><span class="sxs-lookup"><span data-stu-id="25fb3-135">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="25fb3-136">Kestrel은 macOS 및 이전 Windows 버전(예: Windows 7)에서 TLS를 사용하는 HTTP/2를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-136">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="25fb3-137">ASP.NET Core gRPC 템플릿과 샘플은 기본적으로 TLS를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-137">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="25fb3-138">gRPC 서버를 시작하려고 하면 다음과 같은 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-138">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="25fb3-139">IPv4 루프백 인터페이스에서 https://localhost:5001 에 바인딩할 수 없습니다. ‘macOS에는 ALPN 지원이 없으므로 TLS를 통한 HTTP/2가 지원되지 않습니다.’</span><span class="sxs-lookup"><span data-stu-id="25fb3-139">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="25fb3-140">이 문제를 해결하려면 TLS ‘없이’ HTTP/2를 사용하도록 Kestrel 및 gRPC 클라이언트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-140">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="25fb3-141">이 작업은 개발 중에만 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-141">You should only do this during development.</span></span> <span data-ttu-id="25fb3-142">TLS를 사용하지 않으면 gRPC 메시지가 암호화되지 않고 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-142">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="25fb3-143">Kestrel은 *Program.cs*에서 TLS 없이 HTTP/2 엔드포인트를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-143">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="25fb3-144">TLS 없이 HTTP/2 엔드포인트를 구성한 경우 엔드포인트의 [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols)를 `HttpProtocols.Http2`로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-144">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="25fb3-145">HTTP/2를 협상하려면 TLS가 필요하기 때문에 `HttpProtocols.Http1AndHttp2`를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-145">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="25fb3-146">TLS가 없을 경우 모든 엔드포인트 연결은 기본적으로 HTTP/1.1로 설정되며 gRPC 호출이 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-146">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="25fb3-147">또한 TLS를 사용하지 않도록 gRPC 클라이언트를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-147">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="25fb3-148">자세한 내용은 [.NET Core 클라이언트를 사용하여 안전하지 않은 gRPC 서비스 호출](#call-insecure-grpc-services-with-net-core-client)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="25fb3-148">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="25fb3-149">TLS가 없는 HTTP/2는 앱 개발 중에만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-149">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="25fb3-150">프로덕션 앱은 항상 전송 보안을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-150">Production apps should always use transport security.</span></span> <span data-ttu-id="25fb3-151">자세한 내용은 [ASP.NET Core용 gRPC의 보안 고려 사항](xref:grpc/security#transport-security)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="25fb3-151">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="25fb3-152">gRPC C# 자산은 .proto 파일에서 생성된 코드가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-152">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="25fb3-153">구체적인 클라이언트와 서비스 기본 클래스의 gRPC 코드를 생성하려면 프로젝트에서 protobuf 파일 및 도구를 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-153">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="25fb3-154">다음을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-154">You must include:</span></span>

* <span data-ttu-id="25fb3-155">`<Protobuf>` 항목 그룹에서 사용하려는 *.proto* 파일.</span><span class="sxs-lookup"><span data-stu-id="25fb3-155">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="25fb3-156">[가져온 *.proto* 파일](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions)을 프로젝트에서 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-156">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="25fb3-157">gRPC 도구 패키지인 [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)의 패키지 참조</span><span class="sxs-lookup"><span data-stu-id="25fb3-157">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="25fb3-158">gRPC C# 자산을 생성하는 방법에 대한 자세한 내용은 <xref:grpc/basics>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="25fb3-158">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="25fb3-159">기본적으로 `<Protobuf>` 참조는 구체적인 클라이언트 및 서비스 기본 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-159">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="25fb3-160">reference 요소의 `GrpcServices` 특성을 사용하여 C# 자산 생성을 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-160">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="25fb3-161">유효한 `GrpcServices` 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-161">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="25fb3-162">`Both`(없을 경우 기본값)</span><span class="sxs-lookup"><span data-stu-id="25fb3-162">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="25fb3-163">gRPC 서비스를 호스트하는 ASP.NET Core 웹앱에는 생성된 서비스 기본 클래스만 있으면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-163">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="25fb3-164">gRPC 호출을 수행하는 gRPC 클라이언트 앱에는 생성된 구체적인 클라이언트만 있으면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-164">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="25fb3-165">WPF 프로젝트가 .proto 파일에서 gRPC C# 자산을 생성할 수 없음</span><span class="sxs-lookup"><span data-stu-id="25fb3-165">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="25fb3-166">WPF 프로젝트에는 gRPC 코드 생성이 제대로 작동하지 않도록 하는 [알려진 문제](https://github.com/dotnet/wpf/issues/810)가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-166">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="25fb3-167">`Grpc.Tools` 및 *.proto* 파일을 참조하여 WPF 프로젝트에서 생성된 gRPC 형식을 사용하면 컴파일 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-167">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="25fb3-168">오류 CS0246: 형식 또는 네임스페이스 이름 ‘MyGrpcServices’를 찾을 수 없습니다. (사용 중인 지시문 또는 어셈블리 참조가 없습니까?)</span><span class="sxs-lookup"><span data-stu-id="25fb3-168">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="25fb3-169">이 문제는 다음과 같은 방법으로 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-169">You can workaround this issue by:</span></span>

1. <span data-ttu-id="25fb3-170">새 .NET Core 클래스 라이브러리 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-170">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="25fb3-171">새 프로젝트에서 참조를 추가하여 [ *\*.proto* 파일에서 C# 코드 생성](xref:grpc/basics#generated-c-assets)을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-171">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="25fb3-172">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-172">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="25fb3-173">`<Protobuf>` 항목 그룹에 *\*.proto* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-173">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="25fb3-174">WPF 애플리케이션에서 새 프로젝트의 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-174">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="25fb3-175">WPF 애플리케이션은 새 클래스 라이브러리 프로젝트의 gRPC 생성 형식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25fb3-175">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
