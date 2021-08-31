# Asp Net Core

- [docs](https://docs.microsoft.com/en-us/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-5.0)

## [HttpContext](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/http-context?view=aspnetcore-5.0)

- when you need to access it in a service, use [IHttpContextAccessor](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor?view=aspnetcore-5.0)
  - note that `IHttpContextAccessor` should be used with caution
  - it relies on `AsyncLocal<T>` which can have a negative performance impact on async calls
  - it also creates a dependency on "ambient state" which can make testing more difficult
