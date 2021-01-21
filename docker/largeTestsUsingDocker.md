# Large Tests Using Docker

- resources
  - [The Ultimate Guide to Integration Testing With Docker-Compose and SQL](https://medium.com/swlh/the-ultimate-guide-to-integration-testing-with-docker-compose-and-sql-f288f05032c9)
  - [Using Docker for .NET Core Integration Testing](https://wright-development.github.io/post/using-docker-for-net-core/)
  - [Integration testing with Docker Compose and Visual Studio Team Services. Now it starts getting really interesting!](https://medium.com/@christiansparre/integration-testing-with-docker-compose-and-visual-studio-team-service-83a1166055a8)
  - these have more complex examples and go more into depth

1. setup a docker file that runs your tests like this
    ```dockerfile
    FROM mcr.microsoft.com/dotnet/sdk:3.1
    COPY . /app
    WORKDIR /app
    RUN dotnet build
    ```
2. create your docker compose file with env variable overrides to point to the service running in docker compose
    ```yml
    services:
      db:
        image: mcr.microsoft.com/mssql/server:2019-latest
        environment:
          ACCEPT_EULA: "Y"
          SA_PASSWORD: "some password"
        ports:
          - 1433:1433
      your_service:
        build:
          context: .
        depends_on:
          - db
        environment:
          ConnectionString: "some connection string that references the db used in docker compose"
        entrypoint: bash /app/wait_for_it.sh db:1433 -t 0 -- dotnet test --no-build
    ```
    - [wait_for_it.sh](https://github.com/vishnubob/wait-for-it) is a handy script that waits for the port to open before running the tests, can be replaced for other methods of waiting
3. `docker-compose up`
  - yeah, its that simple
