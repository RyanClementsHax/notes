# Tests

## Property count equals
```c#
/// <summary>
/// Asserts that he given type has exactly a number of properties.
/// </summary>
/// <param name="count">The number of properties expected</param>
/// <param name="type">The type under test</param>
public static void PropertyCountEquals(int count, Type type)
{
    var numProperties = type.GetProperties().Length;
    if (numProperties != count)
    {
        // It is important we fail the test, because otherwise if we use Assert.Inconclusive(msg), I have found that this goes unnoticed in practice
        Assert.Fail($"The type {type} has a different number of properties than expected. Expected: {count}. Actual: {numProperties} This test requires updating to ensure that all of the fields are mapped appropriately.");
    }
}
```

## Collection assert extensions
```c#
/// <summary>
/// Calls .ToList() on both of the IEnumerables first then checks to make sure the lengths are equal,
/// then lastly asserts that each item of the first list matches the corresponding one in the other list
/// according to the function given in
/// </summary>
/// <typeparam name="T">Type of the items in the expected enumerable</typeparam>
/// <typeparam name="U">Type of the items in the actual enumerable</typeparam>
/// <param name="expected">The expected enumerable</param>
/// <param name="actual">The actual enumerable</param>
/// <param name="assertItemsEqual">The lambda passed in that asserts on each pair of items</param>
public static void AssertEqualTo<T, U>(this IEnumerable<T> expected, IEnumerable<U> actual, Action<T, U> assertItemsEqual)
{
    if(expected == null && actual == null)
    {
        return;
    }
    else if (expected == null)
    {
        Assert.Fail("The expected enumerable is null while the actual enumerable wasn't");
        return;
    }
    else if (actual == null)
    {
        Assert.Fail("The actual enumerable is null while the expected enumerable wasn't");
        return;
    }

    var expectedList = expected.ToList();
    var actualList = actual.ToList();
    Assert.AreEqual(expected.Count(), actual.Count());
    for(int i = 0; i < expected.Count(); i++)
    {
        assertItemsEqual(expectedList[i], actualList[i]);
    }
}

/// <summary>
/// Asserts on each item in the enumerable given after calling .ToList()
/// </summary>
/// <typeparam name="T">Type of the items</typeparam>
/// <param name="enumerable">The enumerable that this extension method applies to</param>
/// <param name="assertion">A lambda that asserts on each item in the enumerable</param>
public static void AssertAll<T>(this IEnumerable<T> enumerable, Action<T> assertion)
    => enumerable.ToList().ForEach(x => assertion(x));
```