---
tags: dontlink
sitemap: true
---

# Installing on OpenWrt

`yggdrasil` v0.4+ packages are available in OpenWRT since 17.01!

To configure Yggdrasil with the LuCI web interface, install the package `luci-proto-yggdrasil`.

## Configuration

Use the LuCI web interface to configure Yggdrasil, see the "Network" section.

Alternatively, you can configure Yggdrasil with just a command line.

To show currently configured options, run the following command:

```
uci show yggdrasil
```

To add a new peer:

```
uci add yggdrasil peer
uci set yggdrasil.@peer[-1].uri='tcp://1.2.3.4:5678'
uci commit
/etc/init.d/yggdrasil restart
```

## OpenWrt Yggdrasil on Matrix

Please stop by `#yggdrasil-openwrt:matrix.org` to join the community development channel.


## NodeInfo enabled by default

The Yggdrasil daemon will provide some basic NodeInfo information to the mesh by default.  
This can be disabled by configuring the section in `/etc/config/yggdrasil` or LuCI admin.  

This means the following example can be queried by others in the network:

```
"NodeInfo": {
  "kernel": "4.14.123",
  "hostname": "OpenWrt",
  "model": "Globalscale Marvell ESPRESSOBin Board",
  "board_name": "globalscale,espressobin"
}
```
