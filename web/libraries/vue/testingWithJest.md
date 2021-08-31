# Testing with Jest

## jest.config.js

- the one provided by `@vue/cli-plugin-unit-jest/presets/typescript-and-babel` is pretty good but needs to be overriden a little when using other libraries like `cypress` and `vee-validate`
- example config with cypress

```js
module.exports = {
    preset: '@vue/cli-plugin-unit-jest/presets/typescript-and-babel',
    // specify setup file if needed
    setupFilesAfterEnv: ['<rootDir>/src/jestSetup.ts'],
    // override the test match to only find tests within src
    testMatch: [
        '<rootDir>/src/**/*.spec.[jt]s?(x)',
        '<rootDir>/src/**/__tests__/*.[jt]s?(x)'
    ],
    // make sure to ignore the cypress folder if there is one
    testPathIgnorePatterns: ['<rootDir>/node_modules', '<rootDir>/cypress']
}
```

- see the [vee-validate testing docs](https://vee-validate.logaretm.com/v3/advanced/testing.html#testing-caveats) for how to configure jest so it doesn't blow up when trying to import vee-validate code
