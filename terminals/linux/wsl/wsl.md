# WSL

## [Global options](https://docs.microsoft.com/en-us/windows/wsl/wsl-config#configure-global-options-with-wslconfig)

## GUI support
- eventually we will have this out of the box
- the current [work around](https://autoize.com/xfce4-desktop-environment-and-x-server-for-ubuntu-on-wsl-2/) is to use an `X-Server`
  - doesn't work that great in my experience
  - you may need to reset your graphics driver for wsl if you configure this wrong (`sudo dpkg-reconfigure lightdm`)