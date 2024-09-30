---
sitemap: true
tags: dontlink
---

# Advanced Peerings
This document contains information on more advanced peering setups. 
### Multi-homed machine
On a machine that has multiple network interfaces, where more than one interface can be used to reach a given peer (or the Internet), the `InterfacePeers` option can be used instead of the `Peers` option to create peerings over specific interfaces.

It is possible to connect to the **same peer multiple times** using different interfaces, creating a highly-available peering configuration:

```
InterfacePeers: {
  "eth0": [
    tls://a.b.c.d:e
  ]
  "eth1": [
    tls://a.b.c.d:e
  ]
}
```

### Prioritised listeners
Like in the above multi-homed example, the machine may have two or more network interfaces. An example may be a Wi-Fi interface and a wired ethernet interface. If you want to accept incoming peering connections on both, but to specifically prefer sending traffic over one or other link whenever possible, you can use the `priority` field in the peering URI.

Lower numbers are better. In this example, the `a.b.c.d` IP address belongs to the wired ethernet adapter and the `f.g.h.i` IP address belongs to the Wi-Fi adapter. Peerings to the priority 1 peering port will be used whenever possible, therefore if another node peers to both interfaces, the wired peering will be preferred over wireless one:

```
tls://a.b.c.d:e?priority=1
tls://f.g.h.i:e?priority=2
```

Note that the `priority` field only influences traffic between two peerings to the same node. It does not affect routing decisions across different nodes and cannot be used, for example, to avoid sending traffic to one remote peer over another.

### Prioritised multicast interfaces
If like above you have multiple network interfaces but are using multicast peers, but want to prefer one network interface over the other, you can specify the `Priority` for a given multicast interface.

Lower numbers are better. In this example, peerings that are automatically discovered and established over `eth0` will be preferred over `eth1`:

```
MulticastInterfaces: [
  {
    Regex: eth0
    Beacon: true
    Listen: true
    Priority: 1
  }, 
  {
    Regex: eth1
    Beacon: true
    Listen: true
    Priority: 2
  }
]
```

### Multiple Tor circuits
Peering over Tor is possible using the SOCKS proxy support, although it is typically discouraged as Tor peerings are often slow and fragile. Most notably, Tor circuits can be broken at any time, taking down your Yggdrasil peering connection with it.

To mitigate this, enable the `IsolateSOCKSAuth ` option in Tor and then establish **multiple connections to the same peer** using different SOCKS usernames and passwords. The usernames and passwords can be anything, but these unique pairs will force Tor to use a separate circuit for each peering connection, providing redundancy against one of those circuits breaking:

```
Peers: [
  socks://one:one@localhost:9050/a.b.c.d:e
  socks://two:two@localhost:9050/a.b.c.d:e
  socks://three:three@localhost:9050/a.b.c.d:e
]
```

For more information, see the Tor manual for `IsolateSOCKSAuth`. 