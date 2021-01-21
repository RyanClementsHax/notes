# Terminals

## Unix Terminals

![](./unixShellComparisonChart)

- they are not all created the same
  - they have different feature sets and running scripts designed for one may produce funky errors when run in another
  - getting around these problems can be solved in a few ways
    1. run the script in the shell it was designed for
    2. use a [shebang](https://stackoverflow.com/questions/13872048/bash-script-what-does-bin-bash-mean)
    3. cry

## [Source vs Run](https://superuser.com/questions/176783/what-is-the-difference-between-executing-a-bash-script-vs-sourcing-it)
- `source` brings a script into the context of the running process
- running a script creates a new process