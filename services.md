---
tags: dontlink
sitemap: true
---

# Services

The following services are available on the Yggdrasil network, courtesy of our community users. If you host a service on the Yggdrasil network, please feel free to [open a pull request](https://github.com/yggdrasil-network/yggdrasil-network.github.io/edit/master/services.md) to list it here.

----

## Hosting

### Linux containers

- LXC containers hosted on HP ProLiant DL380 G7 hardware (dual Xeon X5650, 96GB RAM, RAID1+0) at `201:4541:2f84:1188:59ab:e8dd:48a8:d40c`
  - Comes with a native routed Yggdrasil IPv6 address, and a specific allocation of resources based on requirements
  - Containers can reach the Internet on request through a VPN internet provider
  - Contact [neilalexander](https://matrix.to/#/@neilalexander:matrix.org) if you'd like a container to host interesting services on the Yggdrasil network

----

## Websites

### Wikis

- [Internal Yggdrasil wiki](http://[203:e0b0:ec08:4e1f:f004:19a9:577a:90ba]/wiki/) run by Jeff at `http://[203:e0b0:ec08:4e1f:f004:19a9:577a:90ba]/wiki/`

----

## Shell accounts

### BAN.AI Public Access Multics Service

 - `dps8@[201:85d7:5968:d14a:9aca:fe9e:37ed:5ea4]`
   - Open anonymous Guest access and free user account registration.
   - Direct Yggdrasil access via [telnet](telnet://[201:85d7:5968:d14a:9aca:fe9e:37ed:5ea4]), [mosh](mosh://[201:85d7:5968:d14a:9aca:fe9e:37ed:5ea4]), and [ssh](ssh://[201:85d7:5968:d14a:9aca:fe9e:37ed:5ea4]).
   - More information and access available via [cjdns](http://[fc18:cd5:92ad:5ed6:9960:ad6f:d723:b971]/multics), [tor](http://stopaiepslgjzczi.onion/multics), and [clearnet](https://ban.ai/multics) as well.

----

## IRC

### meshIRC

- Accessible via Yggdrasil at `301:4541:2f84:1188:216:3eff:fe84:627d` port `6667` (TCP)

### benisnet

- Yggdrasil channel is `#yggdrasil`
- cathugger's node via Yggdrasil at `201:4806:21d5:c971:407f:4ea9:4d7d:e491` port `6667` (TCP)

### Umbrellix

- Main channel is `#lounge`
- Accessible via Yggdrasil at `202:8fb5:1490:594d:7e29:98fd:8d79:953f` ports `194` (TCP), `994` (TCP+SSL), `6667` (TCP), `6697` (TCP+SSL)
- Accessible via cjdns at `fcda:c930:1c80:ef8a:4fb:6c65:8506:1dd2` ports `194` (TCP), `994` (TCP+SSL), `6667` (TCP), `6697` (TCP+SSL)
- It's preferred that you act like you're just another random clearnetter - Umbrellix is primarily a clearnet IRC network

### PirateIRC

- `y.irc.pirateirc.net` accessible via Yggdrasil at `201:677d:1809:c5eb:202a:d39:e598:305` port `6697` (TCP+SSL, recommended), `6667` (TCP)

### 2f30/Cyberia IRC

- `irc.y.fz.is accessible` via Yggdrasil at `201:918e:f16:bd05:e1f4:7435:7182:42d5` port `6697` (TCP+SSL)

----

## API

### Yggdrasil Network Data

- [y.yakamo.org API](http://y.yakamo.org:3000/) network research & map view of the network:
  - [Current Nodes](http://[301:4541:2f84:1188:216:3eff:feb6:65a3]:3000/current)
  - [Discovered NodeInfo](http://[301:4541:2f84:1188:216:3eff:feb6:65a3]:3000/nodeinfo)
  
### Network Maps
  
- [Arceliar's Interactive Network Map](http://[21f:dd73:7cdb:773b:a924:7ec0:800b:221e])
- [y.yakamo.org Static World Map](http://[301:4541:2f84:1188:216:3eff:feb6:65a3]:3000/static/map.png)

----

## BitTorrent

- [neilalexander](https://matrix.to/#/@neilalexander:matrix.org)'s public tracker available at `http://tracker.y.neilalexander.eu:6969/announce` or `http://[301:4541:2f84:1188:216:3eff:fe34:ec44]:6969/announce`

----

## IPFS

- [neilalexander](https://matrix.to/#/@neilalexander:matrix.org)'s IPFS gateway is available at `http://ipfs.y.neilalexander.eu/ipfs/` ([test link](http://ipfs.y.neilalexander.eu/ipfs/QmZiSAYkU7gZtqYeZWL21yuwgFtRnJu1JjDzR6Qd2qdDBr/))

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
  - `200:c493:950a:d576:a413:faef:990b:fb76` port 53, hosted by [Asymptote Club](https://asymptote.club)
    - OpenNIC resolver
  - `219:8496:8f7b:b1e8:13a6:87d2:a3ca:b13d` port 53, hosted by [jcgruenhage](https://jcg.re)
    - Supports clearnet resolution of standard DNS domains
    - Will ask 1.1.1.1 over TLS, recursive resolving currently disabled due to inferior speed.

----

## ZeroNet

### ZeroNet Public tracker
  - `zero://203:5871:3e33:413c:a824:cb7b:2604:92ab:24589`
  - Add the tracker to your `zeronet.conf` file
  - Don't forget to also add `fileserver_ip = 200:xxxx:your:ygg:ip:here` to `zeronet.conf` otherwise it wont connect.
  
### Yggdrasil landing page
  - http://127.0.0.1:43110/1H2hcep9CaGYrYycMJwFx7Jr5jA81oLC34
  
### Yggdrasil website copy
  - http://127.0.0.1:43110/1PffaPsQp81gec3GbLDTzH3cY57WuRfC36
  
----

## Gopher Holes

### Umbrellix Gopher

  - `gopher-y.umbrellix.net` or `202:8fb5:1490:594d:7e29:98fd:8d79:953f`
  - Hole not updated with details of Yggdrasil, yet.
    
