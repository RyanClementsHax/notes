# File Download

## Downloading a file in js

```js
const link = document.createElement('a');
link.href = window.URL.createObjectURL(new Blob([response.data]));
link.setAttribute('download', 'filename');
document.body.appendChild(link);
link.click();
```
