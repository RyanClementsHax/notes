# Jest

## Debugging

- [troubleshooting jest](https://jestjs.io/docs/troubleshooting)
- [debugging a single jest test](https://stackoverflow.com/questions/50497785/debug-a-single-test-in-jest)
- [use the vs code extension](https://github.com/jest-community/vscode-jest)

## Linting

- [eslint-plugin-jest](https://www.npmjs.com/package/eslint-plugin-jest)

## Architecture

- [jest architecture page](https://jestjs.io/docs/architecture)

## Testing callbacks

- no good way to assert on callback functions aside from directly accessing them via something like `myMock.mock.calls[0][0]` and then calling the callback function directly

## [clear vs reset](https://www.codegrepper.com/code-examples/javascript/frameworks/angular/jest+mock+reset+vs+clear)

- clear wipes invocations from the current mocks
- reset totally resets them (preferred)

## Extending expect

```ts
// this example adds a matcher that asserts that what was passed into it was a func
// it also takes an optional callback for more assertions

// include this block if you are using typescript
declare global {
  // eslint-disable-next-line @typescript-eslint/no-namespace
  namespace jest {
    interface Expect {
      // eslint-disable-next-line @typescript-eslint/no-explicit-any
      func: <T = (...params: any[]) => any>(cb?: (fn: T) => void) => void;
    }
  }
}

expect.extend({
  func(received, cb) {
    if (typeof received !== "function") {
      return {
        pass: false,
        message: () => "expected received to be a function",
      };
    } else {
      cb?.(received);
      return {
        pass: true,
        message: () => "expected received not to be a function",
      };
    }
  },
});
```

## [Mocking modules](https://remarkablemark.org/blog/2021/07/01/jest-mock-node-modules-create-react-app/)
