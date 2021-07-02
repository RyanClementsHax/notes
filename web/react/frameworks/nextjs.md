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

## Rendering
- by default where the page renders depends on how you access the page
  - if you access it directly, it is rendered on the server
  - if accessed via client side link, it is rendered on the client side
- by default no styles are added on the server
  - this will cause that "flash of unstyled content" problem
  - newer versions of next seem to mitigate this somehow
- [getInitialProps(ctx)](https://nextjs.org/docs/api-reference/data-fetching/getInitialProps)
  - populates the page's props with data
  - good place to put api calls
  - can only be used for page components
  - modifying the rendering function to collect styles during the rendering process to then send down as initial props will solve the "flash of unstyled content" problem ([material ui example](https://github.com/mui-org/material-ui/blob/master/examples/nextjs-with-typescript/pages/_document.tsx))
  - make sure you return serializable data from this method as this is what gets sent over the wire to the front end for rendering
  - from the docs
    > For the initial page load, `getInitialProps` will run on the server only. `getInitialProps` will then run on the client when navigating to a different route via the `next/link` component or by using next/router. However, if `getInitialProps` is used in a custom `_app.js`, and the page being navigated to implements `getServerSideProps`, then `getInitialProps` will run on the server.
- `componentDidMount` does not run on the server
- typically, you want a separate, spacially close server to handle api requests so that your render requests on the next server don't get hung (remember that node is single threaded)


## `lib`
- code in this folder gets imported to pages and can run both on the server and browser
- data fetching logic should also go here because api calls can happen either on the client or the server depending on how the page is accessed

## `server`
- code in this folder only gets run on the server

## Tools
- [next-code-elimination](https://next-code-elimination.vercel.app/)
  - shows you the output of the SSR
- [eslint-config](https://github.com/ijsto/eslint-config)

## [Reducing data sent to client](https://www.smashingmagazine.com/2021/05/reduce-data-sent-client-nextjs/)
- this article explains a few ways to do it
- essentially
  - grab only the data you need
  - prevent aggregation on the client