# Object Copy Helper

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