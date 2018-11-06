---
layout: post
title:  "The World Tree"
date:   2018-07-17 00:00:00 -0500
author: Arceliar
---

### Taboo Trade-offs

I spent a long time thinking about what to write for my first contribution to this blog.
It makes sense for me to cover the gritty details, to try to explain why and how this thing works under the hood, but there's a lot to go over.
Still, we have to start somewhere, and this seems as good a place as any.

There are things we ideally want a network to do.
We want to find the shortest path between any two nodes in a network.
We want to use as little resources as possible.
We want latency to be as low as possible.
We want as much bandwidth as possible.
We want to minimize packet loss.
We want the network to be fault tolerant, and re-converge as quickly as possible if/when disruptions do occur.
We want security.
We want simplicity.
The list goes on.

Unfortunately, some of these things involve fundamental trade-offs.
Mathematicians have proven, from a pretty inescapable information theory argument, that there's a trade-off between memory and the efficiency of the paths you can find:
If you want to guarantee memory usage below `~O(n^(1/k))`, you must accept stretch as high as `2k-1`.
Similarly, if you want more bandwidth, they you must sometimes use higher-latency paths, and both can get in the way of reliability / low packet loss.

Yggdrasil is an experimental implementation of a number of different ideas.
In this blog post, and likely my next several, I'll try to go over some of them, but at the end of the day, it's all about trade-offs.
When seeking to improve performance in one area, we must sometimes make sacrifices in another.
Today's blog post focuses on Yggdrasil's routing logic.
On realistic network topologies, we achieve polylogarithmic memory scaling by sacrificing something held sacred in most other routing schemes: shortest path routing.
To explain how and why, I think it's best if we first review how things are done elsewhere.

### Hard CIDR

Networks today are built around hierarchically allocated addressing and [classless inter-domain routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) (CIDR).
The basic idea is to allocate addresses in a network from a contiguous address range, often as small as a `/64` for IPv6 addresses (a block of addresses with the first 64 bits in common, e.g. `1111:2222:3333:4444::/64` denotes all addresses from `1111:2222:3333:4444:0000:0000:0000:0000` to `1111:2222:3333:4444:ffff:ffff:ffff:ffff`).

A gateway node `G` in network `A`, with a connection to another network `B`, can advertise to network `A` a route to the address range of network `B`, and vice versa.
This allows nodes in network `A` and `B` to communicate with each other by routing through gateway node `G`.
This system of gateways, often between a hierarchy of networks with progressively smaller address ranges, is meant to keep routing tables small.

