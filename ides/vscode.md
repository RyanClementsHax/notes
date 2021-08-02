# VSCode

## Omnisharp

- sometimes this doesn't load project properly
- recloning the repo and reopening vscode a few times seems to resolve these issues for me
- this could also be caused by large sln's
  - try making a sln with just what you need
- don't modify code as the server is starting up, this will lead to crazily buggy intellisense
- follow [this](https://stackoverflow.com/questions/47352611/visual-studio-code-doesnt-format-c-sharp-code) if you want omnisharp to auto format code

## Getting color scheme from current settings

- [can generate json file](https://superuser.com/questions/1431557/is-there-any-place-i-can-find-all-the-hex-code-color-values-for-vscodes-defaul)

## Tasks

### Dotnet tasks

- [example](https://github.com/graphql-dotnet/example-aspnetcore/blob/master/.vscode/tasks.json)

  ```json
  {
    "version": "2.0.0",
    "tasks": [
      {
        "label": "build",
        "command": "dotnet",
        "type": "process",
        "args": [
          "build",
          "${workspaceFolder}/src/Example/Example.csproj"
        ],
        "problemMatcher": "$msCompile"
      }
    ]
  }
  ```

### [Docker tasks](https://code.visualstudio.com/docs/containers/reference)

- ensure you have the docker vscode extension installed as well as docker on your machine
- example

  ```json
  {
    "label": "dev db rm volume",
    "command": "docker",
    "type": "shell",
    "args": ["volume", "rm", "dev_db-data"],
    "problemMatcher": []
  }
  ```

#### [Docker compose](https://code.visualstudio.com/docs/containers/reference#_docker-compose-task)

- example

  ```json
  {
    "version": "2.0.0",
    "tasks": [
      {
        "label": "Run docker-compose up",
        "type": "docker-compose",
        "dockerCompose": {
          "up": {
            "detached": true,
            "build": true,
            "services": ["myservice"]
          },
          "files": [
            "${workspaceFolder}/docker-compose.yml",
            "${workspaceFolder}/docker-compose.debug.yml"
          ]
        }
      }
    ]
  }
  ```

## Launch configurations

### Dotnet

```json
{
  "name": "My.Api launch",
  "type": "coreclr",
  "request": "launch",
  "preLaunchTask": "build",
  "program": "${workspaceFolder}/My.Api/bin/Debug/net5.0/My.Api.dll",
  "args": [],
  "cwd": "${workspaceFolder}/My.Api/bin/Debug/net5.0/",
  "stopAtEntry": false,
  "launchBrowser": {
    "enabled": true,
    "args": "${auto-detect-url}/swagger/index.html",
    "windows": {
      "command": "cmd.exe",
      "args": "/C start ${auto-detect-url}/swagger/index.html"
    },
    "osx": {
      "command": "open"
    },
    "linux": {
      "command": "xdg-open"
    }
  },
  "env": {
    "ASPNETCORE_ENVIRONMENT": "Development",
    "ASPNETCORE_URLS": "https://+:5001;http://+:5000"
  }
}
```

- this configuration runs an api and opens the browser to the swagger page automatically
- it also auto detects the url it is on from the console output
- it requires that there is a `build` task defined because of `"preLaunchTask": "build"`

```json
{
  "name": "Docker .NET Core Attach (Preview)",
  "type": "docker",
  "request": "attach",
  "platform": "netCore",
  "sourceFileMap": {
    "/src": "${workspaceFolder}"
  }
}
```

- i believe this requires the c# vscode extension
- this allows you to attach to running dotnet core containers

### Compound

```json
{
  "compounds": [
    {
      "name": "My Api and E2E",
      "configurations": [
        "My.Api launch",
        "My.Api.E2E launch"
      ]
    }
  ]
}
```

- this section of the launch settings allows you to launch multiple launch configurations at once
