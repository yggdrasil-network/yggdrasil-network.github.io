# Platforms

## Linux

- Should work out of the box on most Linux distributions with `iproute2` installed.
- systemd service scripts are included in the `contrib/systemd/` folder so that it runs automatically in the background (using `/etc/yggdrasil.conf` for configuration), copy the service files into `/etc/systemd/system`, copy `yggdrasil` into your `$PATH`, i.e. `/usr/bin`, and then enable the service:
```
systemctl enable yggdrasil
systemctl start yggdrasil
```
- Once installed as a systemd service, you can read the `yggdrasil` output:
```
systemctl status yggdrasil
journalctl -u yggdrasil
```

## macOS

- Tested and working out of the box on macOS 10.13 High Sierra.
- May work in theory on any macOS version with `utun` support (which was added in macOS 10.7 Lion), although this is untested at present.
- TAP mode is not supported on macOS.

## FreeBSD, NetBSD

- Works in TAP mode, but currently doesn't work in TUN mode.
- You may need to create the TAP adapter first if it doesn't already exist, i.e. `ifconfig tap0 create`.

## OpenBSD

- Works in TAP mode, but currently doesn't work in TUN mode.
- You may need to create the TAP adapter first if it doesn't already exist, i.e. `ifconfig tap0 create`.
- OpenBSD is not capable of listening on both IPv4 and IPv6 at the same time on the same socket (unlike FreeBSD and NetBSD). This affects the `Listen` and `AdminListen` configuration options. You will need to set `Listen` and `AdminListen` to use either an IPv4 or an IPv6 address.
- You may consider using [relayd](https://man.openbsd.org/relayd.8) to allow incoming Yggdrasil connections on both IPv4 and IPv6 simultaneously.

## Windows

- Tested and working on Windows 7 and Windows 10, and should work on any recent versions of Windows, but it depends on the [OpenVPN TAP driver](https://openvpn.net/index.php/open-source/downloads.html) being installed first.
- Has been proven to work with both the [NDIS 5](https://swupdate.openvpn.org/community/releases/tap-windows-9.9.2_3.exe) (`tap-windows-9.9.2_3`) driver and the [NDIS 6](https://swupdate.openvpn.org/community/releases/tap-windows-9.21.2.exe) (`tap-windows-9.21.2`) driver, however there are substantial performance issues with the NDIS 6 driver therefore it is recommended to use the NDIS 5 driver instead.
- Be aware that connectivity issues can occur on Windows if multiple IPv6 addresses from the `fd00::/8` prefix are assigned to the TAP interface. If this happens, then you may need to manually remove the old/unused addresses from the interface (though the code has a workaround in place to do this automatically in some cases).
- TUN mode is not supported on Windows.
- Yggdrasil can be installed as a Windows service so that it runs automatically in the background. As an example, create the folder `C:\Program Files\Yggdrasil\` and copy `yggdrasil.exe` into it. From an Administrator Command Prompt:
  - Generate a configuration file, if you have not already:
  ```
  "C:\Program Files\Yggdrasil\Yggdrasil.exe" -genconf > "C:\Program Files\Yggdrasil\yggdrasil.conf"
  ```
  - Install the Windows service:
  ```
  sc create yggdrasil binpath= "\"C:\Program Files\Yggdrasil\yggdrasil.exe\" -useconffile \"C:\Program Files\Yggdrasil\yggdrasil.conf\""
  sc config yggdrasil displayname= "Yggdrasil Service"
  sc config yggdrasil start= "auto"
  sc start yggdrasil
  ```
  - Alternatively, if you want the service to autoconfigure instead of using an `yggdrasil.conf`, replace the `sc create` line from above with:
  ```
  sc create yggdrasil binpath= "\"C:\Program Files\Yggdrasil\yggdrasil.exe\" -autoconf"
  ```

## EdgeRouter

- Tested and working on the EdgeRouter X, using the [vyatta-yggdrasil](https://github.com/neilalexander/vyatta-yggdrasil) wrapper package.
