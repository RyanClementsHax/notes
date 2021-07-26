# Extension Methods

## "Hiding" other methods
- sometimes when calling an extension method, the signature resolves to the extension method rather than an instance method
- this happened to me when I was trying to mock out the `bool TryGetValue(object key, out object value)` method on `IMemoryCache`, but the code resolved the `static bool TryGetValue<TItem>(this IMemoryCache cache, object key, out TItem value)` method on `Microsoft.Extensions.Caching.Memory.CacheExtensions`
- the trick was to help the type system by specifying the type of value as explicitly `object`
- in most cases casting with `(object)` works just fine, but that doesn't work here because `value` is an `out` parameter
- in this case I had to either create a new variable that was explicitly an object (e.g. `object temp = myValue`) or wrap it in a function like below
    ```cs
    // notice here that value is explicitly typed as "object"
    private Guid PutInCache(object value)
    {
        var token = Guid.NewGuid();
        _mockMemoryCache
            .Setup(x => x.TryGetValue(token, out value))
            .Returns(true);
        return token;
    }
    ```