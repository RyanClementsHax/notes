# Migrating From Javascript

## The method that I used
1. do your research on how typescript works
    - read the docs (all of them)
    - read best practices with your ecosystem (i.e. what libraries are good, how to integrate typescript into your project, etc)
2. set up strict linting and a strict `tsconfig`
    - this helps you write good typescript until you learn when it is appropriate to break the strict rules
3. get good IDE support for typescript
4. (recommended) set up git hooks that lint/test/type check your code before committing or pushing
5. make sure your project can build, develop, test, and deploy WITH LITTLE TO NO ADDITIONAL FRICTION with the typescript integration even without any typescript files
    - it is so much harder to adopt a new tool when it makes any of these day to day processes harder
6. migrate one file at a time
    - migrate tests for that file at the same time
7. celebrate once the mirgation is done!

## Tips
- I recommend setting up typescript with eslint to help catch a lot of problems
  - this includes eslint for other libraries you might use (e.g. jest and cypress offer eslint plugins)
- it might be tempting to allow "loose" typescript (e.g. `any`) when making the migration, but this doesn't teach you to write good typescript and requires unnecessary relearning and rework
- it is in your best interest to read through the entire documentation and watch a view vidoes on how typescript works
  - this enables you to make the migration with more tools
- really understand that typescript is a STATIC type checker, and provides NO runtime type safety
  - this is harder to digest for someone coming from a background with a language like c# such as myself
  - you will need to bring a separate validation library in to your project in order to gain runtime type safety (preferrably one with good typescript support like [ajv](https://www.npmjs.com/package/ajv))
- do your research on best practices with typescript and best practices with typescript in the ecosystem you are writing it with
  - e.g. when trying to use typescript with cypress, look up at the cypress documentation on how to integrate the two tools together and make best use of the typing cypress provides
  - e.g. when trying to use typescript with jest, look up the jest documentation on how to integrate the two tools together and do your research on what typescript libraries exist out there to help with jest testing with typescript (i recommend seriously looking into [jest-mock-extended](https://www.npmjs.com/package/jest-mock-extended))
- come up with proof of concept repos for how to best migrate your code instead of using your large codebase as the proof of concept
  - this was helpful when I tried to decide how to best express vue components in typescript as there were about 4 different ways to do this
  - isolating the migration to a hello world repo helped me see how the libraries interacted with each other and allowed me to see the pros and cons of the approaches i was considering
- realize that *sometimes* you have to bail out of the type system in order to best express something in typescript
  - in order to get mocking to work in jest with typescript, many libraries use proxies and other methods of stubbing which causes problems when trying to express types thus leading them to use "dirty" typescript hacks like `any` casting
  - avoid these hacks if you can, but do realize there are valid edge cases to use them
- it is in your best interest to learn how modules and namespaces work once you are on your feet with typescript
- typescript support needs to be factored into your choice of libraries now
  - it is very powerful to have a library that provides type safety
  - it is very frustrating to try to coerce type safety into a library that just isn't expressed well in typescript
- realize that you're not going to write perfect typescript in the beginning, but do realize that at least you're not writing javascript lol
  - you will improve over time and can go back to refactor what you did into better practices
- avoid other large refactors at the same time if you can
  - javascript to typescript is a big enough jump
  - it is easy to get overwhelmed in problems if you don't focus on just this one
- identify the parts of your project that are problematic to migrate and don't put them off
  - its ok to postpone migrating this problematic parts until you understand typescript and migrating to typescript better, but it might not give you too much value to put it off
  - you can learn a hell of a lot about typescript by migrating problematic parts
  - getting the problematic parts done quicker can help with team morale
- try to have "reference migrations" for the different "types" of migration patterns
  - every project will ideally have different components and patterns that it reuses across the code base
  - each component/pattern will have its own way of being expressed in typescript
  - doing one migration of a given "type" (component/pattern) instance is usesful as a reference for when others try to migrate other instances of that "type"
  - much like anything with software engineering, the first type implementing a pattern or technology is the most difficult because you probably don't have anything to reference
    - think about how hard it is to write your first unit test from scratch as opposed to adding a unit test to a file that already has the framework set up, mocking implemented, etc
  - for example, consider a simple front end that has components and state management patterns
    - getting the first component migrated is the hard part
    - other component migrations will build off of this first one
    - even though you migrated a bunch of components, you will need to use different patterns for migrating your state management solution
    - getting one example of how to migrate a state management file will serve as the reference migration better than the component migrations will
- there are a collection of hard problems that you have to pay upfront. dont let this scare you because once you solve them once, its downhill from there. here they are
    1. how does typescript work?
    2. how do I onboard my team/coworkers to typescript?
    3. how can my project build with typescript?
    4. how can I integrate linting?
    5. how do I integrate IDE support?
    6. how can I debug my project with typescript?
    7. how can I troubleshoot a typescript project? (i.e. source maps are loaded correctly and errors point to the correct line of code in the typescript file)
    8. how does typescript work with my core framework/library I'm using to develop my project?
    9. how does typescript work with other libraries I use?
    10. how does typescript work with testing?
    11. how does typescript work with my pipeline/deployments?
    12. how do I plan to migrate my project?