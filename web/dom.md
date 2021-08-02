# DOM

## Intersection observer

- handy, performant tool for determining if an element is intersecting with another, say the viewport
- can be used to determine what is in view and other handy things
- [css tricks article](https://css-tricks.com/an-explanation-of-how-the-intersection-observer-watches/)

## Browser bugs

- you can test for browser bugs by inserting the potentially broken elements into the dom, then checking the functionality (this is what jquery does)

## [requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)

- this is used for handling dom updates when things like scroll updates happen more frequently than the dom rerenders
