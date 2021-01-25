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