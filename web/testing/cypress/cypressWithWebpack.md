# Cypress With Webpack

## Webpack config
- you can configure the way webpack is used under the hood by hooking into the `file:preprocessor` event
1. install `@cypress/webpack-preprocessor` and `webpack`
  - [as of 3/27/20 @cypress/webpack-preprocessor doesn't work well with webpack 5.x](https://github.com/cypress-io/cypress/issues/15447)
2. create a webpack config for cypress (example config below that adds an alias and loads typescript files)
    ```js
    const path = require('path')

    module.exports = {
      webpackOptions: {
        resolve: {
          extensions: ['.ts', '.js'],
          alias: {
            '@cypress': path.resolve(__dirname, './cypress')
          }
        },
        module: {
          rules: [
            {
              test: /\.ts$/,
              exclude: [/node_modules/],
              use: [
                {
                  loader: 'ts-loader'
                }
              ]
            }
          ]
        }
      }
    }
    ```
3. within `cypress/plugins/index.js` add the following configuration
    ```js
    /// <reference types="cypress" />
    // ***********************************************************
    // This example plugins/index.js can be used to load plugins
    //
    // You can change the location of this file or turn off loading
    // the plugins file with the 'pluginsFile' configuration option.
    //
    // You can read more here:
    // https://on.cypress.io/plugins-guide
    // ***********************************************************

    // This function is called when a project is opened or re-opened (e.g. due to
    // the project's config changing)
    const webpackPreprocessor = require('@cypress/webpack-preprocessor')

    module.exports = on => {
      on(
        'file:preprocessor',
        webpackPreprocessor(require('../path/to/webpack.cypress.config'))
      )
    }
    ```