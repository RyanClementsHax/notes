# Cypress

## WSL
- out of the box, cannot open the GUI with `npx cypress open`, you need to do [a lot of round about things](https://nickymeuleman.netlify.app/blog/gui-on-wsl2-cypress/) to get a GUI working
- even then, [not everything works](https://github.com/cypress-io/cypress/issues/15251)
- if you have a GUI configured, and `npx cypress run` hangs, it is likely because the `DISPLAY` env var is set to point to the `X-Server`
  - to fix, just reset it when you run the command: `DISPLAY= npx cypress run`

## Debug mode
```bash
DEBUG=cypress:* npx cypress run
```