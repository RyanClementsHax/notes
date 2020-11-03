# Enums

# Serializing enums
```c#
services
    .AddControllers()
        .AddNewtonsoftJson(opts => opts.SerializerSettings.Converters.Add(new StringEnumConverter()))
```
