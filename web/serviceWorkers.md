# Service Workers

- only work over https connections
- good for caching
- good for stale-while-revalidate strategies
- good for downloads without requiring any tabs to be open
- good for prefetching data
- a single tab can be controlled by at most one worker
- not ideal for caching data like product data (use indexdb instead)

## Manifest

- used to configure how the app gets downloaded and displayed on the mobile device
- not all of the fields are supported on ios

## Scope

- scope of service worker is determined by where in the folder structure it exists
- it controls multiple tabs of the same website for a given scope
- if it is in the root, its scope is the root
- if it is in the `img` folder, its scope is the `img` folder

## Registration

- registration is handled by normal js code (the kind that has access to the dom)
- doesn't get deleted when the page is refreshed (unless the code gets changed)
- lifecyle
    1. sw registered
       - this promise may not resolve in coordination with other events (i.e. could be out of order)
       - sw registration will _always_ happen as opposed to install and active events
    2. install event
       - if a previous version of the service worker is already active, the new service worker waits for all instances  to become inactive before continuing
       - this prevents breaking changes from replacing the sw while the app is running
    3. service worker becomes active
    4. active event
    5. service worker 'listens' for events

## Strategies

- cache first
- cache only
- network first
- network only
- stale while revaildate

## Workbox

- library to handle service worker code
- has implementations of all the common strategies
- [great tutorial series](https://www.youtube.com/playlist?list=PLNYkxOF6rcIB2xHBZ7opgc2Mv009X87Hh)

### [Strategies](https://www.youtube.com/watch?v=q4k-GOmI8Tg&list=PLNYkxOF6rcIB2xHBZ7opgc2Mv009X87Hh&index=18&ab_channel=GoogleChromeDevelopersGoogleChromeDevelopersVerified)

## [Support](https://caniuse.com/?search=serviceworker)

- not all browsers support them
