---
tags: dontlink
sitemap: true
---

# Services

The following services are available on the Yggdrasil network, courtesy of our community users. If you host a service on the Yggdrasil network, please feel free to [open a pull request](https://github.com/yggdrasil-network/yggdrasil-network.github.io/edit/master/services.md) to list it here.

----

## Hosting

### Linux containers

- LXC containers hosted on HP ProLiant DL380 G7 hardware (dual Xeon X5650, 96GB RAM, 1.5TB on Smart Array) at `201:4541:2f84:1188:59ab:e8dd:48a8:d40c`
  - Comes with a native routed Yggdrasil IPv6 address, and a specific allocation of resources based on requirements
  - Containers can route to Yggdrasil only and cannot route to the public Internet, although access to Debian repos is available through a proxy (and other specific locations on request if needed)
  - Contact [neilalexander](https://matrix.to/#/@neilalexander:matrix.org) if you'd like a container to host interesting services on the Yggdrasil network

----

## Shell accounts

### BAN.AI Public Access Multics Service

 - `dps8@[201:85d7:5968:d14a:9aca:fe9e:37ed:5ea4]`
   - Open anonymous Guest access and free user account registration.
   - Direct Yggdrasil access via [telnet](telnet://[201:85d7:5968:d14a:9aca:fe9e:37ed:5ea4]), [mosh](mosh://[201:85d7:5968:d14a:9aca:fe9e:37ed:5ea4]), and [ssh](ssh://[201:85d7:5968:d14a:9aca:fe9e:37ed:5ea4]).
   - More information and access available via [cjnds](http://[fc18:cd5:92ad:5ed6:9960:ad6f:d723:b971]/multics), [tor](http://stopaiepslgjzczi.onion/multics), and [clearnet](https://ban.ai/multics) as well.

----

## IRC

### meshIRC

- Accessible via Yggdrasil at `301:4541:2f84:1188:216:3eff:fe84:627d` port 6667
- Accessible via cjdns at `fc2a:897b:4630:86f8:c3a5:23cb:7ea5:a69a` port 6667

### benisnet

- cathugger's node via Yggdrasil at `201:4806:21d5:c971:407f:4ea9:4d7d:e491` port `6667`, ygg channel `#yggdrasil`
  
----

## API

### Yggdrasil Network Data

- [y.yakamo.org API](http://y.yakamo.org:3000/) network research & map view of the network:
  - [Current nodes](http://[301:4541:2f84:1188:216:3eff:feb6:65a3]:3000/current)
  - [World Map](http://[301:4541:2f84:1188:216:3eff:feb6:65a3]:3000/static/map.png)

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

## Social

### Scuttlebot Pub Server
  - `[200:680e:76d0:2435:a2f0:c956:c484:c267]:8008`
  - Invite code:
```
200:680e:76d0:2435:a2f0:c956:c484:c267:
8008:@gBeNk0gWi92lTUd5im8yGgOitRPSQcZkt
4KxkMl6YZ0=.ed25519~8Cr4z+P9UCY9aQ2CGWw
BoqjCnw4JHMqx+00mOw8g0Hw=
```
----

## VCS

### Gitea Server
  - [git.y.cryptocloud.icu](http://git.y.cryptocloud.icu/)
  - Account registrations are open
  
----

## Misc

### NodeList
  - [nodelist](https://github.com/yakamok/yggdrasil-nodelist/blob/master/nodelist) on github
    - Node names can be a plain alias or a domain name
    - Programs like [ygg-stats](https://github.com/yakamok/yggdrasil-stats) uses it to replace the IPv6 address with a pretty name
