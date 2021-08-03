# [Hellang.Middleware.ProblemDetails](https://www.nuget.org/packages/Hellang.Middleware.ProblemDetails/)

- handles standardization of problem details via middleware
- has lots of helper methods to configure how problem details are returned

## Setup

```cs
public void ConfigureServices(IServiceCollection services)
    => services
        // other services and configuration omitted for brevity
        .AddProblemDetails(options =>
        {
            // this is where we configure how problem details are returned and with what status codes
            // see the repo for more detail on what you can do
            options.MapToStatusCode<DomainException>(StatusCodes.Status400BadRequest);
            options.MapToStatusCode<NotFoundException>(StatusCodes.Status404NotFound);
        })
        .AddControllers()
            // other controller configuration omitted for brevity
            // this configures the controllers to use the same problem details conventions as everything else
            // very handy
            .AddProblemDetailsConventions();
            
public void Configure(IApplicationBuilder app)
{
    app
        .UseProblemDetails();
        // other middleware omitted for brevity
}
```

## Usage with FluentValidation

- these return custom exceptions foreign to this middleware package
- in order to return a validation problem details like the rest of the app and also to take advantage of configuration regarding such validation problem details, we need to configure the middleware to be aware of this
  - this is the recommended approach by the library author
- first create an extension for the middleware that maps the exception to produce a validation problem details

    ```cs
    public static class ProblemDetailsExtensions
    {
        public static void MapFluentValidationExceptions(this ProblemDetailsOptions options) =>
            options.Map<ValidationException>((ctx, ex) =>
            {
                var factory = ctx.RequestServices.GetRequiredService<ProblemDetailsFactory>();

                var errors = ex.Errors
                    .GroupBy(x => x.PropertyName)
                    .ToDictionary(
                        x => x.Key,
                        x => x.Select(x => x.ErrorMessage).ToArray()
                    );

                return factory.CreateValidationProblemDetails(ctx, errors);
            });
    }
    ```

- then use it when configuring the middleware

    ```cs
    .AddProblemDetails(options =>
    {
        options.MapFluentValidationExceptions();
    })
    ```
