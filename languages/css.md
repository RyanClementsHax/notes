# CSS

## Multiple sticky headers
- the only way to implement this properly seems to be using [sticky positions with hardcoded offset values](https://stackoverflow.com/questions/54689034/pure-css-multiple-stacked-position-sticky)

## Asterisk on form fields
- in this example, add the `form-group-required` class to the `label` you want to add an asterisk to
```css
label.form-group-required :after {
    content: ' *';
    color: red;
    font-weight: 800;
}
```