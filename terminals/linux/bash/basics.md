# Basics

## Variables
- readonly
    ```bash
    declare -r myVar='foo'
    myVar='bar' # errors
    ```

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

## for loop

- with a dynamic end value (this is hard to due cuz bash sucks)
    ```bash
    END=5
    for ((i=1;i<=END;i++)); do
        echo $i
    done
    # or this, but it will create a subprocess which may not be good if $END is really big
    for i in $(seq 1 $END); do
        echo $i;
    done
    ```

## seq
```bash
seq 5
# 1
# 2
# 3
# 4
# 5
```
- handy helper to iterate through a range of numbers
- particularly helpful if you don't know the end value
  - you can subcommand this instead of using static brace expansion `{1..10}` which cannot take dynamic values because brace expansion is done before any other expansions are run

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
    ```bash
    >&2 echo "error"
    ```
  - [this](https://stackoverflow.com/questions/818255/in-the-shell-what-does-21-mean) Stack Overflow post explains further
  - discarding stderr: `2>/dev/null`
    

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

## Variables
- check if variable null or empty
    ```bash
    if [ -z "${tag}" ]; then
        echo "var is null or empty"
    else
        echo "var is not null or empty"
    fi
    ```

## trap
- used to call functions on failure or interrupt
- [useful article](https://opensource.com/article/20/6/bash-trap)
- can use `trap --list` to see all of the signals it can catch

## Strings
- lowercase the first letter of a string
    ```bash
    str="HELLO"
    lowerCase=${str,}
    echo $lowerCase
    # hELLO
    ```
- lowercase a string
    ```bash
    str="HELLO"
    lowerCase=${str,,}
    echo $lowerCase
    # hello
    ```
- uppercase the first letter of a string
    ```bash
    str="hello"
    upperCase=${str^}
    echo $upperCase
    # Hello
    ```
- uppercase a string
    ```bash
    str="hello"
    upperCase=${str^^}
    echo $upperCase
    # HELLO
    ```

## Arrays
- these are constructs that can only exist inside of bash so you cannot export them as variables from the script
  - this is because not all shells support arrays like this
  - you have to export the array as a string of combined contents delimited by something if you need to export it
- declaring
    ```bash
    myArray=(
        'item1'
        'item2'
        'item3'
    )
    ```
- adding to an array
    ```bash
    myArray+=('item4')
    ```
- represent contents of array as one variable
    ```bash
    for i in "${myArray[*]}"; do
        echo "$i"
    done
    ```
- prepresent contents of array as separate variables
    ```bash
    for i in "${myArray[@]}"; do
        echo "$i"
    done
    ```
- joining the elements of an array with custom delimiters ([ref](https://stackoverflow.com/questions/1527049/how-can-i-join-elements-of-an-array-in-bash))
    ```bash
    # supports multicharacter delimiters
    function join_by {
        local d=$1;
        shift
        local f=$1
        shift
        printf %s "$f" "${@/#/$d}"
    }
    
    join_by , a b c # a,b,c
    join_by ' , ' a b c # a , b , c
    join_by ')|(' a b c # a)|(b)|(c
    join_by ' %s ' a b c # a %s b %s c
    join_by $'\n' a b c # a<newline>b<newline>c
    join_by - a b c # a-b-c
    join_by '\' a b c # a\b\c
    join_by '-n' '-e' '-E' '-n' # -e-n-E-n-n
    join_by , #
    join_by , a # a
    ```
    ```bash
    # supports only single character delimiters
    function join_by {
        local IFS="$1"
        shift
        echo "$*"
    
    }

    join_by , a "b c" d # a,b c,d
    join_by / var local tmp # var/local/tmp
    join_by , "${FOO[@]}" # a,b,c
    ```

## Regexes
- you can check strings against regexes
    ```bash
    # check against the docker tag regex
    if [[ "$var" =~ ^[a-zA-Z0-9_][a-zA-Z0-9_.-]{0,127}$ ]] ; then
      echo "matches regex"
    else
      echo "doesn't match regex"
    fi
    ```
- `\w` isn't supported in bash regexes so you have to expand it to `[a-zA-Z0-9_]` yourself

## touch
- creates a file
    ```bash
    touch file_name
    ```

## Linking a file

```bash
ln -s file.txt link1
```

## Debugging a bash script
- `bash -x ./myScript.sh` will print out all of the commands and branches taken by that script
- you can also put this option in the `shebang` as such: `#!/bin/bash -x`
- to select specific areas to print this debug information, use `set -x` to turn it on and `set +x` to turn it off

## Misc
- i've run into problems running a script that I was also modifying at the same time
  - it may be that bash is not inherently thread safe when it comes to this
  - it could also be just what happens when I do this on ubuntu in WSL


