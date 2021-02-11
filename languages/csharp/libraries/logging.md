# Logging

## Creating a logger statically without a host
```cs
private static ILogger _logger = LoggerFactory.Create(x => x.AddConsole()).CreateLogger<YourClassName>();
```
- needs `Microsoft.Extensions.Logging.Console` added to the project
