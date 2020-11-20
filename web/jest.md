---
layout: page
title: Jest
permalink: /web/jest
---

## Global
- When mocking out anything in the global scope, you mock it out on the `global` object
    ```js
    global.window = {
      config: {
        foo: 'bar'
      }
    };
    ```
- It should be noted that `global.window === global` when testing

## Timers

  - use `jest.useFakeTimers();`
  ```js
  describe('something with an interval', () => {

    beforeEach(() => {
      jest.useFakeTimers();
    });

    it('sets an interval that does something', () => {
      jest.runOnlyPendingTimers();

      // assertions

      jest.runOnlyPendingTimers();

      // more assertions
    });
  });
  ```

## Mocking imports
```js
// needs to be at the top of the file, else everything blows up
const mockImport = (importName, mock) => {
  jest.mock(importName, () => mock);
};
```

## Matchers
- the `expect.fail('msg')` doesn't seem to exist, so below is a way to make it
```js
expect.extend({
  fail: (_, message) => ({
    pass: false,
    message: () => message,
  })
});
```
