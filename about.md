---
sitemap: true
---

# About

Yggdrasil is a new and experimental compact routing scheme designed for mesh or even Internet-like networks. It is predominanently a shortest-path scheme, whereby the network will attempt to find the most direct path to the destination.

Compared to the structured and typically hierarchial routing schemes in use today on many networks, Yggdrasil is strongly decentralised and largely self-arranging. Each node on the network is identified by a cryptographic public key and, in our [current experimental implementation](implementation.md), IPv6 addresses are generated from this key. The network topology is adaptive, aiming to make use of whichever links are available in order to provide full routability between all network participants. This is made possible by the fact that all Yggdrasil nodes are routers, sharing routing knowledge and forwarding traffic on behalf of other network participants.

The following table illustrates high-level differences between traditional networks like the Internet, and the Yggdrasil Network:

|                                                                 | Traditional | Yggdrasil |
| --------------------------------------------------------------- | ----------- | --------- |
| End-to-end encryption for all traffic across the network        | No          | Yes       |
| Decentralised routing information shared using a DHT            | No          | Yes       |
| Cryptographically-bound addressing with no central authority    | No          | Yes       |
| Node is aware of its relative location to other nodes           | No          | Yes       |
| Mobile addressing that stays with the device as it moves around | No          | Yes       |
| Topology extends gracefully across different mediums, i.e. mesh | No          | Yes       |

### What are the problems today?

The Internet as we know it today doesn't conform to a well-defined topology. This has largely happened over time - as the Internet has grown, more and more networks have been "bolted together" with peering arrangements between service providers. The lack of defined topology gives us some unavoidable problems:

1. The routing tables that hold a "map" of the Internet are huge and inefficient, as every provider has to relay information about IP prefixes belonging to all other providers
1. There isn't really any way for a computer to know where it is located on the Internet relative to anything else — most machines are only aware of a "default gateway"
1. It's difficult to examine where a packet will go on its journey from source to destination without actually sending it

These problems have been somewhat mitigated (but not really solved) through centralisation - rather than your computers at home holding a copy of the global routing table, your service provider does so on your behalf. Your computers and network devices are configured just to "send traffic upstream" and to let your provider decide where it goes from there. This leaves you entirely at the mercy of your ISP who can redirect your traffic anywhere they like and to inspect, manipulate or intercept it.

ISP networks are also typically structured in design and often hierarchical in nature, and as a result, many existing routing protocols have been designed with this in mind. Some optimisations such as prefix aggregation are used to try and reduce the number of routing entries that a provider must send out into the world. These protocols are usually not suitable for use in a network where the topology is not well defined or changes frequently — a wireless mesh network, for example, therefore it has been very difficult in the past for communities to build their own wireless mesh infrastructure on an ad-hoc basis.

### What does Yggdrasil do differently?

Yggdrasil takes a very different approach to sharing routing knowledge. Rather than distributing address ranges as paths through centrally assigned autonomous systems, Yggdrasil instead builds up a single global network topology in a distributed fashion.

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

The true goal of this project is to test the scalability of the Yggdrasil routing scheme and we are doing so with our [overlay network implementation](implementation.md). We need as many participants to run and test the software as possible so that we can study the behaviour of the network as it grows. We've done our best to support [as many platforms as possible](installation.md) and have a number of [public peers](https://github.com/yggdrasil-network/public-peers) that you can connect to in order to join the network, so please feel free to experiment. That said, we don't recommend running mission critical or life-or-death workloads over Yggdrasil yet — there may be failure modes that we don't yet know about.

The project is likely to reach a number of possible outcomes:

1. The project may reach a reasonably stable state but never attract a large enough number of users
1. The project may attract a large enough number of users but reveal inherent design flaws in the process (a learning exercise for a future project or protocol version perhaps)
1. The project may end up working perfectly even as the network grows, in which case it will become worthwhile to look at writing better-optimised implementations and/or moving the important parts into other projects
