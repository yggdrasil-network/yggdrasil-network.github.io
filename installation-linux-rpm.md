---
tags: dontlink
sitemap: true
---

# Installing on Red Hat Enterprise Linux, Fedora, CentOS and similar

**These instructions apply to any RPM-based distribution with `systemd`.**

RPM binary packages exist to simplify the installation of Yggdrasil. These
will also work on any other RPM-based distribution.

## Package install from the S3 repository

To start with, import the repository key to your `gpg` keyring and export it
to your `rpm` keyring:
```
gpg --fetch-keys https://neilalexander.s3.dualstack.eu-west-2.amazonaws.com/deb/key.txt
gpg --armor --no-comment --export-options export-minimal --export 569130E8CA20FBC4CB3FDE555898470A764B32C9 | sudo tee /etc/pki/rpm-gpg/RPM-GPG-KEY-yggdrasil
```

Add the repository:
```
echo '[yggdrasil]
name = Yggdrasil
baseurl = https://neilalexander.s3.dualstack.eu-west-2.amazonaws.com/rpm/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RP^CGPG-KEY-yggdrasil' | sudo tee /etc/yum.repos.d/yggdrasil.repo
```

Create the `yggdrasil` group on your system:
```
sudo groupadd --system yggdrasil
```

Install Yggdrasil:
```
sudo dnf install yggdrasil
```
Configuration will be generated automatically into `/etc/yggdrasil.conf` when
the package is installed, and the Yggdrasil service will automatically be
installed into `systemd`.

Enable and start the service after install/upgrade:
```
sudo systemctl enable yggdrasil
sudo systemctl start yggdrasil
```

## One-off package install from CircleCI

Visit our [Builds](builds.md) page and download the relevant `.rpm` file, then
install it on your system:
```
sudo groupadd --system yggdrasil
sudo rpm -i yggdrasil...rpm
```
Configuration will be generated automatically into `/etc/yggdrasil.conf` when
the package is installed, and the Yggdrasil service will automatically be
installed into systemd and started.

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
