---
layout: page
title: CORS
permalink: /dotnet/webapi/cors
---

# Configuring CORS
```c#
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