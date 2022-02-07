---
sitemap: true
---

# About

Yggdrasil is an experimental implementation of a new routing scheme designed for mesh or even Internet-like networks. The current implementation is built as an overlay network, where network nodes are userspace software routers, connected together using virtual peerings over local area networks, point-to-point links or the Internet. 

Compared to the structured and typically hierarchial routing schemes in use today on many networks, Yggdrasil is strongly decentralised and largely self-arranging. Each node on the network is identified by a cryptographic public key and IPv6 addresses are generated from this key. The network topology is adaptive, aiming to make use of whichever links are available in order to provide full routability between all network participants. This is made possible by the fact that all Yggdrasil nodes are routers, sharing routing knowledge and forwarding traffic on behalf of other network participants.

In order to ensure that traffic is private as it is routed across other network nodes, all traffic is end-to-end encrypted at all times. Even plain-text application traffic is encrypted in transit. Network traffic is also effectively "sealed sender" in that the source address information is also encrypted and only the destination address is left unencrypted in the packet headers, making it very difficult for intermediate nodes on the network to examine usage patterns or to identify specific traffic flows. 

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

The internet as we know it today doesn't conform to a well-defined topology. This has largely happened over time - as the internet has grown, more and more networks have been "bolted together" with peering arrangements between service providers. The lack of defined topology gives us some unavoidable problems:

1. The routing tables that hold a "map" of the internet are huge and inefficient, as every provider has to relay information about IP prefixes belonging to all other providers
1. There isn't really any way for a computer to know where it is located on the internet relative to anything else — most machines are only aware of a "default gateway"
1. It's difficult to examine where a packet will go on its journey from source to destination without actually sending it

These problems have been somewhat mitigated (but not really solved) through centralisation - rather than your computers at home holding a copy of the global routing table, your service provider does so on your behalf. Your computers and network devices are configured just to "send traffic upstream" and to let your provider decide where it goes from there. This leaves you entirely at the mercy of your ISP who can redirect your traffic anywhere they like and to inspect, manipulate or intercept it. 

ISP networks are also typically structured in design and often hierarchical in nature, and as a result, many existing routing protocols have been designed with this in mind. Some optimisations such as prefix aggregation are used to try and reduce the number of routing entries that a provider must send out into the world. These protocols are usually not suitable for use in a network where the topology is not well defined or changes frequently — a wireless mesh network, for example, therefore it has been very difficult in the past for communities to build their own wireless mesh infrastructure on an ad-hoc basis. 

### What does Yggdrasil do differently?

Yggdrasil takes a very different approach to sharing routing knowledge. Rather than distributing address ranges as paths through centrally assigned autonomous systems, Yggdrasil instead builds up a single global network topology. 

A spanning tree has the following properties:

1. There is always a single "root" node at the "top" of the tree
1. Every other node in the spanning tree has:
  - Exactly one parent
  - One or more children
1. Every node is connected to at least one other node

By arranging every device in such a way that they all agree on the same global
spanning tree, we can do some things that we previously couldn't do before:

1. We can assign a "locator" to a device, effectively a path from the root of
the tree downwards
1. We can determine where our destination is on the tree up front relative to
the source
1. We always have a single worst-case-scenario path to every node by
"walking the tree"
1. We have the ability to infer a number of possible routes (both direct and
indirect) from the structure of the tree, and even take "shortcuts"
1. We can spread out information about the tree itself across nodes and not need
to store it all centrally

By using a distributed hash table (DHT) to share routing information, this
allows any single device on the network to find out enough information to route
to another node, without depending on centralised infrastructure.

Cryptographic signatures are used when exchanging routing information between
peers, such that the network can agree on the same candidate to be the global
root and to lay out the coordinates of each node beneath it.

### What are the benefits?

It should be efficient for the following reasons:

1. It lets every device on the network make the same assumptions about the
topology of the network
1. The use of locators for sending traffic across the network simplifies the
switching layer, as forwarding does not require nodes to maintain state tables
of anything beyond their own peers
1. All route determination is automatic - no manual configuration of routes is
required
1. Devices don't need to store lots of information about the topology of the
network - in fact, storing information about only a small number of nodes is
usually enough to reach the entire network
1. We can bridge reliable/static networks very easily with dynamic/non-static
networks without flooding large amounts of routing information between different
areas (i.e. point-to-point or mesh wireless networks)
1. The network responds gracefully to changes in topology without intervention,
and networks can even join and split without interrupting connectivity between
local nodes

We believe that Yggdrasil should be able to scale to very large networks as a
result.

### What is the status of the project?

Yggdrasil is currently an alpha project, early in development but actively
maintained. Our expectation is that a future “beta” quality release should know
enough to be compatible in the face of wire format changes, and reasonably
feature complete. A “stable” 1.0 release, if it ever happens, would probably be
feature complete, with no expectation of future wire format changes, and free of
known critical bugs.

The true goal of this project is to test the scalability of the Yggdrasil
routing scheme, therefore it needs as many participants to run and test the
software as possible so that we can study the behaviour of the network as it
grows. We've done our best to support [as many platforms as possible](installation.md)
and have a number of [public peers](https://github.com/yggdrasil-network/public-peers)
that you can connect to in order to join the network, so please feel free to
experiment.

The project is likely to reach a number of possible outcomes:

1. The project may reach a reasonably stable state but never attract a large
enough number of users
1. The project may attract a large enough number of users but reveal inherent
design flaws in the process (a learning exercise for a future project perhaps)
1. The project may end up working perfectly even as the network grows, in which
case it will become worthwhile to look at writing better-optimised
implementations and/or moving the important parts into other projects (like
[cjdns](https://github.com/cjdelisle/cjdns))

