# Lodash

## [Bundle size](https://www.azavea.com/blog/2019/03/07/lessons-on-tree-shaking-lodash/)
- the way you import lodash functions matter
- good
    ```js
    import foo from 'lodash/foo'
    ```
- bad
    ```js
    import { foo } from 'lodash'
    ```