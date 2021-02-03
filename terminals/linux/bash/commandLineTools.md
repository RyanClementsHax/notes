# Command Line Tools

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