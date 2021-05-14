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

## [Passing slots through to child components](https://stackoverflow.com/questions/50891858/vue-how-to-pass-down-slots-inside-wrapper-component)
```html
<my-wrapper>
  <b-table v-bind="$attrs" v-on="$listeners">
    <template v-for="(_, slot) of $scopedSlots" v-slot:[slot]="scope"><slot :name="slot" v-bind="scope" /></template>
  </b-table>
</my-wrapper>
```
- this lets you pass all slots to the child component with binded scopes