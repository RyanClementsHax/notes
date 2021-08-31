# Keys

## Verify public keys

- This generates a public key for a given private key

    ```bash
    ssh-keygen -y -f <private key file>
    ```

- Compare with the public key you are checking against using git diff

## Convert key format

```bash
ssh-keygen -p -N "" -m pem -f /path/to/key
```
