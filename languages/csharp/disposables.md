# Disposables

## `HttpClient`
- be careful to not use this directly because it is designed to be used once per application
- use `IHttpClientFactory` to get around this

## With `yield`
- when using disposables in functions with the `yield` keyword, the generator greated will only fire when iterated
- this means that the `using` block will dispose the disposable before anything is yielded