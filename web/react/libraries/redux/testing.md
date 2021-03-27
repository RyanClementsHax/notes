# Testing Redux

## Reducers and Selectors
- Can parameterize selector/reducer tests

## Testing Redux Tool Kit constructs
- Testing thunks
    ```js
    it('calls items/{id}/details and returns the data', async () => {
        when(axios.get)
            .calledWith(`items/${id}/details`)
            .mockResolvedValue({
                data: details
            });

        const result = await myThunk(id)(dispatch, getState, extra);

        expect(result.payload).toBe(details);
    });
    ```

## Mocking thunks
- when mocking out thunks, make the mocks return something like an action which contains a unique name and the payload passed in so assertions can look something like
    ```js
    expect(dispatch).toHaveBeenCalledWith(thunk.thunk("bazz"));
    ```
- mocking thunk files:
    - can take in a `path` param wich is the path to the file to be mocked relative to where the mocking code is
