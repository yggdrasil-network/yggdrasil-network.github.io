---
layout: post
title:  "Upcoming v0.5 Release"
date:   2023-10-22 00:00:00 +0000
author: Arceliar
---

### Introduction

With the v0.5.0 release coming soon, now seems like a good time to explain what we've been working on for the past couple of years. While we've generally been pretty happy with v0.4.X, there are a few problems with that design which can cause the network to behave in ways we do not like. This blog post is meant to give a short review of how v0.4 works, explain the problems with this approach, and describe the changes we've made in v0.5 to try to address them.

### Background

The v0.4.X design has 3 major components to the routing scheme:

1. A DHT-based routing scheme, used to route traffic when no route to the destination is known.
2. A greedy treespace routing scheme, used to route certain protocol traffic in the DHT and the "pathfinder" for source routing.
3. A source routings scheme, which encodes a path found through treespace in packet headers, so traffic can take a more direct route than what the DHT offers (and keep routing while the state of the tree is changing).

The life cycle of a connection walks through those three stages in sequence. During the initial key exchange, no path to the destination is known, so traffic is routed over the DHT. When nodes receive traffic that was routed over the DHT, they initiate pathfinding, to find a more efficient route through treespace. When pathfinding finishes, they switch to source routing, which encapsulates the DHT-routeable packet inside of a source routed packet. If a source routed packet ever hits a dead end, the source routing header is removed and finishes routing via the DHT. Receving this DHT routed packet triggers pathfinding in the background, so a new path can be found.

Overall, this design works well. Nodes can begin communicating (in our case, sending key exchange traffic) before needing to look up any routes, and things fall back gracefully. No special protocol traffic is needed to detect broken paths, since the DHT fallback takes care of signaling that a path has failed.

### Problems

While I don't have any concerns with the overall design of v0.4, the individual components all have issues.

First and foremost, the DHT design used in v0.4 does not scale as well as we had hoped. Nodes need to keep track of not only the paths to their keyspace neighbors, but also any such paths that go through that node. This means some fraction of nodes are stuck knowing a large percentage of all paths through their node. That leads to high memory costs and potentially high bandwidth. The v0.4 network's DHT bandwidth use is relatively low, since the DHT is predominantly hard state, but attempts at a more secure DHT all led to soft state designs where the bandwidth costs can become significant. Without securing the DHT, it would remain vulernable some attacks (or behave badly in the presence of miscongifured nodes, such as accidental anycast nodes). The more insidious issue is DHT convergence time: it takes `O(n)` "steps" to converge in the worst case, and we have good reason to believe that some typical use cases experience this. Additionally, the hard state design required actively monitoring each peer link, to quickly detect when a link is dead. This leads to a lot more idle traffic between peers than what we'd like to see.

Secondly, the tree can produce inconsistent views of the network, depending on which peer's information a node pays attention to. This leads to "flapping", when a non-parent ancestral link fails, as nodes tend to switch to a new parent that used the same (now broken) link, but which hasn't had time to advertise the link failure yet. So nodes tend to switch from their parent to an alternative, and then back to the original parent, when the alternative eventually advertises the same failure. That flapping causes down-tree (child) nodes to flap, which can cascade through the network. There are mechanisms in place to throttle how fast things flap in v0.4, but that's a bandaid fix to an underlying problem in the design.

Lastly, source routing is good in principle, but the packet format we used for this is not. It's too easy for a malicious node to insert multiple redundant hops to produce a (finite) loop, which can waste bandwidth on a targeted set of links.

### Changes

Quite a number of changes have been made to the design of Yggdrasil in an effort to combat the above issues. The new approaches are not necessarily how we want the network to function long term, but rather they are alternatives that we wanted to test to better explore the solution space. Generally speaking, these are not user-facing, outside of some changes to the information available in `yggdrasilctl`'s API.

#### Destination Lookups

