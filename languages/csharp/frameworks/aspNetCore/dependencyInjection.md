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

## Testing DI setup
- it's easier than you think
    ```cs
    public static IServiceCollection MyExtension(this IServiceCollection services)
        => services
            .AddTransient<ISomeService, SomeService>();
    ```
    ```cs
    [TestMethod]
    public void MyTest()
    {
        var provider = new ServiceCollection()
            .MyExtension()
            .BuildServiceProvider();

        var service = provider.GetRequiredService<ISomeService>();

        Assert.IsNotNull(service);
    }
    ```
- when using config, use `new ConfigurationBuilder()`
    ```cs
    public class MyOptions
    {
        public string Foo { get; set; }
    }
    ```
    ```cs
    public static IServiceCollection MyExtension(this IServiceCollection services, IConfiguration config)
        => services
            .Configure<MyOptions>(config.GetSection(nameof(MyOptions)))
            .AddTransient<ISomeService, SomeService>();
    ```
    ```cs
    [TestMethod]
    public void MyTest()
    {
        var config = new ConfigurationBuilder
            // its easy to add config values directly using AddInMemoryCollection
            .AddInMemoryCollection(new Dictionary<string, string>
            {
                ["MyOptions__Foo"] = "bar"
            })
            .Build();
        var provider = new ServiceCollection()
            .MyExtension(config)
            .BuildServiceProvider();

        var options = provider.GetRequiredService<IOptions<MyOptions>>().Value;

        Assert.AreEqual("bar", options);
    }
    ```

## [ServiceDescriptor](https://www.c-sharpcorner.com/article/using-servicedescriptor-to-register-dependencies-in-asp-net-core/)
- this is a low level way to interact with the DI system
- seems like you can ignore this unless you are creating complex DI extensions
