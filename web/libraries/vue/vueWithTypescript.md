# Vue With Typescript

## Vue2 + typescript

- not well supported
- [migration guide](https://medium.com/js-dojo/adding-typescript-to-your-existing-vuejs-2-6-app-aaa896c2d40a)
- the `Vue.extend({})` function given doesn't have very good implicit typing so you have to use the generic version `Vue.extend<Data, Methods, Computed, Props>({})`
- [@vue/composition-api](https://github.com/vuejs/composition-api) has a function in its library called `defineComponent` that does the same thing as the one in vue 3, it just doesn't do typing as well, but its very close
  - it also plugs in the vue 3 composition api which enables those benefits (albiet with limits)
  - this is my recommended approach as it also helps with migration and gives access to most of the composition api features
  - it is also a relatively small library
  - the Vetur template interpolation service doesn't work well with prop types yet
  - other options considered were class based components and using `Vue.extend`
    - class based components did the job, but they seemed more verbose and can be more of a learning curve if you are most familiar with the options api
    - `Vue.extend` didn't have nearly as good typescript support as `defineComponent`
  - overall it makes for a great option for migrating from vue2 to vue 3
- do know that typescript types are erased at runtime so prop validation won't work the same way you expect
  - to get typescript type checking in your ide, you have to use `Vetur` for this by configuring `Vetur` as follows

    ```json
    // .vscode/settings.json
    {
        // this enables template intellisense
        "vetur.experimental.templateInterpolationService": true,
        // this enables prop validation in templates
        "vetur.validation.templateProps": true
    }
    ```

    - prop validation for custom types is [currently broken](https://github.com/vuejs/vetur/issues/2343)
- [vue-property-decorator](https://github.com/kaorun343/vue-property-decorator) gives as good type support as the other options with the same limitations with prop type validation
  - this is a more verbose option

## Vue 3 + typescript

- supported out of the box
- please use vue 3 with typescript if you can

### `reactive`

- this hook takes an object and makes all of its fields reactive
- this is good for objects created in setup that need to be reactive arbitrarily
- this works with typescript, but becasue it does some recursive typing unwrapping any refs that are in those types, any nominal value the types once had are erased

```ts
type MyType =
  | "this"
  | "that"

interface MyInterface {
  field1: string
  field2: MyType
}

var obj = {
  field1: "foo",
  field2: "bar"
}

var reactiveObj = reactive(obj)
/*
  this will appear as the following type without any reference to MyInterface or MyType
  {
    field1: string,
    field2: "this" | "that"
  }
*/
```

### [Directives](https://www.itread01.com/content/1505211611.html)

- unfortunately the docs don't specify this api well in typescript

```ts
// color-directive.ts

import { DirectiveOptions } from ‘vue‘

const directive: DirectiveOptions = {
    inserted(el, node) {
        /**
         * Using value:
         * v-colorDirective={color: ‘red‘, backgroundColor: ‘blue‘}
         */
        if (node.value) {
            el.style.backgroundColor = node.value.backgroundColor;
            el.style.color = node.value.color;
        }

        /**Using modifiers:
         * v-colorDirective.color
         * v-colorDirective.backgroundColor
         */
        if (node.modifiers.color){
            el.style.color = node.value;
        }

        if (node.modifiers.backgroundColor) {
            el.style.backgroundColor = node.value;
        }
    }
};

export default directive;
```
