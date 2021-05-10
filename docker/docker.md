# Docker

## Run azurite
```bash
docker run -d -p 10000:10000 -p 10001:10001 --name azurite -v path_to_where_you_want_azurite_to_save_files_to:/data mcr.microsoft.com/azure-storage/azurite:latest
```

## Change timezone in image
```dockerfile
USER root
ENV TZ=America/New_York
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

USER www-data
```

## Run as privileged
- Add `docker run --privileged`
- Allows for network mounting

## Network Mounting
```bash
rm /opt/smbcreds # wherever you want ot store your smbcreds
echo "username=$1
password=$2
domain=<your domain>" > /opt/smbcreds

mkdir -p /opt/local_folder

sudo mount -t cifs -v -o credentials=/opt/smbcreds //network_drive_location/remote_folder /opt/local_folder
```
- `//` is very important

## Delete all containers
```bash
docker rm $(docker ps -a -q)
```

## Delete all images
```bash
docker rmi $(docker images -a -q)
```

## Ports
host_port:container_port

## Exec into running container
```bash
docker exec -it container_name_or_id /bin/bash
```

## `.dockerignore`
- only used at build time
- when docker sends over the build context to the daemon, it won't include anything specified in this file
- can specify a `.dockerignore` per docker file
  - `foo.Dockerfile` gets `foo.dockerignore`
  - `bar.Dockerfile` gets `bar.dockerignore`

## COPY move vs rename
```Dockerfile
# this moves the file into the container's file path of /some/container/path/ and keeps its same name of file
COPY ./some/host/path/file /some/container/path/
# this moves the file into the container's file path of /some/container/ and renames it to path
COPY ./some/host/path/file /some/container/path
```

## RUN
- a `cd` in this command doesn't change the working directory
```Dockerfile
WORKDIR /app
RUN cd src/
# this will run in context of /app
RUN npm install
```

## Running containers
- running a container with `-it` allows you to exit it without stopping it by pressing `ctrl+p` then `ctrl+q`
  - [ref](https://stackoverflow.com/questions/19688314/how-do-you-attach-and-detach-from-dockers-process)

## Tags
 - [ref](https://forums.docker.com/t/what-is-the-naming-conventions-of-docker-tag/34042)
 - regex
    ```go
    // TagRegexp matches valid tag names. From docker/docker:graph/tags.go.
    TagRegexp = match(`[\w][\w.-]{0,127}`)
    ```

## The daemon
- this is the process running in the background controlling all of your containers
- this is not always configured to start up when the os starts up
  - this can lead to unexpected downtime when your vm restarts but doesn't restart docker

## restart
- this is just a light wrapper around `docker stop <container>` and `docker start <container>`

## Moving files via cli
- `docker cp` can be used to move files around between containers, from a container to your host, or within a container
```bash
docker cp containerA/fileA host/file
docker cp host/file containerB/fileB
```

## Volumes
- sometimes I run into mounting problems
- they seem to be caused by volumes already existing so try to remove the volume using `docker volume rm`

## `commit`
- takes a running container and commits it into an image
- any data in volumes are not committed to the image as you would expect

## Builds
- it doesn't seem like there is native support to use local caches like your hosts `.m2` cache to build docker containers, but there seem to be [wrappers](https://aboullaite.me/speed-up-your-java-application-images-build-with-buildkit/) that exist to try to provide such a functionality

## Dynamically removing a volume attached to a container
```bash
docker volume rm $(docker inspect -f '{{range $p, $conf := .Mounts}} {{$conf.Name}} {{end}}' $(docker inspect --format='{{.Id}}' container_name))
```

## Restarting docker desktop
```powershell
$processes = Get-Process "*docker desktop*"
if ($processes.Count -gt 0) {
  $processes[0].Kill()
  $processes[0].WaitForExit()
}
Start-Process "C:\Program Files\Docker\Docker\Docker Desktop.exe"
```

## [Rate limiting](https://www.docker.com/increase-rate-limits)
- the only way around this is to create an account and use that and potentially pay, or use your own repo
