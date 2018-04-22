---
tags: dontlink
---

# Windows

Yggdrasil has been tested and is working on the following versions of Windows:

- Windows 7
- Windows 10
- Windows Server 2016

It may work on other versions of Windows too, although these are untested.

## TAP Driver

Yggdrasil depends on the OpenVPN TAP driver to work on Windows. There are two flavours of this driver:

- [NDIS 5](https://swupdate.openvpn.org/community/releases/tap-windows-9.9.2_3.exe) (`tap-windows-9.9.2_3`) - recommended
- [NDIS 6](https://swupdate.openvpn.org/community/releases/tap-windows-9.21.2.exe) (`tap-windows-9.21.2`) - functional

Yggdrasil works with either driver, although the performance is remarkably better with the older NDIS 5 driver, therefore it is recommended to use that instead of the NDIS 6 driver.

Please note that if you already have OpenVPN for Windows, you likely have one of the two drivers installed already and should not install it again.

## Notes

- TUN mode is not supported on Windows, and attempts to configure TUN mode will default to TAP mode automatically.

## Installation

Start by either [downloading the latest Yggdrasil binary](https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts) and use Windows Explorer to copy it into a sensible location. The below examples assume `C:\Program Files\Yggdrasil`.

## Generate configuration

Before starting Yggdrasil, you should generate configuration. Open a Command Prompt as Administrator:
```
"C:\Program Files\Yggdrasil\yggdrasil.exe" > "C:\Program Files\Yggdrasil\yggdrasil.conf"
```

## Run Yggdrasil

### Run once

Open a Command Prompt as Administrator and start Yggdrasil using your generated configuration:
```
"C:\Program Files\Yggdrasil\yggdrasil.exe" -useconffile "C:\Program Files\Yggdrasil\yggdrasil.conf"
```
Alternatively, start Yggdrasil in auto-configuration mode:
```
"C:\Program Files\Yggdrasil\yggdrasil.exe" -autoconf
```

### Run as a background service

Running as a background system service means that Yggdrasil will automatically start up in the background when Windows boots.

Assuming that Yggdrasil is installed into `C:\Program Files\Yggdrasil` and your configuration *already exists* in `C:\Program Files\Yggdrasil\yggdrasil.conf`, as above, then you can install Yggdrasil as a Windows service. Open a Command Prompt as Administrator:
```
sc create yggdrasil binpath= "\"C:\Program Files\Yggdrasil\yggdrasil.exe\" -useconffile \"C:\Program Files\Yggdrasil\yggdrasil.conf\""
sc config yggdrasil displayname= "Yggdrasil Service"
sc config yggdrasil start= "auto"
sc start yggdrasil
```
Alternatively, if you want the service to autoconfigure instead of using an `yggdrasil.conf`, replace the `sc create` line from above with:
  ```
  sc create yggdrasil binpath= "\"C:\Program Files\Yggdrasil\yggdrasil.exe\" -autoconf"
  ```

The Yggdrasil service can then be stopped and started using `services.msc`, or in more recent versions of Windows, the Task Manager.
