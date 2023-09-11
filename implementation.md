---
sitemap: true
---

# Implementation

The public Yggdrasil Network is a testbed overlay network running the Yggdrasil routing protocol.

All network nodes are userspace software routers that run under a single process. The router optionally provides a virtual TUN adapter to the host operating system which allows sending and receiving IPv6 traffic over the Yggdrasil Network. Peering connections between these nodes are established over local area networks, point-to-point links or the Internet using mainly TCP/TLS connections. There are a number of [Public Peers](https://publicpeers.neilalexander.dev) that can be used to join the public network.

Implementing Yggdrasil as an overlay network allows us to test the protocol and routing scheme at scale relatively easily, as the user-space router can easily run on most computers. Users also do not need to be physically close to one another in order to join or test the network. Users can just download the software and establish some peerings over the Internet or other over networks. However, the Yggdrasil design does not constrain it to only being implemented as an overlay network — it could also be implemented as a native routing protocol with physical links instead.

### Security

In order to ensure that traffic is private as it is routed across other network nodes, all traffic is end-to-end encrypted at all times. Even plain-text application traffic will be encrypted using the destination node's public key, ensuring that intermediate nodes cannot snoop on the contents of traffic being forwarded through the network.

It is possible for anyone to join the public Yggdrasil Network and it should therefore be considered as an untrusted network, similar to a public Wi-Fi network or a direct Internet connection. An IPv6-capable firewall that blocks unexpected incoming traffic is highly recommended in order to prevent services on your machine from being accidentally exposed to all users of the Yggdrasil Network.

### Peerings

Network nodes establish peerings either in one of two ways:

1. Manually by the user. Any node can be configured to accept peering connections and can establish outbound connections to other listening nodes. These are referred to as "static peerings" in that they are present in the configuration file and do not typically change at runtime.
2. Link-local multicast discovery. Nodes can optionally advertise their presence on the local network to other listening nodes, which will then attempt to establish peerings automatically. For the most part, this allows any two nodes that are connected together with an IP link to automatically discover each other and create a peering connection in a "plug and play" fashion.

It is important to note that Yggdrasil nodes **never** establish new peerings with remote (that is, non-multicast-discovered) nodes automatically. Yggdrasil intentionally has no concept of peer exchange, as is often found in other overlay and DHT designs, and we have no plans at this time to add it.

This is because it is the goal of the Yggdrasil protocol to find the best possible paths using only the set of links that are already available. It is therefore essential that we strictly do not make any assumptions about the network topology underneath the peerings, nor do we assume that any two nodes would be able to establish a direct link on demand. In real-world networks with physical links, automatic establishment of new peering links between arbitrary pairs of nodes would simply not be possible.

### Node identities

In the Yggdrasil design, a node's true identity is its public key. The current overlay implementation generates an IPv6 address based on the truncated public key. Translation between IPv6 addresses and partial public keys, as well as searching the network based on those keys, is performed transparently by the Yggdrasil router, therefore all you need to know in order to communicate with a remote node is the IPv6 address it has generated.

Domain Name System (DNS) and other name/service lookup mechanisms are specifically out of scope for the project and there is no "official" DNS system that is endorsed for use on the Yggdrasil Network, although some third-party projects attempting to solve this problem are indeed available.

### Link metrics

As a decentralised routing protocol design, we believe it is important that routing decisions are only ever made using information that can be securely and independently verified by any given node in a low-cost fashion. We therefore consider most interactive link quality metrics to be out-of-scope at this time, although this will likely change in the future as the protocol becomes more mature.

We are aware of the fact that this, combined with the ability to freely and easily create peerings over the Internet, means that suboptimal peerings are likely to appear on the public Yggdrasil testbed network.

Network congestion is managed automatically at each node using a form of fair packet queuing. This attempts to balance traffic flows between different nodes equally where possible, as well as ensuring that single nodes cannot saturate all available link bandwidth over a given peering or set of peerings. This also helps to keep overall network latency down where possible.

### Comparison with other projects

The Yggdrasil Network often attract comparisons with other projects attempting to create anonymous overlays, such as Tor, I2P, Lokinet and others. These projects are very different to Yggdrasil, in part because they are attempting to provide anonymity, which Yggdrasil does not, and in part because they are intentionally overlay-by-design rather than by convenience. Any anonymity or pseudonymity that can occur with the Yggdrasil testbed network is purely coincidental and should not be relied upon.

Yggdrasil is frequently compared with VPN projects such as Wireguard, Tailscale, Nebula and Zerotier. Although the Yggdrasil Network can be used as a kind of point-to-point VPN if appropriately configured, it is not a primary goal of ours to provide VPN tunnelling.

We do not see Yggdrasil as being in competition with these projects as they are attempting to solve a different set of problems.

The Yggdrasil Network does not natively provide access to the public Internet or to other networks, although it can be achieved with proxies or other tunnelling solutions.
