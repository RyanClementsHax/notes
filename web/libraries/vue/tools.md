# Tools

## [Disabling all inputs within component](https://stackoverflow.com/questions/55905055/vue-need-to-disable-all-inputs-on-page)
- to avoid having to put `:disabled="myBoolean"` on every form, you could use a directive to handle this for you
- here is a typescript version
```ts
import type { DirectiveFunction } from 'vue'

const disableAll: DirectiveFunction = (el, binding) => {
  if (binding.value === binding.oldValue) return
  const tags = ['input', 'button', 'textarea', 'select', 'a']

  tags.forEach(tagName => {
    const nodes = el.getElementsByTagName(tagName)
    for (let i = 0; i < nodes.length; i++) {
      ;(<HTMLInputElement>nodes[i]).disabled = binding.value
      ;(<HTMLInputElement>nodes[i]).tabIndex = binding.value ? -1 : 0
      binding.value
        ? nodes[i].classList.add('disabled')
        : nodes[i].classList.remove('disabled')
    }
  })
}

export default disableAll
```

## [Passing slots through to child components](https://stackoverflow.com/questions/50891858/vue-how-to-pass-down-slots-inside-wrapper-component)
```html
<my-wrapper>
  <b-table v-bind="$attrs" v-on="$listeners">
    <template v-for="(_, slot) of $scopedSlots" v-slot:[slot]="scope"><slot :name="slot" v-bind="scope" /></template>
  </b-table>
</my-wrapper>
```
- this lets you pass all slots to the child component with binded scopes

## Submitting form in a modal with `ctrl + enter`
```js
{
  // ...
  mounted() {
    document.addEventListener('keyup', this.onKeyup, false)
  },
  destroyed() {
    document.removeEventListener('keyup', this.onKeyup, false)
  },
  methods: {
    onKeyup(e /* KeyboardEvent */) {
      if (
        this.$refs.modal.isShow &&
        e.ctrlKey &&
        e.keyCode === 13 /* enter */
      ) {
        this.onSubmit()
      }
    },
  }
  // ...
}
```
  - there may be a better way of doing this, but this solution hooks an even listener globally, then only acts if the modal is in view

