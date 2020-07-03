ASP.NET Core [id](xref:security/authentication/identity) 는 또는 통합과 같은 고급 시나리오를 제외 하 고는 [SameSite 쿠키](xref:security/samesite) 의 영향을 받지 않습니다 `IFrames` `OpenIdConnect` .

를 사용 하 `Identity` 는 경우 쿠키 공급자를 추가 하거나를 호출 ***하지*** 마세요 ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)` `Identity` .