# [CLI](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet)

## Installing the SDK

- [there is a command line helper for installing this](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-install-script)
- installing on ubuntu

    ```bash
    sudo apt-get install -y dotnet-sdk-3.1 # donet core 3.1
    sudo apt-get install -y dotnet-sdk-5.0 # dotnet 5.0
    ```

## Uninstalling an sdk

- [there is a script for doing this](https://docs.microsoft.com/en-us/dotnet/core/additional-tools/uninstall-tool?tabs=windows)

## `dotnet restore`

- `dotnet restore` restores the packages, but does not build
- i have come by a problem where this command when run in a dockerfile would stall
  - it would have network timeouts to nuget after 100s
  - this seemed to be a network problem on nuget's side

## `dotnet build`

- `dotnet build` implicitly restores and then builds the project
  - can disable the implicit restore with `--no-restore`

## `dsotnet test`

- implicitly restores and builds, then runs the tests
  - can disable implicit restore with `--no-restore`
  - can disable implicit build with `--no-build`
- running `dotnet test --no-build` without building the project first will result in odd errors like: `error MSB4181: The "Microsoft.TestPlatform.Build.Tasks.VSTestTask" task returned false but did not log an error.`
- logging in tests not showwing up? try adding the following command line option: `--logger:"console;verbosity=detailed"`

## `dotnet clean`

- cleans out the `bin` and `obj` folders but doesn't delete the folder structures created by `dotnet build` and `dotnet restore`

## [`dotnet sln`](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-sln)

- adding a project to a solution:

    ```bash
    dotnet sln your.sln add proj1.csproj [proj2.csproj...]
    ```

- creating a new sln

    ```bash
    dotnet new sln name_of_sln.sln
    ```

## `dotnet tools`

- for installing add on cli features like `dotnet ef`
- needs a path to the tools installation folder in order to work
  - i had to manually add it to my path for wsl `PATH="$PATH:$HOME/.dotnet/tools"`, but it should be automatically configured
  - not having this on your path will lead to frustrating errors
- installing a tool

  ```bash
  dotnet tool install --global dotnet-ef [--version some.version.number]
  ```
