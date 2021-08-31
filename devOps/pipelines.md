# Pipelines

## Deploying to prod

- it is dangerous to have a pipeline deploy to prod directly because it means your CI system has prod keys
- it is preferrable to have the CI system publish a new container or artifact which a separate system deploys
  - the separate system can be configured to deploy the new container or artifact via polling or web hook

## Packaging your app

- dont package your app with the creds it needs to run because it likely has to sit in a container registry somewhere and is now tied to the environment it lives in
- packaging config with app because that means you have to redeploy everytime you change config or secrets (ex: if your current secrets are compromised)

## Branches

- it is important to delete unused branches because pipelines typically do scans of the repo for some of its actions
- having 10k branches will cause this step to take a long time

## [Docker in docker](http://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/)

- it is valuable to be able to run docker commands in a pipeline
  - integration tests
  - building images
  - etc
- this gets complicated when the agent running the pipeline is in docker itself
- problems with permissions
  - this might require you to run in `-privileged` mode to overcome security issues of the inner docker trying to do things that the outer docker doesn't want to allow by default
  - newer versions of docker seem to not need this flag due to sandboxing improvements
- problems with storage drivers
  - the inner docker runs on top of a copy-on-write system (AUFS, BTRFS, Device Mapper, etc.) whereas the outer docker runs on top of a normal file system
  - not all inner/outer file system combinations work
    - cannot run AUFS on top of AUFS
    - if you run BTRFS on top of BTRFS it should work at first, but once you have nested subvolumes, removing the parent subvolume will fail
    - Device Mapper is not namespaced so if multiple instances of docker use it on the same machine, they will be able to see (and affect) each other's image and container backing devices
  - there are workarounds for many of these issues
    - if you want to use AUFS in the inner docker, just promote `/var/lib/docker` to be a volume and you'll be fine
    - docker added some basic namespacing to Device Mapper target names, so that if multiple invocations of Docker run on the same machine, they won't step on each other
- problems with docker cache
  - you can try to bind-mount `/var/lib/docker` from the host to the inner docker, but this is how you get data corruption
  - the docker deamon was explicity designed to have exclusive access to `/var/lib/docker`
  - without caching however, every time you restart your inner docker, you will nuke your cache
- the clean solution seems to be to use [sysbox](https://github.com/nestybox/sysbox)
- another solution for simple use cases is to bind the docker socket as a volume,e.g. `docker run -v /var/run/docker.sock:/var/run/docker.sock ...`
  - this means that any containers created will be sibling containers, not child containers
  - the build cache will be shared though
  - you may run into permissions issues (e.g. jenkins not being part of the docker users group) if you do it this way
- another solution, then, is to use a sidecar container like [socat](https://hub.docker.com/r/alpine/socat/)

## Tips

- having smaller changes being deployed into production frequently reduces the surface area of code you need to inspect when finding bugs
  - this paired with feature flags can make batch sizes smaller
