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

## Installing on OpenWrt 18.06 and below

Officially, there is no package support on the OpenWrt 18.06, so you may have any problems.
```
opkg update
opkg install http://downloads.openwrt.org/releases/packages-19.07/xxxxxx_xxx/packages/yggdrasil_x.x.x-x_xxxxxx_xxx.ipk
```
Where xxxxxx_xxx your architecture, and yggdrasil_x.x.x-x_xxxxxx_xxx.ipk name of package
For example 
```
opkg install http://downloads.openwrt.org/releases/packages-19.07/mipsel_24kc/packages/yggdrasil_0.3.5-4_mipsel_24kc.ipk
```
This method does not guarantee a successful installation

## After installation

Read the [OpenWrt platform page](platform-openwrt.md) page for information
on how to configure Yggdrasil and guidance on modifying the configuration.
