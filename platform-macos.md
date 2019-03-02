---
tags: dontlink
sitemap: true
---

# macOS

Yggdrasil is well supported on macOS out of the box, thanks to the inclusion of the `utun` driver in macOS.

## Notes

- Tested and working out of the box on macOS 10.13 High Sierra and 10.14 Mojave.
- May work in theory on any macOS version with `utun` support (which was added in macOS 10.7 Lion), although this is untested at present.
- TAP mode is not supported on macOS, therefore the `IfTAPMode` option will have no effect and will default to TUN mode automatically.
- The maximum MTU size supported on macOS is 65535.

## Installation

### Using the macOS installer

Visit the [Builds](builds.md) page and download the relevant `-macos-amd64.pkg` package file.

Once downloaded, open the Downloads folder in Finder, right-click the package and click "Open". (Alternatively, double-click and then use System Preferences > Security to "Open Anyway" after the developer prompt.)

Once installed, Yggdrasil will automatically start in the background. 

### Manual installation

System Integrity Protection in macOS prevents you from copying files into `/usr/bin`, therefore you should install into `/usr/local/bin` instead. This may not exist by default so create it if it doesn't exist:
```
sudo mkdir -p /usr/local/bin
```

Then [download the latest Yggdrasil binary](https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts) and install it:
```
sudo cp ~/Downloads/yggdrasil-x.x.xx-darwin-amd64 /usr/local/bin/yggdrasil
sudo chmod +x /usr/local/bin/yggdrasil
```
Alternatively, compile Yggdrasil from source (below) and install:
```
sudo cp /path/to/yggdrasil-go/yggdrasil /usr/local/bin/yggdrasil
sudo chmod +x /usr/local/bin/yggdrasil
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

### Run as a background service

Running as a background system service means that Yggdrasil will automatically start up in the background when your Mac boots. It also ensures that Yggdrasil will be restarted automatically if the process is terminated for some reason.

You can install Yggdrasil as a launchd service using the launchd scripts in the Git repository. Locate the [`yggdrasil.plist` file in the `contrib` folder](https://raw.githubusercontent.com/yggdrasil-network/yggdrasil-go/master/contrib/macos/yggdrasil.plist) of the repository and make sure that the paths match your installation (i.e. `/usr/local/bin/yggdrasil` and `/etc/yggdrasil.conf`) and then copy it into `/Library/LaunchDaemons/`.

If you already have the source repository to hand, you can instead retrieve `yggdrasil.plist` from there instead of downloading as above:
```
cd /path/to/yggdrasil-go
sudo cp contrib/macos/yggdrasil.plist /Library/LaunchDaemons/
```

Once the `yggdrasil.plist` file is in `/Library/LaunchDaemons/`, you can start the service:
```
sudo launchctl load /Library/LaunchDaemons/yggdrasil.plist
```
When using the launchd scripts from the repository, standard output is logged to `/tmp/yggdrasil.stdout.log` and error output is logged to `/tmp/yggdrasil.stderr.log`.

If you want to stop the Yggdrasil service and prevent it from being restarted by launchd:
```
sudo launchctl unload /Library/LaunchDaemons/yggdrasil.plist
```

## Build instructions

macOS has most of the tools needed to build Yggdrasil from source - you just need to install Go 1.11 or later.

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
