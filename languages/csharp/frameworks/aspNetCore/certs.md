# Certs

## Dev certs
- there seems to be support for only one dev cert at a time
- you can create one by doing the following
    ```bash
    dotnet dev-certs https --clean
    dotnet dev-certs https -t
    ```
- [Unable to configure HTTPS endpoint. No server certificate was specified, and the default developer certificate could not be found](https://stackoverflow.com/questions/53300480/unable-to-configure-https-endpoint-no-server-certificate-was-specified-and-the)
