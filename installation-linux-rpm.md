---
tags: dontlink
sitemap: true
---

# Installing on Red Hat Enterprise Linux, Fedora, CentOS and similar

**These instructions apply to any RPM-based distribution with `systemd`.**

RPM binary packages exist to simplify the installation of Yggdrasil. These
will also work on any other RPM-based distribution.

## Install RPM package from COPR repository

There's a [Fedora COPR repository](https://copr.fedorainfracloud.org/coprs/neilalexander/yggdrasil-go/) available, which provides recent versions of Yggdrasil for some RPM-based distributions.

### Fedora

Installation on Fedora is easy as:

```bash
dnf copr enable neilalexander/yggdrasil-go
dnf install yggdrasil
```

... and you're ready to go!

## Making configuration changes

Modify the `/etc/yggdrasil.conf` file and then either restart the Yggdrasil daemon:
```
systemctl restart yggdrasil
```

## After installation

Read the [Linux platform page](configuration.html) page for further
information about Linux platform support.
