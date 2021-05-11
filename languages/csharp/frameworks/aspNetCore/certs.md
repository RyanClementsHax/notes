# Certs

## Creating one
```bash
dotnet dev-certs https --clean
dotnet dev-certs https -t
```
- there seems to be support for only one cert at a time, hence why you need to clean the certs before creating a new one
- [Unable to configure HTTPS endpoint. No server certificate was specified, and the default developer certificate could not be found](https://stackoverflow.com/questions/53300480/unable-to-configure-https-endpoint-no-server-certificate-was-specified-and-the)
  - this error means that the server couldn't find the certificate
  - either you didn't create one, or it cannot find one on the path given

## With docker
- launching the docker or docker compose support that visual studio provides automatically handles all of this for you (I have no idea what it does)
  - it seems to create multiple certificates specific to each container you spin up
- if you're not using visual studio, you need to create dev certs manually, then point kestrel to use them
  - [here is a tutorial](https://codeburst.io/hosting-an-asp-net-core-app-on-docker-with-https-642cde4f04e8) on one way to do this
  - [here is a github issue](https://github.com/dotnet/AspNetCore.Docs/issues/6199) that shows that many people are confused on how to do this
  - first run `dotnet dev-certs https -ep $APPDATA/ASP.NET/Https/dotnet-dev.pfx -p password --trust` to create a certificate with the password as `password`
    - kestrel doesn't seem to like certs without passwords
    - the name of the cert doesn't seem to be important
  - if you have another certificate installed, run `dotnet dev-certs https --clean` which will remove the current https certificate (just be sure that any applications that depend on it are also updated)
  - make sure the following volume exists on your container
    - `${APPDATA}/ASP.NET/Https:/root/.aspnet/https:ro`
  - now add the following environment variables in your container
    ```yml
    ASPNETCORE_Kestrel__Certificates__Default__Path: /root/.aspnet/https/dotnet-dev.pfx
    ASPNETCORE_Kestrel__Certificates__Default__Password: password
    ```
  - now kestrel should be happy