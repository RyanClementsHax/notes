# HTTP

- upgrade all http ajax requests
    ```html
    <!-- Make all AJAX requests https instead of http-->
    <meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests">
    ```

- browser complains about insecure connections can be caused by the dropping of `X-Forwarded-...` headers
    - [the reverse proxy will add these to the http request](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Forwarded)

- detecting if request sent from mobile
  - one way to do this is check the `user-agent` header and compare it against a regex
    ```ts
    const mobileRE = /(android|bb\d+|meego).+mobile|avantgo|bada\/|blackberry|blazer|compal|elaine|fennec|hiptop|iemobile|ip(hone|od)|iris|kindle|lge |maemo|midp|mmp|mobile.+firefox|netfront|opera m(ob|in)i|palm( os)?|phone|p(ixi|re)\/|plucker|pocket|psp|series(4|6)0|symbian|treo|up\.(browser|link)|vodafone|wap|windows (ce|phone)|xda|xiino/i

    export function isMobile(opts) {
        if (!opts) {
            opts = {}
        }

        let ua = opts.ua

        if (!ua && typeof navigator !== 'undefined') {
            ua = navigator.userAgent
        }

        if (!ua && opts.req && opts.req.headers && typeof opts.req.headers['user-agent'] === 'string') {
            ua = opts.req.headers['user-agent']
            console.log(ua)
        }

        if (typeof ua !== 'string') {
            return false
        }

        return mobileRE.test(ua)
    }
    ```

