# Web Assembly

- neither for the web nor is it assembly
- it is born out of the web, sure, but it is agnostic to its hosting environment so you can use it in more places than the web
- languages can compile down to wasm by taking their intermediate representation and translating it to wasm
- note that wasm has no runtime, but many languages do, so you have to compile the runtime to wasm as well
  - e.g. you have to compile v8 to wasm in order to compile js to wasm
