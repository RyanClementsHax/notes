# Web Architecture

## Stats

- time to meaningful paint is the stat you want to use in most situations when talking about how fast a web page loads
  - this is the time where the UI actually displays something meaningful as opposed to just a loading spinner

## Internet speeds

- when architecting something to work on an international scale, you need to consider that not all places have the kind of internet connection we have in the US
- some people have to travel to a different town just to download something at an affordable rate or just to get connection at all
- even updating apps are something these people have to worry about because that takes up network resources
- server side rendered apps go a long way here to reduce the download size on the client and the rendering workload too

## Http requests

- GETs should not have side effects

## Cache invalidation

- Next.js has a clever cache invalidation policy such that when a request comes in, but the data is invalidated for whatever reason, it will serve up the invalid content then work on regenerating the content instead of making that request wait for the new data
- its a small price to pay, but the fact that only one more user gets invalid content doesn't really matter
