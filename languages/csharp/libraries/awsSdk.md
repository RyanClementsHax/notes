# AWS SDK

## Transfer Utility
- this is a wrapper around uploads and multipart uploads to S3
- [there is no current way](https://github.com/aws/aws-sdk-net/issues/1095) to "stream" content into S3 using this language's sdk

## Testing collection extensions that inject AWS services
- take the following extension method and lets say that `SomeService` depends on `IAmazonS3`
    ```cs
    public static IServiceCollection MyExtension(this IServiceCollection services)
        => services
            .TryAddAWSService<IAmazonS3>()
            .AddTransient<ISomeService, SomeService>();
    ```
- when `ISomeService` is resolved, `IAmazonS3` is resolved too
  - this means that a concrete instance of `IAmazonS3` will be instantiated
  - when that class is instantiated, it looks for AWS creds to use and if it can't find any, it will error out
  - locally, it will find our own credentials assuming they're configured
  - what if the environment that runs these tests doesn't have any aws creds (e.g. CI environment) however?
  - we definitely don't care about its ability to find creds when we are trying to test if `ISomeService` can be resolved
  - ideally, we want to "mock" out the creds that this service finds
  - to do this, we just add the env vars it needs to operate
    - inspiration was taken from how the maintainers of [aws-sdk-net](https://github.com/aws/aws-sdk-net) [test their DI extensions](https://github.com/aws/aws-sdk-net/blob/475822dec5e87954b7a47ac65995714ae1f1b115/extensions/test/NETCore.SetupTests/DependencyInjectionTests.cs#L16)
  - you can test this by renaming your `.aws` folder and running these tests and seeing if it can still resolve all of the types
    ```cs
    [TestMethod]
    public void MyTest()
    {
        var config = new ConfigurationBuilder
            .AddInMemoryCollection(new Dictionary<string, string>
            {
                ["AWS:Region"] = "us-west-2",
            })
            .Build();
        var provider = new ServiceCollection()
            .AddDefaultAWSOptions(config.GetAWSOptions())
            .MyExtension()
            .BuildServiceProvider();

        var service = provider.GetRequiredService<ISomeService>();

        Assert.IsNotNull(service);
    }
    ```