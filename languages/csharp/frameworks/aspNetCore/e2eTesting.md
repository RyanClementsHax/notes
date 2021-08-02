# E2E Testing

- setting up your project to handle integration testing from the front end can be very valuable, but tricky if you don't know how to handle database resetting or service stubbing
- but there are ways to accomplish this

## E2E api

- you can create a separate startup project just to handle all of the dependency stubbing
  - just make sure ports don't conflict
  - you can also hook this up with docker compose to spin up everything easily
- would contain all the endpoints to do things like reset the db, seed data, or stub service responses
- for service stubbing, just point the api under test to the e2e api via environment variables instead of using the actual services

## Database resetting/seeding

- if using entity framework or other library/framework to handle migrations, you likely can have that tool spit out db reset sql for your tests to use on the database
  - it is however annoying to remember to update these whenever the db model changes
- could add separate endpoints just for testing, but it is a security concern
  - what happens if you accidentally expose these endpoints in production?
- you could use the e2e api mentioned before which gives you the most flexibility and avoids many of the security concerns of adding endpoints to the api under test
  - you could just take whatever endpoints you wanted to add on the api under test and add them to the e2e api
