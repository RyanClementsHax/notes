# Enzyme

## General
- Finding component by props
    ```js
    wrapper.find({ foo: 'bar' })
    ```

## Mounted testing
- finding by a prop will return both the component and the rendered html
    - I'm not sure how to get past this
    ```js
    const wrapper = mount(<div id="foo" />);

    wrapper.find('#foo') // will return 2 nodes
    ```
