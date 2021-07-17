---
tags: dontlink
sitemap: true
---

# Services (v0.4 network)

The following services are available on the Yggdrasil v0.4 network, courtesy of our community users. If you host a service on the Yggdrasil network, please feel free to [open a pull request](https://github.com/yggdrasil-network/yggdrasil-network.github.io/edit/master/services.md) to list it here.

----

## Hosting

### Linux containers

- LXD containers hosted on HP ProLiant DL360 Gen9 hardware (dual Xeon E5-2603v3, 128GB RAM, RAID6) at `300:7232:2b0e:d6e9::1`
  - Comes with a native routed Yggdrasil IPv6 address, and a specific allocation of resources based on requirements
  - Containers can reach the Internet on request through a VPN internet provider
  - Contact [neilalexander](https://matrix.to/#/@neilalexander:matrix.org) if you'd like a container to host interesting services on the Yggdrasil network

----

## Websites

- [Yggdrasil Network](http://[319:3cf0:dd1d:47b9:20c:29ff:fe2c:39be]/) - this website
  - `http://[319:3cf0:dd1d:47b9:20c:29ff:fe2c:39be]/`

- [Public Peers](http://[319:3cf0:dd1d:47b9:20c:29ff:fe2c:39bd]/)
  - `http://[319:3cf0:dd1d:47b9:20c:29ff:fe2c:39bd]/`

- [YaCy](http://[300:7232:2b0e:d6e9:216:3eff:fe38:cefc]:8090/) - internal search engine
  - `http://[300:7232:2b0e:d6e9:216:3eff:fe38:cefc]:8090/`

- [Yggdrasil v0.4 Map](http://[202:d7cd:bd04:6bbc:acc6:b002:da12:86c7]/)
  - `http://[202:d7cd:bd04:6bbc:acc6:b002:da12:86c7]/`

- [deavmi's homepage](http://[203:75b7:45af:ecca:d641:cd42:38dc:4788]/)
  - `http://[203:75b7:45af:ecca:d641:cd42:38dc:4788]/`

- [Drommedaris Services Project](http://[201:754:2ca2:57f8:461:f090:b1cd:685e]/)
  - `http://[201:754:2ca2:57f8:461:f090:b1cd:685e]/`

- [CRXN statistics page](http://[200:21fe:6a74:aca4:6884:614f:cd24:67c3]/munin/)
  - `http://[200:21fe:6a74:aca4:6884:614f:cd24:67c3]/munin/`

- [Yggdrasil Web directory](http://[21e:a51c:885b:7db0:166e:927:98cd:d186]/)
  - `http://[21e:a51c:885b:7db0:166e:927:98cd:d186]/`

- [HowTo Ygg: Yggdrasil wiki](http://[222:a8e4:50cd:55c:788e:b0a5:4e2f:a92c]/)
  - `http://[222:a8e4:50cd:55c:788e:b0a5:4e2f:a92c]/`
 
- [Acetone's service node](http://[324:71e:281a:9ed3::ace]/)
  - `http://[324:71e:281a:9ed3::ace]/`

- [Alexandria - Content Mirror run by Toronto Mesh](http://[200:4233:d244:4f72:fbba:cf19:5d1a:fbb]/)
  - `http://[200:4233:d244:4f72:fbba:cf19:5d1a:fbb]/`
  - `https://y.alexandria.tomesh.net/`

- [Toronto Mesh Matrix Server](https://y.matrix.tomesh.net/) - Matrix Server (requires DNS)
  - `https://y.matrix.tomesh.net/`

- [Nikat's homepage](https://[302:a2a5:dead:ded::a2a5]/)
  - `https://[302:a2a5:dead:ded::a2a5]/`

- [Causa Arcana](http://[200:f0a1:c2ef:37a2:467f:9f74:ae64:9954]/) - Russian blog about information technologies, security and cryptoanarchism
  - `http://[200:f0a1:c2ef:37a2:467f:9f74:ae64:9954]/`
 
- [Git hosting](http://[325:5a4:d1c9:db96::2]/) - Gitea, mirror of [git.3301.su](https://git.3301.su/) (ALFIS: `gitea.ygg`) hosted by [cofob](https://t.me/cofob)
  - `http://[325:5a4:d1c9:db96::2]/`

- [Minecraft server homepage](http://[325:5a4:d1c9:db96::3]/) - mirror of [2buldzha2t.ru](https://2buldzha2t.ru/) hosted by [cofob](https://t.me/cofob)
  - `http://[325:5a4:d1c9:db96::3]/`

- [Speedtest server](http://[200:ad55:3db7:b8cf:3aa8:664b:bca3:bd81]:8989/)
  - `http://[200:ad55:3db7:b8cf:3aa8:664b:bca3:bd81]:8989/`

----

## Proxy services

- HTTP proxy to other networks like Yggdrasil, Tor, I2P and clearnet:
  - `324:71e:281a:9ed3::ace` port `8888` hosted by [acetone](http://[324:71e:281a:9ed3::ace]/)

----

## IRC

### BonoboNET

- Accessible via Yggdrasil at the following servers:
  - `200:ad55:3db7:b8cf:3aa8:664b:bca3:bd81` port `6667`/`6697`
  - `202:89be:399d:8ee1:306a:9d32:a0d4:6ecb` port `6667`/`6697`
- Notable channels are `#general`, `#networking` and `#programming`
- More information can be found at the [BonoboNET homepage](http://[203:75b7:45af:ecca:d641:cd42:38dc:4788]/projects/bonobonet/) - we're using unrealircd and open to peering

### benisnet

- Accessible via Yggdrasil at the following servers:
  - `203:b00a:1684:860d:880f:ea9c:92dc:fabb` port `6667`
  - `201:94b8:c01b:bc26:c17a:1a4e:b5e:bb6c` port `6667`

### ILITA IRC

- Primarily Russian IRC network of users and developers of meshnets and overlay networks (like I2P) — English is fine too
- Accessible via Yggdrasil at the following servers:
  - `324:71e:281a:9ed3::41` port `6667`
- Join `#en` for english discussions
- More information can be found at the [ILITA homepage](http://[324:71e:281a:9ed3::41]/) with additional information about access

----

## BitTorrent

- [neilalexander](https://matrix.to/#/@neilalexander:matrix.org)'s public tracker available at `http://tracker.y.neilalexander.eu:6969/announce` or `http://[300:7232:2b0e:d6e9:216:3eff:fe34:ec44]:6969/announce`

----

## IPFS

- [neilalexander](https://matrix.to/#/@neilalexander:matrix.org)'s IPFS gateway is available at `http://ipfs.y.neilalexander.eu/ipfs/` ([test link](http://ipfs.y.neilalexander.eu/ipfs/QmZiSAYkU7gZtqYeZWL21yuwgFtRnJu1JjDzR6Qd2qdDBr/))

----

## DNS

- `324:71e:281a:9ed3::53` port `53`, hosted by [acetone](http://[324:71e:281a:9ed3::ace]/)
  - [Alfis](https://github.com/Revertron/Alfis), [Meshname](https://github.com/zhoreeq/meshname), Google DNS   
- `200:2892:d0f:bc20:8a8e:c7be:9d9e:ab55`, hosted by [darkdrgn2k](http://github.com/darkdrgn2k/)
  - Basic `named` DNS clear-net server maintained running [OpenNIC](https://www.opennic.org/)
- `302:db60::53` port `53`, hosted by [Revertron](https://t.me/Revertron), Praha, Czechia
  - [Alfis](https://github.com/Revertron/Alfis), [AdGuard DNS](https://github.com/AdguardTeam/AdGuardHome)   
- `300:6223::53` port `53`, hosted by [Revertron](https://t.me/Revertron), Bratislava, Slovakia
  - [Alfis](https://github.com/Revertron/Alfis), [AdGuard DNS](https://github.com/AdguardTeam/AdGuardHome)   
- `302:7991::53` port `53`, hosted by [Revertron](https://t.me/Revertron), Saint Petersburg, Russia
  - [Alfis](https://github.com/Revertron/Alfis), [AdGuard DNS](https://github.com/AdguardTeam/AdGuardHome) 
- `325:5a4:d1c9:db96::53` port `53`, hosted by [cofob](https://t.me/cofob), Moscow, Russia
  - [Alfis](https://github.com/Revertron/Alfis), [AdGuard DNS](https://github.com/AdguardTeam/AdGuardHome) 
- `301:e9b7:c2c:b52e::53` port `53`, hosted by [cofob](https://t.me/cofob), Moscow, Russia
  - [Alfis](https://github.com/Revertron/Alfis), [AdGuard DNS](https://github.com/AdguardTeam/AdGuardHome) 
- `300:de9b:ea32:3e4a::53` port `53`, hosted by [cofob](https://t.me/cofob), Germany
  - [Alfis](https://github.com/Revertron/Alfis), [AdGuard DNS](https://github.com/AdguardTeam/AdGuardHome) 

----

## VoIP

### Hidden Murmur

- [Web page](http://[324:71e:281a:9ed3::cafe]/). Mumble server is accessible from Yggdrasil, Tor and I2P.
  - `324:71e:281a:9ed3::cafe` port `64738`

----

## NTP

- `202:a2a5:dead:ded:9a54:4ab5:6aa7:1645` port `123`, hosted by [nikat](https://[302:a2a5:dead:ded::a2a5]/)

----

## Minecraft

### Java Edition

- `[325:5a4:d1c9:db96::3]` port `25565` 1.17 Minecraft server hosted by [cofob](https://t.me/cofob)

----

### WebIndexP2P

- `201:436:b73a:ef28:953f:bbfd:49ed:8754` port `9472`. Wip2p node on the default `General Sherman` tree accepting websocket connections.
  - If you have IPFS, you can load the Arborist UI at http://127.0.0.1:8080/ipns/arborist.wip2p.eth/ and add the node above as a peer.
