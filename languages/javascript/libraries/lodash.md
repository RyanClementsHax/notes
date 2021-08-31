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

- [chaining lodash implicitly brings everything in](https://stackoverflow.com/questions/35973992/load-a-single-lodash-methods-for-smaller-builds-with-browserify-rollup-webpack)
