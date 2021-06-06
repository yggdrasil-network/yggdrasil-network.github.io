---
sitemap: true
tags: dontlink
---

# Configuration

Yggdrasil can run in one of two modes: with a configuration file, or in autoconfigure mode.

A static configuration file simplifies most setups as it allows you to keep the same keypair (and therefore IP address), maintain a list of peers and so on. For most users this will be the recommended configuration. Most Yggdrasil distribution packages will generate a configuration file automatically.

However, autoconfigure mode allows you to quickly start Yggdrasil using sane-ish default settings, with `yggdrasil --autoconf`. In this mode, Yggdrasil will automatically attempt to peer with other nodes on the same subnet, but it also generates a random set of keys each time it is started, and therefore a random IP address.

Yggdrasil supports configuration in either HJSON or JSON format. HJSON is the default preferred format, as it has comments, although JSON support is available due to ease of manipulation.

## Generating a new config file

If you installed Yggdrasil through one of the platform packages (i.e. macOS, Debian, RPM) then a default configuration file may already exist in `/etc/yggdrasil.conf`.

Otherwise, you can generate a configuration file in the following ways:

| **Generate HJSON**: | `yggdrasil -genconf > /etc/yggdrasil.conf` |
| **Generate JSON**: | `yggdrasil -genconf -json > /etc/yggdrasil.conf` |

## Using a config file

Yggdrasil can accept a configuration file either through `stdin` or by being given a path on the filesystem to a configuration file:

| **Using stdin**: | `yggdrasil -useconf < /etc/yggdrasil.conf` |
| **Using file:** | `yggdrasil -useconffile /etc/yggdrasil.conf` |

## Normalising a config file

If you want to see the original format of the configuration file, or convert between HJSON and JSON formats, you can use the `-normaliseconf` option, e.g.

| **Convert from HJSON to JSON**: | `yggdrasil -normaliseconf -useconffile /etc/yggdrasil.conf -json` |
| **Convert from JSON to HJSON**: | `yggdrasil -normaliseconf -useconffile /etc/yggdrasil.conf` |

Normalising the configuration also adds any missing configuration items with their default values. This can be useful when upgrading to a newer version of Yggdrasil that adds new configuration options. Many of our distribution packages normalise the configuration automatically during upgrade.

## Manually Connecting to Peers

Yggdrasil can be configured to connect to other peers by adding entries into the `Peers` configuration section. At startup, Yggdrasil will attempt to open a connection to these peers. 

A peering URI will look similar to one of the following:

| **Direct TCP connection**: | `tcp://hostname:port` |
| **Direct TCP+TLS connection**: | `tls://hostname:port` |
| **Via a SOCKS proxy**: | `socks://proxyhostname:proxyport/hostname:port` |

By default, only link-local auto-peering is enabled. This connects devices that are connected directly to each other at layer 2, including devices on the same LAN, directly connected by ethernet or configured to use the same ad-hoc wireless network.

As the network uses ordinary TCP, it is possible to connect over other networks, such as the Internet or WAN links, provided that the connecting node knows the address and port to connect to and that the connection is not blocked by a NAT or firewall. If the node resides behind a NAT, then port forwarding may be required in order to accept incoming connections.

If you are unable to find nodes in the nearby area, a best effort is made to maintain a list of [Public Peers](https://github.com/yggdrasil-network/public-peers) for new users looking to join or test the network.

## Advertising a Prefix

While it is generally encouraged that nodes run the software locally, to provide end-to-end cryptographic sessions and participate in routing, this is not always practical. The device may not have the resources to run the software or may not be able to run user code. To that end, Yggdrasil also provides each node with a routed `/64` subnet in addition to their IPv6 address. A node running Yggdrasil that acts as a router may advertise this prefix to other devices just as they would any other ordinary IPv6 network.

This may be best illustrated by example.
Suppose a node has generated the address: `200:1111:2222:3333:4444:5555:6666:7777`.
Then the node may also use addresses from the prefix: `300:1111:2222:3333::/64` (note the `200` changed to `300`, a separate `/8` is used for prefixes, but the rest of the first 64 bits are the same).

On Linux, something like the following should be sufficient to advertise a prefix and a route to `200::/7` using radvd to a network attached to the `eth0` interface:

1. Enable IPv6 forwarding (e.g. `sysctl -w net.ipv6.conf.all.forwarding=1` or add it to `sysctl.conf`)

2. `ip addr add 300:1111:2222:3333::1/64 dev eth0` or similar, to assign an address for the router to use in that prefix, where the LAN is reachable through `eth0`

3. Install/run `radvd` with something like the following in `/etc/radvd.conf`:
```
interface eth0
{
        AdvSendAdvert on;
        AdvDefaultLifetime 0;
        prefix 300:1111:2222:3333::/64 {
            AdvOnLink on;
            AdvAutonomous on;
        };
        route 200::/7 {};
};
```

Note that a `/64` prefix has fewer bits of address space available to check against the node's ID, which in turn means hash collisions are more likely. As such, it is unwise to rely on addresses as a form of identity verification for the `300::/8` address range.

## Generating Stronger Addresses (and Prefixes)

While 128 bits is long enough to make collisions technically impractical, if not outright impossible, it's not unreasonable to think that 64 bits may be attackable at some point if not now.
Without going too far into the details, addresses are a truncated hash of a node's public key, with leading `1` bits accumulated and suppressed (along with the inevitable first `0` bit).
Thanks to the accumulator, it is possible to brute force generate keys which include more bits of the node's ID in the node's IPv6 address, thereby making collisions more difficult.
This can partially mitigate the fact that IPv6 addresses are only 128 bits long, and, more importantly, that prefixes are a mere 64 bits, 16 bits of which are sacrificed to the `200::/7` prefix and 1-byte accumulator in either case.

In short, if you plan to advertise a prefix, or if you want your address to be exceptionally difficult to collide with, then it is strongly advised that you burn some CPU cycles generating a harder-to-collide set of keys, using the following tool:

```
go run cmd/genkeys
```

This continually generates new keys and prints them out each time a new best set of keys is discovered.
These keys may then be manually added to the configuration file.
