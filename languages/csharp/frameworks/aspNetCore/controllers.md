# Controllers

## Response types

```cs
[HttpPost("foo")]
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(typeof(ValidationResult), StatusCodes.Status400BadRequest)]
[ProducesDefaultResponseType]
public IActionResult Foo()
```

## From Query

```cs
[HttpGet("foo")]
public IActionResult Foo([FromQuery] DateTime beginDate)
```

## From Body

```cs
[HttpPost("foo")]
public IActionResult Foo([FromBody] DateTime beginDate)
```

## Required

```cs
[HttpGet("foo")]
public IActionResult Foo([FromQuery, Required] DateTime beginDate)
```

## Regex

```cs
[HttpGet("foo/{param}")]
public IActionResult Foo([RegularExpression(@"^\d{10}$", ErrorMessage="param must be 10 digits")] string param)
```

## Conditionally using mapping

- [UseWhen and MapWhen](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/middleware/?view=aspnetcore-5.0#branch-the-middleware-pipeline)

## Patch endpoints

- one easy way to implement partial patching of a document is to use `JsonPatchDocument<TypeToUpdate>`
- just know that `JsonPatchDocument` doesn't support `System.Text.Json` if you are using that

## Returning a file

- downloading a file is as simple as returning some instance of a `FileResult` (there are many implementations) and give it a byte array or stream to read from
- if your controller extends from `BaseController`, you can use the handy `File(...)` methods it has
- the trickiest part is making sure the stream doesn't pool in memory (if that is a requirement)

## [[ApiController]](https://docs.microsoft.com/en-us/aspnet/core/web-api/?view=aspnetcore-5.0)

- opinionated way of defining api controller bahaviors like automatic model validation
