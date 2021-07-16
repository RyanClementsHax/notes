# Curl

## Downloading a file
```bash
curl -X GET "https://localhost:5001/download/some-token" --output test.zip
```

## Ignoring ssl problems
- use the `-k` option

## Getting the status code from the request
- use `-s -w "%{http_code}\n"`
- this eliminates any other output it seems though