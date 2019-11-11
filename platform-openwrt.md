---
tags: dontlink
sitemap: true
---

# OpenWrt

Yggdrasil is well supported for OpenWrt next release. Previous release series may not directly support automatic building & packaging of yggdrasil. If you can, update your gear to Snapshots.

| Series           | Version | Buildroot | SDK | ImageBuilder |
| ---------------- | ------- | --------- | --- | ------------ |
| Latest Snapshots | 19.07.+ | ✔         | ✔   | ✔            |
| Current Stable   | 18.06.+ | ⍰         | ⍰   | ❌            |
| Last Stable      | 17.01.+ | ⍰         | ⍰   | ❌            |



## Recommended: Snapshots 19.07.+

#### From Buildroot, SDK and ImageBuilder
Yggdrasil is primarily supported on 19.07.+ It is recommended to use this release target because the package will receive updates through OpenWrt Feeds. Please follow the OpenWrt documentation to build Yggdrasil from source, sdk and imagebuilder.

#### Within OpenWrt

To install on a running system simply run the two opkg  commands:  
```
opkg update
opkg install yggdrasil
```

#### Automatic Firewall
The yggdrasil package initialization handles firewall zone creation with a default drop of all INPUT traffic for interface `ygg0`. SSH and LuCI can be accessed over the network by enabling the new yggdrasil-ssh or yggdrasil-luci firewall rules.

The yggdrasil configuration file is written to `/etc/yggdrasil.conf`.   

Yggdrasil installation scripts copy `hostname` and details from OpenWrt `board.json` for the final yggdrasil config file. The details are stuffed into the `NodeInfo` section.

#### Automatic NodeInfo
This section of node information can be queried by discovery bots on the yggdrasil-network. See yggdrasil Map for example.

```
"NodeInfo": {
  "kernel": "4.14.123",
  "hostname": "OpenWrt",
  "model": "Globalscale Marvell ESPRESSOBin Board",
  "board_name": "globalscale,espressobin"
}
```

## Issues, Merge and Feature Requests


Please file Issues under Github [openwrt/packages](https://github.com/openwrt/packages/)  
For the yggdrasil package source see [openwrt/packages/net/yggdrasil](https://github.com/openwrt/packages/tree/master/net/yggdrasil)  


## Current Stable & Last Stable: 18.06.+ and 17.01.+

OpenWrt series the Buildroot, SDK and ImageBuilder will copy the contents of `./files` to the rootfs partition. Yggdrasil can be cross compiled with correct matching `GOARCH` variables to suit the following OpenWrt architectures.

#### Cross compiling yggdrasil

| OpenWrt Target Architecture | Golang Target Architecture |
| --------------------------- | -------------------------- |  
| aarch64                     | `GOARCH=arm64`             |
| arc                         | `GOARCH=risc`              |
| arc                         | `GOARCH=risc`              |
| arm                         | `GOARCH=arm`               |
| armeb                       | `GOARCH=armbe`             |
| i386                        | `GOARCH=386`               |
| mips                        | `GOARCH=mips`              |
| mips64                      | `GOARCH=mips64`            |
| mipsel                      | `GOARCH=mipsle`            |
| powerpc                     | `GOARCH=ppc64`             |

1. Use the specific to GOARCH variable and `./build` yggdrasil
2. From OpenWrt SDK or ImageBuilder directory create directory `./files/usr/bin/`, copy the yggdrasil binary into this path, then run `make` from the top build directory.

The resulting firmware image will have the yggdrasil binary found at `/usr/bin/yggdrasil`.
