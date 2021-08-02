# Serialization

- to some extent, aspnet core takes care of this for you, but breaks down when you want to do something like serialize complex types
- this is where libraries like `Newtonsoft` come in

## Deserializing the body in controllers

- use `[FromBody]` in the parameter definition

## Including serialization

- this is as simple as including the serialization you want in the `ConfigureServices` method

```cs
// this example configures Newtonsoft for controllers
services
    .AddControllers()
        .AddNewtonsoftJson();
```
