# Auth

## [Globally apply auth to endpoints](https://andrewlock.net/setting-global-authorization-policies-using-the-defaultpolicy-and-the-fallbackpolicy-in-aspnet-core-3/)

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
