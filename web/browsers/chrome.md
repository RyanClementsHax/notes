# Chrome

## Dev tools
- typing `$0` in the console will give you the currently selected element
  - you can also use `$1` to get the secondly last selected element and so on

### Layers
- 3D view of your web page that can easily show layers

### Rendering
- allows you to modify rendering settings to emulate browsers or users with preferences/disabilities
- [this is how rendering works](https://developers.google.com/web/fundamentals/performance/rendering)
  - make sure you don't hit expensive layers if you want to remain performant

### Performance
- [list of features](https://developer.chrome.com/docs/devtools/evaluate-performance/reference/)

### Lighthouse
- [Why is Largest Contentful Paint almost 4 seconds?](https://stackoverflow.com/questions/62327643/why-is-largest-contentful-paint-almost-4-seconds)
  - when doing a lighthouse mobile profile, it emulates cpu on 4x slowdown and network at fast 4g, but this doesn't show up in the performance report
  - if you want to see the actual performance report, do one yourself manually by loading the page with these throttles on

## [Increasing default text size](https://mcmw.abilitynet.org.uk/making-text-larger-google-chrome-windows-10)

## [Use chrome in ubuntu wsl](https://scottspence.com/2021/01/05/use-chrome-in-ubuntu-wsl/)