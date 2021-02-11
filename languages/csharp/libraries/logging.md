# Logging

## Creating a logger statically without a host
```cs
private static ILogger _logger = LoggerFactory.Create(x => x.AddConsole()).CreateLogger<YourClassName>();
```
- needs `Microsoft.Extensions.Logging.Console` added to the project

## String interpolation
- it is preferred to use the log overloads that do the string interpolation for you so you can query your logs based on log template assuming you use structured logging (where logs are outputted as json containing the log and metadata surrounding it)
- it seems that most logging libraries (even the logging facilities in `NUnit`) use some form of [String.Format](https://docs.microsoft.com/en-us/dotnet/api/system.string.format?view=net-5.0) under the hood
