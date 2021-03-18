# Controllers

## Response types
```cs
[HttpPost("foo")]
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(typeof(ValidationResult), StatusCodes.Status400BadRequest)]
[ProducesDefaultResponseType]
public IActionResult Foo()
```

# From Query
```cs
[HttpGet("foo")]
public IActionResult Foo([FromQuery] DateTime beginDate)
```

# From Body
```cs
[HttpPost("foo")]
public IActionResult Foo([FromBody] DateTime beginDate)
```

# Required
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