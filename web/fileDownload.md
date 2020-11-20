---
layout: page
title: File Download
permalink: /web/file-download
---

## Downloading a file in js
```js
    const link = document.createElement('a');
    link.href = window.URL.createObjectURL(new Blob([response.data]));
    link.setAttribute('download', 'filename');
    document.body.appendChild(link);
    link.click();
```