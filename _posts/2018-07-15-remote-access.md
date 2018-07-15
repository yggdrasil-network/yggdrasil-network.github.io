---
layout: post
title:  "Using Yggdrasil for remote access"
date:   2018-07-15 22:40:00 +0100
author: Neil Alexander
---

### Replacing the traditional VPN

One of my favourite and most common use-cases for Yggdrasil to date has been
reaching back to my own machines from remote locations, like from work or
wherever I happen to have taken my laptop on a given day. My main point of
interest lately has been connecting to my iMac at home using VNC - which happens
to be built into macOS - over Yggdrasil. I have found Yggdrasil to be an
excellent tool for the job and it effectively replaces my need for a traditional
VPN entirely.

Some of the benefits of using Yggdrasil for remote access are:

1. End-to-end encryption, which provides transport security for protocols that
are normally not very secure out of the box (like VNC or FTP)
1. Superior TCP-over-TCP performance - remarkably better than over OpenVPN or
SSH forwarding!
1. Source verifiability, for being able to whitelist based on source IPv6
address and firewall everyone else out
1. Routable from anywhere on the network

#### End-to-end encryption

All tunnelled traffic over Yggdrasil takes place within encrypted "sessions". A
session is established between you and a remote node when you attempt to send
traffic to that node. As a part of the session handshake, your public encryption
keys are exchanged, which are used to provide complete end-to-end encryption of
all encapsulated network traffic.

Even plain-text protocols will appear to be encrypted in transit across the
Yggdrasil network, ensuring that no intermediate routers or adversaries can spy
on your traffic, and making it much safer to use protocols like VNC which may
not be robustly secure by themselves.

It also means that it is possible to send and receive traffic over Yggdrasil
with confidence, without worrying so much about the security of the network in a
given location. Open Wi-Fi networks in public places are particularly a
nightmare for any kind of connection security, as they are often not protected
with wireless encryption, but this is less of a concern with Yggdrasil.

#### TCP-over-TCP performance

In the past I had tried using OpenVPN and SSH tunnelling as a method of reaching
back home by terminating the VPN connection on my EdgeRouter X. The network at
my workplace allows outbound TCP connection on a small number of ports and
seemingly drops UDP at the internet boundary altogether, therefore I was
restricted to using OpenVPN in TCP mode rather than UDP mode.

For transferring small amounts of information, this turned out to be functional
but certainly not exceptional. OpenVPN doesn't perform any kind of optimisation
on TCP traffic, therefore the performance on anything latency-sensitive was
awful - VNC back to my iMac was simply unusable. SSH forwarding was no better.

Instead, I installed Yggdrasil onto my EdgeRouter X and used this as a gateway
to route to my home machines directly (a topic for a future blog post). Every
Yggdrasil node has a routed /64 subnet which, in my case, was `NETMAP`'d using
`iptables` to my home IPv6 ULA range. This effectively makes my entire home
network routable on the Yggdrasil network (even devices that are not
Yggdrasil-aware or do not have it installed), subject to some firewall
restrictions which I will discuss shortly.

The main benefits here are that Yggdrasil uses a LIFO queue for session traffic
and also takes advantage of huge MTUs (as discussed in my previous blog post) to
lessen the effect of TCP control message amplification and improved congestion
handling. This results in a much more usable and stable connection when
tunnelling TCP over a TCP Yggdrasil peering - the stability and the
responsiveness of VNC proved to be much improved in this setup than over OpenVPN
or SSH.

#### Firewalls and source verifiability

Of course, there are other people on the Yggdrasil network and you can't be
certain who they are or if they may or may not be malicious. Therefore just like
on the Internet, it is wise to make use of a firewall. Source verifiability
comes in particularly useful here as it makes it quite easy to allow only
specific machines to send traffic through your firewall and to keep everyone
else out.

In Yggdrasil, your IPv6 address is directly associated with your encryption
keypair, therefore any changes to your keypair result in your IPv6 address
changing with it. When you generate a configuration (using `yggdrasil -genconf`)
then you are effectively deriving a new IPv6 address. As long as you continue to
use the same Yggdrasil configuration with the same encryption keypair on a given
machine, then that machine will keep the same Yggdrasil IPv6 address forever.

This meant that I could create a whitelist on my EdgeRouter firewall to drop
connection headed towards my network unless it came from a specific list of
Yggdrasil IPv6 addresses known to me already, i.e. my work computer and my
laptop. Any other unexpected connections from addresses outside of this
whitelist are dropped, hiding my machines from anyone else on the network.

An example of how I achieved this on my EdgeRouter X:
```
set firewall group ipv6-address-group YGG_TRUSTED ipv6-address 'xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx'
set firewall group ipv6-network-group YGG_TRUSTEDNETS ipv6-network 'xxx:xxx:xxx::/48'

set firewall ipv6-name YGG_IN default-action drop
set firewall ipv6-name YGG_IN rule 10 action accept
set firewall ipv6-name YGG_IN rule 10 state established enable
set firewall ipv6-name YGG_IN rule 10 state related enable
set firewall ipv6-name YGG_IN rule 20 action drop
set firewall ipv6-name YGG_IN rule 20 state invalid enable
set firewall ipv6-name YGG_IN rule 30 action accept
set firewall ipv6-name YGG_IN rule 30 source group ipv6-address-group YGG_TRUSTED
set firewall ipv6-name YGG_IN rule 40 action accept
set firewall ipv6-name YGG_IN rule 40 source group ipv6-network-group YGG_TRUSTEDNETS

set firewall ipv6-name YGG_LOCAL default-action drop
set firewall ipv6-name YGG_LOCAL rule 10 action accept
set firewall ipv6-name YGG_LOCAL rule 10 state established enable
set firewall ipv6-name YGG_LOCAL rule 10 state related enable
set firewall ipv6-name YGG_LOCAL rule 20 action drop
set firewall ipv6-name YGG_LOCAL rule 20 state invalid enable
set firewall ipv6-name YGG_LOCAL rule 30 action accept
set firewall ipv6-name YGG_LOCAL rule 30 source group ipv6-address-group YGG_TRUSTED

set interfaces yggdrasil tun0 firewall in ipv6-name YGG_IN
set interfaces yggdrasil tun0 firewall local ipv6-name YGG_LOCAL
```

#### Routable from the whole network

You don't need to be directly peered to a node to be able to remotely access it -
you can route traffic from anywhere on the Yggdrasil network. It is preferable
to always peer with nodes that are geographically close to your own location, as
this helps to reduce latency across the network. You can, however, set up in a
new location (for example, with a laptop) and connect to your nearest Yggdrasil
node and still be able to reach your machines like before.

### Conclusion

Yggdrasil has proven to be a more-than-capable method for remotely accessing my
home network and very much normalises the idea that all network traffic should
be encrypted and treated as if it is private, even in locations where connection
security would not otherwise be guaranteed.
