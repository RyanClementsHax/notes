# Cypress

- [best practices](https://docs.cypress.io/guides/references/best-practices.html)
- [env vars](https://docs.cypress.io/guides/guides/environment-variables.html#Option-4-env)

## WSL
- out of the box, cannot open the GUI with `npx cypress open`, you need to do [a lot of round about things](https://nickymeuleman.netlify.app/blog/gui-on-wsl2-cypress/) to get a GUI working
- even then, [not everything works](https://github.com/cypress-io/cypress/issues/15251)
- if you have a GUI configured, and `npx cypress run` hangs, it is likely because the `DISPLAY` env var is set to point to the `X-Server`
  - to fix, just reset it when you run the command: `DISPLAY= npx cypress run`

## Debug mode
```bash
DEBUG=cypress:* npx cypress run
```

## Asserting network calls
- use [cy.intercept()](https://docs.cypress.io/api/commands/intercept.html)

## Waiting on responses
- use [cy.wait(@alias)](https://docs.cypress.io/api/commands/wait.html) to wait for responses

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
       - ex: you can generate db reset scripts from entity framework and call them from a cypress task against the database
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