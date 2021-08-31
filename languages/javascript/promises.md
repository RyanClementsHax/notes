# Promises

- cannot return anything from a promise constructor function

    ```js
    // this promise will never get resolved because the resolve function is never getting called
    const promise = new Promise((resolve) => {
        // this return value is never used
        return service.someMethodThatReturnsSomething()
    })
    ```

## `Promise.all`

- really good for awaiting for things in no particular order

### [Handling errors](https://stackoverflow.com/questions/30362733/handling-errors-in-promise-all)

- `Promise.all` rejects as soon as *one* of the promises reject which means you have to do fancy error handing in order to wait for all of the promises to finish and then handle *all* of the errors

## Tools

### delay

- there is no `Promise.delay` unfortunately, but this is a function that does the same thing

```ts
function delay(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

delay(3000).then(() => alert('runs after 3 seconds'));
```
