# Platforms

## Linux

- Should work out of the box on most Linux distributions.
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
- If you are running a Debian-based system (including Ubuntu and elementaryOS), you may instead prefer to use the [automatically built](builds.md) Debian packages. These install the systemd scripts and generate a configuration file into `/etc/yggdrasil.conf` automatically, and can be easily upgraded when needed.

## macOS

- See the [macOS platform page](platform-macos.md).

## Windows

- See the [Windows platform page](platform-windows.md).

## EdgeRouter

- See the [EdgeRouter platform page](platform-edgerouter.md).

## FreeBSD, NetBSD

- Works in TAP mode, but currently doesn't work in TUN mode.
- You may need to create the TAP adapter first if it doesn't already exist, i.e. `ifconfig tap0 create`.

## OpenBSD

- Works in TAP mode, but currently doesn't work in TUN mode.
- You may need to create the TAP adapter first if it doesn't already exist, i.e. `ifconfig tap0 create`.
- OpenBSD is not capable of listening on both IPv4 and IPv6 at the same time on the same socket (unlike FreeBSD and NetBSD). This affects the `Listen` and `AdminListen` configuration options. You will need to set `Listen` and `AdminListen` to use either an IPv4 or an IPv6 address.
- You may consider using [relayd](https://man.openbsd.org/relayd.8) to allow incoming Yggdrasil connections on both IPv4 and IPv6 simultaneously.
