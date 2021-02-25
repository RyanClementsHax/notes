# Postgres

- can do msg queueing

## Using with `docker-compose`
```yml
version: '3.4'

services:
  db:
    image: postgres:latest
    ports:
      - 5432:5432 # default port
    volumes:
      - db-data:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: "admin"
      POSTGRES_PASSWORD: "password"
      POSTGRES_DB: "MyApp"

volumes:
  db-data:
```