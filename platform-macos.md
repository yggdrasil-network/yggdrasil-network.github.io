---
tags: dontlink
---

# macOS

Yggdrasil is well supported on macOS out of the box, thanks to the inclusion of the `utun` driver in macOS.

## Notes

- Tested and working out of the box on macOS 10.13 High Sierra.
- May work in theory on any macOS version with `utun` support (which was added in macOS 10.7 Lion), although this is untested at present.
- TAP mode is not supported on macOS, therefore the `IfTAPMode` option will have no effect.
- The maximum MTU size supported on macOS is 65535.

## Installation

Start by either [downloading the latest Yggdrasil binary](https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts) and install it:
```
sudo cp ~/Downloads/yggdrasil-x.x.xx-darwin-amd64 /usr/bin/yggdrasil
```
Alternatively, compile Yggdrasil from source (below) and install:
```
sudo cp /path/to/yggdrasil-go/yggdrasil /usr/bin/yggdrasil
```

## Generate configuration

Before starting Yggdrasil, you should generate configuration:
```
sudo yggdrasil -genconf > /etc/yggdrasil.conf
```

## Run Yggdrasil

### Run once

Open Terminal.app and start Yggdrasil using your generated configuration:
```
sudo nohup yggdrasil -useconffile /etc/yggdrasil.conf &
```
Alternatively, start Yggdrasil in auto-configuration mode:
```
sudo nohup yggdrasil -autoconf &
```

### Run as a system service

Running as a system service means that Yggdrasil will automatically start up in the background when your Mac boots. It also ensures that Yggdrasil will be restarted automatically if the process is terminated for some reason.

Assuming that Yggdrasil is installed into `/usr/bin` and your configuration *already exists* in `/etc`, as above, then you can install Yggdrasil as a launchd service using the launchd scripts in the Git repository:
```
cd /path/to/yggdrasil-go
sudo cp contrib/macos/yggdrasil.plist /Library/LaunchDaemons/
sudo launchctl load /Library/LaunchDaemons/
```
When using the launchd scripts from the repository, standard output is logged to `/tmp/yggdrasil.stdout.log` and error output is logged to `/tmp/yggdrasil.stderr.log`. 

## Build instructions

macOS has most of the tools needed to build Yggdrasil from source - you just need to install Go 1.9 or later.

1. Download the latest [Go .pkg installer for macOS](https://golang.org/dl/)
1. Run the installer to install Go on your system
1. Open Terminal.app, clone the repository and build:
```
cd /path/to
git clone https://github.com/yggdrasil-network/yggdrasil-go
cd yggdrasil-go
./build
```
1. The resulting `yggdrasil` file is your build - optionally install it into your system, as above.
