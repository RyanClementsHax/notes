# Popouts

## Popout Component

- to close popout on window close, hook onto the `beforeunload` event on the main window
  - be sure to unhook your callback when the popout closes
- Accessing .cssRules on a style sheet reference when the css comes from a different origin violates CORS policies in some browsers (e.g. newer versions of Chrome) and will throw
