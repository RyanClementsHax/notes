# Rendering

## SSR (Server Side Rendering)
- initial page load is super fast
- more noticeable on slower connections
- only requires one round trip to make the page functional

## CSR (Client Side Rendering)
- subsequent page loads are typically faster because they only require additional data (unless you do lazy loading or code splitting)
- has some problems
  - caching
  - seo
  - sharability of content