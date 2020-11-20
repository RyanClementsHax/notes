---
layout: page
title: Enums
permalink: /dotnet/webapi/enums
---

# Serializing enums
```c#
services
    .AddControllers()
        .AddNewtonsoftJson(opts => opts.SerializerSettings.Converters.Add(new StringEnumConverter()))
```
