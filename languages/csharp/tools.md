# Tools

## Accessing index in `foreach` loop
- there is no way to do this without helper methods
```cs
public static IEnumerable<(T item, int index)> WithIndex<T>(this IEnumerable<T> source)
{
    return source.Select((item, index) => (item, index));
}
```
```cs
foreach (var (item, index) in collection.WithIndex())
{
    DoSomething(item, index);
}
```
## Object Copy Helper
- this copies the properties from one object to another
- it is preferred to use a tool like [AutoMapper](https://automapper.org/)
```cs
public static class ObjectCopyHelper
{
    /// <summary>
    /// Copy properties from one object to another. Used the following:
    /// https://stackoverflow.com/questions/930433/apply-properties-values-from-one-object-to-another-of-the-same-type-automaticall
    /// </summary>
    /// <param name="source"></param>
    /// <param name="destination"></param>
    public static void CopyProperties(object source, object destination)
    {
        // Getting the Types of the objects
        var typeDest = destination.GetType();
        var typeSrc = source.GetType();

        // Collect all the valid properties to map
        var results = from srcProp in typeSrc.GetProperties()
                        let targetProperty = typeDest.GetProperty(srcProp.Name)
                        where srcProp.CanRead
                            && targetProperty != null
                            && targetProperty.GetSetMethod(true) != null && !targetProperty.GetSetMethod(true).IsPrivate
                            && (targetProperty.GetSetMethod().Attributes & MethodAttributes.Static) == 0
                            && targetProperty.PropertyType.IsAssignableFrom(srcProp.PropertyType)
                        select new { sourceProperty = srcProp, targetProperty };

        //map the properties
        foreach (var props in results)
        {
            props.targetProperty.SetValue(destination, props.sourceProperty.GetValue(source, null), null);
        }
    }
}
```

## Timing code
- time an async action
    ```cs
    private static async Task<long> TimeActionAsync(Func<Task> a)
    {
        var w = new System.Diagnostics.Stopwatch();
        w.Start();
        await a();
        w.Stop();
        return w.ElapsedMilliseconds;
    }
    ```
- time an async func
    ```cs
    private static async Task<(T, long)> TimeFuncAsync<T>(Func<Task<T>> a)
    {
        var w = new System.Diagnostics.Stopwatch();
        w.Start();
        T ret = await a();
        w.Stop();
        return (ret, w.ElapsedMilliseconds);
    }
    ```

## [String to Stream and back](https://www.csharp411.com/c-convert-string-to-stream-and-stream-to-string/)
- from string to stream
    ```cs
    byte[] byteArray = Encoding.ASCII.GetBytes( test );
    MemoryStream stream = new MemoryStream( byteArray );
    ```
- from stream to string
    ```cs
    StreamReader reader = new StreamReader( stream );
    string text = reader.ReadToEnd();
    ```

## `switch` on type parameter
- because of reasons I'm not aware of, `switch` expressions that switch on type, can't use the types directly
- instead, switching on their name would be better
```cs
public void MyFunction<T>() where T : MyBaseType
{
    // ...
    // can't use nameof(T) here (compiler error)
    var type = typeof(T).Name switch
    {
        nameof(SubType1) => "is type 1",
        nameof(SubType2) => "is type 2",
        nameof(SubType3) => "is type 3",
        nameof(SubType4) => "is type 4",
        _ => throw new NotImplementedException()
    };
    // ...
}
```

## [enum to IEnumerable<Enum>](https://stackoverflow.com/questions/12447473/how-can-i-create-an-ienumerable-from-an-enum)
```cs
public enum City
{
    London    = 1,
    Liverpool  = 20,
    Leeds       = 25
}
```
```cs
IEnumerable<City> allCityValues = (City[])Enum.GetValues(typeof(City));
```