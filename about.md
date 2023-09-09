---
sitemap: true
---

# About

Yggdrasil is an experimental implementation of a new compact routing scheme designed for mesh or even Internet-like networks. It is predominanently a shortest-path scheme, whereby the network will attempt to find the most direct path to the destination.

Compared to the structured and typically hierarchial routing schemes in use today on many networks, Yggdrasil is strongly decentralised and largely self-arranging. Each node on the network is identified by a cryptographic public key and, in our current experimental implementation, IPv6 addresses are generated from this key. The network topology is adaptive, aiming to make use of whichever links are available in order to provide full routability between all network participants. This is made possible by the fact that all Yggdrasil nodes are routers, sharing routing knowledge and forwarding traffic on behalf of other network participants.

The following table illustrates high-level differences between traditional networks like the Internet, and the Yggdrasil Network:

|                                                                 | Traditional | Yggdrasil |
| --------------------------------------------------------------- | ----------- | --------- |
| End-to-end encryption for all traffic across the network        | No          | Yes       |
| Decentralised routing information shared using a DHT            | No          | Yes       |
| Cryptographically-bound addressing with no central authority    | No          | Yes       |
| Node is aware of its relative location to other nodes           | No          | Yes       |
| Mobile addressing that stays with the device as it moves around | No          | Yes       |
| Topology extends gracefully across different mediums, i.e. mesh | No          | Yes       |

### Implementation notes

The current implementation of Yggdrasil is built as an overlay network, where network nodes are userspace software routers that run under a single process on a host machine. Peering connections between these nodes are established over local area networks, point-to-point links or the Internet using mainly TCP connections. In order to ensure that traffic is private as it is routed across other network nodes, all traffic is end-to-end encrypted at all times. Even plain-text application traffic is encrypted in transit, ensuring that intermediate nodes cannot read traffic as it is forwarded through the network.

This allows us to test the Yggdrasil design at scale relatively easily, as the user-space router can easily run on most computers regardless of configuration or other external factors and users do not need to be physically close to one another in order to join or test the network. They can just download the software and establish some peerings over the Internet to get a feel for how the scheme should work.

This also means that we often attract comparisons with other overlay-by-design networks such as Tor, I2P, Lokinet and others, although we don't believe that such comparisons are especially useful. Not least because many other overlay projects often have differing primary goals, i.e. providing anonymity, but because these projects were designed and built to always be overlays over other network infrastructure.

Instead, we see Yggdrasil as competing with, if not superseding, other routing schemes that are in widespread use today and experimenting with the routing scheme as an overlay network is merely a means to an end. We don't intend that Yggdrasil will only ever continue to exist as an overlay network, nor are we interested in spending too much time and effort trying to solve problems which would only be present on an overlay network. For this reason, there are some specific areas that we consider specifically **out of scope**:

1. **Peer discovery and exchange** — We are researching how to best provide good connectivity between nodes with the set of links that we have been given, not how to automatically establish new links as many other peer-to-peer networks do. We do this because we are trying to strictly limit the number of assumptions we make about the underlying physical topology.
1. **Insecure link metrics** — In order for Yggdrasil to work in a decentralised fashion, we need to be very careful about how much trust we place in other nodes to not lie to us in ways that could influence path selection or routing decisions. We do not consider link quality metrics that cannot be securely and independently verified.
1. **Anonymity or pseudonymity** — Yggdrasil is not an anonymous network. Any anonymity or pseudonymity that can be achieved over the Yggdrasil overlay network today is by coincidence and not by intention or design.
1. **Node naming and lookup** — We are interested in solving the problem of how to route between nodes effectively on complex topologies, not replacing DNS. Decentralised node naming can and should be solved by separate projects.

### What are the problems today?

The internet as we know it today doesn't conform to a well-defined topology. This has largely happened over time - as the internet has grown, more and more networks have been "bolted together" with peering arrangements between service providers. The lack of defined topology gives us some unavoidable problems:

