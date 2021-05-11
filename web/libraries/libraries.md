# Libraries

- [quill.js](https://github.com/quilljs/quill/blob/develop/docs/guides/why-quill.md)
  - WYSIWYG editor that can support many things like code editing
- [monaco-editor](https://microsoft.github.io/monaco-editor/index.html)
  - the editor that powers vscode
- [testing-library](https://testing-library.com/)
  - a library for dom testing
  - has implementations for pretty much any rendering framework/library
  - provides some accessibility testing out of the box
- [node-sass](https://github.com/sass/node-sass)
  - this library seems to be sensitive to the node runtime version for both installation and at runtime
    - if you are getting cryptic errors, mess with the node version to figure out what it is ok with
    - you can also try upgrading to the most recent version (i had to pin myself at an older version because the newer versions didn't play well with the libraries i was using)
  - i think this library on install uses python to build some stuff and it is possible that any python environments you have configured could leak into the way it builds (possibly)