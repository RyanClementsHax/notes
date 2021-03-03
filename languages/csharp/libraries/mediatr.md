# Mediatr

## Validating requests in the pipeline
- [MediatR.Extensions.FluentValidation.AspNetCore](https://github.com/GetoXs/MediatR.Extensions.FluentValidation.AspNetCore) will automatically hook up a pipeline behavior for you
    ```cs
    // Startup.cs
    services
        .AddFluentValidation(new[] { typeof(Startup).Assembly });
    ```
- if you use this library, your feature code can look like this
    ```cs
    public static class Create
    {
        [AutoMap(typeof(Entities.Models.Thing), ReverseMap = true)]
        public record Command(string Name) : IRequest<Domain.Thing>;

        // This validator will automatically be called before the handler
        // No need to explicitly set this up with DI or anything
        public class CommandValidator : AbstractValidator<Command>
        {
            public CommandValidator()
                => RuleFor(m => m.Name)
                    .NotEmpty();
        }

        public class Handler : IRequestHandler<Command, Domain.Thing>
        {
            private readonly AppDbContext _db;
            private readonly IMapper _mapper;

            public Handler(AppDbContext db, IMapper mapper)
            {
                _db = db;
                _mapper = mapper;
            }

            public async Task<Domain.Thing> Handle(Command request, CancellationToken cancellationToken)
            {
                var thing = _mapper.Map<Command, Entities.Models.Thing>(request);

                await _db.Things.AddAsync(thing, cancellationToken);
                await _db.SaveChangesAsync(cancellationToken);

                return _mapper.Map<Entities.Models.Thing, Domain.Thing>(thing);
            }
        }
    }
    ```
- this by default returns 500's on validation errors unless caught by other behaviors or middleware in the request pipeline
- the following is an example of using middleware to handle these validation exceptions
    ```cs
    // Startup.cs
    app
        // ...
        .UseExceptionHandlerMiddleware()
        // ...
    ```
    ```cs
    public static class ExceptionHandlerMiddleware
    {
        public static IApplicationBuilder UseExceptionHandlerMiddleware(this IApplicationBuilder builder)
            => builder.UseExceptionHandler(builder =>
                builder.Run(async context =>
                {
                    var errorFeature = context.Features.Get<IExceptionHandlerFeature>();
                    var ex = errorFeature.Error;

                    // https://tools.ietf.org/html/rfc7807#section-3.1
                    var problemDetails = new ProblemDetails
                    {
                        Detail = ex.Message,
                        Instance = errorFeature switch
                        {
                            ExceptionHandlerFeature e => e.Path,
                            _ => "unknown"
                        },
                        Extensions =
                        {
                            ["trace"] = Activity.Current?.Id ?? context?.TraceIdentifier
                        },
                    };

                    switch (ex)
                    {
                        case ValidationException validationEx:
                            problemDetails.Status = StatusCodes.Status400BadRequest;
                            problemDetails.Title = "One or more validation errors have occurred";
                            problemDetails.Extensions["errors"] = validationEx.Errors;
                            break;
                        default:
                            problemDetails.Status = StatusCodes.Status500InternalServerError;
                            problemDetails.Title = "An unexpected exception has occurred";
                            break;
                    }

                    problemDetails.Extensions["stack_trace"] = ex.ToString();

                    context.Response.ContentType = "application/problem+json";
                    context.Response.StatusCode = problemDetails.Status.Value;
                    context.Response.GetTypedHeaders().CacheControl = new CacheControlHeaderValue()
                    {
                        NoCache = true
                    };
                    await context.Response.WriteAsJsonAsync(problemDetails);
                }));
    }
    ```
- [ref](https://timdeschryver.dev/blog/creating-a-new-csharp-api-validate-incoming-requests)
- [ref](https://stackoverflow.com/questions/54104138/mediatr-fluent-validation-response-from-pipeline-behavior)