# Javascript

## Turn based processing

- this is how many event driven systems like web browsers and UI frameworks are written
- Javascript is single threaded so all threads of execution need to share the same thread using "turns"
- law of turns: Never wait. Never block. Finish fast.
- because turn based processing unwinds the stack after every turn and there are no blocks, exceptions from IO can't be "thrown"
  - this is because the stack trace is list
  - this is a big reason why promises are used

## [class extends syntax](https://blog.sessionstack.com/how-javascript-works-the-internals-of-classes-and-inheritance-transpiling-in-babel-and-113612cdc220)

- this just compiles down to higher order components

## [WeakMap](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)

- not supported by all browsers and CANNOT be polyfilled
- normal maps will hold on to strong references of objects thus preventing them from being garbage collected
- this map holds a weak reference allowing for the map's hold on the object to not be counted towards garbage collection

## Logging

- `console.log` is synchronous so consider using a different logging solution for something in a production app

## Errors

- because you can throw anything in javascript, not everything that can be caught has a stack trace so make sure you wrap whatever you want to throw in an `Error` if it doesn't already have a stack trace
- uncaught errors will crash node
- uncaught promise rejections currently don't crash node, but will eventually in future versions

## [Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

- `Symbol`s are often used to add unique property keys to an object that won’t collide with keys any other code might add to the object, and which are hidden from any mechanisms other code will typically use to access the object
- every `Symbol()` call is guaranteed to return a unique `Symbol`
- every `Symbol.for("key")` call will always return the same `Symbol` for a given value of `"key"`
- when `Symbol.for("key")` is called, if a `Symbol` with the given key can be found in the global `Symbol` registry, that `Symbol` is returned. otherwise, a new `Symbol` is created, added to the global `Symbol` registry under the given key, and returned.
