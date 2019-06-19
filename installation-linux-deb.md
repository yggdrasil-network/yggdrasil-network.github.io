---
tags: dontlink
sitemap: true
---

# Installing on Debian, Ubuntu, elementaryOS, Linux Mint and similar

**These instructions apply to any Debian-based distribution with `systemd`.**

Yggdrasil is supported on Debian Linux. Debian binary packages exist to simplify
the installation of Yggdrasil. These will also work on any Debian-based
distribution, e.g. elementaryOS.

## Package install from the S3 repository

On some platforms, e.g. Raspberry Pi, you may need to start by installing
`dirmngr` if it is not already available:
```
sudo apt-get install dirmngr
```

Then import the repository key to your `gpg` keyring and export it to your
`apt` keyring:
```
gpg --fetch-keys https://neilalexander.s3.dualstack.eu-west-2.amazonaws.com/deb/key.txt
gpg --export 569130E8CA20FBC4CB3FDE555898470A764B32C9 | sudo apt-key add -
```

Add the repository into your `apt` sources:
```
echo 'deb http://neilalexander.s3.dualstack.eu-west-2.amazonaws.com/deb/ debian yggdrasil' | sudo tee /etc/apt/sources.list.d/yggdrasil.list
sudo apt-get update
```

**Note**: The repository also works over HTTPS - you will need to `sudo apt-get install apt-transport-https` and then edit the above URL so that it starts with `https://` instead of `http://` in `/etc/apt/sources.list.d/yggdrasil.list`.

Install Yggdrasil:
```
sudo apt-get install yggdrasil
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

Visit our [Builds](builds.md) page and download the relevant `.deb` file, then
install it on your system. If you want to install the latest `.deb` from the
`master` branch:
```
curl -so- "https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts?branch=master&filter=successful" | \
egrep -o "https.*yggdrasil\-.*$(dpkg --print-architecture).deb" | \
while read line; do curl -O $line && dpkg -i $(basename $line); done
```
Configuration will be generated automatically into `/etc/yggdrasil.conf` when
the package is installed, and the Yggdrasil service will automatically be
installed into systemd and started.

### Making configuration changes

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
