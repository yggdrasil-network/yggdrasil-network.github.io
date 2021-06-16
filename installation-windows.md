---
tags: dontlink
sitemap: true
---

# Installing using the Windows installer

Yggdrasil is supported on Windows. You can [download the latest installer from
GitHub](https://github.com/yggdrasil-network/yggdrasil-go/releases).

On Windows 7 and Server 2008 R2, you must install hotfix KB2921916 before installing
Yggdrasil:

* [KB2921916 for 64-bit systems](https://download.wireguard.com/windows-toolchain/distfiles/Windows6.1-KB2921916-x64.msu)
* [KB2921916 for 32-bit systems](https://download.wireguard.com/windows-toolchain/distfiles/Windows6.1-KB2921916-x86.msu)

## TUN driver

Starting with version 0.3.13, Yggdrasil on Windows uses the Wireguard TUN
driver. If this is not installed on the system already, it will be installed
automatically by the Yggdrasil installer.

For this reason, it is important that you use the correct installer for your
architecture - make sure to use the `x64` installer on 64-bit Windows and the
`x86` installer on 32-bit Windows.

Please note that the OpenVPN TAP driver is **no longer supported**.

Once Yggdrasil is started, a new virtual network adapter will be created called
`Yggdrasil` by default, although this can be renamed using the `IfName` option
in the configuration file (below). The virtual network adapter will *not* be
visible on the system when Yggdrasil is not running.

## Configuration

The Yggdrasil installer will automatically generate an `yggdrasil.conf`
configuration file, if one does not exist, in:

```
%ALLUSERSPROFILE%\Yggdrasil\yggdrasil.conf
```

## Windows Service

Yggdrasil is installed as a Windows service that starts automatically with
Windows. You can start, stop and restart Yggdrasil using the Services MMC
snap-in (`services.msc`) or, in more recent versions of Windows, the "Services"
tab in Task Manager.

You will need to restart the Yggdrasil service after each change to the
configuration file.

## Windows Firewall

Windows Firewall may prompt for the Yggdrasil process to allow incoming or
outgoing connections. If so, you should allow this or Yggdrasil may not be able
to establish peerings correctly.

Note that Yggdrasil, by default, does allow incoming traffic over the
`Yggdrasil` virtual adapter, therefore you may wish to designate the `Yggdrasil`
virtual network adapter as a public network in the Windows Firewall so that
unexpected incoming connections are blocked automatically.

If you do, pay particular attention to ensure that file sharing (SMB), remote
procedure call (RPC) or remote desktop (RDP) services are not allowed on public
networks unless you explicitly need them to be accessible from remote Yggdrasil
hosts.

## yggdrasilctl

The `yggdrasilctl` utility is also installed into the same location above. You
can query Yggdrasil's runtime state using this tool using a Command Prompt or
PowerShell command line, e.g.
```
"C:\Program Files\Yggdrasil\yggdrasilctl.exe" getPeers
```
