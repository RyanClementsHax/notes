# Modules

- by default, this has strict mode enabled
- any file containing a top level `import` or `export` is considered a module
- a file without any top level import or export declarations is treated as a script whose contents are available in the global scope (and therefore to modules as well)
- modules are executed within their own scope, not in the global scope
  - this means that variables, functions, classes, etc. declared in a module are not visible outside the module unless they are explicitly exported using one of the export forms
  - conversely, to consume a variable, function, class, interface, etc. exported from a different module, it has to be imported using one of the import forms
- when using raw es modules without some fancy builder like webpack, you need to use file extensions
- you no longer have `__filename` or `__dirname`

    ```ts
    const __filename = fileURLToPath(import.meta.url)
    const __dirname = path.dirname(__filename)
    ```
