# Kestrel

## Certs

- for docker, you need the following volumes set up to be able to use your local certs for development

```yml
volumes:
    - ${APPDATA}/Microsoft/UserSecrets:/root/.microsoft/usersecrets:ro
    - ${APPDATA}/ASP.NET/Https:/root/.aspnet/https:ro
```

## Connection logging

```cs
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder
                .ConfigureKestrel(serverOptions =>
                {

                    serverOptions.ListenLocalhost(5003, listenOptions =>
                    {
                        listenOptions.UseHttps();
                        listenOptions.UseConnectionLogging();
                    });
                })
                .UseStartup<Startup>();
        });
```
