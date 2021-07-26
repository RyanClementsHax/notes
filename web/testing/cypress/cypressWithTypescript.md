# Cypress With Typescript

- cypress works well with typescript, although you will need to add some additional configuration
1. add `"cypress"` in your `tsconfig.json`'s `"types"` field
2. install `ts-loader`
3. make sure you setup webpack config for cypress as seen in [webpack config](#webpack-config)
4. modify the cypress webpack config to include the `ts-loader`
    ```js
    module.exports = {
      webpackOptions: {
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

## Typing custom commands
- there are plenty of ways to do this, but adding a custom command boils down to three steps
  1. declare the function
  2. add the function type to the `Chainable` type in the `Cypress` namespace
  3. register the command
- here is one way to do it
  1. create an object full of the commands you want to register
      ```ts
      const commands = {
        func1(arg1: number): string { /* ... */ }
        func2(arg1: SomeType): CustomType { /* ... */ }
      }
      ```
  2. add the types to the namespace
      ```ts
      type cmdsType = typeof commands // can't extend typeof commands directly

      declare global {
        namespace Cypress {
          interface Chainable extends cmdsType {}
        }
      }
      ```
  3. register the functions
      ```ts
      for (const [k, v] of Object.entries(commands)) {
        Cypress.Commands.add(k, v)
      }
      ```

## Typing custom assertions
1. define the assertion
    ```ts
    chai.use((_chai: Chai.ChaiStatic) => {
      _chai.Assertion.addMethod('inViewport', function () {
        const subject = this._obj

        const windowHeight = Cypress.config().viewportHeight
        const bottomOfCurrentViewport = windowHeight || 0
        const rect = subject[0].getBoundingClientRect()

        this.assert(
          (rect.top > 0 && rect.top < bottomOfCurrentViewport) ||
            (rect.bottom > 0 && rect.bottom < bottomOfCurrentViewport),
          'expected #{this} to be in viewport',
          'expected #{this} to not be in viewport',
          subject
        )
      })
    })
    ```
2. register the type for the `should` syntax
    ```ts
    declare global {
      namespace Cypress {
        interface Chainer<Subject> {
          (chainer: 'be.inViewport'): Chainable<Subject>
          (chainer: 'not.be.inViewport'): Chainable<Subject>
        }
      }
    }
    ```