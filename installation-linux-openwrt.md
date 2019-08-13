---
tags: dontlink
sitemap: true
---

# Installing on OpenWrt 19.07

Yggdrasil is supported on the OpenWrt based router.


Perform installation steps over SSH by connecting to the Router as the
`root` user, e.g. `ssh root@192.168.1.1`, or another admin-level user if
configured.

## Package install from repository

Install the `yggdrasil` package:
```
opkg update
opkg install yggdrasil
```

Yggdrasil can then be updated in the future by updating the packages:
```
opkg update
opkg upgrade yggdrasil
```

## After installation

Read the [OpenWrt platform page](platform-openwrt.md) page for information
on how to configure Yggdrasil and guidance on modifying the configuration.
