# Configuration

## Microsoft.Extensions.Configuration.EnvironmentVariables
- [This](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0#environment-variables) details how env vars work with this library

## User Secrets
- this can be configured with visual studio and only load when the environment is set to `Development` if you use the default host builder

## Stronly typed config
- it might be tempting to only create getters for the properties you create for your config, but this will result in the properties never being bound