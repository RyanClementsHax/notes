# Colored Output

[This](https://stackoverflow.com/questions/5947742/how-to-change-the-output-color-of-echo-in-linux) Stack Overflow link explains it well

```bash
RED='\033[0;31m'
GREEN='\033[0;32m'
NC='\033[0m' # no color

printf "${RED}this is red${NC}\n"
printf "${GREEN}this is green${NC}\n"
```
