# Middleware

- be careful not to call `next.Invoke` after the response has been sent to the client
  - bad stuff can happen

## Exception handling

- you should be able to use something like the following, but at the time of writing this, it only works in `IIS Express` as reported by my first `Aspnet Core` github [issue](https://github.com/dotnet/aspnetcore/issues/31024)!

    ```cs
    app.UseExceptionHandler(builder =>
        builder.Run(async context =>
        {
            var errorFeature = context.Features.Get<IExceptionHandlerFeature>();
            var ex = errorFeature.Error;
        
            context.Response.StatusCode = ex switch
            {
                DomainException domainEx => StatusCodes.Status400BadRequest,
                NotFoundException notFoundEx => StatusCodes.Status404NotFound, // if you change this status code to anything else, it will be fine
                _ => StatusCodes.Status500InternalServerError,
            };
        
            await context.Response.WriteAsync("There was an error caught in the exception handler lambda");
        }));
    ```

  - as stated in the github issue, this is an intentional design on the part of the aspnet core team
- as a work around, you can use the following

    ```cs
    app.Use(async (context, next) =>
    {
        try
        {
            await next();
        }
        catch (Exception ex)
        {
            context.Response.StatusCode = ex switch
            {
                DomainException => StatusCodes.Status400BadRequest,
                // if you change this status code to anything else, it will be fine
                NotFoundException => StatusCodes.Status404NotFound,
                _ => StatusCodes.Status500InternalServerError,
            };

            await context.Response.WriteAsync("There was an error caught in the exception handler lambda");
        }
    });
    ```
