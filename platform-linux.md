---
tags: dontlink
sitemap: true
---

# Linux

Yggdrasil is well supported on Linux.

## Notes

- Should work with any kernel that includes the `tun` and/or `tap` modules.
- The maximum MTU size supported on Linux is 65535 in TUN mode and 65521 in TAP mode.
- IPv6 needs to be enabled in order for Yggdrasil to work - IPv6 is usually enabled by default, but if not, enable using `sysctl -w net.ipv6.conf.all.disable_ipv6=0` or similar.
- If using TUN/TAP then `/dev/net/tun` should be present on your system.
- The [BBR](https://github.com/google/bbr) congestion control algorithm is strongly recommended, as it can significantly reduce latency when under load. On recent distributions, this can be done with `sysctl -w net.ipv4.tcp_congestion_control=bbr` or similar.
