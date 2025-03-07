---
tags: dontlink
sitemap: true
---

# Services

The following services are available on the Yggdrasil Network, courtesy of our community users. If you host a service on the Yggdrasil Network or find that a service is no longer available, please feel free to [open a pull request on GitHub](https://github.com/yggdrasil-network/yggdrasil-network.github.io/edit/master/services.md)

----

## Commercial services

### IT consultancies

- [Thingylabs GmbH](https://yyy.thingylabs.io/) - An IT consultancy for Web, Cloud, and Apps.
  - `https://yyy.thingylabs.io/`
  - `http://[22a:bd80:1588:5365:e467:c5fa:6123:5018]/`
 
- [IncogNET LLC](https://incognet.io/) - Privacy and Free Speech Focused ISP - 5Gbps AMD Epyc VPS, Domain Registrar, WireGuard VPN, Etc.
  - `http://ygg.incognet.io/`
  - `http://[202:7f4c:a4fe:e85b:4410:21f4:7b8c:99c]/`

----

## Websites

- [Yggdrasil Web directory](http://[21e:a51c:885b:7db0:166e:927:98cd:d186]/)
  - `http://[21e:a51c:885b:7db0:166e:927:98cd:d186]/`

- [Yggdrasil Map](http://[21e:e795:8e82:a9e2:ff48:952d:55f2:f0bb]/)
  - `http://[21e:e795:8e82:a9e2:ff48:952d:55f2:f0bb]/`

- [deavmi's homepage](http://[203:75b7:45af:ecca:d641:cd42:38dc:4788]/)
  - `http://[203:75b7:45af:ecca:d641:cd42:38dc:4788]/`

- [HowTo Ygg: Yggdrasil wiki](http://[222:a8e4:50cd:55c:788e:b0a5:4e2f:a92c]/)
  - `http://[222:a8e4:50cd:55c:788e:b0a5:4e2f:a92c]/`

- [Acetone's service node](http://[324:71e:281a:9ed3::ace]/)
  - `http://[324:71e:281a:9ed3::ace]/`

- [Nikat's homepage](https://[302:a2a5:dead:ded::a2a5]/)
  - `https://[302:a2a5:dead:ded::a2a5]/`

- [Yggdrasil Forum](http://[21a:34aa:c782:3ad2:1bf8:73f8:141:66e8]) - Forum in yggdrasil, about yggdrasil
  - `http://[21a:34aa:c782:3ad2:1bf8:73f8:141:66e8]`

- [IP and Port Checker](http://[200:56bd:a9e9:c1fa:8f99:1d3c:3c84:6507])
  - `http://[201:30d4:d28:2185:b947:8817:cca5:ef95]`

----

## BitTorrent

- Yggdrasil-only torrent tracker, operated by jeff:
  - With DNS: `http://yggtracker.i2p.rocks/` (statistics at `http://yggtracker.i2p.rocks/stats`)
  - Without DNS: `http://[200:1e2f:e608:eb3a:2bf:1e62:87ba:e2f7]/announce` (statistics at `http://[200:1e2f:e608:eb3a:2bf:1e62:87ba:e2f7]/stats`)
  - Filters out non-Yggdrasil IP addresses automatically from announcements

----

## Proxy services

- Proxy to other networks like Yggdrasil, Tor, I2P and clearnet hosted by [acetone](http://[324:71e:281a:9ed3::ace]/):
  - SOCKS: `324:71e:281a:9ed3::fa11` port `1080`
  - HTTP: `324:71e:281a:9ed3::fa11` port `3128`

- Community-driven proxy catalog with configuration examples by [YGGverse](https://github.com/YGGverse/YGGbro):
  - `http://[316:c51a:62a3:8b9::4]/YGGverse/YGGbro`

### Tor bridges

*Note:* If you modified your `torrc` file to exclude groups of non-exit nodes (e.g. using `ExcludeNodes` with country codes, using `ExcludeExitNodes` is fine), Tor will strictly follow the constraints. However because Yggdrasil addresses are not and will not be listed in any IP-to-country databases, Tor cannot guarantee that bridge you entered falls under whatever exclusion policies you specified and will refuse to connect to bridge. In that case you can add `GeoIPExcludeUnknown 0` to your `torrc`.

- `[21b:321:3243:ecb6:a4cf:289c:c0f1:d6eb]:16728 835FFE642EFA3BB7936663D2365A15D319FB6226` operated by [Vort](https://github.com/Vort) ([homepage](http://[21b:321:3243:ecb6:a4cf:289c:c0f1:d6eb]), [metrics](https://metrics.torproject.org/rs.html#details/835FFE642EFA3BB7936663D2365A15D319FB6226))
- `[21f:5234:5548:31e5:a334:854b:5752:f4fc]:9770 6C4C89ABE4D06987AB1F51C06939410282A1BF58` operated by tbxv ([homepage](http://[21f:5234:5548:31e5:a334:854b:5752:f4fc]))
- `[224:6723:7ae0:5655:e600:51c9:4300:a2fb]:9001 F873E91048B40656694BE94ACAB6F0D32CAF8E17` operated by [Werwolf](https://matrix.to/#/@werwolf2517:matrix.org) ([homepage](http://itrus.su), [metrics](https://metrics.torproject.org/rs.html#details/F873E91048B40656694BE94ACAB6F0D32CAF8E17))
- `obfs4 [218:4feb:a509:9db2:2b34:6e7e:e071:5dee]:1992 F805F6B4E5E203EFE2A7FFB1E5042AFE8BD986B4 cert=0GcjnEnZ0rJ8/nfxo4ZSkjMZ0fqHSrvj/MdwEtbbuzx8qgqFTaqHTuWelGw2MxJ5wW2QaQ iat-mode=0` operated by Marek Küthe ([homepage](https://p2p-node.de), [metrics](https://metrics.torproject.org/rs.html#details/F805F6B4E5E203EFE2A7FFB1E5042AFE8BD986B4))

----

## IRC

### BonoboNET

- Accessible via Yggdrasil at the following servers:
  - **Preferred:** `y.irc.bnet.eu.org` port `6667` (round-robin DNS entry for all servers listed below)
  - `219:3cf0:dd1d:47b9:555e:75ac:2ab5:493b` port `6667`
  - `204:fb3e:d9e:9f20:7af1:27ab:6aed:df32` port `6667`
  - `200:6f99:2afe:41fd:fc3b:b1f7:af9d:f3a0` port `6667`
- Notable channels are `#general`, `#networking` and `#programming`
- More information can be found at the [BonoboNET homepage](http://[203:75b7:45af:ecca:d641:cd42:38dc:4788]/projects/bonobonet/) - we're using unrealircd and open to peering

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
- `302:db60::53` port `53`, hosted by [Revertron](https://t.me/Revertron), Praha, Czechia
  - [Alfis](https://github.com/Revertron/Alfis), [AdGuard DNS](https://github.com/AdguardTeam/AdGuardHome)
- `300:6223::53` port `53`, hosted by [Revertron](https://t.me/Revertron), Bratislava, Slovakia
  - [Alfis](https://github.com/Revertron/Alfis), [AdGuard DNS](https://github.com/AdguardTeam/AdGuardHome)
- `302:7991::53` port `53`, hosted by [Revertron](https://t.me/Revertron), Saint Petersburg, Russia
  - [Alfis](https://github.com/Revertron/Alfis), [AdGuard DNS](https://github.com/AdguardTeam/AdGuardHome)

----

## VoIP

### Hidden Murmur

- [Web page](http://[324:71e:281a:9ed3::cafe]/). Mumble server is accessible from Yggdrasil, Tor and I2P.
  - `324:71e:281a:9ed3::cafe` port `64738`

----

## NTP

- `202:a2a5:dead:ded:9a54:4ab5:6aa7:1645` port `123`, hosted by [nikat](https://[302:a2a5:dead:ded::a2a5]/)
- `223:180a:b95f:6a53:5c70:e704:e9fc:8b8f` port `123`, hosted by mkb2191
- `200:8ce3:6def:9d4d:a976:9dfb:f0a6:91ce` port `123`, hosted by [Unkn0wnCat](https://kevink.dev), stratum 2, [NTP Pool Monitored](https://www.ntppool.org/scores/45.9.61.155)
- `201:72f6:9b56:e977:2d40:c7da:667b:f7a0` port `123`, hosted by [lcharles123](https://github.com/lcharles123), Located in US
  - `ygg.nerdvm.mywire.org`

----

## Minecraft

### Java Edition

- `201:92e3:d9df:4932:ca24:586a:4475:6e8b` or `mc.ygg.at` **1.21.1** - Vanilla survival server. Minimal rules and plugins. Fight with mobs, build beautiful buildings. Have a good time with your friends. And leave a trace in the history of this server with other users yggdrasil! Site: [web.mc.ygg](http://web.mc.ygg.at)

### Minetest

- `[203:e93b:9902:9064:5efe:575b:3284:e1d2]:30000` Minetest server hosted by [mittwerk](mittwerk@yggdrasil.link)

----

## Half-Life

### Stats

- `http://[201:5eb5:f061:678e:7565:6338:c02c:5251]/hl/` - [Open Source](https://github.com/YGGverse/HLState) `0200::/7` tracker of `hl.ygg:27010` master server
  - `http://hl.ygg` - Alfis DNS alias

### Masters

- `http://[201:5eb5:f061:678e:7565:6338:c02c:5251]:27010` - Xash3D / FWGS `0200::/7` server by [YGGverse](https://github.com/YGGverse)
  - `hl.ygg:27010` - Alfis DNS alias

### Game servers

- `[300:eaff:7fab:181b::e949]:7588` - Xash3D / FWGS server by [Skunky](http://[300:eaff:7fab:181b::e621]/)
- `[201:5eb5:f061:678e:7565:6338:c02c:5251]:27015` - Xash3D / FWGS server by [YGGverse](https://github.com/YGGverse) with Crossfire map and funny bots
  - `hl.ygg:27015` - Alfis DNS alias
  - `94.140.114.89:27015` - Clearnet alias

----

## NNCP

- `203:5096:b2e:5bf3:b3c8:194a:a64f:dd0d` port `5400`. This is the [quux.org NNCP public relay](https://www.complete.org/quux-org-nncp-public-relay) hosted by [jgoerzen](https://www.complete.org/john-goerzen/).

----

## iPerf3 for speedtests

- [ygg.thingylabs.io/speedtest.html](https://ygg.thingylabs.io/speedtest.html) sponsored by [Thingylabs](https://www.thingylabs.io/)
  - `iperf3 -6 -c ygg-only.thingylabs.io`
  - `iperf3 -4 -c ygg-uplink.thingylabs.io`
 
----

## Reticulum nodes

- `200:73eb:2e4:14be:aac7:90b3:784b:71a3` port `4242` TCP
