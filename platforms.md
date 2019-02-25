---
sitemap: true
---

# Platforms

## Linux

- See the [Linux platform page](platform-linux.md).

## macOS

- See the [macOS platform page](platform-macos.md).

## iOS

- See the [iOS platform page](platform-ios.md).

## Windows

- See the [Windows platform page](platform-windows.md).

## EdgeRouter

- See the [EdgeRouter platform page](platform-edgerouter.md).

## FreeBSD, NetBSD

- Works in TAP mode, but currently doesn't work in TUN mode.
- You may need to create the TAP adapter first if it doesn't already exist, i.e. `ifconfig tap0 create`.
- A [FreeBSD service script](https://github.com/yggdrasil-network/yggdrasil-go/blob/master/contrib/freebsd/yggdrasil) is available in the `contrib` folder. This might be adaptable to other BSDs.

## OpenBSD

- Works in TAP mode, but currently doesn't work in TUN mode.
- You may need to create the TAP adapter first if it doesn't already exist, i.e. `ifconfig tap0 create`.
- OpenBSD is not capable of listening on both IPv4 and IPv6 at the same time on the same socket (unlike FreeBSD and NetBSD). This affects the `Listen` and `AdminListen` configuration options. You will need to set `Listen` and `AdminListen` to use either an IPv4 or an IPv6 address.
- You may consider using [relayd](https://man.openbsd.org/relayd.8) to allow incoming Yggdrasil connections on both IPv4 and IPv6 simultaneously.
