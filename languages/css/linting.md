# Linting

- stylelint is a common tool (prob the industry standard) for linting css and other variants
- has a vs code plugin

## Example

```json
// package.json
{
  // ...
  "devDependencies": {
    // ...
    "prettier": "^2.3.2",
    "stylelint": "^13.13.1",
    "stylelint-a11y": "^1.2.3",
    "stylelint-config-prettier": "^8.0.2",
    "stylelint-config-rational-order": "^0.1.2",
    "stylelint-config-recommended-scss": "^4.3.0",
    "stylelint-config-standard": "^22.0.0",
    "stylelint-declaration-block-no-ignored-properties": "^2.4.0",
    "stylelint-declaration-strict-value": "^1.7.12",
    "stylelint-no-unsupported-browser-features": "^5.0.1",
    "stylelint-order": "^4.1.0",
    "stylelint-prettier": "^1.2.0",
    "stylelint-scss": "^3.20.1",
    // ...
  }
  // ...
}
```

```js
// stylelint.config.js
module.exports = {
  extends: [
    'stylelint-config-standard',
    'stylelint-config-recommended-scss',
    'stylelint-config-rational-order',
    'stylelint-a11y/recommended',
    'stylelint-declaration-block-no-ignored-properties',
    'stylelint-prettier/recommended'
  ],
  plugins: [
    'stylelint-declaration-block-no-ignored-properties',
    'stylelint-declaration-strict-value',
    'stylelint-no-unsupported-browser-features'
  ],
  rules: {
    'declaration-block-trailing-semicolon': null,
    'selector-pseudo-element-no-unknown': [
      true,
      {
        // vue specific selector
        ignorePseudoElements: ['v-deep']
      }
    ],
    'a11y/media-prefers-reduced-motion': null,
    'plugin/declaration-block-no-ignored-properties': true,
    'scale-unlimited/declaration-strict-value': [
      ['/color$/'],
      {
        disableFix: true,
        ignoreKeywords: ['initial']
      }
    ],
    'plugin/no-unsupported-browser-features': [
      true,
      {
        severity: 'warning'
      }
    ]
  }
}
```

## [Usage with webpack](https://webpack.js.org/plugins/stylelint-webpack-plugin/)

## [stylelint-no-unsupported-browser-features](https://github.com/ismay/stylelint-no-unsupported-browser-features)

- this is a stylelint plugin that uses your [.browserslistrc](https://github.com/browserslist/browserslist) to assert that you are only using css that is compatible with the browsers that you target
- you also end up learning a lot about browser compatibility in the process too!
- tip: use `npx browserslist --config=".browserslistrc"` to list out the browsers that your `.browserslistrc` file specifies

## Verbose

- use `--formatter verbose` for verbose output
