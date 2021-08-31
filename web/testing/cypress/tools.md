# Tools

## Conditional tests

- [not a good idea and HIGHLY not recommended by cypress](https://docs.cypress.io/guides/core-concepts/conditional-testing) due to the async nature of dom updates
- conditionally doing something based on a class

    ```js
    cy.get('#navbar').within(() => {
        cy.get('#nav-collapse')
            .first()
            .invoke('attr', 'class')
            .then(classes => {
                if (!classes.includes('show')) {
                    cy.get('#navbar-toggler').click()
                }
                cy.get('.nav-link')
                    .contains('my page')
                    .click()
            })
    })
    ```

## Fill form without having to wait for slow typing

- `cy.type('some string')` will take 10ms per character minimum which can feel like forever if you have a lot of forms
- you can side step this if you use [cypress-fill-command](https://www.npmjs.com/package/cypress-fill-command) paired with a simple helper function
- the keys of the object passed in are the selectors of the fields to enter their respective values into

```ts
Cypress.Commands.add('fillForm', (input: { [key: string]: string }) => {
    Object.keys(input).forEach(key => {
        if (input[key] !== undefined) {
            cy.get(key).fill(input[key])
        }
    })
})

cy.fillForm({
    '#username': 'user name to enter',
    '#password': 'password to enter'
})
```

## Waiting for idle network

- this can be helpful if waiting for all assets to download before performing some action
- the following is an adapted version of [this](https://github.com/cypress-io/cypress/issues/1773#issuecomment-813812612) and uses [cypress-wait-until](https://github.com/NoriSte/cypress-wait-until) under the hood

```ts
Cypress.Commands.add('waitForIdleNetwork', () => {
    const idleTimesInit = 3
    let idleTimes = idleTimesInit
    let resourcesLengthPrevious: number

    cy.window().then(win =>
        cy.waitUntil(() => {
            const resourcesLoaded = win.performance.getEntriesByType('resource')

            if (resourcesLoaded.length === resourcesLengthPrevious) {
                idleTimes--
            } else {
                idleTimes = idleTimesInit
                resourcesLengthPrevious = resourcesLoaded.length
            }

            return !idleTimes
        })
    )
})
```

## Assertions while a network request is pending

- this is a little hacky because there isn't a "blessed" way of doing this in cypress yet
- we need some help from [cypress-wait-until](https://github.com/NoriSte/cypress-wait-until#readme)

```ts
type ResolveCb = (thenableOrResult?: unknown) => void

const suspendOptions =
    (cb: (resolve: ResolveCb) => void) =>
        (req: CyHttpMessages.IncomingHttpRequest): Bluebird<unknown> =>
            new Cypress.Promise(resolve => {
            cb(resolve)
        }).then(() => req.reply())
  

it('does something while request pending', () => {
    let resolveReq: ResolveCb
    cy
        .intercept(
            'GET',
            `${Cypress.env('apiBaseUrl')}/some-endpoint`,
            suspendOptions(resolve => (resolveReq = resolve))
        )
    
    // do action that fires the request
    // do assertions while request is pending

    // we wait until the resolveReq callback has been set
    // sometimes we run into race conditions where it isn't set
    cy
      .waitUntil(() => resolveReq)
      .then(() => resolveReq())

    // additional assertions after the request resolves
})
```

## Asserting url equal to something

- somtimes we want to match the current url without having to worry about the base url or match using a regex

```ts
Cypress.Commands.add('urlEq', (path: string | RegExp) => {
    return path instanceof RegExp
        ? cy.url().should(
            'match',
            new RegExp(
                // here we can interpolate the regex to have some base url to keep the api for this function cleaner
                `${Cypress.config().baseUrl}/${path
                    .toString()
                    /* remove some regex syntax that prevents it from being interpolated into another regex */
                    .replace(/(^\/|\/$|\/([igm]+)$)/g, '')}`
            )
        )
        : cy.url().should('equal', `${Cypress.config().baseUrl}/${path}`)
})

cy.urlEq('some/route')
cy.urlEq(/ab+c/)
```
