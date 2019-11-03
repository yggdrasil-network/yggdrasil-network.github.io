---
layout: post
title:  "Crypto-Key Routing"
date:   2018-11-06 23:00:00 -0000
author: Neil Alexander
---

### Tunnelling over Yggdrasil

Up until now, Yggdrasil has worked entirely using internal addressing from the
`0200::/7` range. These addresses are cryptographically bound and allow
end-to-end communication between any two given nodes. However, this approach is
somewhat limiting in that it assumes an Yggdrasil node is, at most, a gateway
for a single /64 IPv6 subnet. This therefore meant that it is very difficult
to route between or bridge two networks without using another encapsulation
protocol such as GRE. It also meant that it was very difficult to provide access
to networks like the Internet internally.

Today I created a pull request which implements Crypto-Key Routing (CKR), which
you may already be familiar with if you have used Wireguard. The premise behind
CKR is that it allows you to define routes stating that specific subnets should
be routed to a given node on the network, identified by their public encryption
key. The traffic is encrypted end-to-end, as normal, and sent to the specified
node, otherwise completely unmodified. This gives us effectively the ability to
run VPNs over the Yggdrasil network without the need for additional software.

### Configuration

The new CKR module proposes the addition of some new configuration options. They
are summarised below, although may be subject to change at the point that v0.3
is released:
```
TunnelRouting:
{
  Enable: false
  IPv6RemoteSubnets: {}
  IPv6LocalSubnets: []
  IPv4RemoteSubnets: {}
  IPv4LocalSubnets: []
}
```

The `IPv6RemoteSubnets` and `IPv4RemoteSubnets` options are used to specify
crypto-key routes. The subnet refers to an encryption public key of another
node, for example:
```
IPv6RemoteSubnets: {
  "fd64:642b:1a20::/48": "ef78da7fc983c6c210609529921a701ca3e43fa5cfd79f5f20cc67bf66e45c1a",
  "fd25:8a33:9311:a53b::/64": "417fd0a66a104f050ae3544b3bc03eeb3648dded4a8c1fb085d65ffa25e83d6e"
}
```
As with a typical routing table, more specific routes are preferred.

The `IPv6LocalSubnets` and `IPv4LocalSubnets` options are used to specify which source
addresses are eligible to be routed across a tunnel. These options are mandatory.

### Bridging networks

Assume that node A is `a.a.a.a/24` and `aaaa:aaaa:aaaa:aaaa::/64` and node B is `b.b.b.b/24` and `bbbb:bbbb:bbbb:bbbb::/64`. On node A, use the following `TunnelRouting` configuration:
```
Enable: true
IPv6RemoteSubnets: {
  "bbbb:bbbb:bbbb:bbbb::/64": "xxxxxxxxxxxxxx"
}
IPv6LocalSubnets: {
  aaaa:aaaa:aaaa:aaaa::/64
}
IPv4RemoteSubnets: {
  "b.b.b.b/24": "xxxxxxxxxxxxxx"
}
IPv4LocalSubnets: {
  a.a.a.a/24
}
```
On node B, use the reverse `TunnelRouting` configuration:
```
Enable: true
IPv6RemoteSubnets: {
  "aaaa:aaaa:aaaa:aaaa::/64": "xxxxxxxxxxxxxx"
}
IPv6LocalSubnets: {
  bbbb:bbbb:bbbb:bbbb::/64
}
IPv4RemoteSubnets: {
  "a.a.a.a/24": "xxxxxxxxxxxxxx"
}
IPv4LocalSubnets: {
  b.b.b.b/24
}
```
The Yggdrasil TUN/TAP adapters on each side can now be used to tunnel between `a.a.a.a/24` and `b.b.b.b/24` in IPv4 and `aaaa:aaaa:aaaa:aaaa::/64` and `bbbb:bbbb:bbbb:bbbb/64` in IPv6.

### Conclusion

It is my hope that this will be merged into the `develop` branch soon, and will
be a feature targeted for general release in v0.3. With this, Yggdrasil is able
to take on more advanced routing tasks and will hopefully be more useful in a
wider range of scenarios.