The most significant change is the removal of the DHT-based routing scheme used to initially set up routes through treespace. We now use a simpler YggIP/key->coord lookup protocol which resembles ARP/NDP lookups in an ethernet broadcast network (but without broadcast traffic through the full network). Nodes keep track of which peers are reachable by an on-tree link (that is, the node's parent and children) along with a bloom filter of the keys of all nodes reachable by that link (with keys truncated to the parts used in /64 prefixes, to allow for IP/prefix lookups). A lookup packet received from an on-tree link is forwarded to any other on-tree link where the destination is found in the bloom filter.

While there are down sides to this approach, it does a number of advantages. First, accidental anycast configurations (using the same key from multiple nodes) will not break any network-wide data structures, it simply causes lookup traffic to arrive at more than one node. Subsequent steps will generally fail (route lookup, key exchange, etc), but there is no collateral damage to the rest of the network. Secondly, this requires very little idle maintenance traffic, and only needs a *constant* amount of state per peer. This means nodes in the core of the network are not responsible for maintaining a view of anything more than their immediate neighborhood, and are not hammered with idle DHT maintenance traffic originating at distant nodes. Similarly, nodes at the edge of the network do not need to send any regular DHT keepalive traffic, which may help with bandwidth use and power consumption on mobile devices. Third, this structure converges asynchronously and in time proportional to the depth of the tree, rather than sequentially and in time proportional to the size of the network, so the very poor worst-case-scenario convergence times of the DHT are avoided.

The major down side to this approach is that bloom filters can and will generate false positives as they fill. In practice, we would expect filters in the "core" of the network to saturate, where every node appears to be reachable by every path. This in turn means that a node's route to the "core" of the network (generally via their parent) will take on the role of a "default route" and receive a copy of every lookup sent by the node. We expect lookup traffic will reach the core of the network, effectively act like broadcast traffic within the core, and then be culled by the bloom filters as it approaches the edges (such that a leaf node is unlikely to receive any traffic for which they are not the intended recipient). In short, the nodes in the core will see lower memory use and less bandwidth used by idle maintenance traffic, but active network use will consume more bandwidth. It remains to be seen whether or not this is a worth-while trade-off.

Just to put some hard numbers on things: we use 8192-bit bloom filters with 8 hash functions. If there is a node that acts as a gateway to a subnet with 200 nodes in it, then that has a false positive rate of about 1 in a million (that is, we expect that network needs about a million nodes before the gateway sees *any* false positive lookup traffic). A majority of lookup traffic is true positives up to a gateway to a 500 node subnet in a 1 million node network.

So in practice, most nodes should not see any meaningful number of false positives, unless the are acting as the gateway to a very large subnet (or are in a network many orders of magnitude larger than the current v0.4 network). In our current network, a handful of nodes may find themselves in the "core" region, where they receive false positive lookup traffic from most lookups. We hope this is still preferable to constant idle DHT maintenance traffic and potentially very high memory requirements.

#### CRDT Tree

Previously, each node's location in the tree was verified by a chain of signatures (reach referencing their parent) from the node back to the root. This can lead to inconsistencies where different nodes have mutually incompatible views of the same ancestor (e.g. node A says parent P has grandparent G, but node B says the same parent P has grandparent G'), which complicates parent selection in response to changes in network state. To address this, we have broken up the tree information into separate per-link information, which is gossiped between nodes and merged into a CRDT structure. This forces nodes to have a locally consistent view of the network, which prevents unnecessary "flapping" in some cases where a node's route to the root has broken. This also reduces the amount of information which must be sent over the wire, as a node does not need to send information back to a peer when it knows the peer has already seen it.

#### Greedy Routing

Source routing (from v0.4) has been removed in favor of greedy routing (as was done in v0.3.X). In a stable network, this has no effect on the route that packets take, only on how the decision to take that route is made. We may move back to a source routed approach in the future, but the approach used in v0.4 had some issues that would need to be addressed first. Source routing is a nice performance optimization to have, if it can be done securely, but it's not an explicit goal of this project. While I have ideas on how to do this, it isn't a high priority in the short term. Since the source routed scheme would presumably still depend on greedy routing for pathfinding, I think it's useful to focus on stress testing the greedy routing part of the network in this release, and leave source routing for when other parts of the stack are closer to stable.

#### Per-peer Keepalive Removed

We no longer spam peer links with keepalive traffic every few seconds. Instead, when traffic is sent, we require an acknowledgement within a few seconds (unless the traffic we sent was an ack). This means we do not detect link failures as quickly in an idle network (we need to wait for user traffic or protocol traffic to use the link), but it should reduce idle bandwidth consumption (and likely reduce power consumption for mobile devices). Note that this is separate from e.g. TCP's own keepalive mechanisms, which are left enabled.

### New Features

There are also a few new features added in v0.5. It is now possible to restrict peers with a `?password=X` argument to the listen and connecting strings (and multicast configuration). This reqires nodes to agree on the password before they will peer. Note that this does not allow for network isolation: nodes can still peer with the rest of the network if they wish, and reachability is still transitive. This does make it easier to restrict who can automatically connect within a subnet, or to set up a node that's public-facing withot allowing connections from everyone who finds it. There's also support for `quic://` connections. Peering over QUIC will only use a single stream of traffic, so it's largely the same semantics as peering over TCP/TLS, but it may be useful in cases where UDP packets have an easier time punching through a NAT or firewall. We generally expect it to perform worse than TCP/TLS, so we do not recommend using it when it's not needed.

### Summary

Barring any unforeseen delays, Yggdrasil v0.5 should be out within the next few weeks. We've hopefully addressed the most significant issues with stability and scaling in v0.4, and significantly reduce the memory footprint and idle bandwidth consumption for some nodes. Some aspects of the new design are radically different from v0.4, so it remains to be seen how well these changes will work in the real world. Preliminary tests (and lots of simulation work) have us optimistic that v0.5 will give us a stable foundation to build on for the immediate future, as we study any limitations of this new approach and work on the inevitable redesign for v0.6.
