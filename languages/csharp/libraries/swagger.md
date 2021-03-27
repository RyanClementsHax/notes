# Swagger

## [Showing summary text in swagger ui](https://docs.microsoft.com/en-us/samples/aspnet/aspnetcore.docs/getstarted-swashbuckle-aspnetcore/?tabs=visual-studio)
1. add this section to some property group section in your `.csproj`
    ```xml
    <PropertyGroup>
        <GenerateDocumentationFile>true</GenerateDocumentationFile>
        <NoWarn>$(NoWarn);1591</NoWarn>
    </PropertyGroup>
    ```
2. add this to your `ConfigureServices` method in `Startup`
    ```cs
    services.AddSwaggerGen(c =>
    {
        // other config

        // Set the comments path for the Swagger JSON and UI.
        var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
        c.IncludeXmlComments(xmlPath);
    });
    ```
3. start adding summaries
    ```cs
    /// <summary>
    /// Deletes a specific TodoItem.
    /// </summary>
    /// <param name="id"></param>        
    [HttpDelete("{id}")]
    public IActionResult Delete(long id)
    {
        var todo = _context.TodoItems.Find(id);

        if (todo == null)
        {
            return NotFound();
        }

        _context.TodoItems.Remove(todo);
        _context.SaveChanges();

        return NoContent();
    }
    ```

## [Integrating with JsonPatchDocument](https://michael-mckenna.com/swagger-with-asp-net-core-3-1-json-patch/)