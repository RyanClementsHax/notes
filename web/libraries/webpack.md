# Webpack

- [module federation](https://webpack.js.org/concepts/module-federation/)
- [prefetch/preloading](https://medium.com/webpack/link-rel-prefetch-preload-in-webpack-51a52358f84c)
  - prefetching is when the browser will fetch resources after the loading has been done
    - good for resources you will likely need soon like a modal
  - preloading is when the browser will fetch resources during the loading because they are needed immediately
    - good for resources needed immediately

## Bundlesize limits
- this is [configurable](https://webpack.js.org/configuration/performance/#performancemaxentrypointsize)
- if you exceed this, you will get [warnings in the console](https://stackoverflow.com/questions/49348365/webpack-4-size-exceeds-the-recommended-limit-244-kib)
- it is recommended to follow them

## [devtool](https://webpack.js.org/configuration/devtool/)
- try not to ship this enabled in production
```js
devtool: process.env.NODE_ENV === 'development' ? 'source-map' : undefined
```