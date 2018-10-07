---
tags: dontlink
sitemap: true
---

# Services

The following services are available on the Yggdrasil network, courtesy of our community users. If you host a service on the Yggdrasil network, please feel free to [open a pull request](https://github.com/yggdrasil-network/yggdrasil-network.github.io/edit/master/services.md) to list it here.

----

## Shell accounts

### BAN.AI Public Access Multics Service

 - `dps8@[201:85d7:5968:d14a:9aca:fe9e:37ed:5ea4]`
   - Open anonymous Guest access and free user account registration.
   - Direct Yggdrasil access via [telnet](telnet://[201:85d7:5968:d14a:9aca:fe9e:37ed:5ea4]), [mosh](mosh://[201:85d7:5968:d14a:9aca:fe9e:37ed:5ea4]), and [ssh](ssh://[201:85d7:5968:d14a:9aca:fe9e:37ed:5ea4]).
   - More information and access available via [cjnds](http://[fc18:cd5:92ad:5ed6:9960:ad6f:d723:b971]/multics), [tor](http://stopaiepslgjzczi.onion/multics), and [clearnet](https://ban.ai/multics) as well.
   
### Linux containers

- LXC containers hosted on ProLiant DL380 Gen8 hardware (dual Xeon, 96GB RAM) at `201:4541:2f84:1188:59ab:e8dd:48a8:d40c`
- Routable to Yggdrasil only (not the public Internet), although access to Debian repos is available through a proxy
- Hosted by neilalexander, ask in the Matrix channel if you'd like a container

----

## API

### Yggdrasil Network Data

- [y.yakamo.org API](http://y.yakamo.org:3000/) network research & map view of the network:
  - [Current nodes](http://[201:3ad4:37ce:3341:af74:976f:59c9:ddeb]:3000/current)

----

## Gaming

### Minecraft
  - `202:baed:9808:83c8:738:4041:bd8b:8c1d`, hosted by piele
    - DNS name: `mc.y.creativeserver.net`
    - Survival mode map, ask piele to be whitelisted

----

## DNS

### Public DNS Server
  - `200:8993:bb44:e1f3:bde7:6234:3f3e:fdd1` port 53, hosted by [Famicoman](https://phillymesh.net)
    - Supports clearnet resolution of standard DNS domains
    - Supports resolution of [OpenNIC](https://www.opennic.org/) domains
	- [DNSCrypt on port 5353](https://servers.opennicproject.org/edit.php?srv=ns7.nh.nl.dns.opennic.glue)

----

## Misc

### NodeList
  - [nodelist](https://github.com/yakamok/yggdrasil-nodelist/blob/master/nodelist) on github
    - Node names can be a plain alias or a domain name
    - Programs like [ygg-stats](https://github.com/yakamok/yggdrasil-stats) uses it to replace the IPv6 address with a pretty name
