---
layout: page
title: VPN with WSL
permalink: /terminal/zsh/vpn-with-wsl
---

## Make vpn work with wsl

    ```bash
    grep -q 'nameserver <your vpn server>' /etc/resolv.conf
    if [ $? -eq 1 ]; then
        sed -i '/# This file/ a nameserver <your vpn server>' /etc/resolv.conf
    fi
    ```