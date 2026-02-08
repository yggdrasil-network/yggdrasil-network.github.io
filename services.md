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

- [Yggdrasil Map](http://[227:bc24:9a82:e290:7f4a:f992:2ef6:5b34]:8008/)
  - `http://[227:bc24:9a82:e290:7f4a:f992:2ef6:5b34]:8008/`

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
  - `http://[200:56bd:a9e9:c1fa:8f99:1d3c:3c84:6507]`

- [Services by it's port number](http://[220:73c5:19cd:3cba:de5d:b065:807f:8b67])
  - `http://[220:73c5:19cd:3cba:de5d:b065:807f:8b67]`

- [Yggdrasil search engine](http://[200:b48d:469e:c7c7:3e13:c41d:ba4d:d2b8])
  - `http://[200:b48d:469e:c7c7:3e13:c41d:ba4d:d2b8]` 

- [Ukrainian community forums](http://[202:68d0:f0d5:b88d:1d1a:555e:2f6b:3148])
  - `http://[202:68d0:f0d5:b88d:1d1a:555e:2f6b:3148]` 

- [Freedit instance: Reddit-like forums without JavaScript or email requirements](http://[201:9d6a:f4e2:32d9:b005:cee0:2e6:47cd])
  - `http://[201:9d6a:f4e2:32d9:b005:cee0:2e6:47cd]` 

----

## BitTorrent

- Yggdrasil-only torrent tracker, operated by jeff:
  - With DNS: `http://yggtracker.i2p.rocks/` (statistics at `http://yggtracker.i2p.rocks/stats`)
  - Without DNS: `http://[200:1e2f:e608:eb3a:2bf:1e62:87ba:e2f7]/announce` (statistics at `http://[200:1e2f:e608:eb3a:2bf:1e62:87ba:e2f7]/stats`)
  - Filters out non-Yggdrasil IP addresses automatically from announcements
- Yggdrasil-only torrent tracker:
  - `udp://[202:68d0:f0d5:b88d:1d1a:555e:2f6b:3148]:6969` - announce
    - `http://[202:68d0:f0d5:b88d:1d1a:555e:2f6b:3148]:6969` - stats
----

## Proxy services

- Proxy to other networks like Yggdrasil, Tor, I2P and clearnet hosted by [acetone](http://[324:71e:281a:9ed3::ace]/):
  - SOCKS: `324:71e:281a:9ed3::fa11` port `1080`
  - HTTP: `324:71e:281a:9ed3::fa11` port `3128`

- Community-driven proxy catalog with configuration examples by [YGGverse](https://github.com/YGGverse/YGGbro):
  - `http://[316:c51a:62a3:8b9::4]/YGGverse/YGGbro`

### Tor bridges

*Note:* If you modified your `torrc` file to exclude groups of non-exit nodes (e.g. using `ExcludeNodes` with country codes, using `ExcludeExitNodes` is fine), Tor will strictly follow the constraints. However because Yggdrasil addresses are not and will not be listed in any IP-to-country databases, Tor cannot guarantee that bridge you entered falls under whatever exclusion policies you specified and will refuse to connect to bridge. In that case you can add `GeoIPExcludeUnknown 0` to your `torrc`.

- `Bridge [218:4feb:a509:9db2:2b34:6e7e:e071:5dee]:1991 F805F6B4E5E203EFE2A7FFB1E5042AFE8BD986B4` operated by Marek Küthe ([homepage](https://mk16.de/tor/), [metrics](https://metrics.torproject.org/rs.html#details/F805F6B4E5E203EFE2A7FFB1E5042AFE8BD986B4))
- `Bridge [220:f022:cd6c:22a9:5285:79e2:2e19:b66a]:1991 6580023D474DD3C06920027530C3B5E39B89DC03` operated by Marek Küthe ([homepage](https://mk16.de/tor/), [metrics](https://metrics.torproject.org/rs.html#details/6580023D474DD3C06920027530C3B5E39B89DC03))
- `Bridge [219:a32f:307b:8372:25d3:e1d0:c8fb:d2f6]:1991 49A53936AF2895A1612603AAA3C1CF8A01830157` operated by Marek Küthe ([homepage](https://mk16.de/tor/), [metrics](https://metrics.torproject.org/rs.html#details/49A53936AF2895A1612603AAA3C1CF8A01830157))
- `obfs4 [200:ba50:4b2c:8dc9:9529:38de:5677:57a0]:7917 79F9F7CBD0E2A4458F11B7874008D783BCF5C523 cert=3ues7AA498O9YrQ1wsdWyz3/n3YBXN88wXP8mppkE0lTw1YB6FSlDMkm3Ry6jXlj0phzMg iat-mode=0` or `[200:ba50:4b2c:8dc9:9529:38de:5677:57a0]:7918 79F9F7CBD0E2A4458F11B7874008D783BCF5C523` operated by ValeriusN ([telegram](https://t.me/valeriusN))

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

### YGGverse

- `http://[202:68d0:f0d5:b88d:1d1a:555e:2f6b:3148]:6680` - Shared ZNC bouncer for Yggdrasil, Mycelium, I2P, or Tor channels

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

## Code Forges

### [Radicle](https://radicle.xyz) Nodes

 - `z6MkocYY4dgMjo2YeUEwQ4BP4AotL7MyovzJCPiEuzkjg127@[202:f094:502b:1b03:9e0:2c3d:bc8b:428b]:8776` ([web](http://[202:f094:502b:1b03:9e0:2c3d:bc8b:428b])), hosted by [Lorenz](https://lorenz.leutgeb.xyz) on a VPS in Germany.

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
- `203:b2b2:24a0:2e7d:a851:3786:3566:a0a3` **1.21.11** - Vanilla survival server, overworld is desert only. Site is [here](http://[203:b2b2:24a0:2e7d:a851:3786:3566:a0a3]/minecraft/)
- `202:a6cd:5030:bb11:a49d:4ee2:5377:5135` **1.18.2** - Vanilla survival server with minimal mods. The only rule is to not grief.

### Minetest

- `[203:e93b:9902:9064:5efe:575b:3284:e1d2]:30000` Minetest server hosted by [mittwerk](mittwerk@yggdrasil.link)

----

## Half-Life

[Xash3D / FWGS](https://github.com/FWGS/xash3d-fwgs) servers by [lost+skunk](http://[222:a8e4:50cd:55c:788e:b0a5:4e2f:a92c]/user:skunky)

### Master

- `[300:dee4:d3c0:953b::2019]:27010`

### Game servers

- `[300:eaff:7fab:181b::e949]:7588`
- `[301:f137:d1ac:920e::2019]:27015` - 9 bots, Yggdrasil only

----

## NNCP

- `203:5096:b2e:5bf3:b3c8:194a:a64f:dd0d` port `5400`. This is the [quux.org NNCP public relay](https://www.complete.org/quux-org-nncp-public-relay) hosted by [jgoerzen](https://www.complete.org/john-goerzen/).

----

## iPerf3 for speedtests

- [ygg.thingylabs.io/speedtest.html](https://ygg.thingylabs.io/speedtest.html) sponsored by [Thingylabs](https://www.thingylabs.io/)
  - `iperf3 -6 -c ygg-only.thingylabs.io`
  - `iperf3 -4 -c ygg-uplink.thingylabs.io`
 
----
## Monero Nodes

- [XMR.GD](https://xmr.gd/) - Public (Mainnet) Monero Node available on Yggdrasil (And clearnet, Tor, and I2P). Sponsored by [IncogNET](https://incognet.io/)
  - `ygg.xmr.gd:18089`
  - `201:2d47:2607:ecaf:2909:2dc5:926d:a8df:18089`

----

## Reticulum nodes

- `200:73eb:2e4:14be:aac7:90b3:784b:71a3` port `4242` TCP
- [more...](https://github.com/markqvist/Reticulum/wiki/Community-Node-List#yggdrasil)
