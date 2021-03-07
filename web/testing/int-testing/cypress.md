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