---
tags: dontlink
sitemap: true
---

# Installing on Debian, Ubuntu, elementaryOS, Linux Mint and similar

**These instructions apply to any Debian-based distribution with `systemd`.**

Yggdrasil is supported on Debian Linux. Debian binary packages exist to simplify
the installation of Yggdrasil. These will also work on any Debian-based
distribution, e.g. elementaryOS.

## One-off installation

Debian packages are built and are available in the [GitHub Releases](https://github.com/yggdrasil-network/yggdrasil-go/releases).
Install the package using `dpkg -i filename.deb`.

## Package install from the S3 repository

On some platforms, e.g. Raspberry Pi, you may need to start by installing
`dirmngr` if it is not already available:
```
sudo apt-get install dirmngr
```

Then import the repository key to your `gpg` keyring and export it to your
`apt` keyring:
```
sudo mkdir -p /etc/apt/keyrings
sudo curl https://neilalexander.s3.dualstack.eu-west-2.amazonaws.com/deb/key.txt -o /etc/apt/keyrings/yggdrasil.asc
```

Add the repository into your `apt` sources:
```
echo 'deb [signed-by=/etc/apt/keyrings/yggdrasil.asc] http://neilalexander.s3.dualstack.eu-west-2.amazonaws.com/deb/ debian yggdrasil' | sudo tee /etc/apt/sources.list.d/yggdrasil.list
sudo apt-get update
```

**Note**: The repository also works over HTTPS - you will need to `sudo apt-get install apt-transport-https` and then edit the above URL so that it starts with `https://` instead of `http://` in `/etc/apt/sources.list.d/yggdrasil.list`.

Install Yggdrasil:
```
sudo apt-get install yggdrasil
```

## Package install from your distribution

Some Debian-based distributions may have an `yggdrasil-go` package available in
their repositories:
```
sudo apt-get install yggdrasil
```

⚠️ **WARNING**: Distribution packages are often out-of-date! If you are unable to connect to peers, this is probably due to a minor (0.x) version mismatch. Use `apt show yggdrasil` to verify the package version against the latest [GitHub Releases](https://github.com/yggdrasil-network/yggdrasil-go/releases).

## After installation

Configuration will be generated automatically into `/etc/yggdrasil.conf` when
the package is installed, and the Yggdrasil service will automatically be
installed into `systemd`.

Enable and start the service after install/upgrade:
```
sudo systemctl enable yggdrasil
sudo systemctl start yggdrasil
```

To make configuration changes, modify the `/etc/yggdrasil.conf` file and then restart the Yggdrasil daemon:
```
systemctl restart yggdrasil
```

Read the [Linux platform page](configuration.md) page for further
information about Linux platform support.
