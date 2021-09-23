# AutoMapper

## Using profiles from another assembly with DI

- you need to add this to your DI setup code by taking any class from that assembly and passing it into the `.AddAutoMapper(...)` function on startup

    ```cs
    .AddAutoMapper(typeof(AnyClassFromTheAssemblyYouWantToLoadProfilesFrom))
    ```

## Projections

### Explicit projections

- by default, projection will bring the record and any owned objects with it, is dangerous
- [you can make it so that you have to explicitly expand that one object to get its fields](https://github.com/AutoMapper/AutoMapper/blob/bdc0120497d192a2741183415543f6119f50a982/src/UnitTests/Projection/ExplicitExpansionWithInheritance.cs) when setting up the mapping profile

## Lists

- by default, it will map collections for you
- by default, null collections are mapped to empty collections
  - if you don't want this, [you need to set the option](https://stackoverflow.com/questions/35293356/automapper-returning-an-empty-collection-i-want-a-null/56241910#56241910)

## With unit tests

- don't mock automapper directly, instead use it like you would in actual code
- even the creator says [mocking isn't necessary](https://github.com/AutoMapper/AutoMapper/issues/3175)
- you may have to create a service provider, register all of the profiles like you do in your startup, then resolve `IMapper` but its worth it
