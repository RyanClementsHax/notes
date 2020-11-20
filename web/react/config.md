---
layout: page
title: React Config
permalink: /web/react/config
---

## Absolute imports
```json
// jsconfig.json in root directory
{
  "compilerOptions": {
    "baseUrl": "src"
  },
  "include": ["src"]
}
```

## Env vars
- I'm not sure if this is the best way, but you can use `envsub` (npm package) to do interpolate env vars into a template file
    - `public/config.js` will be excluded from the repo, but included as a script in `index.html`
    ```json
    "prestart": "envsub --syntax dollar-both --env-file local.env public/config.template.js public/config.js",
    ```
    ```env
    FOO = bar
    ```
    ```js
    // public/config.template.js
    window.config = {
        url: '$FOO'
    }
    ```