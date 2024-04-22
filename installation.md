---
sitemap: true
---

# Installation

Yggdrasil can be downloaded for major platforms from our [GitHub Releases](https://github.com/yggdrasil-network/yggdrasil-go/releases) page. Additionally, installation guides are available for the following platforms/distributions.

Once you have installed Yggdrasil, take a look at:

- [Configuration](configuration.md) — information about configuring Yggdrasil
- [Public Peers](https://github.com/yggdrasil-network/public-peers) — public nodes to connect to
- [Public Services](services.md) — services running inside the Yggdrasil Network

---

🚨 **Please remember to check for Yggdrasil updates regularly.** 🚨 Yggdrasil does not have a built-in method of notifying you when new versions are available. You may want to subscribe to the [GitHub Releases Atom feed](https://github.com/yggdrasil-network/yggdrasil-go/releases.atom) or consider [watching the GitHub repository](https://github.com/yggdrasil-network/yggdrasil-go) (a "Custom" watch can be used to subscribe to release notifications only). Release notifications are also sent in our [Matrix room](https://matrix.to/#/#yggdrasil:matrix.org).

💡 **For secure operation without exposing local ports**, consider using [Yggstack](https://github.com/yggdrasil-network/yggstack). It provides SOCKS5 proxy server and TCP port forwarder functionalities, enabling network access without needing root/administrator privileges and TUN adapter support.

---

### Linux

Yggdrasil is well supported on Linux. There are a number of distribution packages and additional installation instructions available:

- [Debian, Ubuntu, elementaryOS, Linux Mint and similar](installation-linux-deb.md)
- [Red Hat Enterprise Linux, Fedora, CentOS and similar](installation-linux-rpm.md)
- [Gentoo Linux](installation-linux-gentoo.md)
- [OpenWrt](installation-linux-openwrt.md)
- [Manual install or build from source](installation-linux-other.md)

### macOS

Yggdrasil is well supported on macOS. The preferred installation method is the `.pkg` installer:

- [macOS `.pkg` installer](installation-macos-pkg.md)
- [Manual install or build from source](installation-macos-other.md)

### Windows

Yggdrasil works on Windows and [an MSI installer is available](installation-windows.md), but the installer is supported on a best-effort basis only and is not well tested.

### iOS

Yggdrasil is supported on iOS with the following applications:

- [Yggdrasil for iOS on TestFlight](https://testflight.apple.com/join/jZNsIkRr), reference client based on the [v0.4 release candidate](https://yggdrasil-network.github.io/2021/06/19/preparing-for-v0-4.html) ([source code](https://github.com/yggdrasil-network/yggdrasil-ios))

### Android

Yggdrasil is supported on Android with the following applications:

- [Yggdrasil for Android](https://github.com/yggdrasil-network/yggdrasil-android/releases), reference client based on the [v0.4 release candidate](https://yggdrasil-network.github.io/2021/06/19/preparing-for-v0-4.html)
- [Crispa for Android](https://github.com/yggdrasil-network/crispa-android/releases), contributed by the community

### FreeBSD

Yggdrasil is well supported on FreeBSD and [an official `net/yggdrasil` port](https://www.freshports.org/net/yggdrasil/) is available.

### Ubiquiti EdgeRouter

Yggdrasil is well supported on EdgeOS 2.x and [packages are available](installation-linux-edgeos.md).
