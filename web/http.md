# HTTP

- upgrade all http ajax requests
    ```html
    <!-- Make all AJAX requests https instead of http-->
    <meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests">
    ```

- browser complains about insecure connections can be caused by the dropping of `X-Forwarded-...` headers
    - [the reverse proxy will add these to the http request](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Forwarded)

