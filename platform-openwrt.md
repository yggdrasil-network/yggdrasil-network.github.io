---
tags: dontlink
sitemap: true
---

# OpenWrt

Yggdrasil is supported on OpenWrt 19.07 and above.

## Notes
- You may need change your firewall config for peering local hosts
- After installation you may need to set up your config it is /etc/yggdrasil.conf

## Default Config
You can look all default setting on https://github.com/openwrt/packages/blob/openwrt-19.07/net/yggdrasil/files/yggdrasil.defaults


## Default Config Firewall

By default, script creates the following rules:

```
allow ICMP from yggdrasil zone, e.g. ping6
allow SSH from yggdrasil zone (port 22)
allow LuCI access from yggdrasil zone (port 80)
```

It is stored in /etc/config/firewall

## Control commands

Syntax: /etc/init.d/yggdrasil [command]

Available commands:
```
        start   Start the service
        stop    Stop the service
        restart Restart the service
        reload  Reload configuration files (or restart if service does not implement reload)
        enable  Enable service autostart
        disable Disable service autostart
```
