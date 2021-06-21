---
tags: dontlink
sitemap: true
---

# Installing using the macOS installer

Yggdrasil is supported on macOS. Download the macOS `.pkg` file from [GitHub Releases](https://github.com/yggdrasil-network/yggdrasil-go/releases). 

## Install using Finder

Locate the downloaded `.pkg` installer in Finder. Right-click it and click Open.

Step through the installer as usual.

Once completed, the Yggdrasil configuration will have automatically been
generated, the `launchd` service will have been installed and the service will
be running.

## Install using Terminal

Open Terminal.app and install the package:
```
sudo installer -pkg /path/to/yggdrasil-xxx-macos.pkg -target /
```

Once completed, the Yggdrasil configuration will have automatically been
generated, the `launchd` service will have been installed and the service will
be running.

## After installation

Read the [macOS platform page](platform-macos.md) page for further
information about macOS platform support.
