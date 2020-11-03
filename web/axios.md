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