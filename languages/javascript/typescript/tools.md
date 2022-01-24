# Tools

## Refining T|undefined to T in filter

```ts
function notUndefined<T>(x: T | undefined): x is T {
  return x !== undefined;
}
relevantEntities = ids.map((id) => entities.get(id)).filter(notUndefined);
```

or anonymously

```ts
relevantEntities = ids
  .map((id) => entities.get(id))
  .filter((x): x is Entity => x !== undefined);
```

## [Disabling a button](https://stackoverflow.com/questions/31635975/how-to-enble-disable-a-button-in-typescript-1-5/31640983)

- you need to cast the html element before accessing element specific properties

```ts
(<HTMLInputElement>document.getElementById("btnExcel")).disabled = true;
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
      return foo<T>(e);
    }
  }

  type FooInternalType<T> = ReturnType<Wrapper<T>["wrapped"]>;
  type Y = FooInternalType<number>;
  // Y === InternalType<number>
  ```

## Debugging

- [here](https://gist.github.com/cecilemuller/2963155d0f249c1544289b78a1cdd695#gistcomment-3376582) is a launch configuration for debugging `ts-node` in VSCode

  ```json
  {
    "name": "Launch TypeScript",
    "type": "node",
    "request": "launch",
    "runtimeArgs": ["--nolazy", "-r", "ts-node/register"],
    "args": ["./your_entrypoint_file.ts", "--transpile-only"],
    "cwd": "${workspaceFolder}",
    "internalConsoleOptions": "openOnSessionStart",
    "skipFiles": ["<node_internals>/**", "node_modules/**"],
    "env": {
      "TS_NODE_PROJECT": "${workspaceFolder}/tsconfig.json"
    }
  }
  ```

  - the only problem with this is that I haven't found where VSCode puts the logs produced by running the application in this debug configuration

## [Making a single property optional](https://stackoverflow.com/questions/43159887/make-a-single-property-optional-in-typescript)

```ts
type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>;
type PartialBy<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>;

interface Person {
  name: string;
  hometown: string;
  nickname: string;
}

type MakePersonInput = PartialBy<Person, "nickname">;
```

## Evenly distributed array sampling

```ts
const forEvenlyDistributedSampleOf =
  (num: number): (<T>(value: T, index: number, array: T[]) => boolean) =>
  (_, i, arr) => {
    // handle the case that the array is less than the sample we want to collect
    if (arr.length <= num) return true;
    // always include the first and last elements
    if (i === 0 || i === arr.length - 1) return true;
    // get all elements in between that lie on the interval
    const interval = Math.floor(arr.length / (num - 2));
    if (i % interval === 0) return true;
    // ignore all other elements
    return false;
  };

data.filter(forEvenlyDistributedSampleOf(10));
```

## [Get all required fields of object](https://stackoverflow.com/questions/52984808/is-there-a-way-to-get-all-required-properties-of-a-typescript-object)

```ts
type RequiredKeys<T> = {
  [K in keyof T]-?: {} extends Pick<T, K> ? never : K;
}[keyof T];
```

## [Get all optional fields of object](https://stackoverflow.com/questions/52984808/is-there-a-way-to-get-all-required-properties-of-a-typescript-object)

```ts
type OptionalKeys<T> = {
  [K in keyof T]-?: {} extends Pick<T, K> ? K : never;
}[keyof T];
```

## [Optional union](https://github.com/microsoft/TypeScript/issues/35263#issuecomment-995108422)

```ts
type OptionalUnion<T1, T2> = {
  [P in keyof Omit<T1 & T2, keyof (T1 | T2)>]?: (T1 & T2)[P];
} & (T1 | T2);

interface Common {
  name: string;
  age: number;
}

interface A extends Common {
  address: string;
}

interface B extends Common {
  city: string;
}

type MyType = OptionalUnion<A, B>;

const func = (arg?: MyType) => {
  // (property) B.city?: string | undefined
  const res = arg?.city;
};
```

## [Exclusive OR](https://github.com/Microsoft/TypeScript/issues/14094)

- if you think you need this, consider function overloading as an alternative

## [Make a single property optional](https://stackoverflow.com/questions/43159887/make-a-single-property-optional-in-typescript)

```ts
type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>;
type PartialBy<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>;

interface Person {
  name: string;
  hometown: string;
  nickname: string;
}

type MakePersonInput = PartialBy<Person, "nickname">;
```
