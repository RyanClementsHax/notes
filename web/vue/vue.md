# Vue

## Components
- all components are vue instances and take the same config object as the root vue instance (with the exception of root specific options)
- sometimes, you may need to use the `var vm = this` technique to circumvent `this` problems
- `data`
  - automatically makes its fields reactive
  - any fields added to this after creation won't be reactive
  - `Object.freeze(...)` will prevent reactivity
  - if it is an object, the data is shared across all instances
  - if it is a function, the data is local
- avoid arrow functions because they don't have a `this`
- templates
  - can only have single root element
  - interpolations
    - support javascript, but only one statement (i.e. can't do control flow)
  - `v-bind` shorthand
    ```html
    <!-- full syntax -->
    <a v-bind:href="url"> ... </a>

    <!-- shorthand -->
    <a :href="url"> ... </a>

    <!-- shorthand with dynamic argument (2.6.0+) -->
    <a :[key]="url"> ... </a>
    ```
    - can bind all attrs with `v-bind="$attrs"` (good for passthrough props)
  - `v-on` shorthand
    ```html
    <!-- full syntax -->
    <a v-on:click="doSomething"> ... </a>

    <!-- shorthand -->
    <a @click="doSomething"> ... </a>

    <!-- shorthand with dynamic argument (2.6.0+) -->
    <a @[event]="doSomething"> ... </a>
    ```
    - can bind all event listeners with `v-on="$listeners"` (good for passthrough listeners)
- name
  - [this is not needed unless you are writing recursive components, but still recommended](https://stackoverflow.com/questions/62958194/does-the-vue-js-component-require-a-name-option#:~:text=1%20Answer&text=When%20register%20one%20component%2C%20its,As%20Vue%20Guide%3A%20Vue.&text=But%20it%20will%20be%20a,give%20names%20to%20your%20components.)
- computed
  - essentially fields that derive their values from `data`
  - caches values
  - can specify getters and setters
- style classes
  - can bind class field to a computed object
    ```html
    <div v-bind:class="classObject"></div>
    ```
  - can use array syntax too
    ```html
    <div v-bind:class="[activeClass, errorClass]"></div>
    ```
  - also ternaries
    ```html
    <div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
    ```
  - can combine styles too
    ```html
    <div v-bind:class="[{ active: isActive }, errorClass]"></div>
    ```
- styles
  - same syntax as classes
  - a css property that requires vendor prefixes will automatically be added those prefixes by vue
  - [cannot style bind to a pseudo element](https://stackoverflow.com/questions/50625973/vue-js-v-bindstyle-pseudo-element-after-content-icon)
    - I would just create an actual element to do what you want
  - [scoped css](https://vue-loader.vuejs.org/guide/scoped-css.html#child-component-root-elements)
- conditional rendering
  - vue will try to reuse elements so if you don't want state to be shared between two rendering branches, you can use the `key` prop to differentiate them
  - `v-if` and `v-show` are [different](https://vuejs.org/v2/guide/conditional.html#v-if-vs-v-show)
- rendering
  - `is=component-name` is used to get around parsing errors (ex: `li` is the only element valid in a `ul` but you have a custom `li` component)
- registering
  - `Vue.component(MyComponent)` registers the component globally
    - not ideal for tree shaking when a component stops being used
    - must take place before the vue root instance is created
  - local registration
    - not available in sub components
    ```js
    var ComponentA = { /* ... */ }

    var ComponentB = {
        components: {
            'component-a': ComponentA
        },
        // ...
    }
    ```
- props
  - object or array defaults must be returned from a factory function
  - if you specify a default value, it must be of the type you specify too otherwise you will run into odd, slient errors
- watch
  - can get creative with watch properties
  - you can specify deep watches
    ```js
    'form.title'() {
      console.log(this.form.title)
    }
    ```
- methods
  - calling methods from the parent component is possible, but know that any state changes made by calling those methods from parent components may not hook into vue's reactivity

### Lifecycle
![vueLifecycle.png](./vueLifecycle.png)

## Environment vars
- vue makes this [pretty easy](https://cli.vuejs.org/guide/mode-and-env.html#environment-variables)

## Scoped css
- [deep selectors in vue](https://vue-loader.vuejs.org/guide/scoped-css.html#child-component-root-elements)
- hot reload cannot handle a change from unscoped to scoped so you will need to reload the page to see the changes
- can have both scoped and unscoped `<style>` tags in the same component

## Misc
- `import MyComponent from './MyComponent'` will first grab `MyComponent.js` over `MyComponent.vue`
