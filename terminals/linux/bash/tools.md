# Tools

## Get OS info

```bash
uname -a
```

## Get Debian Version

```bash
cat /etc/issue
# or
cat /etc/os-release
```

## Make file of random bits

```bash
dd if=/dev/urandom of=sample.dat bs=1M count=64
```

## Download and unpack .tar.gz files

```bash
curl -O downloaded_file_name -L url_to_download_from \
    # && sha1sum downloaded_file | grep some_checksum \ # if you want to do integrity checking
    && tar xf downloaded_file_name -C output_directory
```

- curl by default writes to stdout unless you give tell it to output to a local file with the `-O` option
  - if `downloaded_file_name` is not given, it will use the last part of url_to_download_from to name the file
  - you can also do `curl <address> -o <file>`
- curl can be configured to follow redirects with the `-L` option
- tar can be given an output directory with the `-C` option

## Print all env vars

```bash
printenv
```

## Find package source for package

```bash
apt-cache policy <package name>
```

## List all installed packages

```bash
apt list --installed
```

## Print all available shells

```bash
cat /etc/shells
```

## Count the number of files in a directory

```bash
find <dir> -type f | wc -l
```

## Get size of directory

```bash
du -sh
```

- `du` estimates the disk usage (du) of the directory recursively in question
- `-s` is to print out size
- `-h` is to make it human readable

## Get all processes you started

```bash
ps -ef | grep `whoami`
```

## Kill all processes you started

```bash
pkill -u `whoami`
```

## Check if two files are the same

```bash
cmp file1 file2
```

- this will output the first byte they mismatch
- if they are the same, it will output nothing

## Changing line endings

```bash
# from windows to linux
find . -type f -exec dos2unix {} \;
# from linux to windows
find . -type f -exec unix2dos {} \;
```

## Print date

```bash
date +"%T"
```

## [Remove last line](https://stackoverflow.com/questions/4881930/remove-the-last-line-from-a-file-in-bash/12349094#12349094)

```bash
head -n -1 foo.txt
```

## [Get filename from given path](https://www.cyberciti.biz/faq/bash-get-filename-from-given-path-on-linux-or-unix/)

```bash
basename /path/to/file
# file
```

## [Monitor changes in a file](https://serverfault.com/questions/1669/shell-command-to-monitor-changes-in-a-file/1670)

```bash
tail -f myFile.txt
```

- good for following the progress of curl when it is outputted to a file
