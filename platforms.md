---
sitemap: true
---

# Platform Notes

## Linux

- See the [Linux platform page](platform-linux.md).

## macOS

- See the [macOS platform page](platform-macos.md).

## iOS

- See the [iOS platform page](platform-ios.md).

## EdgeRouter

- See the [EdgeRouter platform page](platform-edgerouter.md).

## FreeBSD, NetBSD

- Works in TAP mode, but currently doesn't work in TUN mode.
- You may need to create the TAP adapter first if it doesn't already exist, i.e. `ifconfig tap0 create`.
- A [FreeBSD service script](https://github.com/yggdrasil-network/yggdrasil-go/blob/master/contrib/freebsd/yggdrasil) is available in the `contrib` folder. This might be adaptable to other BSDs.

## OpenBSD

- Yggdrasil is well supported on OpenBSD.
- IfName should only contain the interface name instead of the full path, i.e. `tun0` not `/dev/tun0`
