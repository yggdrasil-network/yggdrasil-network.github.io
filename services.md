---
tags: dontlink
sitemap: true
---

# Services

The following services are available on the Yggdrasil network, courtesy of our community users. If you host a service on the Yggdrasil network, please feel free to [open a pull request](https://github.com/yggdrasil-network/yggdrasil-network.github.io/edit/master/services.md) to list it here.

----

## Hosting

### Linux containers

- LXD containers hosted on HP ProLiant DL360 Gen9 hardware (dual Xeon E5-2603v3, 128GB RAM, RAID6) at `201:4541:2f84:1188:59ab:e8dd:48a8:d40c`
  - Comes with a native routed Yggdrasil IPv6 address, and a specific allocation of resources based on requirements
  - Containers can reach the Internet on request through a VPN internet provider
  - Contact [neilalexander](https://matrix.to/#/@neilalexander:matrix.org) if you'd like a container to host interesting services on the Yggdrasil network

----

### Clearnet Tunnel Service

- Automated GRE over Yggdrasil service for clearnet IPv4 access:
  - Accessible at http://[301:a003:3f0f:1aa0::1001]/dokuwiki/doku.php?id=ygre:howto
 
- Proxy to Internet, Tor and I2P networks:
  - Web page: `http://[324:9de3:fea4:f6ac::ace]/`

## Websites

### Search

- [YaCy](http://[301:4541:2f84:1188:216:3eff:fe38:cefc]:8090/) - an internal search engine.
  - Accessible via Yggdrasil at `http://[301:4541:2f84:1188:216:3eff:fe38:cefc]:8090/`.

### Forums

- [Mesh Forum](http://[303:60d4:3d32:a2b9::4]/) - a chit-chat forum about everything. Run by @pztrn.
  - Accessible via Yggdrasil at `http://[303:60d4:3d32:a2b9::4]/`.

### Wikis

- [Internal Yggdrasil wiki](http://[203:e0b0:ec08:4e1f:f004:19a9:577a:90ba]/wiki/) run by Jeff at `http://[203:e0b0:ec08:4e1f:f004:19a9:577a:90ba]/wiki/`
- [HowtoYgg](http://[300:529f:150c:eafe::6]/doku.php) - Yggdrasil User Guide run by newbie. Russian language and other.
  - Accessible via Yggdrasil at `http://[300:529f:150c:eafe::6]/doku.php`
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

- Accessible via Yggdrasil at `201:870:9573:4095:f4e6:dfde:cc68:c783` port `6667` (TCP)
- Accessible via cjdns at `fcdc:71fe:e8c2:f3ba:ca93:80f4:3d97:2096` port `6667` (TCP)

### benisnet

- Yggdrasil channel is `#yggdrasil`
- cathugger's node via Yggdrasil at `201:4806:21d5:c971:407f:4ea9:4d7d:e491` port `6667` (TCP)
- Jeff's node via Yggdrasil at `203:e0b0:ec08:4e1f:f004:19a9:577a:90ba` port `6667` (TCP)

### Umbrellix

- Main channel is `#lounge`
- Accessible via Yggdrasil at `202:8fb5:1490:594d:7e29:98fd:8d79:953f` ports `194` (TCP), `994` (TCP+SSL), `6667` (TCP), `6697` (TCP+SSL)
- Accessible via cjdns at `fcda:c930:1c80:ef8a:4fb:6c65:8506:1dd2` ports `194` (TCP), `994` (TCP+SSL), `6667` (TCP), `6697` (TCP+SSL)
- It's preferred that you act like you're just another random clearnetter - Umbrellix is primarily a clearnet IRC network

### PirateIRC

- `y.irc.pirateirc.net` accessible via Yggdrasil at `201:677d:1809:c5eb:202a:d39:e598:305` port `6697` (TCP+SSL)

### 2f30/Cyberia IRC

- `irc.y.fz.is accessible` via Yggdrasil at `201:918e:f16:bd05:e1f4:7435:7182:42d5` port `6697` (TCP+SSL)

### ILITA IRC

- Primarily russian IRC network of users and developers of meshnets and overlay networks (like I2P) - English is fine too
- Accessible via Yggdrasil at `303:60d4:3d32:a2b9::3` ports `16667` (TCP), `16697` (TCP+SSL)
- Accessible via public Internet at `irc.pztrn.name` ports `16667` (TCP), `16697` (TCP+SSL)
- Accessible via I2P at `irc.ilita.i2p` (and servers in the network are linked via I2P)
- Yggdrasil's SSL port uses same certificate as the public Internet node, so you **will** receive SSL warning, this is expected
- Join `#en` for english discussions

### ED12 IRC

- `y.kaotisk-hund.tk` is accessible via Yggdrasil at `202:68fd:1f72:8505:74d6:b751:29ce:b5bb` port `6667` (TCP)

- Main channel is `#general`
- Other channels can be listed with description as their topic
- Also accessible via cjdns at `fc42:7cfa:b830:e988:f192:717f:6576:ed12` port `6667` (TCP)

### BonoboNET

- Main channel is `#general`
    - Other notable channels are `#networking` and `#programming`
    - `/list` will list them all
- Accessible via the public internet at `41.157.98.109` port `9006`
   - Or on yggdrasil at `200:f8fd:8493:cf13:618b:6684:6964:f00b` port `6667`
   - Or on CRXN at `10.6.4.1` port `6667`
- On yggdrasil at `200:c2a4:4104:ce7b:c778:46ef:e914:96bf` port `6667`
   - Or on CRXN at `10.1.0.3` port `6667`
- On yggdrasil at `200:c307:591a:fc3e:66e9:a5c1:85b2:edc6` port `6667`
- On yggdrasil at `201:cb93:9864:4f8c:d2d7:264b:6fe:ba73` port `6667`
- On yggdrasil at `200:a4df:d66c:3f3a:b551:5224:c32f:cf8d` port `6667`
- On yggdrasil at `200:74e1:ddb:836b:8d46:6478:be06:8ff7` port `6667`
   - Or on cjdns at `fcc6:30a9:c8c6:49b4:58e5:b320:b312:58d7` port `6667`
   - Or on CRXN at `10.0.0.7` port `6667`
- More information can be found at the [BonoboNET homepage](http://[205:e59c:110d:4c0e:c3af:10df:3d56:41f3]/projects/bonobonet/) - we're using NGIRCD and open to peering

----

## XMPP

### chat.station.moe

- `chat.station.moe`, run by [zhoreeq](https://github.com/zhoreeq)
    - Webchat is at [http://[305:1bba:6a3:7f24::23]/](http://[305:1bba:6a3:7f24::23]/) or http://chat.station.moe/. General chat room is at `ygg@conference.station.moe`.
    - Unnecessary TLS is removed. XMPP clients should allow authorization over plaintext and disable TLS/SSL/STARTTLS enforcement.
    - XMPP federation is enabled inside Yggdrasil network. [Example config file for Prosody](https://gist.github.com/zhoreeq/0f293cc7c338554369261cfdfab55ddc).

### infradian.icu and ultradian.club
- `infradian.icu` and `ultradian.club`, run by [Umbrellix](http://yww.umbrellix.net/contact/)
  - The SRV record `_xmpp-server._tcp` for all of the relevant subdomains has a domain that points to `202:8fb5:1490:594d:7e29:98fd:8d79:953f` at priority 1, and the clearnet machine which that represents at priority 2 (so that clearnet XMPPers/Snikketers/Jabberers will be able to message you if you are on infradian or ultradian)
  - The SRV record `_xmpp-client._tcp` for @ only has the domain which points to `202:8fb5:1490:594d:7e29:98fd:8d79:953f`. There is a non-obvious way to connect over clearnet, which will not be disclosed here.
  - HTTP upload, unlike on the other domain Umbrellix has which is clearnet-based, is disabled.

----

## API

### Yggdrasil Network Data

- [y.yakamo.org API](http://y.yakamo.org:3000/) network research & map view of the network:
  - [Current Nodes](http://[301:4541:2f84:1188:216:3eff:feb6:65a3]:3000/current)
  - [Discovered NodeInfo](http://[301:4541:2f84:1188:216:3eff:feb6:65a3]:3000/nodeinfo)
  
### Network Maps
  
- [Arceliar's Interactive Network Map](http://[21f:dd73:7cdb:773b:a924:7ec0:800b:221e])
- [y.yakamo.org Static World Map](http://[301:4541:2f84:1188:216:3eff:feb6:65a3]:3000/static/map.png)

### IPv6 address information 
- http://[300:aa35:f9c1:dcce::2]/

----

## NNTP

### pztrn's NNTP server

- [Website with rules, news and documentation](https://nntp.pztrn.name).
- Registration required to access all groups beyond `local.regreq` which is used for registration requests. No anonymous postings allowed.
- ygg.* hierarchy for talks about Yggdrasil! :)
- Accessible via Yggdrasil at `201:e64d:1a9:d2b0:9c74:f285:ea73:5e05` ports `119` (plain text) and `563` (TLSv1.0+ only).
- Accessible via public Internet at `nntp.pztrn.name` ports `119` (plain text) and `563` (TLSv1.0+ only).
- SSL warnings via Yggdrasil expected, certificate is for *.pztrn.name. You can workaround it by adding entry in hosts file.
- Peering is welcome, especially via Yggdrasil.

----

## BitTorrent

- [neilalexander](https://matrix.to/#/@neilalexander:matrix.org)'s public tracker available at `http://tracker.y.neilalexander.eu:6969/announce` or `http://[301:4541:2f84:1188:216:3eff:fe34:ec44]:6969/announce`
- Jeff's public tracker available at `udp://[203:e0b0:ec08:4e1f:f004:19a9:577a:90ba]:6969` (with a [stats page](http://[203:e0b0:ec08:4e1f:f004:19a9:577a:90ba]:6969/stats))

----

## IPFS

- [neilalexander](https://matrix.to/#/@neilalexander:matrix.org)'s IPFS gateway is available at `http://ipfs.y.neilalexander.eu/ipfs/` ([test link](http://ipfs.y.neilalexander.eu/ipfs/QmZiSAYkU7gZtqYeZWL21yuwgFtRnJu1JjDzR6Qd2qdDBr/))

### IPFS Bootstrap Swarm

- Irvine, CA Based Server Boostrap Node hosted by @KylerChin
`/ip6/202:d0ca:a9d7:b4e8:bd3c:ffde:5c89:a3d7/tcp/4001/ipfs/QmZEiPvrfZHapq4uiyTDEcR2szCUhDnjdS4q3Uv2b1Uh88`

----

## Gaming

### Minecraft
  - `202:baed:9808:83c8:738:4041:bd8b:8c1d`, hosted by piele
    - DNS name: `mc.y.creativeserver.net`
    - Survival mode map, ask piele to be whitelisted
      
### Xonotic
  - `201:9d54:3c57:d6d2:e8d7:a8ce:841f:eb89:26000` 
    - DNS name: `xonotic.y.netwhood.online:26000`

### Super Tux Kart
  - `[224:9de3:fea4:f6ac:6d7c:68f5:6c8e:f9a9]:7711` 
    - Download game from [clearnet site](https://supertuxkart.net/Download) or [intranet repository](http://[324:9de3:fea4:f6ac::ace]/files/software/games/SuperTuxKart/). 
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
  - `200:a120:8cf8:2ad5:7509:7341:c861:34aa` port 53 hosted by [sin](https://2f30.org)
    - Supports clearnet resolution of standard DNS domains via root lookups
  - `200:d0c4:68ee:e87b:c206:67b8:5fa5:d4be` port 53, hosted by [Medium](https://github.com/medium-isp)
    - Internal Yggdrasil services resolver
  - `200:5bf2:a7a5:27c7:54b8:6669:eb74:1813` port 53, hosted by [User2k](https://user2k.eu)
    - Medium DNS, OpenDNS, Google DNS and CloudFlare DNS
----

## ZeroNet

### ZeroNet Public tracker
  - `zero://201:d53:22ac:5084:2591:2a5e:eafd:6aa7`
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

----

## Direct Connect

- bepis DC-hub run by *Jeff* `adc://[203:e0b0:ec08:4e1f:f004:19a9:577a:90ba]:1511`

----

## [iPerf3](https://iperf.fr)

- `iperf3 -6 -c y.thingylabs.io` on [ygg.thingylabs.io](https://ygg.thingylabs.io/)
  - Link: 10 GBit
  - Location: Nuremberg, Germany

----

## Bitcoin

### Node by [vkeb6rza](https://vkeb6rza.github.io/) at `204:381:4c98:df09:d15b:ab1e:894b:d99e`
  - Port 8333 - Bitcoin Core with BIP157 and BIP158 experimental support
  - Port 9735 - Lightning Network - `038758ca700b8c4c73d1b86440acb963be93e5b11f9d6f363041be43572c8cbd43@[204:381:4c98:df09:d15b:ab1e:894b:d99e]:9735`
  - Port 9911 - LND Watchtower
  - Port 50001 - Electrum server

----

## VoIP

### netwhood.online mumble server
  - `200:5802:538e:6a8f:3cb3:1e3c:8019:a9e8`, port `64738`
  - umurmurd daemon
  - Same Let's Encrypt certificate as at [netwhood.online](https://netwhood.online/).

### Hidden Murmur
Mumble server is accessible from Yggdrasil, Tor and I2P. Run by acetone.
  - `324:9de3:fea4:f6ac::cafe`, port `64738`
  - Web page: 
    - Yggdrasil: `http://[324:9de3:fea4:f6ac::cafe]/`
    - Tor: `http://z2tg7kdh4yuade6ahvgz2hmu2vbey6alkfhbildeqphgola6zm7it7ad.onion/`
    - I2P: `http://plpu63ftpi5wdr42ew7thndoyaclrjqmcmngu2az4tahfqtfjoxa.b32.i2p/`

----
