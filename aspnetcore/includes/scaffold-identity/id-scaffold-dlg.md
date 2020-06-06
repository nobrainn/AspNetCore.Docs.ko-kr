Id 스 캐 폴더를 실행 합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 > **Add**  >  **새 스 캐 폴드 항목**추가를 클릭 합니다.
* **새 스 캐 폴드 항목 추가** 대화 상자의 왼쪽 창에서 **id**  >  **추가**를 선택 합니다.
* **Id 추가** 대화 상자에서 원하는 옵션을 선택 합니다.
  * 기존 레이아웃 페이지를 선택 하거나 잘못 된 태그를 사용 하 여 레이아웃 파일을 덮어씁니다.
    * `~/Pages/Shared/_Layout.cshtml`Razor Pages
    * `~/Views/Shared/_Layout.cshtml`MVC 프로젝트의 경우
    * Blazor 서버 템플릿 ()에서 만든 Blazor 서버 앱은 `blazorserver` 기본적으로 Razor Pages 또는 MVC에 대해 구성 되지 않습니다. 레이아웃 페이지 항목을 비워 둡니다.
  * 단추를 선택 **+** 하 여 새 **데이터 컨텍스트 클래스**를 만듭니다. 기본값을 그대로 적용 하거나 클래스를 지정 합니다 (예: `MyApplication.Data.ApplicationDbContext` ).
* **추가**를 선택합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

이전에 ASP.NET Core 스 캐 폴더를 설치 하지 않은 경우 지금 설치 합니다.

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

프로젝트 파일 (*.csproj*)에 필요한 NuGet 패키지 참조를 추가 합니다. 프로젝트 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

다음 명령을 실행 하 여 Identity 스 캐 폴더 옵션을 나열 합니다.

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

프로젝트 폴더에서 원하는 옵션을 사용 하 여 Id 스 캐 폴더를 실행 합니다. 예를 들어 기본 UI와 최소 파일 수를 사용 하 여 id를 설정 하려면 다음 명령을 실행 합니다.

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
