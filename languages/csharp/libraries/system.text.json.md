# System.Text.Json

- this is a newer library which replaces `Newtonsoft.Json`
- this is developed by Microsoft as part of the sdk it seems and it is faster than Newtonsoft
- as of 2/2021, there is no glaring need to switch to `System.Text.Json` if using `Newtonsoft.Json`
- as of 2/2021, there doesn't seem to be much of an ecosystem around this, so you might have to build out some of your own wrappers to do what you want
- otherwise it seems like a solid library and looks a lot like Newtonsoft
- as of 3/27/20, `JsonPatchDocument` [doesn't support this serialization library](https://stackoverflow.com/questions/64377440/unexpected-error-using-jsonpatchdocument-with-blazor), better to stick to something like `Newtonsoft` for that type

## Converter precedence
- there is an open [github issue](https://github.com/dotnet/runtime/issues/1130) for this
- converters can be registered at design-time vs run-time and at a property-level vs. class-level. The basic rules for priority over which is selected
    - Run-time has precedence over design-time
    - Property-level has precedence over class-level
    - User-specified has precedence over built-in
- thus the priority from highest to lowest:
    - runtime+property: Future feature: options.Converters.Add(converter, property)
    - designtime+property: [JsonConverter] applied to a property.
    - runtime+class: options.Converters.Add(converter)
    - designtime+class: [JsonConverter] applied to a custom data type or POCO.
    - Built-in converters (primitive types, JsonElement, arrays, etc).

## Serializing Enums
- as expected, enum serialization is as ints
- you need to specifically include an enum converter to map strings to enums and vice versa

### Gotchas
- the default enum serializer ignores the naming policy you give it
- there is currently (as of 2/2021) a [github issue](https://github.com/dotnet/runtime/issues/31619) for this
- [Macross.Json.Extensions](https://github.com/Macross-Software/core/tree/develop/ClassLibraries/Macross.Json.Extensions) offers a solid workaround
- you need to make sure that properties have public setters when deserializing them, otherwise they will never get set
  - [example](https://github.com/dotnet/aspnetcore/issues/21030)

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

## Deserializing abstract classes
- [ref](https://josef.codes/polymorphic-deserialization-with-system-text-json/)
- extending upon the reference listed above, I have added the ability to define type discriminators via attribute
    ```cs
    internal class AbstractClassConverter<T> : JsonConverter<T> where T : class
    {
        private IDictionary<string, Type> _discriminatorToTypeMap;

        // JsonSerializer caches converters, so you can be sure that this only runs once per type T
        public AbstractClassConverter()
            => _discriminatorToTypeMap = Assembly.GetAssembly(typeof(T))
                .GetTypes()
                .Where(x => x.IsClass && x.IsSubclassOf(typeof(T)) && x.GetCustomAttributes(typeof(JsonDiscriminatorAttribute)).Any())
                .ToDictionary(
                    x => ((JsonDiscriminatorAttribute)x.GetCustomAttribute(typeof(JsonDiscriminatorAttribute))).Discriminator.ToLower(),
                    x => x
                );

        public override bool CanConvert(Type typeToConvert) =>
            typeToConvert.IsAssignableFrom(typeof(T));

        public override T Read(ref Utf8JsonReader reader, Type typeToConvert, JsonSerializerOptions options)
        {
            if (JsonDocument.TryParseValue(ref reader, out var doc))
            {
                if (doc.RootElement.TryGetProperty("type", out var typeProp))
                {
                    var typeValue = typeProp.GetString();

                    if (_discriminatorToTypeMap.TryGetValue(typeValue.ToLower(), out var type))
                    {
                        return JsonSerializer.Deserialize(doc.RootElement.GetRawText(), type, options) as T;
                    }
                    else
                    {
                        throw new JsonException($"{typeValue} has not been mapped to a custom type yet!");
                    }
                }

                throw new JsonException("Failed to extract type property, it might be missing?");
            }

            throw new JsonException("Failed to parse JsonDocument");
        }

        public override void Write(Utf8JsonWriter writer, T value, JsonSerializerOptions options)
        {
            throw new NotImplementedException();
        }
    }
    ```
- the attribute is defined as this (no inheritance is important in case multiple classes in the same inheritance chain use this deserializer)
    ```cs
    [AttributeUsage(AttributeTargets.Class, Inherited = false, AllowMultiple = false)]
    internal class JsonDiscriminatorAttribute : Attribute
    {
        public string Discriminator { get; init; }

        public JsonDiscriminatorAttribute(string discriminator)
        {
            this.Discriminator = discriminator;
        }
    }
    ```
- declaration on the super class looks like this
    ```cs
    [JsonConverter(typeof(AbstractClassConverter<Location>))]
    public abstract record Location(
        Vector3 Center
    );
    ```
- declaration on the subclass looks like this
    ```cs
    [JsonDiscriminator("cylinder")]
    public record CylinderLocation(
        Vector3 Center,
        double Height,
        double Radius,
        double Rotation
    ) : Location(Center);
    ```