# Linting

The following is an example with vue using typescript

## Packages
```json
{
  // ...
  "devDependencies": {
    // ...
    "@typescript-eslint/eslint-plugin": "^4.26.0",
    "@typescript-eslint/parser": "^4.26.0",
    "@vue/cli-plugin-eslint": "~4.5.0",
    "@vue/cli-plugin-typescript": "^4.5.13",
    "eslint": "^6.8.0",
    "eslint-config-prettier": "^8.3.0",
    "eslint-config-typescript": "^3.0.0",
    "eslint-plugin-prettier": "^3.4.0",
    "eslint-plugin-prettier-vue": "^3.0.0",
    "eslint-plugin-vue": "^6.2.2",
    "eslint-plugin-vuejs-accessibility": "^0.6.1",
    "prettier": "^2.3.0",
    // ...
  }
  // ...
}
```

## Example `.eslintrc`
```json
{
  "root": true,
  "env": {
    "node": true
  },
  "extends": [
    // js/ts specific
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "typescript",
    // vue
    "plugin:vue/recommended",
    "plugin:prettier-vue/recommended",
    "plugin:vuejs-accessibility/recommended",
    // cypress
    "plugin:cypress/recommended",
    // must be last for prettier to work well with eslint
    "prettier"
  ],
  "parserOptions": {
    "parser": "@typescript-eslint/parser"
  },
  "rules": {
    "no-console": "warn",
    "no-empty": "warn",
    "vue/no-unused-components": "warn",
    "@typescript-eslint/no-unused-vars": "warn",
    "@typescript-eslint/no-empty-interface": "off",
    "@typescript-eslint/no-namespace": "off",
    "@typescript-eslint/no-explicit-any": "warn",
    "@typescript-eslint/explicit-module-boundary-types": "warn",
    "@typescript-eslint/no-empty-function": "warn"
  }
}
```