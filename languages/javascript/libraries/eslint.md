# Eslint

## [forbid-unneccessary-backticks](https://github.com/prettier/eslint-config-prettier#forbid-unnecessary-backticks)
```json
{
    // ...
    "rules": {
        // ...
        "quotes": [
            "warn",
            "single",
            { "avoidEscape": true, "allowTemplateLiterals": false }
        ],
        // ...
    }
    // ...
}
```

## Typescript integration
- this is an example `.eslintrc` that integrates with typescript and prettier
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
            // must be last for prettier to work well with eslint
            "prettier"
        ],
        "parserOptions": {
            // this is needed to parse typescript files
            "parser": "@typescript-eslint/parser",
            // this is to support advanced linting features like no-floating-promises
            "project": "./tsconfig.json"
        },
        "rules": {
            "quotes": [
                "warn",
                "single",
                { "avoidEscape": true, "allowTemplateLiterals": false }
            ],
            "no-console": "off",
            "no-empty": "warn",
            // this needs the parserOptions.project to be set to the tsconfig to use for linting
            "@typescript-eslint/no-floating-promises": "error"
        }
    }
    ```
    - the `tsconfig` used for linting needs to include all of the files needed for linting
      - not doing this will cause errors in linting
    - if need be, you can create a separate `tsconfig.eslint.json` that extends the one you already have for your project
        ```json
        {
            "extends": "./tsconfig.json",
            "include": ["src", "*.js", "*.json"]
        }
        ```