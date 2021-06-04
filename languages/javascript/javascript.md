# Javascript

## Turn based processing
- this is how many event driven systems like web browsers and UI frameworks are written
- Javascript is single threaded so all threads of execution need to share the same thread using "turns"
- law of turns: Never wait. Never block. Finish fast.
- because turn based processing unwinds the stack after every turn and there are no blocks, exceptions from IO can't be "thrown"
  - this is because the stack trace is list
  - this is a big reason why promises are used

## class extends syntax
- this just compiles down to higher order components
- [ref](https://blog.sessionstack.com/how-javascript-works-the-internals-of-classes-and-inheritance-transpiling-in-babel-and-113612cdc220)

## Modules
- any file containing a top level `import` or `export` is considered a module
- a file without any top level import or export declarations is treated as a script whose contents are available in the global scope (and therefore to modules as well)
- modules are executed within their own scope, not in the global scope
  - this means that variables, functions, classes, etc. declared in a module are not visible outside the module unless they are explicitly exported using one of the export forms
  - conversely, to consume a variable, function, class, interface, etc. exported from a different module, it has to be imported using one of the import forms

## [WeakMap](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)
- not supported by all browsers and CANNOT be polyfilled
- normal maps will hold on to strong references of objects thus preventing them from being garbage collected
- this map holds a weak reference allowing for the map's hold on the object to not be counted towards garbage collection