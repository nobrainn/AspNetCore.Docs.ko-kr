> [!NOTE]
> Azure Portal에서 앱에 대한 범위 URI를 제공하고, 앱이 API에서 401 권한 없음 응답을 받았을 때 처리되지 않은 예외를 throw하는 경우 스키마 및 호스트를 포함하지 않는 범위 URI를 사용해 보세요. 예를 들어 Azure Portal은 다음 범위 URI 형식 중 하나를 제공할 수 있습니다.
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> 스키마 및 호스트가 포함되지 않은 범위 URI를 제공해 보세요.
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
>
> 예를 들어:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
> ```
