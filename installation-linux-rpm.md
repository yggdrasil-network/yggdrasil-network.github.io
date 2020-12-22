---
tags: dontlink
sitemap: true
---

# Installing on Red Hat Enterprise Linux, Fedora, CentOS and similar

**These instructions apply to any RPM-based distribution with `systemd`.**

RPM binary packages exist to simplify the installation of Yggdrasil. These
will also work on any other RPM-based distribution.


## Install RPM package from COPR repository

There's a [Fedora COPR repository](https://copr.fedorainfracloud.org/coprs/rany/yggdrasil/) available, which provides recent versions of Yggdrasil - *only for Fedora!*


### Fedora

Installation on Fedora is easy as:

```bash
dnf copr enable rany/yggdrasil
dnf install yggdrasil
```

... and you're ready to go!


## One-off package install from CircleCI (not recommended!)

Visit our [Builds](builds.md) page and download the relevant `.rpm` file, then
install it on your system:
```
sudo groupadd --system yggdrasil
sudo rpm -i yggdrasil...rpm
```
Configuration will be generated automatically into `/etc/yggdrasil.conf` when
the package is installed, and the Yggdrasil service will automatically be
installed into systemd and started.

**Please note: By using this installation method, you won't receive (security-/) updates for Yggdrasil!**


## Making configuration changes

Modify the `/etc/yggdrasil.conf` file and then either reload the config:
```
systemctl reload yggdrasil
```
... or restart the Yggdrasil daemon altogether:
```
systemctl restart yggdrasil
```

## After installation

Read the [Linux platform page](platform-linux.md) page for further
information about Linux platform support.
