# Cypress

- [best practices](https://docs.cypress.io/guides/references/best-practices.html)
- [env vars](https://docs.cypress.io/guides/guides/environment-variables.html#Option-4-env)
- should make note that when using e2e testing, it is difficult to diagnose where errors lie so you should use multiple testing techniques to ensure functionality

## WSL

- out of the box, cannot open the GUI with `npx cypress open`, you need to do [a lot of round about things](https://nickymeuleman.netlify.app/blog/gui-on-wsl2-cypress/) to get a GUI working
- even then, [not everything works](https://github.com/cypress-io/cypress/issues/15251)
- if you have a GUI configured, and `npx cypress run` hangs, it is likely because the `DISPLAY` env var is set to point to the `X-Server`
  - to fix, just reset it when you run the command: `DISPLAY= npx cypress run`
- check out [this](https://scottspence.com/2021/01/05/use-chrome-in-ubuntu-wsl/) if you want to run chrome in ubuntu on wsl

## Debug mode

```bash
DEBUG=cypress:* npx cypress run
```

## Asserting network calls

- use [cy.intercept()](https://docs.cypress.io/api/commands/intercept.html)
- the placement of this relative to other code can introduce race conditions
  - be sure to place this earlier in the chain of commands so that cypress can actually intercept the request being called as a result of some action

## Waiting on responses

- use [cy.wait(@alias)](https://docs.cypress.io/api/commands/wait.html) to wait for responses

## Controlling when a response is resolved

- there isn't a way to do this out of the box yet, but [here](https://github.com/cypress-io/cypress/issues/3514) is a workaround

## Controlling dependencies during tests

- you have a few options

1. stub out network calls
   - pros
     - no need to set up dependencies to test front end
   - cons
     - you need to make sure that the responses match the contract of what the dependency actually returns
2. call dependencies directly to do things like resetting databases or seeding data
   - pros
     - you can ensure you are using the actual dependency within the testing
     - works well if your dependency code (like data access layer code) in the same project as your front end
     - some dependencies have facilities to do this
       - e.g. you can generate db reset scripts from entity framework and call them from a cypress task against the database
   - cons
     - potentially need to reimplement logic or to implement clients if your dependency exists in a separate repo, don't have control over it, is written in a different language, no premade client already exists in a language callable within cypress, etc
     - need to keep this code up to date with the services they call/represent
3. create special interfaces on your dependencies for testing
   - pros
     - you use the actual service
     - can implement any special function you need
   - cons
     - potential MAJOR security concern if one of the interfaces you create does something like reset a database
     - need access to the service
4. implement a separate service with access to the dependencies you want to control
   - pros
     - you use the actual service
     - total control over dependencies
     - less worry about security concers if you never deploy it or package it
     - can live in a separate repo (sometimes cannot be the case like if you need to reset an entity framework db and thus need access to the database project and thus needs to be added as a separate project or solution within the same repo)
   - cons
     - need to implement and maintain a separate service
     - potentially need access to the repo

## Component visibility

- by default, there is no `.should('not.be.visible')` or `.should('be.visible')` assertion
- [you have to write your own](https://github.com/cypress-io/cypress/issues/877)

## [Failed to deserialize the V8 snapshot blob](https://github.com/cypress-io/cypress/issues/5440)

- this happens sometimes when trying to start cypress
- this is due to a corrupted cache
- the quick fix (and seemingly the one that the cypress staff recommends) is to just delete the cache and reinstall by running `npx cypress install --force`

## Cypress is "too fast"

- sometimes Cypress starts executing commands before your app is ready
- this happened to me when async components weren't finished downloading and cypress was chugging along like it was totally ready leading to silent errors
- [this](https://www.cypress.io/blog/2018/02/05/when-can-the-test-start/) article explains more
- there are a few solutions
  - hook into the dom apis to know when certain handlers are set up
  - set up visual queues that cypress can wait on like loading indicators
  - wait on the network being idle if waiting on assets being downloaded
    - [this](https://github.com/cypress-io/cypress/issues/1773#issuecomment-813812612) is a pretty decent example

## Misc

- it seems that on slower or overloaded dev machines, the cypress timeouts in the UI are sensitive to the window being visible
  - perhaps the windows os scheduler gives lower priority to windows that aren't visible, thus causing timeouts to happen
