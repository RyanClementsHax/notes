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