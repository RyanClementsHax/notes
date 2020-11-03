# Testing

## Wrappers
- updating the wrapper
    - I don't know why this is the only way to do this, but for now this is the best solution as of 10/20/2020
    - wrapper.update() apparently only works with mounted tests???
    ```js
    // testUtils.js
    export const updateWrapper = (wrapper) => wrapper.setProps();
    ```

## Hooks
- shallow testing a component that uses `useState`
    ```js
    setState = jest.fn();
    useStateSpy = jest.spyOn(React, 'useState')
    useStateSpy.mockImplementation((init) => [init, setState]);
    ```
- shallow testing a component that uses `useEffect`
    - support for `useEffect` in enzyme doesn't exist as of 10/20/2020, but [this](https://www.npmjs.com/package/jest-react-hooks-shallow) library can help


## React Redux
- mocking react redux hooks
    - should look into using `jest` spies instead of trying to mock out the imports directly and then forcing the developer to dynamically import their component
    - `immer` is needed to make state changes immutably so that the state changes actually propagate properly
        - like if the component used a memoized selector (ex: `reselect`)
    - `cloneDeep` is needed to avoid accidental state changes bleeding between tests
        - if a test ends up modifying the initial state variable exported from a file, that will be seen throughout all other tests
    - for now, we need to mock out `useEffect` in shallow tests
- updating the store in mounted tests
    - need to find a better way to do this
    ```js
    export const updateStore = (wrapper) => {
        wrapper.props().store.dispatch({ type: 'refreshing the state of the mockStore' });
        updateWrapper(wrapper);
    };
    ```