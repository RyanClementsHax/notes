# Vuex

## State

- in order to get reactivity, you need to make your state flat to preserve reactivity

## [Actions vs mutations](https://blog.logrocket.com/vuex-showdown-mutations-vs-actions-f48f2f7df54b/)

- actions should contain business logic, but mutations should contain individual operations on state
- actions can return things whereas mutations cannot

## Eliminating boilerplate for async functions

- sometimes you need to make changes to the state before, and after making a network call (e.g. updating loading and error state)
- writing out and dispatching every mutation around making the network call in the action will be cumbersome and repetitive if you have multiple network calls you make
- it is easier to create a wrapper that will take a config object and create and dispatch the mutations for you like the following

    ```js
    // give an action name and this function will handle the creation and calling of the myActionPending, myActionFulfilled, and myActionRejected mutations
    var action = createAsyncAction('myAction', {
        // cannot use lambda syntax if you want access to "this"
        operation() {
            // perform your operation and return the result
        },
        pending: (state) => { /* called before the operation handler is called */ },
        fulfilled: (state, res) => { /* handle success */ },
        rejected: (state, err) => { /* handle error */ }
    })

    export default {
        state: () => ({
            myState: {}
        }),
        actions: {
            ...action.action()
        },
        mutations: {
            ...action.mutations()
        }
    }
    ```
