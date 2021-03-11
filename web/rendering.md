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
