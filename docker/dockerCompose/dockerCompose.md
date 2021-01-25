# Docker Compose

## Turn off logging for a service
```yml
services:
  my_service:
    image: my_image
    logging:
      driver: none
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