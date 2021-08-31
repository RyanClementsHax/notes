# System.Net.Http

## `HttpClient`

- don't use an `HttpClient` directly because it was designed to be reused by the whole application
- use `IHttpClientFactory` instead
- see [remarks](https://docs.microsoft.com/en-us/dotnet/api/system.net.http.httpclient?view=net-5.0#remarks)
