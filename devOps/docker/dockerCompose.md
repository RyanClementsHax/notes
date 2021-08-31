# Docker Compose

## Turn off logging for a service

```yml
services:
  my_service:
    image: my_image
    logging:
      driver: none # default is "local"
```

## Networking

```yml
services:
  db:
    build:
      image: some_db
    ports: # this only opens up ports on the host machine to talk to db in this case, it doesn't affect what ports other containers like test can access
      - 1344:1433
  tests:
    depends_on:
      - db
    build:
      context: .
```

## Force recreating the containers

- add `--force-recreate` to `docker-compose up` to recreate the containers (using cache) instead of spinning up the last container made
- this is good if the containers go through mutations when running

## Multiple compose files

- you can use multiple compose files in one command
- you do this by using the `-f` flag

```bash
docker-compose -f base.docker-compose.yml -f prod.docker-compose.yml up
```

- if no files are specified, `docker-compose` defaults to find a `docker-compose.yml` file in the current directory
- if any files are specified, it only uses those files
- specifying a file overwrites everything that came before it

## `.dockerignore`

- as of 1/28/21, there is no way to tell `docker-compose` to use a specific `.dockerignore` file, it will only use the one it finds in the root of the build context directory you supply it

## Using env vars

- `docker-compose` files allow you to do [env subsitution](https://docs.docker.com/compose/compose-file/compose-file-v3/#variable-substitution)
- it uses the variables from the shell env it is run from
- example

  ```yml
  db:
    image: "postgres:${POSTGRES_VERSION}"
  ```

- default values are supported too
  - `${VARIABLE:-default}` evaluates to default if `VARIABLE` is unset or empty in the environment
  - `${VARIABLE-default}` evaluates to default if `VARIABLE` is unset in the environment
- if you need to actually use the `${var}` syntax, double up the `$` like `$${var}`

## Deploying remotely

- you configure the docker host env var or use wrappers like docker context (recommended) to do this for you
- you do need to set up ssh keys beforehand though
- [ref](https://www.docker.com/blog/how-to-deploy-on-remote-docker-hosts-with-docker-compose/)

## [extends](https://docs.docker.com/compose/extends/)

- allows you to extend a service within another compose file
- cannot extend services that have `depends_on` defined

## Naming services

- consider the following yml files

  ```yml
  # int_tests.docker-compose.yml
  services:
    db:
      build:
        image: some_db
      ports:
        - 1344:1433
    tests:
      depends_on:
        - db
      build:
        context: .
  ```

  ```yml
  # docker-compose.yml
  services:
    db:
      build:
        image: some_db
      ports:
        - 1344:1433
    service:
      depends_on:
        - db
      build:
        context: .
  ```

- because both files declare a service called `db`, you can't stand up both services at same time without conflicts (btw they're silent)
- you can fix this by making all service names unique
