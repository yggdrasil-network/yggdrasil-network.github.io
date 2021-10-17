---
tags: dontlink
sitemap: true
---

# Installing on OpenWrt

`yggdrasil` v0.4+ packages are available in OpenWRT since 21.02.

To configure Yggdrasil with the LuCI web interface, install the package `luci-app-yggdrasil`.

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
