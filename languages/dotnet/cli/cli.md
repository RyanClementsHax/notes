# [CLI](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet)

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
