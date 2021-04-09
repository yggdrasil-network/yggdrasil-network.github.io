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

### Wikis

- [Internal Yggdrasil wiki](http://[203:e0b0:ec08:4e1f:f004:19a9:577a:90ba]/wiki/) run by Jeff at `http://[203:e0b0:ec08:4e1f:f004:19a9:577a:90ba]/wiki/`
- [HowtoYgg](http://[300:529f:150c:eafe::6]/doku.php) - Yggdrasil User Guide run by newbie. Russian language and other.
  - Accessible via Yggdrasil at `http://[300:529f:150c:eafe::6]/doku.php`

### Forums


- [RAMBLE](http://[200:718d:c499:3fce:c5e:65c6:73d6:f46d]/) A reddit-like site bridging services from the clearnet, Yggdrasil, TOR, and I2P. Interact with the community through the network of your choice. Privacy and simplicty focused.

- [LoFi Forum](http://[320:bfe2:bf50:3c25::f]/) run by zhoreeq at `http://[320:bfe2:bf50:3c25::f]/`. Russian and English languages.

----

## IRC

### benisnet

- Yggdrasil channel is `#yggdrasil`
- cathugger's node via Yggdrasil at `201:4806:21d5:c971:407f:4ea9:4d7d:e491` port `6667` (TCP)
- Jeff's node via Yggdrasil at `203:e0b0:ec08:4e1f:f004:19a9:577a:90ba` port `6667` (TCP)

### Umbrellix

- Main channel is `#lounge`
- Accessible via Yggdrasil at `202:8fb5:1490:594d:7e29:98fd:8d79:953f` ports `194` (TCP), `994` (TCP+SSL), `6667` (TCP), `6697` (TCP+SSL)
- Accessible via cjdns at `fcda:c930:1c80:ef8a:4fb:6c65:8506:1dd2` ports `194` (TCP), `994` (TCP+SSL), `6667` (TCP), `6697` (TCP+SSL)
- It's preferred that you act like you're just another random clearnetter - Umbrellix is primarily a clearnet IRC network

### BunkerTreff

- primarily english/german IRC Network
- Main channel is #general
- Accessible via Yggdrasil at 202:4768:121:63c1:418f:d523:3826:a0d5 ports 6667 (TCP)

### PirateIRC

- `y.irc.pirateirc.net` accessible via Yggdrasil at `201:677d:1809:c5eb:202a:d39:e598:305` port `6697` (TCP+SSL)

### ILITA IRC

- Primarily russian IRC network of users and developers of meshnets and overlay networks (like I2P) - English is fine too
- Accessible via Yggdrasil at `324:9de3:fea4:f6ac::41` port `6667` (TCP)
- Accessible via I2P at `irc.acetone.i2p` port `6667` (TCP)
- Accessible via Yggdrasil web interface at `http://[324:9de3:fea4:f6ac::41]/`
- Accessible via Tor web interface at `http://ilitafrzzgxymv6umx2ux7kbz3imyeko6cnqkvy4nisjjj4qpqkrptid.onion/`
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

## WebIndexP2P

### General Sherman Tree
  - `ws://[200:e990:4450:8c75:e842:4159:1266:fb18]:9472`

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

---

### Hidden Murmur
Mumble server is accessible from Yggdrasil, Tor and I2P. Run by acetone.
  - `324:9de3:fea4:f6ac::cafe`, port `64738`
  - Web page:
    - Yggdrasil: `http://[324:9de3:fea4:f6ac::cafe]/`
    - Tor: `http://z2tg7kdh4yuade6ahvgz2hmu2vbey6alkfhbildeqphgola6zm7it7ad.onion/`
    - I2P: `http://plpu63ftpi5wdr42ew7thndoyaclrjqmcmngu2az4tahfqtfjoxa.b32.i2p/`

----
