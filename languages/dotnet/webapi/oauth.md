# OAUTH

## Setup with Swagger

```c#
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

## Config
```c#
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

## Auth logger
```c#
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
