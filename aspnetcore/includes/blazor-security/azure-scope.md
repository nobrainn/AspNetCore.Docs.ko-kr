> [!NOTE]
> <span data-ttu-id="86ba8-101">Azure Portal에서 앱에 대한 범위 URI를 제공하고, 앱이 API에서 401 권한 없음 응답을 받았을 때 처리되지 않은 예외를 throw하는 경우 스키마 및 호스트를 포함하지 않는 범위 URI를 사용해 보세요.</span><span class="sxs-lookup"><span data-stu-id="86ba8-101">If the Azure portal provides the scope URI for the app and the app throws an unhandled exception when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="86ba8-102">예를 들어 Azure Portal은 다음 범위 URI 형식 중 하나를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="86ba8-102">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="86ba8-103">스키마 및 호스트가 포함되지 않은 범위 URI를 제공해 보세요.</span><span class="sxs-lookup"><span data-stu-id="86ba8-103">Try supplying the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
>
> <span data-ttu-id="86ba8-104">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="86ba8-104">For example:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
> ```
