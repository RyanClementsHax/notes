# Breakpoint plugin

Note: `VueUse` has a [hook](https://vueuse.org/core/useBreakpoints/) that does all of this for you

- it can be very handy to be able to write something like `v-if="isSmallScreen"`
  - this allows the component to not be mounted in the dom as compared to just using `display: none;` in css which keeps it there
- it is possible to create an observable that can be accessed by any component

1. create your breakpoints (or read them from the dom)

    ```js
    const breakpoints = Object.freeze({
        xs: 0,
        sm: 576,
        md: 768,
        lg: 992,
        xl: 1200
    })
    ```

2. then create a plugin with the following format

    ```js
    export default {
        install(Vue) {
            // create your observable
            const breakpoint = Vue.observable({
                xs: false
                sm: false,
                md: false,
                lg: false,
                xl: false
            })
            // add it to the Vue prototype so that it can be accessed anywhere
            Vue.prototype.breakpoints = breakpoint
            // create currentBreakpoint variable
            // create resize handler
            const onResize = () => {
                // in that handler categorize the width of the screen into a breakpoint
                // if a new breakpoint has been reached, update the observable object
            }
            // add your resize handler as an event listener
            window.addEventListener('resize', onResize)
            // dont forget to call your initial resize!
            onResize()
        }
    }
    ```

3. install the plugin

    ```js
    import BreakpointPlugin from '@/plugins/breakpointPlugin'

    Vue.use(BreakpointPlugin)
    ```

4. use in components

    ```html
    <div v-if="breakpoint.sm">
        This will only show if the screen is at least 576px
    </div>
    ```
