# Configuration

## Microsoft.Extensions.Configuration.EnvironmentVariables
- [This](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0#environment-variables) details how env vars work with this library

## User Secrets
- this can be configured with visual studio and only load when the environment is set to `Development` if you use the default host builder

## Stronly typed config
- it might be tempting to only create getters for the properties you create for your config, but this will result in the properties never being bound

## Json
- [empty arrays will always show up as null because of the way it transforms each record into strings](https://github.com/dotnet/extensions/issues/1341)
- a hack for primitive type arrays is to use `"MyArray": [ null ],` in the configuration, but doesn't work for more complex types like strings, objects, and arrays
- you could try to set a default empty array on the class like the following, but will any value from the json will always be overwritten, even though there is a [stack overflow post](https://stackoverflow.com/questions/46935614/why-does-a-property-get-initialized-to-a-null-instead-of-an-empty-list-from-conf) suggesting this
- the best solution seems to initialize empty arrays using reflection after loading the configuration from these providers
    ```cs
    // ConfigExt.cs
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Reflection;

    namespace MyProject
    {
        public static class ConfigExt
        {
            public static T InitNullIEnumerables<T>(this T source)
            {
                if (source is null)
                    return source;

                foreach (var prop in typeof(T).GetProperties())
                {
                    if (prop.IsIEnumerableType() && source.IsPropertyNull(prop.Name))
                    {
                        var typeArg = prop.PropertyType.GenericTypeArguments.First();
                        typeof(T)
                            .GetProperty(prop.Name)
                            .SetValue(
                                obj: source,
                                value: Array.CreateInstance(typeArg, 0),
                                index: null
                            );
                    }
                    else if (prop.PropertyType.IsClass && prop.PropertyType != typeof(string))
                    {
                        typeof(ConfigExt)
                            .GetMethod(nameof(InitNullIEnumerables))
                            .MakeGenericMethod(prop.PropertyType)
                            .Invoke(
                                obj: null,
                                parameters: new object[] {
                                    source.GetPropertyValue(prop.Name)
                                }
                            );
                    }
                }

                return source;
            }

            private static bool IsIEnumerableType(this PropertyInfo source)
                => source.PropertyType.IsGenericType
                    && source.PropertyType.GetGenericTypeDefinition() == typeof(IEnumerable<>);

            private static bool IsPropertyNull(this object source, string propName)
                => source.GetPropertyValue(propName) is null;

            private static object GetPropertyValue(this object source, string propName)
                => source
                    .GetType()
                    .GetProperty(propName)
                    .GetValue(source, index: null);
        }
    }
    ```
    ```cs
    // wherever you load config
    _config = new ConfigurationBuilder()
        .AddJsonFile("config.json")
        .Build()
        .Get<Config>()
        .InitNullIEnumerables();
    ```
    - this solution can also be easily extended to handle excluding certain values from initialization via attributes