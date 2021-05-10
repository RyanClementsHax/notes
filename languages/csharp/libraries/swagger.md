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

## Create generated clients based on swagger docs

1. Find and copy the url of the json spec of the api (link to it in the top of the swagger page)
1. Open up the project using Visual Studio
1. Right click "Connected Services" and select the "Add Connected Service" option
1. Depending on your version of VS, the next screen will look different. You need to select the add button which will look like a green plus on all version of VS.
1. Then select "URL" from the radio buttons and past in the url copied before
1. You can then leave the rest of the options default because we can edit those in csproj
1. After you add it, you should see something like this in the csproj
    ``` xml
    <OpenApiReference Include="OpenAPIs\swagger1.json" CodeGenerator="NSwagCSharp">
      <SourceUri>https://example.com/swagger/v1/swagger.json</SourceUri>
    </OpenApiReference>
   ```
1. Edit the setting by updating the csproj:
   ``` xml
    <OpenApiReference Include="OpenAPIs\swagger1.json" CodeGenerator="NSwagCSharp">
      <SourceUri>https://example.com/swagger/v1/swagger.json</SourceUri>
	  <Options>/GenerateClientInterfaces:true /GenerateOptionalParameters:true</Options> <!-->You can find the reset of these options here https://github.com/RicoSuter/NSwag/wiki/CSharpClientGeneratorSettings</!-->
	  <Namespace>Weather</Namespace>
      <ClassName>WeatherServiceClient</ClassName>
      <OutputPath>WeatherServiceClient.cs</OutputPath>
    </OpenApiReference>
   ```
1. After you build the project, the generated client should be created and show up in the obj folder of the project
1. If the api updates, you can update the client by going back to the "Connected Services" page in VS and "Refreshing" the reference.
