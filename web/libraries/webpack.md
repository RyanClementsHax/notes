# Webpack

- [module federation](https://webpack.js.org/concepts/module-federation/)
- [prefetch/preloading](https://medium.com/webpack/link-rel-prefetch-preload-in-webpack-51a52358f84c)
  - prefetching is when the browser will fetch resources after the loading has been done
    - good for resources you will likely need soon like a modal
  - preloading is when the browser will fetch resources during the loading because they are needed immediately
    - good for resources needed immediately