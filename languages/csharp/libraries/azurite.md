# Azurite

## Connecting to azurite in docker compose

1. Set your storage connection string to the following

    ```.env
    DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://azurite:10000/devstoreaccount1;
    ```

    - the important parts are `AccountName`, `AccountKey` and the port
        - the account name and key are well known and refer to a default azurite account and they are [safe to commit into your repo](https://docs.microsoft.com/en-us/azure/storage/common/storage-configure-connection-string#configure-a-connection-string-for-azurite) (DONT EVER COMMIT YOUR ACCOUNT KEYS)
        - port `10000` is the default port for blob storage listening
        - port `10001` is the default port for queue service listening (not used here)
    - here, we gave azurite the network alias `azurite`, but this can be whatever you want
    - you can add additional config to this string as needed
1. Configure the service in your docker compose file

    ```yml
    services:
      azurite: # make sure this name matches the one you gave it in the BlobEndpoint in the connection string
        image: mcr.microsoft.com/azure-storage/azurite # this is the azurite image that microsoft provides
        ports:
        - "10000:10000" # make sure you expose all the ports you need
        volumes:
        - ./.azurite-data:/data # hook up a volume if you want to persist the contents that get stored in azurite
    ```
