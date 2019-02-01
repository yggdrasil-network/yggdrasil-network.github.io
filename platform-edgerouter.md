---
tags: dontlink
sitemap: true
---

# EdgeRouter

Yggdrasil is supported on the Ubiquiti EdgeRouter using the [vyatta-yggdrasil](https://github.com/neilalexander/vyatta-yggdrasil) package.

## Notes

- Yggdrasil does not survive an upgrade of the EdgeRouter firmware. You must reinstall it after a system upgrade.
- After reinstalling Yggdrasil, use `load` to reload your configuration and then `commit` to make it effective again. Do not run `save` under any circumstances until after you have reloaded your configuration.

## Installation

Start by [downloading the latest vyatta-yggdrasil .deb package](https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts) and then install it:
```
curl -O https://xx-xxxxxxxxx-gh.circle-artifacts.com/x/vyatta-yggdrasil-x.x.xxx-mipsel.deb
sudo dpkg -i vyatta-yggdrasil-x.x.xxx-mipsel.deb
```

## Generate configuration

Configuration for Yggdrasil is generated automatically when you create an interface:
```
configure
set interfaces yggdrasil tun0
commit
```
At this point, Yggdrasil will start running using default configuration, which includes automatic peer discovery of other Yggdrasil nodes on the same network using multicast.

## Configuration

Other changes should be made to `/config/yggdrasil.tun0.conf` by hand. To make effective, restart Yggdrasil:
```
restart yggdrasil tun0
```

## Masquerade

If you want to allow other IPv6 hosts on your network to communicate through yggdrasil, you can configure an IPv6 masquerade rule. All traffic sent from other hosts on the network through the Yggdrasil interface will be NAT'd.

For example:
```
configure
set interfaces yggdrasil tun0 masquerade from xxxx:xxxx:xxxx::/48
commit
```
If you have multiple IPv6 subnets, then they can be configured individually by setting multiple `masquerade from` source ranges. Both private/ULA and public IPv6 subnets are acceptable.

## Crash Detection

To make sure that the process is restarted if it crashes, schedule the `vyatta-check-yggdrasil` script to run at a regular interval:
```
configure
set system task-scheduler task check-yggdrasil executable path /opt/vyatta/sbin/vyatta-check-yggdrasil
set system task-scheduler task check-yggdrasil interval 1m
commit
```

## Default Firewall Config

Use this as an example firewall configuration, which will allow outgoing connections but prevent unexpected incoming ones, with the exception of ICMPv6 which will be allowed:
```
configure

set firewall ipv6-name YGG_IN default-action drop
set firewall ipv6-name YGG_LOCAL default-action drop

set firewall ipv6-name YGG_IN rule 10 action accept
set firewall ipv6-name YGG_IN rule 10 state established enable
set firewall ipv6-name YGG_IN rule 10 state related enable

set firewall ipv6-name YGG_IN rule 20 action drop
set firewall ipv6-name YGG_IN rule 20 state invalid enable

set firewall ipv6-name YGG_IN rule 30 action accept
set firewall ipv6-name YGG_IN rule 30 protocol icmpv6

set firewall ipv6-name YGG_LOCAL rule 10 action accept
set firewall ipv6-name YGG_LOCAL rule 10 state established enable
set firewall ipv6-name YGG_LOCAL rule 10 state related enable

set firewall ipv6-name YGG_LOCAL rule 20 action drop
set firewall ipv6-name YGG_LOCAL rule 20 state invalid enable

set firewall ipv6-name YGG_LOCAL rule 30 action accept
set firewall ipv6-name YGG_LOCAL rule 30 protocol icmpv6

set interfaces yggdrasil tun0 firewall in ipv6-name YGG_IN
set interfaces yggdrasil tun0 firewall local ipv6-name YGG_LOCAL

commit
```
