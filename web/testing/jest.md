# Jest

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
