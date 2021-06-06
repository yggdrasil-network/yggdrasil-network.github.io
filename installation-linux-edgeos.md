---
tags: dontlink
sitemap: true
---

# Installing on Ubiquiti EdgeOS

Yggdrasil is supported on the Ubiquiti EdgeRouter using the
[vyatta-yggdrasil](https://github.com/neilalexander/vyatta-yggdrasil) package.

Please note that the package only supports EdgeOS 2.x.

Perform installation steps over SSH by connecting to the EdgeRouter as the
`ubnt` user, e.g. `ssh ubnt@192.168.1.1`, or another admin-level user if
configured.

## Notes

- Although your Yggdrasil configuration will persist, the Yggdrasil package itself **does not** survive an upgrade of the EdgeRouter firmware. You must re-add the repository GPG key and re-install the `vyatta-yggdrasil` package after a system upgrade.
- After upgrading firmware and reinstalling Yggdrasil, use `load` to reload your configuration and then `commit` to make it effective again. Do not run `save` until after you have reloaded your configuration.

## Install the package

Download and copy the package onto the router. Once done, log into the router via SSH and use `dpkg` to install it:
```
sudo dpkg -i vyatta-yggdrasil-x.x.xxx-mipsel.deb
```

## Generate configuration

Configuration for Yggdrasil is generated automatically when you create an interface, e.g. as `tun0`:
```
configure
set interfaces yggdrasil tun0
commit
save
```
At this point, Yggdrasil will start running using default configuration, which includes automatic peer discovery of other Yggdrasil nodes on the same network using multicast.

## Configuration

Once you have generated a configuration file, as above, then you should make configuration changes (like [adding peers](https://github.com/yggdrasil-network/public-peers)) by editing the  `/config/yggdrasil.tun0.conf` file.

For example, if using `tun0`:
```
vi /config/yggdrasil.tun0.conf
```
To make configuration changes effective, restart Yggdrasil:
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
save
```
If you have multiple IPv6 subnets, then they can be configured individually by setting multiple `masquerade from` source ranges. Both private/ULA and public IPv6 subnets are acceptable.

## Default Firewall Config

Use this as an example firewall configuration, which will allow outgoing connections but prevent unexpected incoming ones, with the exception of ICMPv6 which will be allowed, e.g. if using `tun0`:
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
save
```
