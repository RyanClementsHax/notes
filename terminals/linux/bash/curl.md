# Curl

- [man page](https://linux.die.net/man/1/curl)

## Downloading a file
```bash
curl -X GET "https://localhost:5001/download/some-token" --output test.zip
```

## Ignoring ssl problems
- use the `-k` option

## Getting the status code from the request
- use `-s -w "%{http_code}\n"`
- this eliminates any other output it seems though

## [Timing a request](https://stackoverflow.com/questions/18215389/how-do-i-measure-request-and-response-times-at-once-using-curl?rq=1)
- you use the formatting api provided by the `-w` option

## [cURL 18 transfer closed with outstanding read data remaining](https://bobcares.com/blog/curl-18-transfer-closed-with-outstanding-read-data-remaining/)
- this problem happened for me when the connection opened went idle
- my solution was adding `--keepalive-time 999999` or some other very high timeout value