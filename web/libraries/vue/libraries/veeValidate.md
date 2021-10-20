# Vee Validate

- [basics](https://vee-validate.logaretm.com/v3/guide/basics.html)

## [Testing caveats](https://vee-validate.logaretm.com/v3/advanced/testing.html)

- there are problems it has with being transformed by the source transform pipeline of jest so you need to exclude it

## Misc

- if injected globally, there might be a few properties that conflict with other components; thus, you might need to disable global injection by doing `Vue.use(VeeValidate, { inject: false })
