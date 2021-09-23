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
<!-- markdownlint-disable MD033 -->
## [enum to IEnumerable<Enum>](https://stackoverflow.com/questions/12447473/how-can-i-create-an-ienumerable-from-an-enum)
<!-- markdownlint-enable MD033 -->

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

## Getting the home path

```cs
public static string GetHomePath() =>
    (Environment.OSVersion.Platform == PlatformID.Unix || Environment.OSVersion.Platform == PlatformID.MacOSX)
        ? Environment.GetEnvironmentVariable("HOME")
        : Environment.ExpandEnvironmentVariables("%HOMEDRIVE%%HOMEPATH%");
```

- this will get the home path when running on windows, mac, or linux
- `%HOMEDRIVE%%HOMEPATH%` [won't work on older windows systems](https://stackoverflow.com/questions/39573548/cant-expand-environment-variables-with-c-sharp-using-windows-7) so you will have to resort to something like `Environment.GetFolderPath(Environment.SpecialFolder.Desktop)`

## [Thread safe counter](https://stackoverflow.com/a/13181780)

- [does not work with properties](https://stackoverflow.com/questions/4518956/a-property-or-indexer-may-not-be-passed-as-an-out-or-ref-parameter) (i.e. `Interlocked.Increment(ref obj.Property)`)

```cs
return Interlocked.Increment(ref COUNTER);
```

## Http client extensions

- the default http client extensions that handle serialization and such don't come with post, patch, or delete functions (why???)
- here are some helper functions for this
- they're designed to match up as close as possible to the way the other http client extension methods were written

```cs
using Microsoft.AspNetCore.JsonPatch;
using Newtonsoft.Json;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Json;
using System.Text;
using System.Text.Json;
using System.Threading;
using System.Threading.Tasks;

public static class HttpClientExtensions
{
    public static async Task<TResp> PostAsJsonAsync<TValue, TResp>(this HttpClient client, string requestUri, TValue value, JsonSerializerOptions options = null, CancellationToken cancellationToken = default)
    {
        var resp = await client.PostAsJsonAsync(requestUri, value, options, cancellationToken);
        return await resp.Content.ReadAsAsync<TResp>(cancellationToken);
    }

    public static async Task<TResp> PatchAsJsonAsync<TValue, TResp>(this HttpClient client, string requestUri, TValue value, JsonSerializerSettings settings = null, CancellationToken cancellationToken = default)
    {
        var str = JsonConvert.SerializeObject(value, settings);
        var content = new StringContent(str, Encoding.UTF8, "application/json-patch+json");
        var resp = await client.PatchAsync(requestUri, content, cancellationToken);
        return await resp.Content.ReadAsAsync<TResp>(cancellationToken);
    }

    public static async Task<HttpResponseMessage> PatchAsJsonAsync<T>(this HttpClient client, string requestUri, T value, MediaTypeFormatter formatter = null, CancellationToken cancellationToken = default)
    {
        var content = new ObjectContent<T>(value, formatter ?? new JsonMediaTypeFormatter());
        return await client.PatchAsync(requestUri, content, cancellationToken);
    }

    public static async Task<TResp> DeleteAsync<TResp>(this HttpClient client, string requestUri, CancellationToken cancellationToken = default)
    {
        var resp = await client.DeleteAsync(requestUri, cancellationToken);
        return await resp.Content.ReadAsAsync<TResp>(cancellationToken);
    }
}
```
  