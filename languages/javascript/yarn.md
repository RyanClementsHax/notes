# Yarn

## Installing global packages
- I've had issues where the global package couldn't be found when I installed it in WSL
- I had to manually add the bin path to my PATH: `PATH="$PATH:$HOME/.yarn/bin"`

## Licenses
- you will get warnings when running yarn commands against a `package.json` without a license defined on the `"license"` field
- if you work on proprietary software and don't need a license, you can put [UNLICENSED](https://stackoverflow.com/questions/32214751/what-should-put-in-the-license-field-of-package-json-if-my-code-is-only-for-use) in that field to get rid of the warnings