---
tags: dontlink
sitemap: true
---

# Installing manually on macOS

Yggdrasil is supported on macOS. You can either [download the latest binary from
CircleCI](builds.md) or you can build from source.

## Download binaries

Download the relevant macOS `yggdrasil` and `yggdrasilctl` binaries from the
[Builds](builds.md) page.

System Integrity Protection in macOS prevents you from copying files into
`/usr/bin`, therefore you should install into `/usr/local/bin`:
```
sudo cp {yggdrasil,yggdrasilctl} /usr/local/bin
```

## Build from source

macOS has most of the tools needed to build Yggdrasil from source - you just
need to install the Go toolchain. Yggdrasil requires Go 1.11 as a minimum.

- Install [the latest version of Go](https://golang.org/dl/) for your platform

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

The build will produce `yggdrasil` and `yggdrasilctl` binaries. System Integrity
Protection in macOS prevents you from copying files into `/usr/bin`, therefore
you should install into `/usr/local/bin`:
```
sudo cp {yggdrasil,yggdrasilctl} /usr/local/bin
```

#### Debug builds

You can create a debug build by running `./build -d` instead of
`./build`. Debug builds contain profiling code as well as additional debugging
symbols. They are likely to be larger files as a result.

## Install launchd service

launchd service scripts are included in the `contrib/macos/` folder so that it
runs automatically in the background (using `/etc/yggdrasil.conf` for
configuration).

Copy the service files:
```
sudo cp contrib/macos/yggdrasil.plist /Library/LaunchDaemons/
```

Enable and start Yggdrasil:
```
sudo launchctl load /Library/LaunchDaemons/yggdrasil.plist
sudo launchctl start /Library/LaunchDaemons/yggdrasil.plist
```

Once installed as a launchd service, you can read the `yggdrasil` output in the
following files:
```
tail -f /tmp/yggdrasil.stdout.log
tail -f /tmp/yggdrasil.stderr.log
```

## Generate configuration

Generate an initial Yggdrasil configuration file:
```
sudo yggdrasil -genconf > /etc/yggdrasil.conf
```

You can make changes to this file as described in the
[Configuration](configuration.md) page.

## After installation

Read the [macOS platform page](platform-macos.md) page for further
information about macOS platform support.
