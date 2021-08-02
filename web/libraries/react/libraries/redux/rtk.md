# Redux Tool Kit

## Component loading

- Can be handled using local state and promise chaining off of the dispatch call

    ```js
    const handleSubmit = () => {
        setIsLoading(true);

        dispatch(callBackend()).then(data => {
            setIsLoading(false);
            setData(data);
        });
    }
    ```

## Issues with circular dependencies

- Can DI needed items into the `extra` argument of thunks to reduce needed imports. This also makes testing them so much easier if you don't have to mock out imports.

    ```js
    export const myThunk = createAsyncThunk(
        `${sliceName}/myThunk`,
        (param, { dispatch, extra: { actions, thunks } }) => {
            dispatch(actions.someAction(param));
            dispatch(thunks.someThunk(param));
        }
    );
    ```

- slice names can be brought into a constants file so you can share your slice names across thunks, selectors, and slice files
- common actions can be put into a separate file (you now have to use `createAction`, and "listen" to it in `extraReducers` in your slice)

## That one slice that needs to be updated when seemingly anything happens

- Extra reducers can get to be a long list and may involve duplicated logic like updating a row
- Consider making a separate action/thunk that needs to be dispatched from thunks/components to update that slice
- This will also help avoid circular dependencies

## RTK Query

- inspired by [react query](https://react-query.tanstack.com/), rtk now has its [own way](https://github.com/reduxjs/redux-toolkit/releases/tag/v1.6.0-alpha.1) to do data fetching with fancy features
