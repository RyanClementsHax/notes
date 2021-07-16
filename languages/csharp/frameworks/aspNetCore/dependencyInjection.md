# Dependency Injection

## Injecting `HttpContext`
- set up the DI in `ConfigureServices`
    ```cs
    services.AddHttpContextAccessor()
    ```
- inject `IHttpContextAccessor` within your service
    ```cs
    public class UserRepository : IUserRepository
    {
        private readonly IHttpContextAccessor _httpContextAccessor;

        public UserRepository(IHttpContextAccessor httpContextAccessor)
        {
            _httpContextAccessor = httpContextAccessor;
        }

        public void LogCurrentUser()
        {
            var username = _httpContextAccessor.HttpContext.User.Identity.Name;
            service.LogAccessRequest(username);
        }
    }
    ```
- there are [many ways](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/http-context?view=aspnetcore-5.0) to get `HttpContext` depending on where you need it