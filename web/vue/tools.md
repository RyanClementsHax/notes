# Tools

## [Disabling all inputs within component](https://stackoverflow.com/questions/55905055/vue-need-to-disable-all-inputs-on-page)
- to avoid having to put `:disabled="myBoolean"` on every form, you could use a directive to handle this for you
```js
import Vue from "vue";

Vue.directive("disable-all", {
  // When all the children of the parent component have been updated
  componentUpdated: function(el, binding) {
    if (!binding.value) return;
    const tags = ["input", "button", "textarea", "select"];
    tags.forEach(tagName => {
      const nodes = el.getElementsByTagName(tagName);
      for (let i = 0; i < nodes.length; i++) {
        nodes[i].disabled = true;
        nodes[i].tabIndex = -1;
      }
    });
  }
});
```