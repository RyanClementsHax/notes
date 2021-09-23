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
