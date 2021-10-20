# Perfect dark mode

- this requires that a script "bootstrap" the application's theme before the first render
  - one way to do this is to inject a blocking script that performs this before anything else has a chance to run
- you can't count on cookies to solve for everything because what are you going to do for statically rendered pages?
- you also want to take user preferences into account
- [The Quest for the Perfect Dark Mode](https://www.joshwcomeau.com/react/dark-mode/) explains it in detail
- [Adding dark mode with Next.js, styled-components, and useDarkMode](https://brianlovin.com/writing/adding-dark-mode-with-next-js) explains another approach
- [next-themes](https://github.com/pacocoursey/next-themes) is a library that handles this
