# SCSS

## Using
- seems to work as long as you install `node-sass` and `sass-loader`
- put `lang=scss` on your style tag

## Global scss vars
- the only way to do this is declare whatever you want to make global in a separate file, then include it in every scss file you want it
- to automate this, you can add some loader options to insert these import statements automatically by adding the following to your `vue.config.js` file
    ```js
    module.exports = {
        css: {
            loaderOptions: {
                sass: {
                    additionalData: `
                        @import "@/yourStyleFileName";
                    `
                }
            }
        }
    }
    ```
- this is good for overriding bootstrap defaults and distributing application specific scss vars/mixins/etc