---
tags: dontlink
sitemap: true
---

# Services (v0.4 network)

The following services are available on the Yggdrasil v0.4 network, courtesy of our community users. If you host a service on the Yggdrasil Network or find that a service is no longer available, please feel free to [open a pull request on GitHub](https://github.com/yggdrasil-network/yggdrasil-network.github.io/edit/master/services.md)

----

## Commercial services

### IT consultancies

- [Thingylabs GmbH](http://[22a:bd80:1588:5365:e467:c5fa:6123:5018]/) - An IT consultancy for Web, Cloud, and Apps.
  - `http://[22a:bd80:1588:5365:e467:c5fa:6123:5018]/`

----

## Websites

- [Yggdrasil v0.4 Map](http://[21e:e795:8e82:a9e2:ff48:952d:55f2:f0bb]/)
  - `http://[21e:e795:8e82:a9e2:ff48:952d:55f2:f0bb]/`

- [deavmi's homepage](http://[203:75b7:45af:ecca:d641:cd42:38dc:4788]/)
  - `http://[203:75b7:45af:ecca:d641:cd42:38dc:4788]/`

- [Yggdrasil Web directory](http://[21e:a51c:885b:7db0:166e:927:98cd:d186]/)
  - `http://[21e:a51c:885b:7db0:166e:927:98cd:d186]/`

- [HowTo Ygg: Yggdrasil wiki](http://[222:a8e4:50cd:55c:788e:b0a5:4e2f:a92c]/)
  - `http://[222:a8e4:50cd:55c:788e:b0a5:4e2f:a92c]/`

- [Acetone's service node](http://[324:71e:281a:9ed3::ace]/)
  - `http://[324:71e:281a:9ed3::ace]/`

- [Nikat's homepage](https://[302:a2a5:dead:ded::a2a5]/)
  - `https://[302:a2a5:dead:ded::a2a5]/`

- [Yggdrasil Forum](http://[21a:34aa:c782:3ad2:1bf8:73f8:141:66e8]) - Forum in yggdrasil, about yggdrasil
  - `http://[21a:34aa:c782:3ad2:1bf8:73f8:141:66e8]`

- [Bible4u](http://[220:da17:52a1:76f3:fb99:f612:3f50:c9b]) - Uncensored Bible for Darknet.
  - `http://[220:da17:52a1:76f3:fb99:f612:3f50:c9b]`
  - `https://ygg.bible4u.net`

- [YGGo](http://[201:23b4:991a:634d:8359:4521:5576:15b7]/yggo) - [open source](https://github.com/YGGverse/YGGo) Search portal with distributed index and API
  - `http://[201:23b4:991a:634d:8359:4521:5576:15b7]/yggo`

- [YGGwave](http://[201:23b4:991a:634d:8359:4521:5576:15b7]/yggwave) - [open source](https://github.com/YGGverse/YGGwave) Radio stations catalog
  - `http://[201:23b4:991a:634d:8359:4521:5576:15b7]/yggwave`

- [YGGstate](http://[201:23b4:991a:634d:8359:4521:5576:15b7]/yggstate) - [open source](https://github.com/YGGverse/YGGstate) Yggdrasil network explorer
  - `http://[201:23b4:991a:634d:8359:4521:5576:15b7]/yggstate`

- [YGGtracker](http://[201:23b4:991a:634d:8359:4521:5576:15b7]/yggtracker) - [open source](https://github.com/YGGverse/YGGtracker) BitTorrent catalog and tracker
  - `http://[201:23b4:991a:634d:8359:4521:5576:15b7]/yggtracker`

- [YaCy Yggdrasil](http://[30a:5fad::e]) - [YaCy](https://yacy.net) instance for Yggdrasil search.
  - `http://[30a:5fad::e]`

----

## BitTorrent

- Yggdrasil-only torrent tracker, operated by jeff:
  - With DNS: `http://yggtracker.i2p.rocks/` (statistics at `http://yggtracker.i2p.rocks/stats`)
  - Without DNS: `http://[200:1e2f:e608:eb3a:2bf:1e62:87ba:e2f7]/announce` (statistics at `http://[200:1e2f:e608:eb3a:2bf:1e62:87ba:e2f7]/stats`)
  - Filters out non-Yggdrasil IP addresses automatically from announcements

- Yggdrasil-only torrent tracker, operated by [YGGverse](https://github.com/YGGverse):
  - Announce: `http://[201:23b4:991a:634d:8359:4521:5576:15b7]:2023/announce`
  - Stats: `http://[201:23b4:991a:634d:8359:4521:5576:15b7]:2023/stats`
  - Scrape: `http://[201:23b4:991a:634d:8359:4521:5576:15b7]:2023/scrape`

----

## Proxy services

- Proxy to other networks like Yggdrasil, Tor, I2P and clearnet hosted by [acetone](http://[324:71e:281a:9ed3::ace]/):
  - SOCKS: `324:71e:281a:9ed3::fa11` port `1080`
  - HTTP: `324:71e:281a:9ed3::fa11` port `3128`

### Tor bridges

*Note:* If you modified your `torrc` file to exclude groups of non-exit nodes (e.g. using `ExcludeNodes` with country codes, using `ExcludeExitNodes` is fine), Tor will strictly follow the constraints. However because Yggdrasil addresses are not and will not be listed in any IP-to-country databases, Tor cannot guarantee that bridge you entered falls under whatever exclusion policies you specified and will refuse to connect to bridge. In that case you can add `GeoIPExcludeUnknown 0` to your `torrc`.

- `[21b:321:3243:ecb6:a4cf:289c:c0f1:d6eb]:16728 835FFE642EFA3BB7936663D2365A15D319FB6226` operated by [Vort](https://github.com/Vort) ([homepage](http://[21b:321:3243:ecb6:a4cf:289c:c0f1:d6eb]), [metrics](https://metrics.torproject.org/rs.html#details/835FFE642EFA3BB7936663D2365A15D319FB6226))
- `[21f:5234:5548:31e5:a334:854b:5752:f4fc]:9770 6C4C89ABE4D06987AB1F51C06939410282A1BF58` operated by tbxv ([homepage](http://[21f:5234:5548:31e5:a334:854b:5752:f4fc]))
- `[224:6723:7ae0:5655:e600:51c9:4300:a2fb]:9001 F873E91048B40656694BE94ACAB6F0D32CAF8E17` operated by [Werwolf](https://matrix.to/#/@werwolf2517:matrix.org) ([homepage](http://itrus.su), [metrics](https://metrics.torproject.org/rs.html#details/F873E91048B40656694BE94ACAB6F0D32CAF8E17))
- `obfs4 [218:4feb:a509:9db2:2b34:6e7e:e071:5dee]:1992 F805F6B4E5E203EFE2A7FFB1E5042AFE8BD986B4 cert=0GcjnEnZ0rJ8/nfxo4ZSkjMZ0fqHSrvj/MdwEtbbuzx8qgqFTaqHTuWelGw2MxJ5wW2QaQ iat-mode=0` operated by Marek Küthe ([homepage](https://p2p-node.de), [metrics](https://metrics.torproject.org/rs.html#details/F805F6B4E5E203EFE2A7FFB1E5042AFE8BD986B4))

----

## IRC

### BonoboNET

- Accessible via following servers:
  - `201:5920:bb25:e003:c9f4:4dd9:df4b:dfef` port `6667`/`6607`
  - `200:3bea:7cd5:eb3e:7c81:e7de:a426:41a8` port `6667`/`6697`
  - `300:7232:2b0e:d6e9:216:3eff:fe3c:c82b` port `6667`/`6697`
  - `204:fb3e:d9e:9f20:7af1:27ab:6aed:df32` port `6667`/`6697`
  - `200:8101:e046:d91e:d4a2:aad1:f3a0:8ef1` port `6667`/`6697`
  - `201:f742:6335:a85d:8378:9b21:1105:d0bf` port `6667`/`6697`
- Notable channels are `#general`, `#networking` and `#programming`
- More information can be found at the [BonoboNET homepage](http://[203:75b7:45af:ecca:d641:cd42:38dc:4788]/projects/bonobonet/) - we're using unrealircd and open to peering
- Web IRC:
  - `http://[200:e1ad:d2cf:7580:3d78:fce:4ff4:b618]:1024`

### benisnet

- Accessible via Yggdrasil at the following servers:
  - `203:b00a:1684:860d:880f:ea9c:92dc:fabb` port `6667`

### ILITA IRC

- Primarily Russian IRC network of users and developers of meshnets and overlay networks (like I2P) — English is fine too
- Accessible via Yggdrasil at the following server:
  - `324:71e:281a:9ed3::41` port `6667`
- Join `#en` for english discussions
- More information can be found at the [ILITA homepage](http://[324:71e:281a:9ed3::41]/) with additional information about access

----

## DNS

- `324:71e:281a:9ed3::53` port `53`, hosted by [acetone](http://[324:71e:281a:9ed3::ace]/)
  - [Alfis](https://github.com/Revertron/Alfis), [Meshname](https://github.com/zhoreeq/meshname), [OpenNIC](https://www.opennic.org/) and legacy clearnet
  - At the same address, port `80`, [Mario DNS tool](https://notabug.org/acetone/mario-dns) is [available](http://[324:71e:281a:9ed3::53])
- `200:2892:d0f:bc20:8a8e:c7be:9d9e:ab55`, hosted by [darkdrgn2k](http://github.com/darkdrgn2k/)
  - Basic `named` DNS clear-net server maintained running [OpenNIC](https://www.opennic.org/)
- `302:db60::53` port `53`, hosted by [Revertron](https://t.me/Revertron), Praha, Czechia
  - [Alfis](https://github.com/Revertron/Alfis), [AdGuard DNS](https://github.com/AdguardTeam/AdGuardHome)
- `300:6223::53` port `53`, hosted by [Revertron](https://t.me/Revertron), Bratislava, Slovakia
  - [Alfis](https://github.com/Revertron/Alfis), [AdGuard DNS](https://github.com/AdguardTeam/AdGuardHome)
- `302:7991::53` port `53`, hosted by [Revertron](https://t.me/Revertron), Saint Petersburg, Russia
  - [Alfis](https://github.com/Revertron/Alfis), [AdGuard DNS](https://github.com/AdguardTeam/AdGuardHome)
- `325:5a4:d1c9:db96::53` port `53`, hosted by [cofob](https://t.me/cofob), Moscow, Russia
  - [Alfis](https://github.com/Revertron/Alfis), [EmerDNS](https://emercoin.com/en/emerdns)
- `200:9ea3:dd3a:ebbd:726a:89b7:5657:c046` port `53`
  - [CRXN](http://deavmi.assigned.network/projects/crxn) DNS but provides clearnet too
- `300:c0de:3488:123a::53` port `53`, hosted by [h3xcode](https://h3xco.de)
  - [Alfis](https://github.com/Revertron/Alfis) and legacy clearnet (Cloudflare DNS)
- `201:8845:9622:204:8966:1479:2811:1` port `53`, hosted by [PufferOverflow](https://puffer.blog/)
  - Simple clear-net DNS proxy, currently using Level 3 DNS as upstream servers

----

## VoIP

### Hidden Murmur

- [Web page](http://[324:71e:281a:9ed3::cafe]/). Mumble server is accessible from Yggdrasil, Tor and I2P.
  - `324:71e:281a:9ed3::cafe` port `64738`

### Deavmi's MumbleShack

- `200:ad55:3db7:b8cf:3aa8:664b:bca3:bd81` port `64738`

----

## NTP

- `202:a2a5:dead:ded:9a54:4ab5:6aa7:1645` port `123`, hosted by [nikat](https://[302:a2a5:dead:ded::a2a5]/)
- `223:180a:b95f:6a53:5c70:e704:e9fc:8b8f` port `123`, hosted by mkb2191
- `200:8ce3:6def:9d4d:a976:9dfb:f0a6:91ce` port `123`, hosted by [Unkn0wnCat](https://kevink.dev), stratum 2, [NTP Pool Monitored](https://www.ntppool.org/scores/45.9.61.155)

----

## Minecraft

### Java Edition

- `201:92e3:d9df:4932:ca24:586a:4475:6e8b` or `mc.ygg.at` **1.20.1** - Vanilla survival server. Minimal rules and plugins. Fight with mobs, build beautiful buildings. Have a good time with your friends. And leave a trace in the history of this server with other users yggdrasil! Site: [web.mc.ygg](http://web.mc.ygg.at)
- `[202:235:decc:eef8:7dc:a2d4:dbde:12ca]:25565` 1.17.1 Minecraft server hosted by [Salem Yaslem](https://sy.sa/)

### Bedrock Edition

- `[202:235:decc:eef8:7dc:a2d4:dbde:12ca]:19132` Minecraft bedrock server hosted by [Salem Yaslem](https://sy.sa/)

----

### Direct Connect

- `adc://hub.1337.cx:1511`
  - Recommended client is [ncdc](https://dev.yorhel.nl/ncdc).

----

## NNCP

- `203:5096:b2e:5bf3:b3c8:194a:a64f:dd0d` port `5400`. This is the [quux.org NNCP public relay](https://www.complete.org/quux-org-nncp-public-relay) hosted by [jgoerzen](https://www.complete.org/john-goerzen/).

----

## Linux repositories

- `21d:73cc:25d9:1fe:5a4a:47df:eb39:7f`, debian, ubuntu and termux repos [http://[21d:73cc:25d9:1fe:5a4a:47df:eb39:7f]/m](http://[21d:73cc:25d9:1fe:5a4a:47df:eb39:7f]/m) by [@Jorropo](https://github.com/Jorropo).

----

## iPerf3 for speedtests

- [ygg.thingylabs.io](https://ygg.thingylabs.io/) hosted by [Thingylabs GmbH](https://www.thingylabs.io/)
  - `iperf3 -6 -c ygg-only.thingylabs.io`
  - `iperf3 -4 -c ygg-uplink.thingylabs.io`
