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

- this script is really handy for monitoring the progress of your tests for CI
    ```bash
    # author: https://blog.harrison.dev/2016/06/19/integration-testing-with-docker-compose.html
    RED='\033[0;31m'
    GREEN='\033[0;32m'
    NC='\033[0m'
    cleanup () {
      docker-compose -p ci kill
      docker-compose -p ci rm -f
    }
    trap 'cleanup ; printf "${RED}Tests Failed For Unexpected Reasons${NC}\n"' HUP INT QUIT PIPE TERM
    docker-compose -p ci -f docker-compose.yml -f docker-compose.tests.yml build && docker-compose -p ci -f docker-compose.yml -f docker-compose.tests.yml up -d
    if [ $? -ne 0 ] ; then
      printf "${RED}Docker Compose Failed${NC}\n"
      exit -1
    fi
    # uncomment if you want to follow the test logs instead of just waiting for the exit code
    # docker logs ci_tests_1 --follow
    TEST_EXIT_CODE=`docker wait ci_tests_1`
    docker logs ci_tests_1
    if [ -z ${TEST_EXIT_CODE+x} ] || [ "$TEST_EXIT_CODE" -ne 0 ] ; then
      docker logs ci_seed_1
      docker logs ci_db_1
      docker logs ci_fun_1
      printf "${RED}Tests Failed${NC} - Exit Code: $TEST_EXIT_CODE\n"
    else
      printf "${GREEN}Tests Passed${NC}\n"
    fi
    cleanup
    exit $TEST_EXIT_CODE
    ```
