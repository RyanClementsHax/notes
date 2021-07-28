# Html

## Meta tags
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```
- this controls the width and scale of the viewport, so, for example, our web application won't look like a desktop screen on a mobile browser

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