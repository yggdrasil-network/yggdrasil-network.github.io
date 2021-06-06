---
sitemap: true
---

# About

Yggdrasil is a proof-of-concept to explore a wholly different approach to
network routing. Whereas current computer networks depend heavily on very
centralised design and configuration, Yggdrasil breaks this mould by making use
of a global spanning tree to form a scalable IPv6 encrypted mesh network.

The following table illustrates high-level differences between traditional
networks like the internet, and the Yggdrasil network:

|                                                                 | Traditional | Yggdrasil |
| --------------------------------------------------------------- | ----------- | --------- |
| End-to-end encryption for all traffic across the network        | No          | Yes       |
| Decentralised routing information shared using a DHT            | No          | Yes       |
| Cryptographically-bound IPv6 addresses                          | No          | Yes       |
| Node is aware of its relative location to other nodes           | No          | Yes       |
| IPv6 address remains with the device even if moved              | No          | Yes       |
| Topology extends gracefully across different mediums, i.e. mesh | No          | Yes       |

### What are the problems today?

The internet as we know it today doesn't conform to a well-defined topology.
This has largely happened over time - as the internet has grown, more and more
networks have been "bolted together". The lack of defined topology gives us some
unavoidable problems:

1. The routing tables that hold a "map" of the internet are huge and inefficient
1. There isn't really any way for a computer to know where it is located on the
internet relative to anything else
1. It's difficult to examine where a packet will go on its journey from source
to destination without actually sending it
1. It's very difficult to install reliable networks into locations that change
often or are non-static, i.e. wireless mesh networks

These problems have been partially mitigated (but not really
solved) through centralisation - rather than your computers at home holding a
copy of the global routing table, your ISP does it for you. Your computers and
network devices are configured just to "send it upstream" and to let your ISP
decide where it goes from there, but this does leave you entirely at the mercy
of your ISP who can redirect your traffic anywhere they like and to inspect,
manipulate or intercept it.

In addition, wireless meshing requires you to know a lot about the network
around you, which would not typically be the case when you have outsourced this
knowledge to your ISP. Many existing wireless mesh routing schemes are not
scalable or efficient, and do not bridge well with existing networks.

### What does Yggdrasil do differently?

Yggdrasil takes a very different approach. Rather than just accepting that the
internet is a sprawling conglomeration of unstructured networks connected
together in an unstructured fashion, we instead arrange the routing scheme of
the entire network into a global spanning tree.

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

### Where can I find more technical details?

Take a look at the [Whitepaper](https://github.com/yggdrasil-network/yggdrasil-go/blob/master/doc/Whitepaper.md)
for a technical deep-dive on how Yggdrasil works, including information about
the name-dependent and name-independent routing components, cryptography and the
behaviour of the distributed hash table (DHT).
