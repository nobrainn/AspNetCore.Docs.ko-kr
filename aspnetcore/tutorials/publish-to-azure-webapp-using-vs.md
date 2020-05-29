---
<span data-ttu-id="84fda-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="84fda-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="84fda-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="84fda-102">'Blazor'</span></span>
- <span data-ttu-id="84fda-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="84fda-103">'Identity'</span></span>
- <span data-ttu-id="84fda-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="84fda-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="84fda-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="84fda-105">'Razor'</span></span>
- <span data-ttu-id="84fda-106">‘SignalR’ uid:</span><span class="sxs-lookup"><span data-stu-id="84fda-106">'SignalR' uid:</span></span> 

---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="84fda-107">Visual Studio를 사용하여 Azure에 ASP.NET Core 앱 게시</span><span class="sxs-lookup"><span data-stu-id="84fda-107">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="84fda-108">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="84fda-108">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="84fda-109">macOS에서 작업하는 경우 [Mac용 Visual Studio를 사용하여 Azure App Service에 웹 앱 게시](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="84fda-109">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="84fda-110">App Service 배포 문제를 해결하려면 <xref:test/troubleshoot-azure-iis>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="84fda-110">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="84fda-111">설치</span><span class="sxs-lookup"><span data-stu-id="84fda-111">Set up</span></span>

* <span data-ttu-id="84fda-112">계정이 없는 경우 [체험 Azure 계정](https://azure.microsoft.com/free/dotnet/)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-112">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="84fda-113">웹앱 만들기</span><span class="sxs-lookup"><span data-stu-id="84fda-113">Create a web app</span></span>

<span data-ttu-id="84fda-114">Visual Studio 시작 페이지에서 **파일 > 새로 만들기 > 프로젝트...** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-114">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![파일 메뉴](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="84fda-116">**새 프로젝트** 대화 상자를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-116">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="84fda-117">**새 ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-117">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="84fda-118">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-118">Select **Next**.</span></span>

![새 프로젝트 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="84fda-120">**새 ASP.NET Core 웹 애플리케이션** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="84fda-120">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="84fda-121">**웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-121">Select **Web Application**.</span></span>
* <span data-ttu-id="84fda-122">인증에서 **변경**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-122">Select **Change** under Authentication.</span></span>

![새 ASP.NET Core 웹 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="84fda-124">**인증 변경** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-124">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="84fda-125">**개별 사용자 계정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-125">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="84fda-126">**확인**을 선택하여 **새 ASP.NET Core 웹 애플리케이션**으로 돌아간 다음 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-126">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **Create**.</span></span>

![새 ASP.NET Core 웹 인증 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="84fda-128">Visual Studio는 솔루션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-128">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="84fda-129">앱 실행</span><span class="sxs-lookup"><span data-stu-id="84fda-129">Run the app</span></span>

* <span data-ttu-id="84fda-130">Ctrl+F5를 눌러 프로젝트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-130">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="84fda-131">**개인 정보** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-131">Test the **Privacy** link.</span></span>

![localhost의 Microsoft Edge에서 열린 웹 애플리케이션](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="84fda-133">사용자 등록</span><span class="sxs-lookup"><span data-stu-id="84fda-133">Register a user</span></span>

* <span data-ttu-id="84fda-134">**등록**을 선택하고 새 사용자를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-134">Select **Register** and register a new user.</span></span> <span data-ttu-id="84fda-135">가상의 전자 메일 주소를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-135">You can use a fictitious email address.</span></span> <span data-ttu-id="84fda-136">제출하면 페이지에 다음과 같은 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-136">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="84fda-137">*"요청을 처리하는 동안 데이터베이스 작업이 실패했습니다. 애플리케이션 DB 컨텍스트에 대한 기존 마이그레이션 적용으로 이 문제를 해결할 수 있습니다.”*</span><span class="sxs-lookup"><span data-stu-id="84fda-137">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="84fda-138">**마이그레이션 적용**을 선택한 다음 페이지가 업데이트되면 페이지를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-138">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![요청을 처리하는 동안 데이터베이스 작업이 실패했습니다.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="84fda-141">앱은 새 사용자를 등록하는 데 사용한 이메일 및 **로그아웃** 링크를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-141">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Microsoft Edge에서 열린 웹 애플리케이션.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="84fda-144">Azure에 앱 배포</span><span class="sxs-lookup"><span data-stu-id="84fda-144">Deploy the app to Azure</span></span>

<span data-ttu-id="84fda-145">솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-145">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![강조 표시된 게시 링크로 열린 바로 가기 메뉴](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="84fda-147">**게시** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="84fda-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="84fda-148">**Azure**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-148">Select **Azure**.</span></span>
* <span data-ttu-id="84fda-149">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-149">Select **Next**.</span></span>

![게시 대화 상자](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="84fda-151">**게시** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="84fda-151">In the **Publish** dialog:</span></span>

* <span data-ttu-id="84fda-152">**Azure App Service(Linux)** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-152">Select **Azure App Service (Linux)**.</span></span>
* <span data-ttu-id="84fda-153">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-153">Select **Next**.</span></span>

![게시 대화 상자: Azure Service 선택](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="84fda-155">**게시** 대화 상자에서 **새 Azure App Service 만들기...** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-155">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![게시 대화 상자: Azure Service 인스턴스 선택](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="84fda-157">**App Service 만들기** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-157">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="84fda-158">**앱 이름**, **리소스 그룹** 및 **App Service 계획** 항목 필드가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-158">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="84fda-159">이러한 이름을 유지하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-159">You can keep these names or change them.</span></span>
* <span data-ttu-id="84fda-160">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-160">Select **Create**.</span></span>

![App Service 만들기 대화 상자](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="84fda-162">만들기가 완료되면 대화 상자가 자동으로 닫히고 **게시** 대화 상자가 다시 포커스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-162">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="84fda-163">방금 만든 새 인스턴스가 자동으로 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-163">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="84fda-164">**마침**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-164">Select **Finish**.</span></span>

![게시 대화 상자: App Service 인스턴스 선택](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="84fda-166">다음으로 **게시 프로필 요약** 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-166">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="84fda-167">이 애플리케이션에 SQL Server 데이터베이스가 필요하므로 이를 구성하라는 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-167">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="84fda-168">**구성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-168">Select **Configure**.</span></span>

![게시 프로필 요약 페이지: SQL Server 종속성 구성](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="84fda-170">**종속성 구성** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-170">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="84fda-171">**Azure SQL Database**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-171">Select **Azure SQL Database**.</span></span>
* <span data-ttu-id="84fda-172">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-172">Select **Next**.</span></span>

![SQL Server 종속성 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="84fda-174">**Azure SQL Database 구성** 대화 상자에서 **SQL 데이터베이스 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-174">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![Azure SQL Database 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="84fda-176">**Azure SQL 데이터베이스 만들기**가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-176">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="84fda-177">**데이터베이스 이름**, **리소스 그룹**, **데이터베이스 서버** 및 **App Service 계획** 항목 필드가 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-177">The **Database name**, **Resource Group**, **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="84fda-178">채워진 값을 유지하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-178">You can keep these values or change them.</span></span>
* <span data-ttu-id="84fda-179">선택한 **데이터베이스 서버**에 대한 **데이터베이스 관리자 사용자 이름** 및 **데이터베이스 관리자 암호**를 입력합니다. 사용하는 계정에 새 Azure SQL 데이터베이스를 만드는 데 필요한 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-179">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="84fda-180">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-180">Select **Create**.</span></span>

![새 Azure SQL Database 대화 상자](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="84fda-182">만들기가 완료되면 대화 상자가 자동으로 닫히고 **Azure SQL Database 구성** 대화 상자가 다시 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-182">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="84fda-183">방금 만든 새 인스턴스가 자동으로 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-183">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="84fda-184">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-184">Select **Next**.</span></span>

![Azure SQL Database 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="84fda-186">**Azure SQL Database 구성** 대화 상자의 다음 단계에서 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-186">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="84fda-187">**데이터베이스 연결 사용자 이름** 및 **데이터베이스 연결 암호** 필드를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-187">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="84fda-188">이들은 애플리케이션이 런타임 시 데이터베이스에 연결하는 데 사용하는 세부 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-188">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="84fda-189">이전 단계에서 사용한 관리자 사용자 이름 및 암호와 동일한 세부 정보를 사용하지 않는 것이 가장 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-189">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="84fda-190">**마침**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-190">Select **Finish**.</span></span>

![Azure SQL Database 구성 대화 상자, 연결 문자열 세부 정보](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="84fda-192">**게시 프로필 요약** 페이지에서 **설정**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-192">In the **Publish Profile summary** page select **Settings**:</span></span>

![게시 프로필 요약 페이지: 설정 편집](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="84fda-194">**게시** 대화 상자의 **설정** 페이지에서:</span><span class="sxs-lookup"><span data-stu-id="84fda-194">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="84fda-195">**데이터베이스**를 확장하고 **런타임 시 이 연결 문자열 사용**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-195">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="84fda-196">**Entity Framework 마이그레이션**을 확장하고 **게시에 이 마이그레이션 적용**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-196">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="84fda-197">**저장**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-197">Select **Save**.</span></span> <span data-ttu-id="84fda-198">Visual Studio가 **게시** 대화 상자로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-198">Visual Studio returns to the **Publish** dialog.</span></span> 

![게시 대화 상자: 설정 패널](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="84fda-200">**게시**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-200">Click **Publish**.</span></span> <span data-ttu-id="84fda-201">Visual Studio는 Azure에 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-201">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="84fda-202">배포가 완료되면 앱이 브라우저에서 열립니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-202">When the deployment completes, the app is opened in a browser.</span></span>

![게시 대화 상자: 설정 패널](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="84fda-204">앱 업데이트</span><span class="sxs-lookup"><span data-stu-id="84fda-204">Update the app</span></span>

* <span data-ttu-id="84fda-205">*Pages/Index.cshtml* Razor 페이지를 편집하고 내용을 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-205">Edit the *Pages/Index.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="84fda-206">예를 들어 단락을 수정하여 “Hello ASP.NET Core!” 문구를 표시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-206">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="84fda-207">**게시 프로필 요약** 페이지에서 다시 **게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-207">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![게시 프로필 요약 페이지](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="84fda-209">앱이 게시된 후 변경 내용이 Azure에서 제공되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-209">After the app is published, verify the changes you made are available on Azure.</span></span>

![작업이 완료되었는지 확인합니다.](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="84fda-211">정리</span><span class="sxs-lookup"><span data-stu-id="84fda-211">Clean up</span></span>

<span data-ttu-id="84fda-212">앱 테스트를 완료하면 [Azure Portal](https://portal.azure.com/)로 이동하고 앱을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-212">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="84fda-213">**리소스 그룹**을 선택한 다음 만든 리소스 그룹을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-213">Select **Resource groups**, then select the resource group you created.</span></span>

![Azure Portal: 사이드바 메뉴에 있는 리소스 그룹](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="84fda-215">**리소스 그룹** 페이지에서 **삭제**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-215">In the **Resource groups** page, select **Delete**.</span></span>

![Azure Portal: 리소스 그룹 페이지](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="84fda-217">리소스 그룹의 이름을 입력하고 **삭제**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-217">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="84fda-218">이 자습서에서 만든 앱과 다른 모든 리소스는 이제 Azure에서 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="84fda-218">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="84fda-219">다음 단계</span><span class="sxs-lookup"><span data-stu-id="84fda-219">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="84fda-220">추가 자료</span><span class="sxs-lookup"><span data-stu-id="84fda-220">Additional resources</span></span>

* <span data-ttu-id="84fda-221">Visual Studio Code는 [게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="84fda-221">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="84fda-222">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="84fda-222">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="84fda-223">Azure 리소스 그룹</span><span class="sxs-lookup"><span data-stu-id="84fda-223">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="84fda-224">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="84fda-224">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
