# CSS

## Sticky

### Multiple sticky headers
- the only way to implement this properly seems to be using [sticky positions with hardcoded offset values](https://stackoverflow.com/questions/54689034/pure-css-multiple-stacked-position-sticky)

### [Sticky position not working](https://www.designcise.com/web/tutorial/how-to-fix-issues-with-css-position-sticky-not-working)
- if any parent/ancestory of the sticky element has any of the following `overflow` properties set, `position: sticky` won't work
  - `overflow: hidden`
  - `overflow: scroll`
  - `overflow: auto`

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
- the article linked in the header relies on transitions which doesn't change the width/height that the element takes up so you cannot take this approach if you are looking to cause layout shifts with expansions
- in my experience, transitioning width/height is ok for a small, one-off animation is ok
  - lucid chart animates their side nav collapse with a transition on the width property
- [Building performant expand & collapse animations](https://developers.google.com/web/updates/2017/03/performant-expand-and-collapse)
- [Using CSS Transitions Auto Dimensions](https://css-tricks.com/using-css-transitions-auto-dimensions/)
- [How can I transition height 0 to height auto using css](https://stackoverflow.com/questions/3508605/how-can-i-transition-height-0-to-height-auto-using-css)

## [Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- if you want flexbox children to be constrained to the width/height of the container, you need to set a definite width every parent of that element so the width/height so the constraint can trickle down to the children, else your children will blow out the width/height of the parent since it has flexible width/height
  - a definite width/height doesn't need to be in something like `px` or `em`, it can be `%` too
  - I had to bail out of using flexbox to grid for some layouts that had these contraints

## [Grid](https://css-tricks.com/snippets/css/complete-guide-grid/)
- think of this as a more advanced version of flexbox
- to help prevent grid blowout, try using `minmax(0, 100%)` when defining its size instead of `auto`

## Box shadow
- there needs to be space around the component using box shadow as defined by the the component's parent, otherwise the shadow will be clipped

## [will-change](https://developer.mozilla.org/en-US/docs/Web/CSS/will-change)
- gives a hint to the css engine that a property will change so it can do optimzations ahead of time