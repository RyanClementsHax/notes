# Visual Studio

- opening a project in visual studio may cause it to create a `launchSettings.json` file which will include a bunch of default configuration that overwrites any other configuration you might have

## Opening a project in WSL file system
- you need to trick visual studio into opening up a folder from the WSL file system because it cannot parse network drive paths (i.e. `\\wsl$\Ubuntu\home\rclements\dev`)
- we get around this by using symlinks
1. create a folder on your windows file system to symlink into the WSL file system
2. [create the symlink](../../../terminals/cmd/cmd.md#create-a-symbolic-link-with-a-network-drive)
3. open the code in visual studio using the file path that contains this symlink

## Debuggging unit tests
- you would expect visual studio to break on unhandled exception when running unit tests
- [this is not the case](https://stackoverflow.com/questions/1488282/why-doesnt-visual-studio-break-on-exceptions-when-debugging-unit-tests) as the test framework technically catches it

## You can use VS to handle secrets you don't want in your codebase
- [ref](https://stackoverflow.com/questions/42268265/how-to-get-manage-user-secrets-in-a-net-core-console-application)

## Docker compose
- there seems to be a really cool docker compose feature you can get by creating a `.dcproj` file
- I've had good success with it, just know that the solution explorer hides the `docker-compose.override.yml` file as a drop down under `docker-compose.yml` like it hides `appsettings.Development.json` under `appsettings.json`
- do note that cleaning your solution also cleans up docker containers
- [this](https://docs.microsoft.com/en-us/visualstudio/containers/docker-compose-properties?view=vs-2019) seems to be a good set of docs for how all this works
- I think you need the `.NET Core cross-platform development` workload installed to do this

## Formatting code
- VS will automatically use the `.editorconfig` placed in your repo
- VS can automatically generate a `.editorconfig` based on your settings too if you go to `Tools > Options > Text Editor > C# > Code Style > Generate .editorconfig file from settings`
- all of the rules can be found [here](https://docs.microsoft.com/en-us/dotnet/fundamentals/code-analysis/style-rules/formatting-rules)

## Testing
- if not all of your tests run even when you hit the run all button, it is possible that one of your tests crashed the test runner process
- look in the more detailed output to figure out what happened
- a stack overflow error did this for me

## Productivity
- [guide](https://docs.microsoft.com/en-us/visualstudio/ide/csharp-developer-productivity?utm_source=VisualStudio&utm_medium=aspnet-getstarted&utm_campaign=VisualStudio&view=vs-2019)

### Code snippets
- [they](https://docs.microsoft.com/en-us/visualstudio/ide/code-snippets?view=vs-2019) look pretty cool
