# Miscellaneous

## .NET Core
- 3.1 and above is not supported in Visual Studio 2017

## Memory dumps
- this brings ALL system information like env vars; thus, they should be treated as very sensitive information.

## Guids
- they are represented as structs not objects in C#

## Covariance
- only `object`s are allowed to have covariance in C#
- you will get a compiler error if you try to do any of the following
    ```cs
    object[] objects = new Guid[] { Guid.NewGuid() };
    object[] objects = new int[] { 1 };
    ```
- you will have to cast each item in the array to an `object` to get around this

## Dictionaries
- these don't support null values likely because null doesn't allow for an implementation of `Object.GetHashCode()`

## `using`
- this [seems](https://www.codeproject.com/Articles/6564/Understanding-the-using-statement-in-C#:~:text=The%20using%20statement%20simplifies%20the,to%20clean%20up%20the%20object.) to compile down to the following
    ```cs
    using (MyResource myRes = new MyResource())
    {
        myRes.DoSomething();
    }

    // the above gets desuggared to the statements below

    MyResource myRes= new MyResource();
    try
    {
        myRes.DoSomething();
    }
    finally
    {
        // Check for a null resource.
        if (myRes!= null)
            // Call the object's Dispose method.
            ((IDisposable)myRes).Dispose();
    }
    ```