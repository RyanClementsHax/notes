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