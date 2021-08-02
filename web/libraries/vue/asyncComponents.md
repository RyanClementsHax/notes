# Async Components

- only fetched if referenced during render
- if the component is not rendered, for example, because the value passed to the `v-if` directive on it evaluated to false, it won't be fetched
- [lazy loading individual vue components and prefetching](https://vueschool.io/articles/vuejs-tutorials/lazy-loading-individual-vue-components-and-prefetching/)
- [lazy loading and code splitting in vue js](https://vueschool.io/articles/vuejs-tutorials/lazy-loading-and-code-splitting-in-vue-js/)

## Programatic refresh of failed load

```js
// import...

/** get AsyncComponent with loading status
 * @param {Function: () => Promise<Component | { default: Component }>} promiseFactory eg: () => import('Component')
 *
 * @returns {Component} AsyncComponent
 */
function getAsync(promiseFactory, loading = LOADING, error = ERROR) {
  const asyncComponentFactory = () => () => ({
    error,
    loading,
    component: promiseFactory(),

    delay: 1,
    timeout: 15000,
  })

  const observe = Vue.observable({ c: asyncComponentFactory() })

  return {
    functional: true,
    render(createElement, { data, children }) {
      //  emit event $ to reloading
      if (data.on ? !data.on.$ : (data.on = {})) {
        data.on.$ = () => {
          observe.c = asyncComponentFactory()
          // parent.$forceUpdate()
        }
      }

      return createElement(observe.c, data, children)
    },
  }
}
```

- the method is listed on [this](https://github.com/vuejs/vue/issues/9788#issuecomment-520519960) github issue comment
- it works well
- just be sure to pass down the listener if you are dispatching the event from a child component
