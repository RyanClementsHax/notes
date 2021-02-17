# CORS

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