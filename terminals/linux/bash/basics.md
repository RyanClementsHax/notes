# Basics

## #?
- this is the return code of the previous statement
- "touching" this will clear out its value
- have to be careful in different environments and other people's scripts as this might not work the way you expect it
  - apparently, this doesn't work very well in AWS (???)

## >
- overwrites file
```bash
echo "hello world" > ex.txt
cat ex.txt
# hello world"
```

## >>
- adds to end of file
```bash
echo "hello world" >> ex.txt
echo "second line" >> ex.txt
cat ex.txt
# hello world
# second line
```

## Here doc
- this is an easy way to implement a multi-lined string
- [this](https://stackoverflow.com/questions/2953081/how-can-i-write-a-heredoc-to-a-file-in-bash-script) Stack Overflow post has a good set of examples
```bash
cat << EOF > ex.txt
These contents will be written to the file.
        This line is indented.
EOF
cat ex.txt
# These contents will be written to the file.
#         This line is indented.
```

## Script output
- there are 3 ways to output something from a script
    1. stdout
        - this is the basic output from a script and is what is redirected by `>`
        - also referred as `1`
    2. stderr
        - this is output that is written to when an error occurs and isn't redirected by `>` by default
        - also referred as `2`
    3. return code
        - this is the return code returned from a script when it exits (default is `0` even when it isn't specified)
- you cannot assume that other scripts you referrence use these outputs in the way you expect\
  - ex: should `sqlcmd` return a non-0 exit code when the sql command you sent through it had bad syntax, but reached the database just fine?
  - this is one reason why bash scripting can be painful
- redirecting bash output
  - you can put `2>&1` to redirect stderr to stdout
  - [this](https://stackoverflow.com/questions/818255/in-the-shell-what-does-21-mean) Stack Overflow post explains further
    

## bash
- `-c` option is how you can pass a string into it to be interpreted as a bash script
```bash
bash -c "echo 'hello world'"
# hello world
```

## Arguments
- `$0` is the argument containing the script invocation
    ```bash
    ../../../some_script.sh arg1 arg2
    ```
    - in this script, `$0` = `../../../some_script.sh`
- `$@` represents all args
    ```bash
    # script.sh
    echo $@

    # in terminal
    ./script.sh hello world
    # hello world
    ```
- `$#` represents the length of the arguments array
    ```bash
    # script.sh
    echo $#

    # in terminal
    ./script.sh hello world
    # 2
    ```
- parsing args example
    ```bash
    #!/bin/bash
    set -euo pipefail
    IFS=$'\n\t'

    display_help() {
        echo
        echo "Usage: ex.sh [options...]"
        echo
        echo "   Options:"
        echo "    -o --output                       Output directory, defaults to '.'"
        echo "    -h  --help,                       Display this help message."
        exit 0
    }
    outputDir='.'
    while :; do
        #         |----- we need to use a default here because we are using "set -u" and this argument may not be defined like if we ran "ex.sh" with no args
        #         v 
        case "${1-_none}" in
        -h | --help)
            echo "Displaying help message"
            display_help # Call help function
            exit 0
            ;;
        -o | --no-cleanup)
            shift 1
            outputDir="${1}" # we interpolate inside of a string so we don't run into issues with special characters
            shift 1 # right now this will fail if we dont provide a parameter for '-o'
            ;;
        -*)
            echo "Error: Unknown option: ${1}"
            display_help # Call help function
            exit 1
            ;;
        *) # No more named options
            break
            ;;
        esac
    done
    echo "output dir: $outputDir"
    ```
- `BASH_SOURCE`
  - holds the paths to called scripts
  - `BASH_SOURCE[0]` holds the directory of the currently running script (fails when symlinks are used)
  - [BASH_SOURCE[0] vs $0](https://stackoverflow.com/questions/35006457/choosing-between-0-and-bash-source)

## trap
- used to call functions on failure or interrupt
- [useful article](https://opensource.com/article/20/6/bash-trap)
- can use `trap --list` to see all of the signals it can catch

## Misc
- i've run into problems running a script that I was also modifying at the same time
  - it may be that bash is not inherently thread safe when it comes to this
  - it could also be just what happens when I do this on ubuntu in WSL
