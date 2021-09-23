# JWT

- the usefulness of these are [hotly debated on the internet](https://stackoverflow.com/questions/21978658/invalidating-json-web-tokens/52407314#52407314)
- they provide the ability in a microservices environment to allow for decentralized authentication
- they're also good to serve as api keys
- they can also be used with cookies, but mitigations against CSRF and XSS attacks must be implemented
- their downside is that as long as the token exists, the token can be used (i.e. revoking is difficult)
  - to mitigate this, a short expiration time is used typically paired with a refresh token
- if you need to invalidate a JWT with password reset, you could include a hash of the user's password in the secret used to hash the jwt
