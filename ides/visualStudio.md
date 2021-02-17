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
