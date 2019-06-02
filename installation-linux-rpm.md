---
tags: dontlink
sitemap: true
---

# Installing on Red Hat Enterprise Linux, Fedora, CentOS and similar

**These instructions apply to any RPM-based distribution with `systemd`.**

RPM binary packages exist to simplify the installation of Yggdrasil. These
will also work on any other RPM-based distribution.


## Install RPM package from COPR repository

There's a [Fedora COPR repository](https://copr.fedorainfracloud.org/coprs/leisteth/yggdrasil/) available, which provides recent versions of Yggdrasil - *not only for Fedora, but also for CentOS/RHEL!*


### Fedora

Installation on Fedora is easy as:

```bash
dnf copr enable leisteth/yggdrasil
dnf install yggdrasil
```

... and you're ready to go!


### CentOS / RHEL

Paste the following into a new file `/etc/yum.repos.d/yggdrasil.repo`:

```
[leisteth-yggdrasil]
name=Copr repo for yggdrasil owned by leisteth
baseurl=https://copr-be.cloud.fedoraproject.org/results/leisteth/yggdrasil/epel-7-$basearch/
type=rpm-md
skip_if_unavailable=True
gpgcheck=1
gpgkey=https://copr-be.cloud.fedoraproject.org/results/leisteth/yggdrasil/pubkey.gpg
repo_gpgcheck=0
enabled=1
enabled_metadata=1
```

*Don't forget to change the `baseurl` according to your RHEL/CentOS version! In the config above, version 7 is used.*

Install yggdrasil:

```bash
yum update
yum install yggdrasil
```


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
