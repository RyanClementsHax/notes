# Strict Mode

- bash has a lot of defaults that aren't intuitive and can cause a lot of debugging problems
- by starting your bash files with the following, you can save yourself a lot of time figuring out why your program doesn't work, or why you are getting some super esoteric error
  - keep in mind that referencing scripts that aren't written to be sourced in strict mode from scripts that have this will cause errors
  - i don't know a way around this right now, so my current strategy is just to avoid strict mode all together when trying to do that
- [this](http://redsymbol.net/articles/unofficial-bash-strict-mode/) article goes further to explain what strict mode does
```bash
#!/bin/bash
set -euo pipefail
IFS=$'\n\t'
```
- using `set -u` will cause you to have to make sure variables are defined before using them
  - when using arguments, you need to give a default value when using it to make the script happy