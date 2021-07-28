# Typescript

## Type fundamentals
- typescript uses structural typing, not nominal typing
    ```ts
    class Car {
        drive() {
            // hit the gas
        }
    }
    class Golfer {
        drive() {
            // hit the ball far
        }
    }

    // No error because the two classes are structurally equivallent
    let w: Car = new Golfer();
    ```
- uses type erasure, so there is no reification of types at runtime
- it does not change the way javascript works under the hood
  - any javascript written or compiled to is guaranteed to work the same _always_
  - type annotations never change the runtime behavior of your program
- has type inference

## [Types vs interfaces](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces)

### Types
- types should be seen as "macros" for type definitions
- cannot participate in declaration merging
- prior to 4.2, type alias names _may_ appear in error messages
- can rename primitives
- useful for defining generic unions

### Interfaces
- interfaces are more easily extendable
- can participate in declaration merging
  - i.e. you can easily add more to the same interface
- always named in error messages
- can only be used to declare the names of an object, not rename primitives
- typescript recommends to use interfaces until you need types
- these compile faster than types

## Type assertions
- used **only** for static type checking purposes
- think of them as casts

## [Never](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#the-never-type)
- this is assignable to every type
- no type is assignable to never
- handly for exhaustive checks
- can be used as a return type to communicate that the function literally never returns (e.g. throws an exception always or terminates program)

## [Functions](https://www.typescriptlang.org/docs/handbook/2/functions.html)
- all parameters must be named
- the function type `(string) => void` means a function with a parameter named string of type `any`
- avoid the `Function` type and opt for the `() => void` if you need to accept an arbitrary function as it is safer

### [Call signatures](https://www.typescriptlang.org/docs/handbook/2/functions.html#call-signatures)
- allows you to declare on a type that it can be called
    ```ts
    type DescribableFunction = {
        description: string;
        (someArg: number): boolean;
    };
    function doSomething(fn: DescribableFunction) {
        console.log(fn.description + " returned " + fn(6));
    }
    ```

### [Construct signatures](https://www.typescriptlang.org/docs/handbook/2/functions.html#construct-signatures)
- allows you to define `new` on a type
    ```ts
    type SomeConstructor = {
        new (s: string): SomeObject;
    };

    function fn(ctor: SomeConstructor) {
        return new ctor("hello");
    }
    ```

## `any` vs `unknown`
- `unknown` represents any value, but is the safer type because it isn't legal to do anything with it

## `as const`
- allows the type system to take values more literally instead of wider types
- think of it as "locking" type definitions
- e.g. `[8, 5]` is seen as a `number[]` which can have 0 or more numbers whereas `[8, 5] as const` is seen as an array of literally the numbers `8` and `5`

## Objects

## [Indexing signatures](https://www.typescriptlang.org/docs/handbook/2/objects.html#index-signatures)
```ts
interface StringArray {
  [index: number]: string;
}
```
- It is possible to support both types of indexers, but the type returned from a numeric indexer must be a subtype of the type returned from the string indexer. This is because when indexing with a `number`, JavaScript will actually convert that to a `string` before indexing into an object. That means that indexing with `100` (a `number`) is the same thing as indexing with `"100"` (a `string`), so the two need to be consistent.

## Extension
- possible for one interface to extend from multiple interfaces

## Intersection types
```ts
interface Colorful {
  color: string;
}
interface Circle {
  radius: number;
}

type ColorfulCircle = Colorful & Circle;
```
- this merges the `Circle` and `Colorful` interfaces into one type alias

## [Extract](https://www.typescriptlang.org/docs/handbook/utility-types.html#extracttype-union)
- constructs a type by extracting from `Type` all union members that are assignable to `Union`
- useful for expressing a type as a subset of a union

## [Wiki](https://github.com/microsoft/TypeScript/wiki)
- [Performance](https://github.com/microsoft/TypeScript/wiki/Performance)