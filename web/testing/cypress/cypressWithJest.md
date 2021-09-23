# Cypress With Jest

## Eslint

- need to setup config so that the cypress rules only apply to where you store your files

## Eslint config

```ts
const baseConfigs = [
  // js/ts specific
  'eslint:recommended',
  'plugin:@typescript-eslint/recommended',
  'typescript',
  // other configs
  // ...
]
// must be last for prettier to work well with eslint
const prettierConfigs = ['prettier']

// applies pretter as the last config so it doesn't break formatting
const withPrettier = configs => [...configs, ...prettierConfigs]
// adds defaults to whatever configs are given
const withDefaults = (configs = []) =>
  withPrettier([...baseConfigs, ...configs])

module.exports = {
  root: true,
  env: {
    node: true
  },
  extends: withDefaults(),
  parserOptions: {
    parser: '@typescript-eslint/parser',
    // it is recommended to have a different tsconfig for eslint
    project: './tsconfig.eslint.json',
  },
  rules: {
      // my rules
  },
  overrides: [
    {
      // jest specific files
      files: [
        'src/**/__tests__/*.{j,t}s?(x)',
        'src/**/*.spec.{j,t}s?(x)',
        'src/jestSetup.ts'
      ],
      // if we didn't use withDefaults to include all of the configs, we would just clobber any configs already specified
      extends: withDefaults([
        // jest specific configs
        'plugin:jest/recommended',
        'plugin:jest/style',
        'plugin:jest-dom/recommended',
        'plugin:testing-library/vue'
      ]),
      env: {
        jest: true
      }
    },
    {
      // cypress specific files
      files: ['cypress/**/*.ts', 'cypress/**/*.js'],
      extends: withDefaults(['plugin:cypress/recommended']),
      rules: {
        '@typescript-eslint/no-namespace': 'off'
      }
    },
    {
      files: ['**/*.json'],
      rules: {
        quotes: 'off'
      }
    }
  ]
}
```
