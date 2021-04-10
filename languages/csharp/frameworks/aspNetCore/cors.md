# [CORS](https://docs.microsoft.com/en-us/aspnet/core/security/cors?view=aspnetcore-5.0)

- many ways to enable
  - middleware
  - attribute
  - endpoint routing

# Configuring CORS
```cs
services
    .AddCors(options =>
    {
        options.AddDefaultPolicy(
            builder =>
            {
                builder
                    .WithOrigins(Configuration["CORS"].Split(','))
                    .AllowAnyMethod()
                    .AllowAnyHeader();
            });
    })
```
```cs
app
    .UseRouting()
    .UseCors() // must be after UseRouting() but before UseAuthorization()
    .UseAuthorization()
```