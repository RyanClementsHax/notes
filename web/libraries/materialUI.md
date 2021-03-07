# Material UI

## [SSR](https://material-ui.com/guides/server-rendering/)
- you need to generate styles on the server side so you don't get a flash of unstyled content
- after doing that, you need to remove the styles on the client side because material UI adds its own styles on the client side and will result in duplicated styles leading to funky problems like modals not showing or another flash of style if the two styles don't match