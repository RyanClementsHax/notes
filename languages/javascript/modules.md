# Modules

- when using raw es modules without some fancy builder like webpack, you need to use file extensions
- you no longer have `__filename` or `__dirname`
    ```ts
    const __filename = fileURLToPath(import.meta.url)
    const __dirname = path.dirname(__filename)
    ```