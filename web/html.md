# Html

## Semantic HTML

- [cheat sheet](https://learn-the-web.algonquindesign.ca/topics/html-semantics-cheat-sheet/)
- [cheat sheet](https://digital.com/tools/html-cheatsheet/)

## Meta tags

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

- this controls the width and scale of the viewport, so, for example, our web application won't look like a desktop screen on a mobile browser
- you can "simulate" response headers [with `http-equiv` attributes on meta tags](https://stackoverflow.com/questions/1341089/is-there-a-meta-tag-to-turn-off-caching-in-all-browsers)

  ```html
  <meta http-equiv="Cache-Control" content="no-store" />
  ```

  - this isn't well supported
  - just use actual headers instead

## Script tags

### `defer`

- this only works for scripts with a `src` attribute
- this will preserve loading scripts in document order
- doesn't block the parsing of html or other scripts in order to load and parse the script
- the scripts will execute right after the document is loaded and parsed

### `async`

- this only works for scripts with a `src` attribute
- this is good for scripts that are independent from the dom or other scripts like google analytics scripts
- these scripts are executed as soon as they are loaded and parsed irrelevant of the state of the dom parsing

## Forms

- buttons are of type `submit` by default so if you want a button to not submit the form, you need to put [type="button" on the button element](https://stackoverflow.com/questions/932653/how-to-prevent-buttons-from-submitting-forms)
