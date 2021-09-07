# Auth

## Schemes

- [difference between auth methods](https://stackoverflow.com/questions/46818980/signinasync-vs-authenticateasync)
  - challenge: used to authenticate the user
  - authenticate: creates a claims principle using result from challenge
  - signin: persist the claims principle created from authenticate (i.e. create a cookie)
  - signout: reverse whatever signin did

## [Globally apply auth to endpoints](https://andrewlock.net/setting-global-authorization-policies-using-the-defaultpolicy-and-the-fallbackpolicy-in-aspnet-core-3/)

- [docs](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/secure-data?view=aspnetcore-5.0#rau)

```cs
services.AddAuthorization(options =>
{
    options.FallbackPolicy = new AuthorizationPolicyBuilder()
        .RequireAuthenticatedUser()
        .Build();
});
```

## OAUTH

### Setup with Swagger

```cs
services
    .UseSwaggerUi3(config =>
    {
        var azureADSettings = Configuration.GetSection("AZUREAD").Get<AzureADSettings>();

        config.OAuth2Client = new OAuth2ClientSettings()
        {
            ClientId = azureADSettings.Swagger_ClientId,
            AdditionalQueryStringParameters =
            {
                { "resource", azureADSettings.ClientId }
            }
        };
    })
```

### Config

```cs
/// <summary>
/// Call to hold AAD configuration
/// </summary>
public class AzureADSettings
{
    public string Instance { get; set; }
    public string ClientId { get; set; }
    public string TenantId { get; set; }
    public string Swagger_ClientId { get; set; }
    public string AuthorizationUrl => $"{Instance}{TenantId}/oauth2/authorize";
    public string TokenUrl => $"{Instance}{TenantId}/oauth2/token";
}
```

### Auth logger

```cs
public class AuthenticationLogger
{
    private readonly RequestDelegate _next;
    private readonly ILogger<AuthenticationLogger> _logger;

    public AuthenticationLogger(RequestDelegate next, ILogger<AuthenticationLogger> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        using (_logger.BeginScope(new Dictionary<string, object>
        {
            ["User"] = context.User.Identity.Name ?? "anonymous"
        }))
        {
            await _next(context);
        }
    }
}
```

## Identity

- aspnet provides an out of the box solution for managing identity
- its worth reading up on when trying to create your own identity solution
- can even use it to help [build refresh token capabilities](https://try2explore.com/questions/10986609)
- has a token system that you can hook into for things like password reset
  - [can even build your own token providers](https://andrewlock.net/implementing-custom-token-providers-for-passwordless-authentication-in-asp-net-core-identity/)

## Handling expiration

- handling cookie expiration
  - instead of returning a 401, 403, or something of the like, you can [silently refresh](https://stackoverflow.com/questions/38890172/how-to-handle-cookie-expiration-in-asp-net-core)
  - you can even use DI within these events by extending `CookieEvents` and setting that type on the cookie options
- handling jwt expriation
  - you can [silently refresh](https://stackoverflow.com/questions/52175302/handling-expired-refresh-tokens-in-asp-net-core) too
  - like with cookie expriation, you can use DI in the events as well