Within a particular network, each node must be able to find a path to every other node within that network.
Virtually all routing schemes in use today are a form of shortest path routing, wherein each node knows the shortest path to every other node in the network, for some definition of path length (such as the number of network hops, latency, some function of bandwidth usage, or often the [expected transmission count](https://en.wikipedia.org/wiki/Expected_transmission_count) in mesh networks).
A notable exception to this is Ethernet, which has historically used the [spanning-tree protocol](https://en.wikipedia.org/wiki/Spanning_Tree_Protocol) (STP) to reduce a network to a tree topology, in an effort to eliminate routing loops, but newer protocols (such as [shortest path bridging](https://en.wikipedia.org/wiki/IEEE_802.1aq)) aim to improve this.
Typically, shortest path routing is implemented either via a proactive protocol, wherein every node in a network maintains a local routing table with information about every other node in the network, or via a reactive protocol using broadcast lookup traffic, wherein routes are found by broadcasting lookups through the entire network.

There are things about this approach which can be problematic.
The main issue, for the purposes of this post, is scalability.
For a large network to scale, it must be subnetted into smaller, more easily manageable networks, which then must in turn be networked together (to form a network of networks from inter-network connections, i.e. the internet).
This requires some level of expertise and planning to do, and tends to favor hierarchies wherein small networks are largely at the mercy of a larger network (e.g. the only connection your LAN has to another network is your connection to an ISP, and "peering" or directly connecting to your neighbor's LAN is virtually unheard of).
If you don't subnet efficiently, then large networks can become overloaded by protocol traffic alone.
What's worse, the kinds of network topologies that show up in practice (small-world / scale-free networks) are among the topologies for which hierarchical addressing and CIDR are least effective at aggregating routes, which contributes to (and I dare say, *causes*) very large [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) routing tables in the [DFZ](https://en.wikipedia.org/wiki/Default-free_zone).

### Compact Routing

So, what if we didn't do that?

[Compact routing](https://arxiv.org/abs/0708.2309) is the study of the fundamental trade-off between the stretch of a routing scheme (the length of the paths it finds, relative to the shortest paths in the network) vs the size of the routing table required to find a path.
Essentially, compact routing proposes that, rather than require each node in the network be able to find the *best* path, we only require that they find a *good* path.
In doing so, it becomes possible to reduce the size of each node's routing table.

To skip over the details, there are basically two aspects to consider that are relevant to this post.
First, compact routing schemes can be name-dependent or name-independent.
Name-dependent routing schemes assign an address (of some kind, not necessarily a familiar IP address) based on a node's location in the network.
Name-independent routing schemes place no requirements on a node's address, and treat it as some opaque identifier in a flat (i.e. non-subnetted) address space.
Secondly, compact routing schemes can either be a universal scheme, with strong guarantees on all possible network types, or they can be specific to certain network topologies.

On paper, compact routing is essentially a solved problem: universal name-dependent routing schemes are known with worst case scenario performance guarantees that are basically equal to the best case scenario lower bounds proven by the mathematicians who do that sort of thing.
Furthermore, name-independent routing schemes are known with the same performance guarantees, albeit much worse observed performance *in practice* (still better than the guaranteed worst cases, but not by as much).
However, practical compact routing has yet to be realized.
Although the schemes themselves are known, in the sense that someone with a full view of the network can figure out who needs to know what to route packets effectively, a distributed algorithm suitable for dynamic networks is still a topic of ongoing work, and no publicly available implementation.

### Greedy Embedded Routing

So, what if we didn't do that?

[Greedy embedded](https://en.wikipedia.org/wiki/Greedy_embedding) routing algorithms take a slightly different approach.
Instead of a routing table about some remote subset of the network, each node knows information about itself and its directly connected (one-hop) neighbors, which we refer to as "peers" in Yggdrasil.
In particular, each node knows the location of itself, and its peers, in some metric space.

For example: consider the case of a two-dimensional square grid, with no gaps, holes, or other irregularities that could obstruct the flow of traffic.
Every node's "address" is simply the X and Y coordinates of the node in the network.
Because there are no gaps in this example, every node can route traffic simply by forwarding it to any neighbor which is closer to the destination's X-Y coordinates than itself.
This is called a greedy routing strategy, and coordinate systems where this strategy is guaranteed to work are called greedy embeddings.

While there is no finite dimensional Euclidean space for which a greedy embedding exists on all possible graphs, so the dimension of Euclidean embeddings need to be changed due to network size and topology, which complicates things, it turns out that [the hyperbolic plane admits a greedy embedding of any network](http://www.cs.cornell.edu/~rdk/papers/pgr.pdf).
Furthermore, this can be done with small addresses, and produces low stretch in realistic network topologies -- this is basically everything we want!
However, the most common approach to implementing such an embedding, which can be done via a distributed algorithm, consist of constructing a spanning tree of the network, and then embedding the spanning tree instead of the original network graph.

### The World Tree

So, what if we didn't do that?
Here's an observation for you to consider: that strategy for greedy hyperbolic embedding just sounds like a tree embedding with extra steps.

Yggdrasil, named after [the mythical world tree of Norse cosmology](https://en.wikipedia.org/wiki/Yggdrasil), uses a form of greedy embedded routing for the "switch" layer.
Instead of embedding a spanning tree into the hyperbolic plane, the spanning tree is used directly to define the metric space: the distance between two nodes in the space is simply their distance apart on the tree, which trivially leads to a greedy embedding.
It must be emphasized that this is not the same as *routing* on the tree, as in STP flavor of Ethernet, because every link is available to forward traffic.
The spanning tree itself is constructed using a STP-like distributed algorithm, but with cryptographically secured advertisements (to prevent forgery-based route hijacking attacks).

This has a number of effects.
First and foremost, as with compact and greedy routing schemes, Yggdrasil is not guaranteed to find the shortest path when routing a packet.
Instead, it merely tries to guarantee that it can find *some* path for any network, and we observe that it tends to find a *good* path (i.e. usually the shortest path, or only slightly longer, on par with the compact routing schemes) for the special case of small-world / scale-free networks (the only kind that ever seems to grow large enough that we need to worry about scaling).
The main benefit is that the amount of maintenance traffic needed by the network is greatly reduced, with the tree requiring something like `O(p*log(n))` state for a node with `p` peers in a network with `n` nodes.
This is in contrast to [distance-vector](https://en.wikipedia.org/wiki/Distance-vector_routing_protocol) or [link-state](https://en.wikipedia.org/wiki/Link-state_routing_protocol) protocols, where each node needs at least `O(n*log(n))` state in their local routing table (e.g. `n` routing table entries, each at least as long as the `log(n)` bits needed for unique node identifiers).

The goal here is that, as the network grows, the resource cost devoted to protocol traffic should scale at a negligible rate -- you never want to stop adding nodes to the network because it costs the rest of the network too much resources to deal with them.
If this strategy works, and it seems to on paper, and in simulation, and it's looking that way from our early tests on real hardware, then there's no longer a requirement that the network subnet the address space for scalability reasons.
This would make it trivial to bridge networks together: you simply need to add links between them, and the protocol takes care of the rest, without requiring any expert knowledge or centralized coordination to partition the address space.
These can be physical links, such as an ethernet or fiberoptic cable, wireless links, such as open wifi in ad-hoc mode, or virtual links tunneled across existing networks, such as connecting to a public node via the internet (to bridge networks that are not within a convenient range for real links).

### Conclusion

I hope that sheds some light on what is arguably the most important part of Yggdrasil.
Of course, there's more to Yggdrasil than just the tree.
It was written to test not only the above greedy routing scheme, but a number of other ideas that I thought would be too experimental to try out in e.g. [cjdns](https://github.com/cjdelisle/cjdns) without prototyping elsewhere first.
I plan to go into more detail about other parts of Yggdrasil in my next few posts, including name-dependent routing with cryptographically self-assigned addresses, ongoing work to add a form of backpressure routing for congestion control, and likely some performance studies to show what the effects of some of these trade-offs.

