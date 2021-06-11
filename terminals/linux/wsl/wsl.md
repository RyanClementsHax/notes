# WSL

## [Global options](https://docs.microsoft.com/en-us/windows/wsl/wsl-config#configure-global-options-with-wslconfig)

## GUI support
- eventually we will have this out of the box
- the current [work around](https://autoize.com/xfce4-desktop-environment-and-x-server-for-ubuntu-on-wsl-2/) is to use an `X-Server`
  - doesn't work that great in my experience
  - you may need to reset your graphics driver for wsl if you configure this wrong (`sudo dpkg-reconfigure lightdm`)

## Troubleshooting
- [local sites running in wsl2 not accessible in browser](https://github.com/microsoft/WSL/issues/5298)
  - seems like the best solution is to do `wsl --shutdown` and then restart it (do know that this stops docker in wsl too and needs to be restarted)