# cmd

## Create a symbolic link with a network drive
```cmd
mklink /D C:\local\symbolic\link \\some_network_drive\some\path
```

## Quickly Create Large Test Files
- [This article](https://tweaks.com/windows/62755/quickly-generate-large-test-files-in-windows/) explains this
- needs cmd run as admin

## Time servers
- print info: `w32tm /query /peers`
- resync: `w32tm /resync`