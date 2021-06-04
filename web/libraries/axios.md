# Axios

## Interceptors
- Interceptors override config placed in the request
    ```js
    
    const instance = axios.create();

    instance.interceptors.request.use(async (config) => ({
        ...config,
        headers: {
            ...config.headers,
            timeout: 1000
        },
    }));

    axios.get('/items', {
        timeout: 2000 // this gets overridden
    });
    ```

## Network errors
- they don't have response codes so you can't treat them like regular exceptions, but axios will throw when they happen
- [ref](https://stackoverflow.com/questions/47067929/how-to-handle-neterr-connection-refused-in-axios-vue-js)