## `useAxios` composable
- the version provided in [vueuse](https://vueuse.org/integrations/useAxios/) has limitations so here is one that allows you to reexecute, but doesn't support everything the vueuse version offers like cancelling requests
- the following is heavily inspired by that same hook however
    ```ts
    // axiosComposables.ts
    import axios, { AxiosResponse, AxiosRequestConfig, AxiosResponse, AxiosInstance } from 'axios'
    // would import these directly from vue if not using the @vue/composition-api plugin
    import {
      shallowRef,
      ref,
      computed,
      inject,
      InjectionKey,
      provide,
      Ref,
      ComputedRef,
      reactive
    } from '@vue/composition-api'

    const api = axios.create({
      baseURL: process.env.BASE_URL
    })

    export interface ProblemDetails {
      status: number
      detail: string
    }

    export type ProblemDetailsResponse = AxiosResponse<ProblemDetails>

    export const axiosKey: InjectionKey<AxiosInstance> = Symbol()
    export const useProvideAxios = (): void => provide(axiosKey, api)

    export interface UseAxiosReturn<TData, TBody = never> {
      data: Ref<TData | undefined>
      loading: Ref<boolean>
      err: Ref<ProblemDetailsResponse | undefined>
      execute: (body: TBody) => Promise<void>
    }

    export interface HookConfig {
      immediate?: boolean
    }

    export function useAxios<TData, TBody = never>(
      url: string,
      config: AxiosRequestConfig = {},
      hookConfig: HookConfig = { immediate: true }
    ): UseAxiosReturn<TData, TBody> {
      const instance = inject(axiosKey)!

      const data = ref<TData>()
      const loading = ref(hookConfig.immediate ?? false)
      const err = shallowRef<ProblemDetailsResponse>()

      const execute = (body?: TBody) => {
        loading.value = true
        err.value = undefined

        return instance(url, { ...config, data: body ? body : config.data })
          .then((r: AxiosResponse<TData>) => {
            data.value = r.data
          })
          .catch(e => {
            err.value = e.response as ProblemDetailsResponse
          })
          .finally(() => {
            loading.value = false
          })
      }

      if (hookConfig?.immediate) {
        execute()
      }

      return {
        execute,
        data,
        err,
        loading
      }
    }
    ```
- to use, first inject axios at some higher level (like the root) to get dependency injection set up
  - there is likely a better way to set up this dependency injection
    ```ts
    setup() {
      useProvideAxios()
    }
    ```
- then call it like any other function
    ```ts
    setup() {
      const { data, err, loading, execute } = useAxios<ResponseBody, RequestBody>(
        'some-endpoint',
        { method: 'POST' },
        { immediate: false }
      )

      return {
        data,
        err,
        loading,
        execute
      }
    },
    ```
- you can also create a helper method to compose loading and error states
    ```ts
    // end of axiosComposables.ts

    // begin typescript black magic to get this working
    export type ComputedCombinedAsyncState = {
      loading: ComputedRef<UseAxiosReturn<unknown>['loading']['value']>
      err: ComputedRef<UseAxiosReturn<unknown>['err']['value']>
    }

    // we use an unwrapped type here to preserve type information that "reactive" would otherwise erase
    export interface UnWrappedUseAxiosReturn<TData, TBody = never> {
      data: TData | undefined
      loading: boolean
      err: ProblemDetailsResponse | undefined
      execute: (body: TBody) => Promise<void>
    }

    export type AsyncStates = { [key: string]: UseAxiosReturn<any, any> }

    export type UseCombinedAsyncStateReturn<T extends AsyncStates> =
      ComputedCombinedAsyncState &
        {
          [K in keyof T]: T[K] extends UseAxiosReturn<infer TData, infer TBody>
            ? UnWrappedUseAxiosReturn<TData, TBody>
            : never
        }
    // end typescript black magic to get this working

    export const useCombinedAsyncStates = <T extends AsyncStates>(
      states: T
    ): UseCombinedAsyncStateReturn<T> =>
      ({
        loading: computed(() => Object.values(states).some(x => x.loading.value)),
        err: computed(() =>
          Object.values(states)
            .map(x => x.err?.value)
            .find(x => x)
        ),
        ...Object.entries(states)
          // we need reactive here so the original state objects keep reactivity when returned from setup since vue doesn't do this for us
          .map(([key, value]) => ({ [key]: reactive(value) }))
          .reduce((acc, curr) => ({ ...acc, ...curr }), {})
      // Object.entries and Object.values don't have great typescript support so we have to resort to a hack
      } as unknown as UseCombinedAsyncStateReturn<T>)
    ```
- then use it like this
    ```ts
    setup() {
      const create = useAxios<CreateResponseBody, CreateRequestBody>(
        '/create',
        { method: 'POST' },
        { immediate: false }
      )
      const edit = useAxios<EditResponseBody, EditRequestBody>(
        '/edit',
        { method: 'POST' },
        { immediate: false }
      )

      const { loading, err, create: createReactive, edit: editReactive } = useCombinedAsyncStates({ create, edit })
      // loading will be true if any of the requests are loading
      // err will be set to the most recent error of either of them
      // create and edit have their original states but are now reactive so their internal states can remain reactive
      return {
        loading,
        error,
        createReactive,
        editReactive
      }
    }
    ```

## Reloading an async component
- vue doesn't have the ability to reload an async component so you have to build this out yourself
- this implementation wraps an async component and injects a `$reload` event listener which will reload the component
- all any component (the async component, the loading component, or error component) would have to do to trigger the reload is call `$emit('$reload')`
- it you can also use a provider/inject pattern to pass down this functionality
  ```ts
  // would import these directly from vue if not using the @vue/composition-api plugin
  import {
    defineComponent,
    ref,
    defineAsyncComponent,
    h
  } from '@vue/composition-api'

  import AsyncError from './async-error.vue' // default error component
  import AsyncLoading from './async-loading.vue' // default loading component

  // this is a hack to get the type of the first parameter since it isn't exported from @vue/composition-api
  export type AsyncComponentSource = Parameters<typeof defineAsyncComponent>[0]

  export const withReload = (
    source: AsyncComponentSource,
    initialProps: Record<string, unknown> = {
      loadingMessage: 'Setting some things up for you...'
    }
  ) =>
    defineComponent({
      setup(props, ctx) {
        const asyncComponentFactory = () =>
          defineAsyncComponent({
            loadingComponent: AsyncLoading,
            errorComponent: AsyncError,
            ...('loader' in source ? source : { loader: source })
            // uncomment this if you want to test the $reload function by artifically adding latency to the resolution of the module
            // loader: () =>
            //   new Promise(resolve => setTimeout(resolve, 3000)).then(
            //     () => import('./async-error.vue')
            //   )
          })

        const asyncComponent = ref(asyncComponentFactory())
        return () =>
          h(asyncComponent.value, {
            ...ctx,
            props: {
              ...(props as Record<string, unknown>),
              ...initialProps
            },
            on: {
              ...ctx.listeners,
              $reload() {
                asyncComponent.value = asyncComponentFactory()
              }
            }
          })
      }
    })
  ```
- using it is as simple as
    ```ts
    withReload(() => import('./path/to/component.vue')
    // or
    withReload({
      loader: () => import('./path/to/component.vue'),
      loadingComponent: SomeOtherLoadingComponent,
      errorComponent: SomeOtherErrorComponent,
      delay: 400 // other options supported too
    })
    ```