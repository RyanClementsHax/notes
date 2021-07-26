# Articles

- [Stop using Page Objects and Start using App Actions](https://www.cypress.io/blog/2019/01/03/stop-using-page-objects-and-start-using-app-actions/)
  - argues to use composable functions to represent complex functionality over using page objects
  - when using composable functions in my own experience, it is best to use star imports to avoid import bloat
      ```js
      import * as utils from './utils.js'
      ```
- [When can the test start](https://www.cypress.io/blog/2018/02/05/when-can-the-test-start/)
- [Waiting for network resources in Cypress](https://www.artmann.co/articles/waiting-for-network-resources-in-cypress)