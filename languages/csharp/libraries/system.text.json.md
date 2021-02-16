# System.Text.Json

- this is a newer library which replaces `Newtonsoft.Json`
- this is developed by Microsoft as part of the sdk it seems and it is faster than Newtonsoft
- as of 2/2021, there is no glaring need to switch to `System.Text.Json` if using `Newtonsoft.Json`
- as of 2/2021, there doesn't seem to be much of an ecosystem around this, so you might have to build out some of your own wrappers to do what you want
- otherwise it seems like a solid library and looks a lot like Newtonsoft

## Serializing Enums
- as expected, enum serialization is as ints
- you need to specifically include an enum converter to map strings to enums and vice versa

### Gotchas
- the default enum serializer ignores the naming policy you give it
- there is currently (as of 2/2021) a [github issue](https://github.com/dotnet/runtime/issues/31619) for this
- [Macross.Json.Extensions](https://github.com/Macross-Software/core/tree/develop/ClassLibraries/Macross.Json.Extensions) offers a solid workaround

## Snake case
- you first need to write something that transfers a string to snake case
    ```cs
    public static string ToSnakeCase(this string str)
    {
        return string.Concat(
            str.Select(
                (x, i) => i > 0 && char.IsUpper(x)
                    ? "_" + x
                    : x.ToString()
                    )
            ).ToLower();
    }
    ```
- then you need to set up a naming policy
    ```cs
    public class SnakeCaseNamingPolicy : JsonNamingPolicy
    {
        public override string ConvertName(string name)
        {
            return name.ToSnakeCase();
        }
    }
    ```
- then add this policy to your options
    ```cs
    _options = new JsonSerializerOptions
    {
        PropertyNamingPolicy = new SnakeCaseNamingPolicy()
    };
    ```