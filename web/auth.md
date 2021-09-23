# Auth

## Passwords

- salting passwords is helpful because you don't know where the salt ends and the password begins

## Cookies

- http only cookies can't be accessed with document.cookie or any other method accessible by the client
- safari doesn't allow localhost cookies
- http only cookies aren't available to browser extensions
- https redirects don't solve the problem of http hijacks because the http call is already made
