# Nextjs With I18n

## Auto redirects

- if someone with a lang preferences lands on your nextjs site, next will automatically redirect the user to the page with their locale ONLY if they requested the root (e.g. `your.domain.com`)
  - i.e. if they requested `your.domain.dom/some/path` they won't get redirected
