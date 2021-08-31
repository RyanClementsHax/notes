# Logging

## Creating a logger statically without a host

```cs
private static ILogger _logger = LoggerFactory.Create(x => x.AddConsole()).CreateLogger<YourClassName>();
```

- needs `Microsoft.Extensions.Logging.Console` added to the project

## String interpolation

- it is preferred to use the log overloads that do the string interpolation for you so you can query your logs based on log template assuming you use structured logging (where logs are outputted as json containing the log and metadata surrounding it)
- it seems that most logging libraries (even the logging facilities in `NUnit`) use some form of [String.Format](https://docs.microsoft.com/en-us/dotnet/api/system.string.format?view=net-5.0) under the hood
- [formatting a double](https://www.csharp-examples.net/string-format-double/)
  - some examples

    ```cs
    // just two decimal places
    String.Format("{0:0.00}", 123.4567);      // "123.46"
    String.Format("{0:0.00}", 123.4);         // "123.40"
    String.Format("{0:0.00}", 123.0);         // "123.00"
    // max. two decimal places
    String.Format("{0:0.##}", 123.4567);      // "123.46"
    String.Format("{0:0.##}", 123.4);         // "123.4"
    String.Format("{0:0.##}", 123.0);         // "123"
    ```

## Logging categories

- [configure logging](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging/?view=aspnetcore-5.0#configure-logging)

## Scopes

- because c# evaluates the `when` clauses of exception handling before it unwinds the stack traces, you can log in context of the scopes of the throwing code by logging in the `when` expressions
- if you log in the catch blocks themselves, you will lose any scope you created
- see [this](https://blog.stephencleary.com/2020/06/a-new-pattern-for-exception-logging.html) for more details
