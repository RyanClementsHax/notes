# Enums

## Serializing enums

```cs
services
    .AddControllers()
        .AddNewtonsoftJson(opts => opts.SerializerSettings.Converters.Add(new StringEnumConverter()))
```
