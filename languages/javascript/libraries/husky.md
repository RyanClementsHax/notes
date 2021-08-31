# [Husky](https://www.npmjs.com/package/husky)

- this is a library that manages githooks for you
- super useful for running linting/formatting/tests before commiting or pushing
- commonly paired with [lint-staged](https://www.npmjs.com/package/lint-staged)

## Example

```json
// package.json
{
  "scripts": {
    // some commands that can be run by this setup
    "lint": "vue-cli-service lint",
    "lint:styles": "stylelint --fix",
    "type-check": "tsc --pretty --noEmit",
    "format": "prettier --write",
    // gets run automatically when installing node modules and installs husky after installing dependencies
    "prepare": "husky install"
  },
  // ...
  "devDependencies": {
    // ...
    "husky": "^7.0.1",
    "lint-staged": "^11.1.1",
    // ...
  }
  // ...
}
```

```json
// .lintstagedrc.json
{
  // lint-staged will pass the files that match this pattern to these commands for the commands to run against them
  "*.@(ts|js|json)|*.*rc!.browserslistrc": ["yarn lint", "yarn format"],
  // same here too
  "*.@(css|scss)": ["yarn lint:styles", "yarn format"]
}
```

```sh
# .husky/pre-commit

#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

yarn lint-staged
```

```sh
# .husky/pre-push

#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

yarn type-check
```
