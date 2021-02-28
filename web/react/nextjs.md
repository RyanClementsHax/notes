# Next.js

## Project structure
├── components                  # React components
├── lib                         # Code available on both client and server
├── pages                       # Pages
├── server                      # Server code
├── public                      # Static resources
├── package.json                # List of packages and scripts

## Document
- wraps all pages
- need to use `<Head/>`, `Html`, `Main`, and `NextScript` must be returned in the render method for the pages of Next.js to render properly
- `<Html lang="en">` helps search engine bots classify the pages to be in English
- rendered on the serve only
- `onClick` and `componentDidMount` are undefined on the server