# Integration Tests

- these are suprisingly simple to set up
- [the docs](https://docs.microsoft.com/en-us/aspnet/core/test/integration-tests?view=aspnetcore-5.0) show you how to create a test host factory
    - you need `Microsoft.AspNetCore.Mvc.Testing` for this
    ```cs
    public class TestWebApplicationFactory<TStartup> : WebApplicationFactory<TStartup> where TStartup : class
    {
        protected override void ConfigureWebHost(IWebHostBuilder builder)
            => builder
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    builder
                        .AddInMemoryCollection(new Dictionary<string, string>
                        {
                            // config overrides can go here
                        })
                        .AddEnvironmentVariables() // but dont forget to overwrite them with env vars to make int testing with docker compose easier
                })
                .ConfigureServices(services =>
                {
                    // stub out/configure services here
                });
    }
    ```
    - this essentially starts up the application exactly how `Startup.cs` configures it to, but here you can overwrite the things you need to to make it testable
    - this example uses `Microsoft.Extensions.Configuration` for `AddInMemoryCollection` and `Microsoft.Extensions.Configuration.EnvironmentVariables` for `AddEnvironmentVariables`
- then your test base can look like this (this only supports sequential execution of tests)
    ```cs
    // TestBase.cs
    [TestClass, TestCategory("Large")]
    public class TestsBase
    {
        private static WebApplicationFactory<Startup> _factory;
        protected static HttpClient _client;
        private IServiceScope _scope;

        [AssemblyInitialize]
        public static void AssemblyInitialize(TestContext _)
        {
            _factory = new TestWebApplicationFactory<Startup>();
            _client = _factory.CreateClient();
        }

        [TestInitialize]
        public async Task Setup()
        {
            _scope = _factory.Services.CreateScope();
            // _someService = _scope.ServiceProvider.GetRequiredService<SomeService>();
        }

        [TestCleanup]
        public void Cleanup()
            => _scope.Dispose();
    }
    ```
    - `_factory.CreateClient();` creates an `HttpClient` you can use
    - you can get any services you need by first creating an `IServiceScope` from `_factory.Services.CreateScope();` then resolving the service with `_scope.ServiceProvider.GetRequiredService<SomeService>();`
      - note that when `_scope.Dispose();` is called, any services created from that scope will also dispose
- then your test can look like this
    ```cs
    // ThingsTests.cs
    [TestClass]
    public class ThingsTests : TestsBase
    {
        [TestMethod]
        public async Task Test()
        {
            // setup code
            // you can call your resolved services here too

            var resp = await _client.GetFromJsonAsync<Thing>("/things");

            // assertions
            // more calls to resolved services
        }
    }
    ```
    - it is recommended you install `System.Net.Http.Json` and `Microsoft.AspNet.WebApi.Client` to have some handy extension methods for deserializing responses

## With EF Core
- start up with the above code
- to get the db context in the tests, resolve it from the scope created
  - it is best practice to delete the database then recreate it on every test run to ensure that you are always starting off with a fresh db
    ```cs
    // TestBase.cs
    [TestInitialize]
    public async Task Setup()
    {
        _scope = _factory.Services.CreateScope();
        _db = _scope.ServiceProvider.GetRequiredService<AppDbContext>();
        await _db.Database.EnsureDeletedAsync();
        await _db.Database.EnsureCreatedAsync();
    }
    ```
- if you update the databse with your service tests, note that the db context you resolved in the `Setup` method won't see these changes, so just recreate the context to get these updates
    ```cs
    // TestBase.cs
    protected void RefreshContext()
    {
        _scope.Dispose(); // disposes everything resolved from the scope so be careful when implementing it like this
        _scope = _factory.Services.CreateScope();
        _db = _scope.ServiceProvider.GetRequiredService<AppDbContext>();
    }
    ```
    ```cs
    // ThingsTests.cs
    [TestMethod]
    public async Task DeleteAcronymsId_ValidId_DeletesAcronymAndReturnsDeletedAcronym()
    {
        // setup

        var resp = await _client.DeleteAsync($"/things/{id}");

        RefreshContext();
        // _db is now able to see that the item is deleted
    }
    ```
- see [the docker notes](../../../../docker/largeTestsUsingDocker.md) for setting this up with docker compose
  - pretty much all you need to be wary of is making sure you have the right config loaded
  - you also need to be sure that the database is able to serve requests by the time your tests start using it
    - you can achieve this with the `wait_for_it.sh` script mentioned in this section
    - or by polling the db in your test assembly setup code