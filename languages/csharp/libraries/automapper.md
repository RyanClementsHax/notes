# AutoMapper

## Using profiles from another assembly with DI
- you need to add this to your DI setup code by taking any class from that assembly and passing it into the `.AddAutoMapper(...)` function on startup
    ```cs
    .AddAutoMapper(typeof(AnyClassFromTheAssemblyYouWantToLoadProfilesFrom))
    ```