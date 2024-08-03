---
sitemap: true
tags: dontlink
---

# Implementation

The public Yggdrasil Network is a testbed overlay network running the Yggdrasil routing protocol.

All network nodes are userspace software routers that run under a single process. The router optionally provides a virtual TUN adapter to the host operating system which allows sending and receiving IPv6 traffic over the Yggdrasil Network. Peering connections between these nodes are established over local area networks, point-to-point links or the Internet using mainly TCP/TLS connections. There are a number of [Public Peers](https://publicpeers.neilalexander.dev) that can be used to join the public network.

Implementing Yggdrasil as an overlay network allows us to test the protocol and routing scheme at scale relatively easily, as the user-space router can easily run on most computers. Users also do not need to be physically close to one another in order to join or test the network. Users can just download the software and establish some peerings over the Internet or other over networks. However, the Yggdrasil design does not constrain it to only being implemented as an overlay network — it could also be implemented as a native routing protocol with physical links instead.

### How is Yggdrasil secured?

In order to ensure that traffic is private as it is routed across other network nodes, all traffic is end-to-end encrypted at all times. Even plain-text application traffic will be encrypted using the destination node's public key, ensuring that intermediate nodes cannot snoop on the contents of traffic being forwarded through the network.

It is possible for anyone to join the public Yggdrasil Network and it should therefore be considered as an untrusted network, similar to a public Wi-Fi network or a direct Internet connection. An IPv6-capable firewall that blocks unexpected incoming traffic is highly recommended in order to prevent services on your machine from being accidentally exposed to all users of the Yggdrasil Network.

### How do nodes peer with each other?

Network nodes establish peerings either in one of two ways:

1. Manually by the user. Any node can be configured to accept peering connections and can establish outbound connections to other listening nodes. These are referred to as "static peerings" in that they are present in the configuration file and do not typically change at runtime.
2. Link-local multicast discovery. Nodes can optionally advertise their presence on the local network to other listening nodes, which will then attempt to establish peerings automatically. For the most part, this allows any two nodes that are connected together with an IP link to automatically discover each other and create a peering connection in a "plug and play" fashion.

It is important to note that Yggdrasil nodes **never** establish new peerings with remote (that is, non-multicast-discovered) nodes automatically. Yggdrasil intentionally has no concept of peer exchange, as is often found in other overlay and DHT designs, and we have no plans at this time to add it.

This is because it is the goal of the Yggdrasil protocol to find the best possible paths using only the set of links that are already available. It is therefore essential that we strictly do not make any assumptions about the network topology underneath the peerings, nor do we assume that any two nodes would be able to establish a direct link on demand. In real-world networks with physical links, automatic establishment of new peering links between arbitrary pairs of nodes would simply not be possible.

### How are nodes identified?

In the Yggdrasil design, a node's true identity is its public key. The current overlay implementation generates an IPv6 address based on the truncated public key. Translation between IPv6 addresses and partial public keys, as well as searching the network based on those keys, is performed transparently by the Yggdrasil router, therefore all you need to know in order to communicate with a remote node is the IPv6 address it has generated.

Domain Name System (DNS) and other name/service lookup mechanisms are specifically out of scope for the project and there is no "official" DNS system that is endorsed for use on the Yggdrasil Network, although some third-party projects attempting to solve this problem are indeed available.

### Which problems exist today?

The Internet doesn't conform to a well-defined topology. This has largely happened over time - as the Internet has grown, more and more networks have been "bolted together" with peering arrangements between service providers. This gives us some unavoidable problems:

1. The routing tables that hold a "map" of the Internet are huge and inefficient, as every provider has to relay information about IP prefixes belonging to all other providers
1. There isn't really any way for a computer to know where it is located on the Internet relative to anything else — most machines are only aware of a "default gateway"
1. It's difficult to examine where a packet will go on its journey from source to destination without actually sending it

These problems have been somewhat mitigated (but not really solved) through centralisation. Rather than your computers at home holding a copy of the global routing table, your service provider does so on your behalf. Your computers and network devices are configured just to "send traffic upstream" and to let your provider decide where it goes from there.

ISP networks are also typically structured in design and often hierarchical in nature, and as a result, many existing routing protocols have been designed with this in mind. Some optimisations such as prefix aggregation are used to try and reduce the number of routing entries that a provider must send out into the world. These protocols are usually not suitable for use in a network where the topology is not well defined or changes frequently — a wireless mesh network, for example, therefore it has been very difficult in the past for communities to build their own wireless mesh infrastructure on an ad-hoc basis.

### What does Yggdrasil do differently?

Yggdrasil takes a very different approach to sharing routing knowledge. Rather than distributing address ranges as paths through centrally assigned autonomous systems, Yggdrasil instead builds up a single distributed global network topology.

A spanning tree is used to provide synchronisation and to allow nodes to allocate themselves a set of tree coordinates, which are used to exchange and establish bootstrap and path setup messages. Nodes then exchange bloom filters which contain information about which keyspace neighbours are reachable through each node. Intermediate nodes then populate their routing tables with these paths, enabling nodes to forward packets closer to their destination public key.

In addition, nodes can pathfind using the spanning tree routing to establish a path that is likely shorter than the path through keyspace. The typically more direct source route will continue to be used for as long as it is available and will fall back to keyspace routing if the tree-routed path breaks.

Cryptographic signatures are used to secure protocol messages against tampering or forgery.

### What are the benefits?

There are a number of benefits to a routing scheme such as this:

1. Devices need to only maintain a comparatively small amount of state in order to function and to be able to forward packets — there is no need for any Yggdrasil node to maintain "full routing tables" like in BGP, and most nodes only have a handful of routing table entries in total
1. Paths are discovered and built through the network automatically, so manual configuration of routing entries is not required — the only configuration needed is the peering connections between nodes themselves
1. The network can setup and tear down paths quickly without needing to discard all routing state, which helps significantly in handling node mobility events without dropping many packets if at all
1. We can bridge reliable/static networks very easily with dynamic/non-static networks without needing to flood large amounts of state
1. Networks automatically form when any two or more Yggdrasil nodes are connected to each other, even if those connections are entirely ad-hoc in nature, which allows building true wireless mesh networks
1. Sparse routing knowledge and only small amounts of protocol traffic should mean that Yggdrasil is able to efficiently scale to very large networks

### Does Yggdrasil have link metrics?

As a decentralised routing protocol design, we believe it is important that routing decisions are only ever made using information that can be securely and independently verified by any given node in a low-cost fashion. We therefore consider most interactive link quality metrics to be out-of-scope at this time, although this will likely change in the future as the protocol becomes more mature.

We are aware of the fact that this, combined with the ability to freely and easily create peerings over the Internet, means that suboptimal peerings are likely to appear on the public Yggdrasil testbed network.

Network congestion is managed automatically at each node using a form of fair packet queuing. This attempts to balance traffic flows between different nodes equally where possible, as well as ensuring that single nodes cannot saturate all available link bandwidth over a given peering or set of peerings. This also helps to keep overall network latency down where possible.
