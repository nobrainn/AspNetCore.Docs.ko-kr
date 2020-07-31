---
title: ASP.NET Core에서 Windows 인증 구성
author: scottaddie
description: IIS 및 HTTP.sys에 대 한 ASP.NET Core에서 Windows 인증을 구성 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330688"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>ASP.NET Core에서 Windows 인증 구성

작성자: [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

[IIS](xref:host-and-deploy/iis/index), [kestrel](xref:fundamentals/servers/kestrel)또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용 하 여 호스트 되는 ASP.NET Core 앱에 대해 Windows 인증 (Negotiate, Kerberos 또는 NTLM 인증이 라고도 함)을 구성할 수 있습니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[IIS](xref:host-and-deploy/iis/index) 또는 [HTTP.sys](xref:fundamentals/servers/httpsys)를 사용 하 여 호스트 되는 ASP.NET Core 앱에 대해 Windows 인증 (NEGOTIATE, Kerberos 또는 NTLM 인증이 라고도 함)을 구성할 수 있습니다.

::: moniker-end

Windows 인증은 운영 체제를 사용 하 여 ASP.NET Core 앱의 사용자를 인증 합니다. 사용자를 식별 하기 위해 Active Directory 도메인 id 또는 Windows 계정을 사용 하 여 회사 네트워크에서 서버를 실행 하는 경우 Windows 인증을 사용할 수 있습니다. Windows 인증은 사용자, 클라이언트 앱 및 웹 서버가 동일한 Windows 도메인에 속하는 인트라넷 환경에 가장 적합 합니다.

> [!NOTE]
> HTTP/2에서는 Windows 인증이 지원 되지 않습니다. 인증 문제는 HTTP/2 응답에서 전송 될 수 있지만 인증 하기 전에 클라이언트에서 HTTP/1.1로 다운 그레이드 해야 합니다.

## <a name="proxy-and-load-balancer-scenarios"></a>프록시 및 부하 분산 장치 시나리오

Windows 인증은 주로 인트라넷에서 사용 되는 상태 저장 시나리오로, 일반적으로 프록시 또는 부하 분산 장치는 클라이언트와 서버 간의 트래픽을 처리 하지 않습니다. 프록시 또는 부하 분산 장치를 사용 하는 경우 Windows 인증은 프록시 또는 부하 분산 장치에 대해서만 작동 합니다.

* 인증을 처리 합니다.
* 인증 정보에 대 한 역할을 하는 응용 프로그램 (예: 요청 헤더)에 사용자 인증 정보를 전달 합니다.

프록시 및 부하 분산 장치를 사용 하는 환경에서 Windows 인증에 대 한 대안은 OIDC (Openid connect Connect)를 사용 하는 페더레이션 서비스 (ADFS) Active Directory입니다.

## <a name="iisiis-express"></a>IIS/IIS Express

<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> 다음 위치에서 (네임 스페이스)를 호출 하 여 인증 서비스를 추가 합니다 `Startup.ConfigureServices` .

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a>시작 설정 (디버거)

시작 설정 구성은 IIS Express 파일의 *속성/launchSettings.js* 에만 영향을 주며 WINDOWS 인증용 IIS는 구성 하지 않습니다. 서버 구성은 [IIS](#iis) 섹션에 설명 되어 있습니다.

Visual Studio 또는 .NET Core CLI를 통해 사용할 수 있는 **웹 응용 프로그램** 템플릿은 Windows 인증을 지원 하도록 구성 될 수 있습니다. 그러면 파일에서 자동으로 *속성/launchSettings.js* 를 업데이트 합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**새 프로젝트**

1. 새 프로젝트를 만듭니다.
1. **새 ASP.NET Core 웹 애플리케이션**을 선택합니다. **새로 만들기**를 선택합니다.
1. **프로젝트 이름** 필드에 이름을 입력 합니다. **위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다. **만들기**를 선택합니다.
1. **인증**에서 **변경** 을 선택 합니다.
1. **인증 변경** 창에서 **Windows 인증**을 선택 합니다. **확인**을 선택합니다.
1. **웹 애플리케이션**을 선택합니다.
1. **만들기**를 선택합니다.

앱을 실행합니다. 사용자 이름은 렌더링 된 앱의 사용자 인터페이스에 표시 됩니다.

**기존 프로젝트**

프로젝트의 속성은 Windows 인증을 사용 하도록 설정 하 고 익명 인증을 사용 하지 않도록 설정 합니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
1. **디버그** 탭을 선택합니다.
1. **익명 인증 사용**에 대 한 확인란의 선택을 취소 합니다.
1. **Windows 인증 사용**확인란을 선택 합니다.
1. 속성 페이지를 저장 하 고 닫습니다.

또는 파일의launchSettings.js노드에서 속성을 구성할 수 있습니다 `iisSettings` . *launchSettings.json*

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

**새 프로젝트**

인수 (ASP.NET Core 웹 앱)를 사용 하 여 [dotnet new](/dotnet/core/tools/dotnet-new) 명령을 실행 `webapp` 하 고 `--auth Windows` 다음을 전환 합니다.

```dotnetcli
dotnet new webapp --auth Windows
```

**기존 프로젝트**

파일의 `iisSettings` *launchSettings.js* 노드를 업데이트 합니다.

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

기존 프로젝트를 수정할 때는 프로젝트 파일에 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) **또는** [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet 패키지에 대 한 패키지 참조가 포함 되어 있는지 확인 합니다.

### <a name="iis"></a>IIS

IIS는 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) 을 사용 하 여 ASP.NET Core 앱을 호스팅합니다. Windows 인증은 *web.config* 파일을 통해 IIS에 대해 구성 됩니다. 다음 섹션에서는 수행 방법을 보여 줍니다.

* 앱이 배포 될 때 서버에서 Windows 인증을 활성화 하는 로컬 *web.config* 파일을 제공 합니다.
* IIS 관리자를 사용 하 여 서버에 이미 배포 된 ASP.NET Core 앱의 *web.config* 파일을 구성 합니다.

아직 수행 하지 않은 경우 IIS에서 ASP.NET Core 앱을 호스트할 수 있도록 설정 합니다. 자세한 내용은 <xref:host-and-deploy/iis/index>를 참조하세요.

Windows 인증에 대해 IIS 역할 서비스를 사용 하도록 설정 합니다. 자세한 내용은 [IIS 역할 서비스에서 Windows 인증 사용 (2 단계 참조)](xref:host-and-deploy/iis/index#iis-configuration)을 참조 하세요.

[IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 는 기본적으로 요청을 자동으로 인증 하도록 구성 됩니다. 자세한 내용은 [iis를 사용 하 여 Windows에서 호스트 ASP.NET Core: iis 옵션 (자동 인증)](xref:host-and-deploy/iis/index#iis-options)을 참조 하세요.

ASP.NET Core 모듈은 기본적으로 Windows 인증 토큰을 앱에 전달 하도록 구성 되어 있습니다. 자세한 내용은 [ASP.NET Core 모듈 구성 참조: aspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)을 참조 하세요.

다음 방법 중 **하나** 를 사용 합니다.

* **프로젝트를 게시 하 고 배포 하기 전에** 프로젝트 루트에 다음 *web.config* 파일을 추가 합니다.

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  프로젝트 파일에서 속성을로 설정 하지 않고 .NET Core SDK에서 프로젝트를 게시 하면 `<IsTransformWebConfigDisabled>` `true` 게시 된 *web.config* 파일에 섹션이 포함 됩니다 `<location><system.webServer><security><authentication>` . 에 대 한 자세한 합니다 `<IsTransformWebConfigDisabled>` 속성 참조 <xref:host-and-deploy/iis/index#webconfig-file>합니다.

* **프로젝트를 게시 하 고 배포한 후** 에는 IIS 관리자를 사용 하 여 서버 쪽 구성을 수행 합니다.

  1. IIS 관리자의 **연결** 세로 막대에 있는 **사이트** 노드 아래에서 iis 사이트를 선택 합니다.
  1. **IIS** 영역에서 **인증** 을 두 번 클릭 합니다.
  1. **익명 인증**을 선택 합니다. **작업** 사이드바에서 **사용 안 함** 을 선택 합니다.
  1. **Windows 인증**을 선택하고 **작업** 사이드바에서 **사용** 을 선택 합니다.

  이러한 작업을 수행 하는 경우 IIS 관리자는 앱의 *web.config* 파일을 수정 합니다. `<system.webServer><security><authentication>`및에 대 한 업데이트 된 설정이 포함 된 노드가 추가 됩니다 `anonymousAuthentication` `windowsAuthentication` .

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  `<system.webServer>`IIS 관리자에서 *web.config* 파일에 추가 하는 섹션은 `<location>` 앱이 게시 될 때 .NET Core SDK에 의해 추가 된 앱 섹션을 벗어납니다. 섹션이 노드 외부에 추가 되기 때문에 `<location>` 모든 [하위 앱](xref:host-and-deploy/iis/index#sub-applications) 에서 현재 앱에 대 한 설정이 상속 됩니다. 상속을 방지 하려면 `<security>` .NET Core SDK 제공 된 섹션 내에서 추가 된 섹션을 이동 합니다 `<location><system.webServer>` .

  Iis 관리자를 사용 하 여 IIS 구성을 추가 하면 서버에 있는 앱의 *web.config* 파일에만 영향을 줍니다. 서버의 *web.config* 복사본이 프로젝트의 *web.config* 파일로 대체 되 면 앱의 후속 배포에서 서버의 설정을 덮어쓸 수 있습니다. 다음 방법 중 **하나** 를 사용 하 여 설정을 관리 합니다.

  * 배포 시 파일을 덮어쓴 후에 IIS 관리자를 사용 하 여 *web.config* 파일의 설정을 다시 설정 합니다.
  * 설정을 사용 하 여 로컬에 *web.config 파일* 을 앱에 추가 합니다.

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a>Kestrel

Windows, Linux 및 macOS에서 Negotiate 및 Kerberos를 사용 하 여 Windows 인증을 지원 하기 위해 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet 패키지를 [kestrel](xref:fundamentals/servers/kestrel) 과 함께 사용할 수 있습니다.

> [!WARNING]
> 자격 증명은 연결에 대 한 요청에서 지속 될 수 있습니다. *프록시가 Kestrel을 사용 하 여 1:1 연결 선호도 (영구 연결)를 유지 관리 하지 않는 한 Negotiate authentication은 프록시와 함께 사용 하면 안 됩니다.*

> [!NOTE]
> Negotiate 처리기는 기본 서버가 Windows 인증을 기본적으로 지원 하는지 여부를 검색 합니다. 서버에서 Windows 인증을 지원 하지만 사용할 수 없는 경우 서버 구현을 사용 하도록 요청 하는 오류가 발생 합니다. 서버에서 Windows 인증을 사용 하도록 설정 하면 Negotiate 핸들러가 투명 하 게이를 전달 합니다.

에서 및을 호출 하 여 인증 서비스를 추가 합니다 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> `Startup.ConfigureServices` .

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

에서를 호출 하 여 인증 미들웨어를 추가 합니다 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> `Startup.Configure` .

 ```csharp
app.UseAuthentication();
```

미들웨어에 대 한 자세한 내용은을 참조 하십시오 <xref:fundamentals/middleware/index> .

익명 요청은 허용 됩니다. [ASP.NET Core 권한 부여](xref:security/authorization/introduction) 를 사용 하 여 인증에 대 한 익명 요청을 시도 합니다.

### <a name="windows-environment-configuration"></a>Windows 환경 구성

[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) 구성 요소는 사용자 모드 인증을 수행 합니다. Spn (서비스 사용자 이름)은 컴퓨터 계정이 아니라 서비스를 실행 하는 사용자 계정에 추가 해야 합니다. `setspn -S HTTP/myservername.mydomain.com myuser`관리 명령 셸에서를 실행 합니다.

### <a name="linux-and-macos-environment-configuration"></a>Linux 및 macOS 환경 구성

Linux 또는 macOS 컴퓨터를 Windows 도메인에 가입 하는 방법에 대 한 지침은 [windows 인증을 사용 하 여 SQL Server에 연결 Azure Data Studio-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) 문서에서 사용할 수 있습니다. 지침은 도메인에서 Linux 컴퓨터에 대 한 컴퓨터 계정을 만듭니다. 해당 컴퓨터 계정에 Spn을 추가 해야 합니다.

> [!NOTE]
> [Windows 인증을 사용 하 여 SQL Server에 Azure Data Studio 연결-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) 문서의 지침에 따라 `python-software-properties` 필요한 경우을로 바꿉니다 `python3-software-properties` .

Linux 또는 macOS 컴퓨터가 도메인에 가입 된 후에는 [keytab 파일](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) 을 spn과 함께 제공 하기 위해 추가 단계가 필요 합니다.

* 도메인 컨트롤러에서 컴퓨터 계정에 새 웹 서비스 Spn을 추가 합니다.
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* [Ktpass](/windows-server/administration/windows-commands/ktpass) 를 사용 하 여 keytab 파일을 생성 합니다.
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * 일부 필드는 표시 된 대로 대문자로 지정 해야 합니다.
* Linux 또는 macOS 컴퓨터에 keytab 파일을 복사 합니다.
* 환경 변수를 통해 keytab 파일을 선택 합니다.`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`
* `klist`을 호출 하 여 현재 사용할 수 있는 spn을 표시 합니다.

> [!NOTE]
> Keytab 파일은 도메인 액세스 자격 증명을 포함 하므로 적절 하 게 보호 해야 합니다.

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) 은 NEGOTIATE, NTLM 또는 기본 인증을 사용 하는 커널 모드 Windows 인증을 지원 합니다.

<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 다음 위치에서 (네임 스페이스)를 호출 하 여 인증 서비스를 추가 합니다 `Startup.ConfigureServices` .

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

Windows 인증 (*Program.cs*)과 함께 HTTP.sys를 사용 하도록 앱의 웹 호스트를 구성 합니다. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>는 <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 네임 스페이스에 있습니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다. 사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다. 머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다. 앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.

> [!NOTE]
> HTTP.sys은 Nano Server 버전 1709 이상에서 지원 되지 않습니다. Nano Server에서 Windows 인증 및 HTTP.sys을 사용 하려면 [Server Core (microsoft/windowsservercore) 컨테이너](https://hub.docker.com/r/microsoft/windowsservercore/)를 사용 합니다. Server Core에 대 한 자세한 내용은 [Windows server에서 Server core 설치 옵션 이란?](/windows-server/administration/server-core/what-is-server-core)을 참조 하세요.

## <a name="authorize-users"></a>사용자 권한 부여

익명 액세스의 구성 상태는 `[Authorize]` 앱에서 및 특성을 사용 하는 방법을 결정 합니다 `[AllowAnonymous]` . 다음 두 섹션에서는 익명 액세스의 허용 되지 않는 구성 상태 및 허용 되는 구성 상태를 처리 하는 방법을 설명 합니다.

### <a name="disallow-anonymous-access"></a>익명 액세스 허용 안 함

Windows 인증을 사용 하도록 설정 하 고 익명 액세스를 사용 하지 않도록 설정 하면 및 특성은 아무런 영향을 주지 `[Authorize]` `[AllowAnonymous]` 않습니다. IIS 사이트에서 익명 액세스를 허용 하지 않도록 구성 된 경우 요청은 앱에 도달 하지 않습니다. 따라서 `[AllowAnonymous]` 특성을 적용할 수 없습니다.

### <a name="allow-anonymous-access"></a>익명 액세스 허용

Windows 인증 및 익명 액세스를 모두 사용 하는 경우 `[Authorize]` 및 특성을 사용 `[AllowAnonymous]` 합니다. `[Authorize]`특성을 사용 하면 인증이 필요한 앱의 끝점을 보호할 수 있습니다. `[AllowAnonymous]`특성은 `[Authorize]` 익명 액세스를 허용 하는 앱의 특성을 재정의 합니다. 특성 사용에 대 한 자세한 내용은을 참조 하십시오 <xref:security/authorization/simple> .

> [!NOTE]
> 기본적으로 페이지에 대 한 액세스 권한이 없는 사용자에 게는 빈 HTTP 403 응답이 표시 됩니다. 사용자에 게 더 나은 "액세스 거부" 환경을 제공 하도록 [Statuscodepages 페이지 미들웨어](xref:fundamentals/error-handling#usestatuscodepages) 를 구성할 수 있습니다.

## <a name="impersonation"></a>가장

ASP.NET Core 가장을 구현 하지 않습니다. 앱은 앱 풀 또는 프로세스 id를 사용 하 여 모든 요청에 대해 앱 id로 실행 됩니다. 앱이 사용자를 대신 하 여 작업을 수행 해야 하는 경우의 [터미널 인라인 미들웨어](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 에서 [가장 된 WindowsIdentity](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) 를 사용 `Startup.Configure` 합니다. 이 컨텍스트에서 단일 작업을 실행 한 다음 컨텍스트를 닫습니다.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated`는 비동기 작업을 지원 하지 않으며 복잡 한 시나리오에 사용할 수 없습니다. 예를 들어 전체 요청 또는 미들웨어 체인 래핑은 지원 되지 않거나 권장 되지 않습니다.

::: moniker range=">= aspnetcore-3.0"

[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) 패키지는 Windows, Linux 및 macos에서 인증을 사용 하도록 설정 하는 반면 가장은 windows 에서만 지원 됩니다.

::: moniker-end

## <a name="claims-transformations"></a>클레임 변환

::: moniker range=">= aspnetcore-3.0"

IIS를 사용 하 여 호스트할 때 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 는 사용자를 초기화 하기 위해 내부적으로 호출 되지 않습니다. 따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다. 클레임 변환을 활성화 하는 코드 예제 및 자세한 내용은을 참조 하십시오 <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

IIS in-process 모드로 호스트 하는 경우 사용자를 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 초기화 하기 위해가 내부적으로 호출 되지 않습니다. 따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다. In-process를 호스팅할 때 클레임 변환을 활성화 하는 코드 예제 및 자세한 내용은을 참조 하십시오 <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

## <a name="additional-resources"></a>추가 자료

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
