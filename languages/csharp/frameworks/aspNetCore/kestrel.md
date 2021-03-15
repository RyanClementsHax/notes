# Kestrel

## Certs
- for docker, you need the following volumes set up to be able to use your local certs for development
```yml
volumes:
    - ${APPDATA}/Microsoft/UserSecrets:/root/.microsoft/usersecrets:ro
    - ${APPDATA}/ASP.NET/Https:/root/.aspnet/https:ro
```