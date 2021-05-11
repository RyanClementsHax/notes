# Bootstrap

- Bootstrap has a ton of useful [utility classes](https://getbootstrap.com/docs/4.0/utilities/flex/) that will prevent you from having to write a lot of stuff yourself
- Try to avoid using Bootstrap classes directly. Instead, use a component library. It makes the code look so much nicer

## Scss variables
- can't find any documentation on these, but [here](https://github.com/twbs/bootstrap/blob/main/scss/_variables.scss) is the github file
- [overwriting bootstrap](https://stackoverflow.com/questions/38792005/how-to-change-the-bootstrap-primary-color) requires you to set variables before importing bootstrap

## Double gutter problem
- sometimes you want the spacing between the columns to match the spacing between the containing row and container containing that row
- you can overwrite the class and row classes to get this spacing to match
```scss
@mixin gutters-mixin() {
  margin-right: 7.5px;
  margin-left: 7.5px;

  > .col,
  > [class*='col-'] {
    padding-right: 7.5px;
    padding-left: 7.5px;
  }
}

@mixin no-gutters-mixin() {
  margin-right: 0;
  margin-left: 0;

  > .col,
  > [class*='col-'] {
    padding-right: 0;
    padding-left: 0;
  }
}

// for overriding on screen size change
@mixin no-gutters-override-mixin() {
  margin-right: 0 !important;
  margin-left: 0 !important;

  > .col,
  > [class*='col-'] {
    padding-right: 0 !important;
    padding-left: 0 !important;
  }
}
```
  - it is safe to hard code these numbers because bootstrap hard codes them to `15px`
- you should probably override container padding too
```scss
.container,
.container-fluid {
  padding: 0;
}
```
- it should also be noted that you can use the `no-gutters` for when you need no gutters

## Complex component creation
- [building a sidebar](https://bootstrapious.com/p/bootstrap-sidebar)

## Extracting breakpoints from dom
```js
function getBreakpoints() {
  const style = getComputedStyle(document.body)
  return {
    xs: +style.getPropertyValue('--breakpoint-xs').replace('px', ''),
    sm: +style.getPropertyValue('--breakpoint-sm').replace('px', ''),
    md: +style.getPropertyValue('--breakpoint-md').replace('px', ''),
    lg: +style.getPropertyValue('--breakpoint-lg').replace('px', ''),
    xl: +style.getPropertyValue('--breakpoint-xl').replace('px', '')
  }
}
```
- this seems to have problems sometimes where the initial render doesn't grab these
- there probably is a way around it that I haven't figured out yet

## Reducing bundle size
- bootstrap can be quite large and can bloat initial bundle sizes
- you could load from a cdn, but this doesn't allow for customization unless you host your custom bootstrap on a cdn
- there is a post css plugin that is supposed to purge css (I think its called purge css) that removes all unused css, so that's worth trying
- another solution is to use code splitting
  - only load the parts of bootstrap you need on initial render
  - defer the other parts of bootstrap
  - the following is the file that is imported when you `@import "bootstrap";` in scss or `import 'bootstrap';` in js (bootstrap v4.6)
    ```scss
    // node_modules/bootstrap/scss/bootstrap.scss

    /*!
     * Bootstrap v4.6.0 (https://getbootstrap.com/)
     * Copyright 2011-2021 The Bootstrap Authors
     * Copyright 2011-2021 Twitter, Inc.
     * Licensed under MIT (https://github.com/twbs/bootstrap/blob/main/LICENSE)
     */

    @import "functions";
    @import "variables";
    @import "mixins";
    @import "root";
    @import "reboot";
    @import "type";
    @import "images";
    @import "code";
    @import "grid";
    @import "tables";
    @import "forms";
    @import "buttons";
    @import "transitions";
    @import "dropdown";
    @import "button-group";
    @import "input-group";
    @import "custom-forms";
    @import "nav";
    @import "navbar";
    @import "card";
    @import "breadcrumb";
    @import "pagination";
    @import "badge";
    @import "jumbotron";
    @import "alert";
    @import "progress";
    @import "media";
    @import "list-group";
    @import "close";
    @import "toasts";
    @import "modal";
    @import "tooltip";
    @import "popover";
    @import "carousel";
    @import "spinners";
    @import "utilities";
    @import "print";
    ```
  - what you need to do is take the import statements that you need immediately and shove them into an `initial-bootstrap.scss` file that you import immediately
  - the other imports can be put in a `deferred-bootstrap.scss` file
  - you also have the opportunity to not include things you don't need
    - ex: no websites I work on need `@import "print";` so I can leave that out
  - this still allows you to customize bootstrap as specified in their docs
  - also note that some of these statements import files that consist of more import statements, like `@import "utilities";` (code below)
    ```scss
    @import "utilities/align";
    @import "utilities/background";
    @import "utilities/borders";
    @import "utilities/clearfix";
    @import "utilities/display";
    @import "utilities/embed";
    @import "utilities/flex";
    @import "utilities/float";
    @import "utilities/interactions";
    @import "utilities/overflow";
    @import "utilities/position";
    @import "utilities/screenreaders";
    @import "utilities/shadows";
    @import "utilities/sizing";
    @import "utilities/spacing";
    @import "utilities/stretched-link";
    @import "utilities/text";
    @import "utilities/visibility";
    ```
  - so there is more opportunity to further reduce bundle size
  - it is smart to keep `@import "reboot";` because it normalizes browser css