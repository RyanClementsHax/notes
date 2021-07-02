# Tools

## Refining T|undefined to T in filter
```ts
function notUndefined<T>(x: T | undefined): x is T {
    return x !== undefined;
}
relevantEntities = ids.map(id => entities.get(id)).filter(notUndefined);
```
or anonymously
```ts
relevantEntities = ids.map(id => entities.get(id)).filter((x): x is Entity => x !== undefined);
```

## [Disabling a button](https://stackoverflow.com/questions/31635975/how-to-enble-disable-a-button-in-typescript-1-5/31640983)
- you need to cast the html element before accessing element specific properties
```ts
(<HTMLInputElement> document.getElementById("btnExcel")).disabled = true;
```

## Class type
- [right now](https://github.com/microsoft/TypeScript/issues/17572), there is no good way to represent a `Class` so to speak in typescript

## Generic return type
- [right now](https://github.com/microsoft/TypeScript/issues/37181), there is no good way to represent something like `ReturnType<typeof genericFunction<T>>` in typescript
- [here](https://stackoverflow.com/a/64919133/16236573) is a hack
    ```ts
    // foo is an imported function that I have no control over
    function foo<T>(e: T): InternalType<T> {
        return e;
    }

    class Wrapper<T> {
        // wrapped has no explicit return type so we can infer it
        wrapped(e: T) {
            return foo<T>(e)
        }
    }

    type FooInternalType<T> = ReturnType<Wrapper<T>['wrapped']>
    type Y = FooInternalType<number>
    // Y === InternalType<number>
    ```