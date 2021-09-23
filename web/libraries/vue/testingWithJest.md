# Testing with Jest

## jest.config.js

- the one provided by `@vue/cli-plugin-unit-jest/presets/typescript-and-babel` is pretty good but needs to be overriden a little when using other libraries like `cypress` and `vee-validate`
- example config with cypress

```js
module.exports = {
    preset: '@vue/cli-plugin-unit-jest/presets/typescript-and-babel',
    // specify setup file if needed
    setupFilesAfterEnv: ['<rootDir>/src/jestSetup.ts'],
    // override the test match to only find tests within src
    testMatch: [
        '<rootDir>/src/**/*.spec.[jt]s?(x)',
        '<rootDir>/src/**/__tests__/*.[jt]s?(x)'
    ],
    // make sure to ignore the cypress folder if there is one
    testPathIgnorePatterns: ['<rootDir>/node_modules', '<rootDir>/cypress']
}
```

- see the [vee-validate testing docs](https://vee-validate.logaretm.com/v3/advanced/testing.html#testing-caveats) for how to configure jest so it doesn't blow up when trying to import vee-validate code

## Testing composables

- use [vue-composable-tester](https://github.com/ktsn/vue-composable-tester)

```ts
import { mount } from 'vue-composable-tester'
import { ref } from 'vue'

function useCounter() {
  const count = ref(0)

  function increment() {
    count.value++
  }

  return {
    count,
    increment
  }
}

it('should increment count', () => {
  const { result } = mount(() => useCounter())

  expect(result.count.value).toBe(0)
  result.increment()
  expect(result.count.value).toBe(1)
})
```

## Example component tests with composition api

- you can mock composables using `jest.mock`
- i'm not in love with the boilerplate though (I'm hoping a better solution comes along)

```ts
import { render } from '@testing-library/vue'
import { axe } from 'jest-axe'
import { mocked } from 'ts-jest/utils'

import { ref } from '@vue/composition-api'
import { useTitle } from '@vueuse/core'

import MyComponent from './my-component.vue'

import type { Ref } from '@vue/composition-api'

jest.mock('@vueuse/core', () => ({
    useTitle: jest.fn()
}))

describe('my-component', () => {
    afterEach(() => {
        jest.resetAllMocks()
    })

    let container: Element
    let titleRef: Ref<string | null | undefined>

    beforeEach(() => {
        titleRef = ref('title')
        mocked(useTitle).mockReturnValue(titleRef)
        ;({ container } = render(MyComponent))
    })

    it('matches snapshot', () => {
        expect(container).toMatchSnapshot()
    })

    it('some test', () => {
        // some assertions
    })
})
```

## A11y testing

- can use [jest-axe](https://github.com/nickcolley/jest-axe)

```ts
it('has no axe violations', async () => {
    expect(await axe(container)).toHaveNoViolations()
})
```
