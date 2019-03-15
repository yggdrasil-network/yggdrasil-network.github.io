---
tags: dontlink
sitemap: true
---

# Installing manually on Linux

Yggdrasil is supported on Linux. You can either [download the latest binary from
CircleCI](builds.md) or you can build from source.

## Download binaries

Download the relevant Linux `yggdrasil` and `yggdrasilctl` binaries from the
[Builds](builds.md) page.

Copy these into a suitable location:
```
sudo cp {yggdrasil,yggdrasilctl} /usr/local/bin
```

## Build from source

Linux has most of the tools needed to build Yggdrasil from source - you just
need to install the Go toolchain. Yggdrasil requires Go 1.11 as a minimum.

- Install [the latest version of Go](https://golang.org/dl/) for your platform
  - Using [godeb](https://github.com/niemeyer/godeb) to install Go is
    recommended for Debian-based distributions

Clone the repository and build:
```
cd /path/to
git clone https://github.com/yggdrasil-network/yggdrasil-go
```

Build Yggdrasil:
```
cd /path/to/yggdrasil-go
./build
```

The build will produce `yggdrasil` and `yggdrasilctl` binaries - copy these
into a suitable location:
```
sudo cp {yggdrasil,yggdrasilctl} /usr/local/bin
```

#### Debug builds

You can create a debug build by running `./build -d` instead of
`./build`. Debug builds contain profiling code as well as additional debugging
symbols. They are likely to be larger files as a result.

## Install systemd service

systemd service scripts are included in the `contrib/systemd/` folder so that it
runs automatically in the background (using `/etc/yggdrasil.conf` for
configuration).

Create the `yggdrasil` group on your system - the `systemd` service will run
with this group:
```
sudo groupadd --system yggdrasil
```

Copy the service files:
```
sudo cp contrib/systemd/yggdrasil.service /etc/systemd/system
sudo systemctl daemon-reload
```

Enable and start Yggdrasil:
```
sudo systemctl enable yggdrasil
sudo systemctl start yggdrasil
```

Once installed as a systemd service, you can read the `yggdrasil` output using
`systemctl` or `journalctl`:
```
systemctl status yggdrasil
journalctl -u yggdrasil
```

## Generate configuration

Generate an initial Yggdrasil configuration file:
```
sudo yggdrasil -genconf > /etc/yggdrasil.conf
```

You can make changes to this file as described in the
[Configuration](configuration.md) page.

## After installation

Read the [Linux platform page](platform-linux.md) page for further
information about Linux platform support.