1. The routing tables that hold a "map" of the internet are huge and inefficient, as every provider has to relay information about IP prefixes belonging to all other providers
1. There isn't really any way for a computer to know where it is located on the internet relative to anything else — most machines are only aware of a "default gateway"
1. It's difficult to examine where a packet will go on its journey from source to destination without actually sending it

These problems have been somewhat mitigated (but not really solved) through centralisation - rather than your computers at home holding a copy of the global routing table, your service provider does so on your behalf. Your computers and network devices are configured just to "send traffic upstream" and to let your provider decide where it goes from there. This leaves you entirely at the mercy of your ISP who can redirect your traffic anywhere they like and to inspect, manipulate or intercept it.

ISP networks are also typically structured in design and often hierarchical in nature, and as a result, many existing routing protocols have been designed with this in mind. Some optimisations such as prefix aggregation are used to try and reduce the number of routing entries that a provider must send out into the world. These protocols are usually not suitable for use in a network where the topology is not well defined or changes frequently — a wireless mesh network, for example, therefore it has been very difficult in the past for communities to build their own wireless mesh infrastructure on an ad-hoc basis.

### What does Yggdrasil do differently?

Yggdrasil takes a very different approach to sharing routing knowledge. Rather than distributing address ranges as paths through centrally assigned autonomous systems, Yggdrasil instead builds up a single global network topology.

A spanning tree is used to provide synchronisation and to allow nodes to allocate themselves a set of tree coordinates, which are used to exchange and establish bootstrap and path setup messages. Nodes then set up paths through the network to their keyspace neighbours, effectively arranging the network into a virtual line, ordered by public keys. Intermediate nodes then populate their routing tables with these paths, enabling nodes to forward packets closer to their destination public key.

In addition, nodes can pathfind using the spanning tree routing to establish a path that is likely shorter than the path through keyspace and then switch a traffic session over to source routing. The typically more direct source route will continue to be used for as long as it is available and will fall back to keyspace routing if the source routed path breaks.

Cryptographic signatures are used to secure tree announcements, bootstrap and path messages against tampering or forgery.

### What are the benefits?

There are a number of benefits to a routing scheme such as this:

1. Devices need to only maintain a comparatively small amount of state in order to function and to be able to forward packets — there is no need for any Yggdrasil node to maintain "full routing tables" like in BGP, and most nodes only have a handful of routing table entries in total
1. Paths are discovered and built through the network automatically, so manual configuration of routing entries is not required — the only configuration needed is the peering connections between nodes themselves
1. The network can setup and tear down paths quickly without needing to discard all routing state, which helps significantly in handling node mobility events without dropping many packets if at all
1. We can bridge reliable/static networks very easily with dynamic/non-static networks without needing to flood large amounts of state
1. Networks automatically form when any two or more Yggdrasil nodes are connected to each other, even if those connections are entirely ad-hoc in nature
1. Sparse routing knowledge and only small amounts of protocol traffic should mean that Yggdrasil is able to efficiently scale to very large networks

### What is the status of the project?

Yggdrasil is currently an alpha project, early in development but actively maintained. Our expectation is that a future “beta” quality release should know enough to be compatible in the face of wire format changes, and reasonably feature complete. A “stable” 1.0 release, if it ever happens, would probably be feature complete, with no expectation of future wire format changes, and free of known critical bugs.

The true goal of this project is to test the scalability of the Yggdrasil routing scheme, therefore it needs as many participants to run and test the software as possible so that we can study the behaviour of the network as it grows. We've done our best to support [as many platforms as possible](installation.md) and have a number of [public peers](https://github.com/yggdrasil-network/public-peers) that you can connect to in order to join the network, so please feel free to experiment. That said, we don't recommend running mission critical or life-or-death workloads over Yggdrasil yet — there may be failure modes that we don't yet know about.

The project is likely to reach a number of possible outcomes:

1. The project may reach a reasonably stable state but never attract a large enough number of users
1. The project may attract a large enough number of users but reveal inherent design flaws in the process (a learning exercise for a future project or protocol version perhaps)
1. The project may end up working perfectly even as the network grows, in which case it will become worthwhile to look at writing better-optimised implementations and/or moving the important parts into other projects
