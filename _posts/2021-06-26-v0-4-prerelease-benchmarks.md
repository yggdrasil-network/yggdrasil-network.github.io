---
layout: post
title:  "v0.4 Pre-release Benchmarks"
date:   2021-06-26 21:00:00 +0000
author: Arceliar
---

### Revisiting v0.3

In the current stable release of Yggdrasil, `v0.3.16`, routing works basically the same way that it has always worked since release. Traffic is forwarded by greedy routing in a metric space. In essence, each node has a "distance label", and given the distance label of any two nodes, you can calculate the distance of some path between them. In the code, this label is usually called `coords`, as it represents a position in the tree, but technically we don't care about the position itself, we only care that it works as a distance label. Traffic is forwarded to whichever peer minimizes that distance to the destination. This has been discussed in an [earlier blog post](2018-07-17-world-tree.md), so lets not worry about the details of how it works for now. Instead, we'll focus on what happens when it *doesn't* work.

To be able to send traffic to a destination `D`, the sender `S` must look up the node's distance label and key in the DHT. This happens just before session setup, where ephemeral keys are exchanged. You can think of it a bit like a DNS lookup: it maps some known static information (the node's Yggdrasil IPv6 address) onto some unknown or dynamic information (the node's static key and dynamic distance label). If anything happens to the network that causes the destination node `D`'s distance label to change, then all traffic to `D` will drop until the `S` can look up `D`'s new distance label. However, that lookup depends on the DHT, and the DHT *also* uses distance labels for communication, so DHT lookups for `D` will fail for some amount of time, until the out-of-date information about `D` times out or is replaced. While that's happening, `S` cannot communicate with `D`, even if the path between `S` and `D` is unaffected. Further exacerbating the problem, the DHT search is an iterative process, which requires round trip communication with multiple nodes. These nodes are, for the most part, randomly distributed across the physical network, meaning most of them are likely to be near the edge of the network, where connections are comparatively unreliable and costly to use. If any part of the lookup fails, then this delays search progress (if it doesn't cause the search to fail entirely).

The network tries to combat these problems by having `D` refresh itself in the DHT and send a notification to `S` when `D`'s distance label changes. However, there is no guarantee that `D` knows every node which is tracking it in the DHT, and these notifications will hit a dead and and be dropped if the distance labels of the recipients have also changed. This often happens if `S` and `D` share a common ancestor in the tree.

To give a concrete example, if `S` and `D` are in a LAN with gateway `G`, and `G`'s connection to the outside world dies, then this disrupts the traffic flow between `S` and `D`. That happens even when the path between them in their own network is unaffected. It also causes various issues in the DHT, which hurt performance for the network in general, and prevents `S` and `D` in particular from being able to resume communication.

### Improvements in v0.4

As noted in a [recent post](2021-06-19-preparing-for-v0-4.md), the upcoming v0.4 release will include a number of major changes to how Yggdrasil routes traffic.
Most of these changes aim to improve performance in dynamic networks and reduce bandwidth consumption from protocol traffic.
Without repeating too much from that earlier blog post, the basic goal here is to insulate the routing from changes to distance labels.
This happens through a mix of reactive opportunistic source routing and falling back to to proactive DHT-based routing, both of which use distance labels for path setup, but neither of which is broken when the distance labels change (provided that the links in the path still work).

Since it may take a while to see how this affects performance in a live network, and because it's a bit difficult to actually measure these things in a real network, it seems like it would be useful to look at some results from benchmarks on simulated networks.

### Mesh Network Lab

All of the results shown here are from [meshnet-lab](https://github.com/mwarning/meshnet-lab). You should probably just read the documentation if you want to know more, but to summarize: meshnet-lab simulates mesh networks using network namespace on linux. Each node is given a network namespace, which can be linked to other namespaces to simulate an arbitrary topology. Links are added and removed as needed to e.g. simulate movement in a mobile adhoc network.

Although meshnet-lab supports many other mesh networking protocols, this post will focus on comparing Yggdrasil `v0.3.16` (the latest stable release) with `v0.4rc3` (the most recent release candidate). Comparisons with other mesh routers would be interesting, but it would be best if those were done by an unbiased 3rd party (and using a stable `v0.4.X` release instead of a release candidate). Instead, this post will try to highlight (qualitatively) what sort of performance changes we expect to see in the new release.

#### Mobility1

The `mobility1` benchmark simulates a dynamic [unit disc graph](https://en.wikipedia.org/wiki/Unit_disk_graph). Nodes are simulated within a two-dimensional Euclidean plane, with each node having connections to other nodes that fall within a certain radius. The network periodically moves all nodes a random distance between 0 and X (X=10,30,60m) in a 1km x 1km virtual space, then waits some amount of time (10s or 30s) before pinging 200 random paths. The paths are limited to source/destination pairs that are in the same connected component, so it only tests paths that plausibly could work.

![mobility1-10-10_arrival_progress](/assets/images/2021-06-26/mobility1-10-10_arrival_progress.svg)
![mobility1-10-30_arrival_progress](/assets/images/2021-06-26/mobility1-10-30_arrival_progress.svg)
![mobility1-10-60_arrival_progress](/assets/images/2021-06-26/mobility1-10-60_arrival_progress.svg)

![mobility1-30-10_arrival_progress](/assets/images/2021-06-26/mobility1-30-10_arrival_progress.svg)
![mobility1-30-30_arrival_progress](/assets/images/2021-06-26/mobility1-30-30_arrival_progress.svg)
![mobility1-30-60_arrival_progress](/assets/images/2021-06-26/mobility1-30-60_arrival_progress.svg)

These mobility tests are an area where Yggdrasil has struggled up to now, as seen in the `v0.3.16` results. Basically, when a node moves, this can affect the coords of other nodes in the network. With the changes in `v0.4rc3`, the 30s tests are generally in good shape. The 10s tests see some loss, due to the time it takes to detect failed links before we can route around them.

#### Mobility2

The `mobility2` test is essentially a much more aggressive variation of the above. Nodes periodically move a random (increasing) step size with a 15s delay before testing 200 random paths. This test also monitors bandwidth usage.

![mobility2_arrival_progress](/assets/images/2021-06-26/mobility2_arrival_progress.svg)
![mobility2_traffic_progress](/assets/images/2021-06-26/mobility2_traffic_progress.svg)

The main feature to note is that, aside from having terrible reliability in this test, `v0.3.16` uses a ridiculous amount of bandwidth when mobility is involved. With `v0.4rc3`, the bandwith use drops to at or below around 10KBps, depending on how mobile things are. I'm fairly certain that most of this bandwith is still a reaction to mobility events in the network, because (as we're about to see) the bandwith use a pretty low in static networks.

#### Scalability1

The `scalability1` test set involves running the network over line, tree, or square grid networks. The line and tree networks start at 50 nodes and increase to 300. The grid network starts at 49 nodes (7x7) and increases the side length by 1 at each step, up to 298 nodes (17x17). This test waits for about 5 minutes before pinging 200 paths (slowly, over an additional 5 minutes), and measures both packet delivery rate and network utilization.

![scalability1-line](/assets/images/2021-06-26/scalability1-line.svg)
![scalability1-rtree](/assets/images/2021-06-26/scalability1-rtree.svg)
![scalability1-grid](/assets/images/2021-06-26/scalability1-grid4.svg)

There's not a whole lot to say here, `v0.4rc3` is just an improvement across the board. Note that it's a little surprising how the bandwidth use *decreases* as the network grows. This may be an artifact of how the test works, since a fixed number of pings may represent proportionally more traffic in small network, but that's speculation.

### Conclusion

The upcoming v0.4 release changes how packets are routed through the network. While it's hard to say exactly how things will perform in the real world, the performance gains in the simulated networks give us reason to be optimistic.

If things go according to plan, then these changes should improve the user experience and overall usefulness of the network. Changes to the network state should no longer affect existing traffic flows, as long as the path the flow is using is unaffected. In cases where the path *is* affected, it should take much less time for the network to detect this and route around the damage (when it's possible to do so). With or without disruptive changes in the network, there should be reduced bandwidth from protocol traffic, leading to lower data use and longer battery life in energy constrained environments (e.g. mobile phones).

