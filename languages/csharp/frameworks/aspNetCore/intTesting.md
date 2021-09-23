# [Int Testing](https://docs.microsoft.com/en-us/aspnet/core/test/integration-tests?view=aspnetcore-5.0)

- this pretty much centers around using a `WebApplicationFactory<TStartup>` to build the application
- this provides a method that generates clients for you to use when int testing
- you can even have access to the DI container and can use it to resolve/replace services
- below is how to add overrides

```cs
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc.Testing;
using Microsoft.Extensions.Configuration;
using System.Collections.Generic;

namespace MyApp.Api.IntegrationTests
{
    public class TestWebApplicationFactory<TStartup> : WebApplicationFactory<TStartup> where TStartup : class
    {
        // can use this to add specific overrides
        protected override void ConfigureWebHost(IWebHostBuilder builder)
            => builder
                .ConfigureAppConfiguration((ctx, builder) =>
                    builder
                        .AddInMemoryCollection(new Dictionary<string, string>
                        {
                            { "ConnectionStrings:MyDbConnection", "host=localhost;port=5400;database=mydb;username=admin;password=password" },
                            { "Key", "Value" }
                        })
                        .AddEnvironmentVariables()
                );
    }
}
```
