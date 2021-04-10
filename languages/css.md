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

## [Auto growing inputs and textareas](https://css-tricks.com/auto-growing-inputs-textareas/)
- cannot do this without js

## [Performant collapse animations](https://css-tricks.com/performant-expandable-animations-building-keyframes-on-the-fly/)
- cannot do without js
- [Using CSS Transitions Auto Dimensions](https://css-tricks.com/using-css-transitions-auto-dimensions/)