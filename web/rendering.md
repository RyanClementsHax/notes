# Rendering

## SSR (Server Side Rendering)
- initial page load is super fast
- more noticeable on slower connections
- only requires one round trip to make the page functional

## CSR (Client Side Rendering)
- subsequent page loads are typically faster because they only require additional data (unless you do lazy loading or code splitting)
- the usage of `/#/` in routes is a hack for CSR applications to do routing without having to navigate away from the page (since `#` in routes is an api for referencing an id on the page)
- has some problems
  - caching
  - seo
  - sharability of content
  - not accessible out of the box (ex: needs extra code to annouce to screen readers)
- routing
  - hash
    - this is when your url in the browser includes a `#` after the domain name like `www.example.com/#/some/route`
    - this is a trick to use html ids that don't exist to store routing info in the url
    - no server configuration necessary
  - history
    - this is when your client uses the history api in the browser to make your route look normal without the `#` like `www.example.com/some/path`
    - this requires that your server is configured to handle these extra routes by serving the same `index.html` on all of these paths
    - doing this potentially means that you will no longer return 404s (which would then never show up in logging)
    - you should also implement a 404 page on the client side to handle routes not found

## Core web vitals
- [An In-Depth Guide To Measuring Core Web Vitals](https://www.smashingmagazine.com/2021/04/complete-guide-measure-core-web-vitals/)
