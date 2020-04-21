---
title: ASP.NET 코어에서 개발 에 응용 프로그램 비밀의 안전한 저장
author: rick-anderson
description: ASP.NET Core 앱을 개발하는 동안 중요한 정보를 앱 비밀로 저장하고 검색하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: 9d4e59c003afc253971ee64fce523c7188d3582a
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661794"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>ASP.NET 코어에서 개발 에 응용 프로그램 비밀의 안전한 저장

::: moniker range=">= aspnetcore-3.0"

[릭 앤더슨,](https://twitter.com/RickAndMSFT) [커크 라킨,](https://twitter.com/serpent5) [다니엘 로스,](https://github.com/danroth27) [스콧 애디](https://github.com/scottaddie)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

이 문서에서는 개발 컴퓨터에서 ASP.NET Core 앱을 개발하는 동안 중요한 데이터를 저장하고 검색하는 기술을 설명합니다. 암호 또는 기타 중요한 데이터를 소스 코드에 저장하지 마십시오. 프로덕션 비밀은 개발 또는 테스트에 사용해서는 안 됩니다. 비밀은 앱과 함께 배포해서는 안 됩니다. 대신 환경 변수, Azure 키 볼트 등과 같은 제어된 수단을 통해 프로덕션 환경에서 비밀을 사용할 수 있어야 합니다. [Azure Key Vault 구성 공급자를](xref:security/key-vault-configuration)통해 Azure 테스트 및 프로덕션 비밀을 저장하고 보호할 수 있습니다.

## <a name="environment-variables"></a>환경 변수

환경 변수는 코드 또는 로컬 구성 파일에 앱 비밀의 저장을 방지하는 데 사용됩니다. 환경 변수는 이전에 지정된 모든 구성 원본에 대한 구성 값을 재정의합니다.

**개별 사용자 계정** 보안이 활성화된 ASP.NET Core 웹 앱을 고려합니다. 기본 데이터베이스 연결 문자열은 키와 `DefaultConnection`함께 프로젝트의 *appsettings.json* 파일에 포함 됩니다. 기본 연결 문자열은 사용자 모드에서 실행되며 암호가 필요하지 않은 LocalDB용입니다. 앱 배포 중에 `DefaultConnection` 환경 변수의 값으로 키 값을 재정의할 수 있습니다. 환경 변수는 중요한 자격 증명을 사용하여 전체 연결 문자열을 저장할 수 있습니다.

> [!WARNING]
> 환경 변수는 일반적으로 암호화되지 않은 일반 텍스트로 저장됩니다. 컴퓨터 또는 프로세스가 손상된 경우 신뢰할 수 없는 당사자가 환경 변수에 액세스할 수 있습니다. 사용자 기밀의 공개를 방지하기 위한 추가 조치가 필요할 수 있습니다.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>비밀 관리자

비밀 관리자 도구는 ASP.NET 핵심 프로젝트를 개발하는 동안 중요한 데이터를 저장합니다. 이 컨텍스트에서 중요한 데이터는 앱 보안 정보입니다. 앱 암호는 프로젝트 트리와 별도의 위치에 저장됩니다. 앱 비밀은 특정 프로젝트와 연결되거나 여러 프로젝트에서 공유됩니다. 앱 비밀이 소스 제어에 체크 인되지 않습니다.

> [!WARNING]
> 비밀 관리자 도구는 저장된 암호를 암호화하지 않으며 신뢰할 수 있는 저장소로 취급해서는 안 됩니다. 그것은 단지 개발 목적을 위한 것입니다. 키와 값은 사용자 프로필 디렉터리에서 JSON 구성 파일에 저장됩니다.

## <a name="how-the-secret-manager-tool-works"></a>시크릿 관리자 도구의 작동 방식

비밀 관리자 도구는 값이 저장되는 위치와 방법과 같은 구현 세부 정보를 추상화합니다. 이러한 구현 세부 정보를 알지 못해도 도구를 사용할 수 있습니다. 값은 로컬 컴퓨터의 시스템 보호 사용자 프로필 폴더에 JSON 구성 파일에 저장됩니다.

# <a name="windows"></a>[Windows](#tab/windows)

파일 시스템 경로:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[리눅스 / 맥OS](#tab/linux+macos)

파일 시스템 경로:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

앞의 파일 경로에서 `<user_secrets_id>` *.csproj* 파일에 지정된 `UserSecretsId` 값으로 바꿉습니다.

비밀 관리자 도구로 저장된 데이터의 위치 나 형식에 따라 코드를 작성하지 마십시오. 이러한 구현 세부 정보는 변경될 수 있습니다. 예를 들어 보안 값은 암호화되지 않지만 나중에 있을 수 있습니다.

## <a name="enable-secret-storage"></a>비밀 저장 사용

비밀 관리자 도구는 사용자 프로필에 저장된 프로젝트별 구성 설정에서 작동합니다.

비밀 관리자 도구에는 `init` .NET 코어 SDK 3.0.100 이상의 명령이 포함되어 있습니다. 사용자 비밀을 사용하려면 프로젝트 디렉터리에서 다음 명령을 실행합니다.

```dotnetcli
dotnet user-secrets init
```

앞의 명령은 `UserSecretsId` *.csproj* 파일 `PropertyGroup` 내에 요소를 추가합니다. 기본적으로 내부 `UserSecretsId` 텍스트는 GUID입니다. 내부 텍스트는 임의이지만 프로젝트에 고유합니다.

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

Visual Studio에서 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 컨텍스트 메뉴에서 **사용자 암호 관리를** 선택합니다. 이 제스처는 `UserSecretsId` GUID로 채워진 요소를 *.csproj* 파일에 추가합니다.

## <a name="set-a-secret"></a>비밀 설정

키와 해당 값으로 구성된 앱 암호를 정의합니다. 비밀은 프로젝트의 `UserSecretsId` 값과 관련이 있습니다. 예를 들어 *.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

앞의 예제에서 콜론은 `Movies` `ServiceApiKey` 속성이 있는 개체 리터럴을 나타냅니다.

비밀 관리자 도구도 다른 디렉토리에서 사용할 수 있습니다. `--project` 옵션을 사용하여 *.csproj* 파일이 있는 파일 시스템 경로를 제공합니다. 다음은 그 예입니다.

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>비주얼 스튜디오에서 JSON 구조 평탄화

Visual Studio의 **사용자 비밀 관리** 제스처는 텍스트 편집기에서 *secrets.json* 파일을 엽니다. *secrets.json의* 내용을 저장할 키 값 쌍으로 바꿉니다. 다음은 그 예입니다.

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON 구조를 통해 `dotnet user-secrets remove` 또는 `dotnet user-secrets set`수정 후 평평해져 있습니다. 예를 들어 `dotnet user-secrets remove "Movies:ConnectionString"` 실행하면 `Movies` 개체가 리터럴로 축소됩니다. 수정된 파일은 다음과 유사합니다.

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>여러 비밀 설정

`set` JSON을 명령으로 파이핑하여 일괄 처리를 설정할 수 있습니다. 다음 예제에서는 *input.json* 파일의 내용을 `set` 명령에 파이프합니다.

# <a name="windows"></a>[Windows](#tab/windows)

명령 셸을 열고 다음 명령을 실행합니다.

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[리눅스 / 맥OS](#tab/linux+macos)

명령 셸을 열고 다음 명령을 실행합니다.

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>비밀에 액세스

[ASP.NET 핵심 구성 API는](xref:fundamentals/configuration/index) 비밀 관리자 비밀에 대한 액세스를 제공합니다.

ASP.NET Core 2.0 이상에서는 프로젝트가 미리 구성된 기본값으로 호스트의 새 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 인스턴스를 초기화하기 위해 호출할 때 사용자 비밀 구성 소스가 개발 모드에 자동으로 추가됩니다. `CreateDefaultBuilder`있는 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> 경우 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>호출 :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

`CreateDefaultBuilder` 호출되지 않으면 생성자를 호출하여 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> 사용자 암호 구성 `Startup` 원본을 명시적으로 추가합니다. 다음 `AddUserSecrets` 예제와 같이 앱이 개발 환경에서 실행되는 경우에만 호출합니다.

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

사용자 암호는 API를 `Configuration` 통해 검색할 수 있습니다.

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]


## <a name="map-secrets-to-a-poco"></a>POCO에 비밀 매핑

전체 개체를 POCO(속성이 있는 간단한 .NET 클래스)에 매핑하는 것은 관련 속성을 집계하는 데 유용합니다.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

선행 비밀을 POCO에 매핑하려면 `Configuration` API의 개체 [그래프 바인딩](xref:fundamentals/configuration/index#bind-to-an-object-graph) 기능을 사용합니다. 다음 코드는 사용자 지정 `MovieSettings` POCO에 바인딩하고 속성 값에 `ServiceApiKey` 액세스합니다.

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString` 및 `Movies:ServiceApiKey` 비밀은 `MovieSettings`다음의 각 속성에 매핑됩니다.

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>비밀이 있는 문자열 교체

암호를 일반 텍스트로 저장하는 것은 안전하지 않습니다. 예를 들어 *appsettings.json에* 저장된 데이터베이스 연결 문자열에는 지정된 사용자의 암호가 포함될 수 있습니다.

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

보다 안전한 방법은 암호를 암호로 저장하는 것입니다. 다음은 그 예입니다.

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password` *appsettings.json의*연결 문자열에서 키-값 쌍을 제거합니다. 다음은 그 예입니다.

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

secret의 값을 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> 개체의 <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> 속성에 설정하여 연결 문자열을 완료할 수 있습니다.

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>비밀 목록

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.

```dotnetcli
dotnet user-secrets list
```

다음 출력이 표시됩니다.

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

앞의 예제에서 키 이름의 콜론은 *secrets.json*내의 개체 계층 구조를 나타냅니다.

## <a name="remove-a-single-secret"></a>단일 비밀 제거

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

앱의 *secrets.json* 파일이 `MoviesConnectionString` 키와 연결된 키 값 쌍을 제거하도록 수정되었습니다.

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`다음 메시지가 표시됩니다.

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>모든 비밀 제거

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.

```dotnetcli
dotnet user-secrets clear
```

응용 프로그램에 대한 모든 사용자 암호는 *secrets.json* 파일에서 삭제되었습니다 :

```json
{}
```

실행 `dotnet user-secrets list` 중에는 다음 메시지가 표시됩니다.

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>추가 자료

* IIS에서 비밀 관리자에 액세스하는 방법에 대한 자세한 내용은 [이 문제를](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 참조하십시오.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[릭 앤더슨,](https://twitter.com/RickAndMSFT) [다니엘 로스,](https://github.com/danroth27) [스콧 애디](https://github.com/scottaddie)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

이 문서에서는 개발 컴퓨터에서 ASP.NET Core 앱을 개발하는 동안 중요한 데이터를 저장하고 검색하는 기술을 설명합니다. 암호 또는 기타 중요한 데이터를 소스 코드에 저장하지 마십시오. 프로덕션 비밀은 개발 또는 테스트에 사용해서는 안 됩니다. 비밀은 앱과 함께 배포해서는 안 됩니다. 대신 환경 변수, Azure 키 볼트 등과 같은 제어된 수단을 통해 프로덕션 환경에서 비밀을 사용할 수 있어야 합니다. [Azure Key Vault 구성 공급자를](xref:security/key-vault-configuration)통해 Azure 테스트 및 프로덕션 비밀을 저장하고 보호할 수 있습니다.

## <a name="environment-variables"></a>환경 변수

환경 변수는 코드 또는 로컬 구성 파일에 앱 비밀의 저장을 방지하는 데 사용됩니다. 환경 변수는 이전에 지정된 모든 구성 원본에 대한 구성 값을 재정의합니다.

**개별 사용자 계정** 보안이 활성화된 ASP.NET Core 웹 앱을 고려합니다. 기본 데이터베이스 연결 문자열은 키와 `DefaultConnection`함께 프로젝트의 *appsettings.json* 파일에 포함 됩니다. 기본 연결 문자열은 사용자 모드에서 실행되며 암호가 필요하지 않은 LocalDB용입니다. 앱 배포 중에 `DefaultConnection` 환경 변수의 값으로 키 값을 재정의할 수 있습니다. 환경 변수는 중요한 자격 증명을 사용하여 전체 연결 문자열을 저장할 수 있습니다.

> [!WARNING]
> 환경 변수는 일반적으로 암호화되지 않은 일반 텍스트로 저장됩니다. 컴퓨터 또는 프로세스가 손상된 경우 신뢰할 수 없는 당사자가 환경 변수에 액세스할 수 있습니다. 사용자 기밀의 공개를 방지하기 위한 추가 조치가 필요할 수 있습니다.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>비밀 관리자

비밀 관리자 도구는 ASP.NET 핵심 프로젝트를 개발하는 동안 중요한 데이터를 저장합니다. 이 컨텍스트에서 중요한 데이터는 앱 보안 정보입니다. 앱 암호는 프로젝트 트리와 별도의 위치에 저장됩니다. 앱 비밀은 특정 프로젝트와 연결되거나 여러 프로젝트에서 공유됩니다. 앱 비밀이 소스 제어에 체크 인되지 않습니다.

> [!WARNING]
> 비밀 관리자 도구는 저장된 암호를 암호화하지 않으며 신뢰할 수 있는 저장소로 취급해서는 안 됩니다. 그것은 단지 개발 목적을 위한 것입니다. 키와 값은 사용자 프로필 디렉터리에서 JSON 구성 파일에 저장됩니다.

## <a name="how-the-secret-manager-tool-works"></a>시크릿 관리자 도구의 작동 방식

비밀 관리자 도구는 값이 저장되는 위치와 방법과 같은 구현 세부 정보를 추상화합니다. 이러한 구현 세부 정보를 알지 못해도 도구를 사용할 수 있습니다. 값은 로컬 컴퓨터의 시스템 보호 사용자 프로필 폴더에 JSON 구성 파일에 저장됩니다.

# <a name="windows"></a>[Windows](#tab/windows)

파일 시스템 경로:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[리눅스 / 맥OS](#tab/linux+macos)

파일 시스템 경로:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

앞의 파일 경로에서 `<user_secrets_id>` *.csproj* 파일에 지정된 `UserSecretsId` 값으로 바꿉습니다.

비밀 관리자 도구로 저장된 데이터의 위치 나 형식에 따라 코드를 작성하지 마십시오. 이러한 구현 세부 정보는 변경될 수 있습니다. 예를 들어 보안 값은 암호화되지 않지만 나중에 있을 수 있습니다.

## <a name="enable-secret-storage"></a>비밀 저장 사용

비밀 관리자 도구는 사용자 프로필에 저장된 프로젝트별 구성 설정에서 작동합니다.

사용자 암호를 사용하려면 `UserSecretsId` `PropertyGroup` *.csproj* 파일 내에서 요소를 정의합니다. 의 `UserSecretsId` 내부 텍스트는 임의이지만 프로젝트에 고유합니다. 개발자는 일반적으로 에 대한 `UserSecretsId`GUID를 생성합니다.

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> Visual Studio에서 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 컨텍스트 메뉴에서 **사용자 암호 관리를** 선택합니다. 이 제스처는 `UserSecretsId` GUID로 채워진 요소를 *.csproj* 파일에 추가합니다.

## <a name="set-a-secret"></a>비밀 설정

키와 해당 값으로 구성된 앱 암호를 정의합니다. 비밀은 프로젝트의 `UserSecretsId` 값과 관련이 있습니다. 예를 들어 *.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

앞의 예제에서 콜론은 `Movies` `ServiceApiKey` 속성이 있는 개체 리터럴을 나타냅니다.

비밀 관리자 도구도 다른 디렉토리에서 사용할 수 있습니다. `--project` 옵션을 사용하여 *.csproj* 파일이 있는 파일 시스템 경로를 제공합니다. 다음은 그 예입니다.

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>비주얼 스튜디오에서 JSON 구조 평탄화

Visual Studio의 **사용자 비밀 관리** 제스처는 텍스트 편집기에서 *secrets.json* 파일을 엽니다. *secrets.json의* 내용을 저장할 키 값 쌍으로 바꿉니다. 다음은 그 예입니다.

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON 구조를 통해 `dotnet user-secrets remove` 또는 `dotnet user-secrets set`수정 후 평평해져 있습니다. 예를 들어 `dotnet user-secrets remove "Movies:ConnectionString"` 실행하면 `Movies` 개체가 리터럴로 축소됩니다. 수정된 파일은 다음과 유사합니다.

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>여러 비밀 설정

`set` JSON을 명령으로 파이핑하여 일괄 처리를 설정할 수 있습니다. 다음 예제에서는 *input.json* 파일의 내용을 `set` 명령에 파이프합니다.

# <a name="windows"></a>[Windows](#tab/windows)

명령 셸을 열고 다음 명령을 실행합니다.

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[리눅스 / 맥OS](#tab/linux+macos)

명령 셸을 열고 다음 명령을 실행합니다.

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>비밀에 액세스

[ASP.NET 핵심 구성 API는](xref:fundamentals/configuration/index) 비밀 관리자 비밀에 대한 액세스를 제공합니다.

프로젝트가 .NET 프레임워크를 대상으로 하는 경우 [Microsoft.Extensions.Configuration.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 패키지를 설치합니다.


ASP.NET Core 2.0 이상에서는 프로젝트가 미리 구성된 기본값으로 호스트의 새 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 인스턴스를 초기화하기 위해 호출할 때 사용자 비밀 구성 소스가 개발 모드에 자동으로 추가됩니다. `CreateDefaultBuilder`있는 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> 경우 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>호출 :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]


`CreateDefaultBuilder` 호출되지 않으면 생성자를 호출하여 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> 사용자 암호 구성 `Startup` 원본을 명시적으로 추가합니다. 다음 `AddUserSecrets` 예제와 같이 앱이 개발 환경에서 실행되는 경우에만 호출합니다.

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

사용자 암호는 API를 `Configuration` 통해 검색할 수 있습니다.

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>POCO에 비밀 매핑

전체 개체를 POCO(속성이 있는 간단한 .NET 클래스)에 매핑하는 것은 관련 속성을 집계하는 데 유용합니다.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

선행 비밀을 POCO에 매핑하려면 `Configuration` API의 개체 [그래프 바인딩](xref:fundamentals/configuration/index#bind-to-an-object-graph) 기능을 사용합니다. 다음 코드는 사용자 지정 `MovieSettings` POCO에 바인딩하고 속성 값에 `ServiceApiKey` 액세스합니다.

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString` 및 `Movies:ServiceApiKey` 비밀은 `MovieSettings`다음의 각 속성에 매핑됩니다.

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>비밀이 있는 문자열 교체

암호를 일반 텍스트로 저장하는 것은 안전하지 않습니다. 예를 들어 *appsettings.json에* 저장된 데이터베이스 연결 문자열에는 지정된 사용자의 암호가 포함될 수 있습니다.

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

보다 안전한 방법은 암호를 암호로 저장하는 것입니다. 다음은 그 예입니다.

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password` *appsettings.json의*연결 문자열에서 키-값 쌍을 제거합니다. 다음은 그 예입니다.

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

secret의 값을 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> 개체의 <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> 속성에 설정하여 연결 문자열을 완료할 수 있습니다.

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>비밀 목록

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.

```dotnetcli
dotnet user-secrets list
```

다음 출력이 표시됩니다.

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

앞의 예제에서 키 이름의 콜론은 *secrets.json*내의 개체 계층 구조를 나타냅니다.

## <a name="remove-a-single-secret"></a>단일 비밀 제거

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

앱의 *secrets.json* 파일이 `MoviesConnectionString` 키와 연결된 키 값 쌍을 제거하도록 수정되었습니다.

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

실행 `dotnet user-secrets list` 중에는 다음 메시지가 표시됩니다.

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>모든 비밀 제거

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행합니다.

```dotnetcli
dotnet user-secrets clear
```

응용 프로그램에 대한 모든 사용자 암호는 *secrets.json* 파일에서 삭제되었습니다 :

```json
{}
```

실행 `dotnet user-secrets list` 중에는 다음 메시지가 표시됩니다.

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>추가 자료

* IIS에서 비밀 관리자에 액세스하는 방법에 대한 자세한 내용은 [이 문제를](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 참조하십시오.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end