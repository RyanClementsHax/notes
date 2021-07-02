# Jest With Typescript

## Mocking
- there are a myriad of mocking solutions out there

### sinon
- does support typescript, but sometimes needs helper functions to provide better typescript support
  - [one example](https://spin.atomicobject.com/2018/06/13/mock-typescript-modules-sinon/)

### jest-mock-extended
- best mocking library for typescript I've seen so far
- uses proxy objects and `jest.fn()` under the hood so you can mock out tons of constructs like classes, interfaces, functions, etc
- the only problem I've had is trying to mock out functions that also had functions declared on them like the default export of `node-archive` but you're going to have this problem anywhere
- here is a helper type for working with the `mockFn` function (the types they provide are verbose and hard to understand)
    ```ts
    // the "T &" is so that it can be casted to anything that requires the type of the function you are mocking
    type MockFn<T extends (...args: any) => any> = T & CalledWithMock<ReturnType<T>, Parameters<T>>
    ```

### ts-jest
- this is a helper function to mock out imports that are mocked out using `jest.mock('path/to/module')`
- it uses type support from the `mocked` function in `ts-jest/utils`
```ts
const capitalize = (str: string): Capitalize<string> =>
    str.charAt(0).toUpperCase() + str.substring(1)

type MockedExports<T> = {
    [P in keyof T as `mock${Capitalize<string & P>}`]: MockedType<T[P]>
}

// helper function to reduce redundancy of calling const mockedExport = mock(export) for every export
const mockExports = <T>(exports: T): MockedExports<T> =>
    Object.fromEntries(
        Object.entries(exports).map(([k, v]) => [`mock${capitalize(k)}`, v])
    ) as MockedExports<T>

const { mockS3Service } = mockExports({ S3Service })
const { mockReadable } = mockExports({ Readable })
```