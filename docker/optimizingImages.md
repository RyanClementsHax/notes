# Optimizing Images

## Multi stage builds
- build containers can be rather large
- using multi stage builds lets you use a build container but copy the output into a runtime container for smaller image sizes
- [this](https://medium.com/@chrislewisdev/optimizing-your-net-core-docker-image-size-with-multi-stage-builds-778c577121d) article explains in more detail

## Copy package files first, then the rest of the project
- one way to speed up build time is to copy the package files for a project first, then run the `install` or `restore` command to install the dependencies of the project
- this will allow docker to cache this layer so it won't run this time consuming set of commands every time you change a file
  - docker does file hashing on `ADD` and `COPY` commands for cache invalidation, so if done this way, you will only rerun these steps if the project files change
- [this](https://andrewlock.net/optimising-asp-net-core-apps-in-docker-avoiding-manually-copying-csproj-files/) article explains more
  - unfortunately, as this article explains, there is no good way to copy only certain files using a regex and retain the original folder structure
  - if you have multiple package files you need to install dependencies for, you have to manually copy each package file and place it into its final directory manually (which is brittle of course), or do tar ball hacks like this guy

## Gotchas

### Volume builds
- hooking up your source code to a docker container and allowing the container to run the build command may sound tempting because it avoids having to copy the code into the container or rebuild any layers from code changing
- if you do this, however, the container will build, install, etc everything for the container's environment which may mismatch with the host environment
- thus, if you try to run, build, install, etc on the host machine after the container dumps its artifacts onto your system, you may come by extremely odd, esoteric problems that are hard to google
- you can fix this by cleaning your repo of build artifacts or recloning the repo
- ex
  - host machine: WSL `Ubuntu 20.04`
  - container image: `mcr.microsoft.com/dotnet/sdk:3.1` (debian based)
  - intent: building and running test code in the docker container for integration tests
