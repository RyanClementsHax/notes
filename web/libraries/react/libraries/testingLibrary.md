# Testing Library

## What the heck is `act` all about?

- read [this](https://github.com/threepointone/react-act-examples/blob/master/sync.md)
- then read [this](https://kentcdodds.com/blog/fix-the-not-wrapped-in-act-warning)

## [screen.debug() not printing everyting?](https://github.com/testing-library/react-testing-library/issues/503)

- use `screen.debug(undefined, Infinity)` instead

## [Mocking components](https://ericdcobb.medium.com/advanced-react-component-mocks-with-jest-and-react-testing-library-f1ae8838400b)

## Problems with testing some components

- some components may render really funky results and have a hard time working with testing library's api
  - e.g. the select component in the material UI doesn't use a native select component
- [one way is to mock out the component or use special props to disable the "funkiness"](https://newbedev.com/react-testing-library-on-change-for-material-ui-select-component)
