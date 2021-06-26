---
layout: post
title:  "v0.4 Pre-release Benchmarks"
date:   2021-06-26 21:00:00 +0000
author: Arceliar
---

### The Problem with v0.3

In the current stable release of Yggdrasil, `v0.3.16`, routing works basically the same way that it has always worked since release. Traffic is forwarded by greedy routing in a metric space. In essence, each node has a distance label (`coords` in the code), and given the distance label of any two nodes, you can calculate the distance of some path between them. Traffic is forwarded to whichever peer minimizes that distance to the destination. This has been discussed in an [earlier blog post](2018-07-17-world-tree.md), so lets not worry about the details of how it works right now. We'll just focus on what happens when it *doesn't* work.

To be able to send traffic to a destination `D`, the sender `S` must look up the node's distance label and key in the DHT. This happens just before session setup, where ephemeral keys are exchanged. You can think of it a bit like a DNS lookup: it maps some static information (the node's Yggdrasil IPv6 address) onto some dynamic information (the node's distance label). If anything happens to the network that causes the destination node `D`'s distance label to change, then all traffic to `D` will drop until the `S` can look up `D`'s new distance label. However, that lookup depends on the DHT, and the DHT *also* uses distance labels for communication, so DHT lookups for `D` will fail for some amount of time until that completes. While that's happening, `S` cannot communicate with `D`, even if the path between `S` and `D` is unaffected. Further exacerbating the problem, the DHT search is an iterative process that requires round trip communication with multiple nodes. These nodes essentially random, meaning most of them are likely to be near the edge of the network, where connections are comparatively unreliable and costly to use. If any part of the lookup fails, then this delays search progress (if it doesn't cause the search to fail entirely).

The network tries to combat these problems by having `D` refresh itself in the DHT and send a notification to `S` when `D`'s distance label changes. However, there is no guarantee that `D` knows every node which is tracking it in the DHT, and these notifications will hit a dead and and be dropped if the distance labels of the recipients have also changed. This often happens if `S` and `D` share a common ancestor in the tree. Basically, if `S` and `D` are in a LAN with gateway `G`, and `G`'s connection to the outside world dies, this disrupts the connection between `S` and `D` (and leaves the DHT in a broken state, where they can't look each other up, until things time out).

### Improvements in v0.4

As noted in a [recent post](2021-06-19-preparing-for-v0-4.md), the upcoming v0.4 release will include a number of major changes to how Yggdrasil routes traffic.
Most of these changes aim to improve performance in dynamic networks and reduce bandwidth consumption from protocol traffic.
Without repeating too much from that earlier blog post, the basic goal here is to insulate the routing from changes to distance labels.
This happens through a mix of reactive opportunistic source routing and falling back to to proactive DHT-based routing, both of which use distance labels for path setup, but neither of which is broken when the distance labels change (provided that the links in the path still work).

Since it may take a while to see how this affects performance in a live network, and becuause it's a bit difficult to actually measure these things in a real network, it seems like it would be useful to look at some results from benchmarks on simulated networks.

### Mesh Network Lab

All of the results shown here are from [meshnet-lab](https://github.com/mwarning/meshnet-lab). You should probably just read the documentation if you want to know more, but to summarize: meshnet-lab simulates mesh networks using network namespace on linux. Each node is give a network namespace, which can be linked to other namespaces to simulate an arbitrary topology. Links are added and removed as needed to e.g. simulate movement in a mobile adhoc network.

Although meshnet-lab supports many other mesh networking protocols, this post will focus on comparing Yggdrasil `v0.3.16` (the latest stable release) with `v0.4rc3` (the most recent release candidate). The point of this post is to highlight what kind of performance changes we expect to see in the new Yggdrasil release, not to compare Yggdrasil to other mesh routers.

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

The `mobility2` test is essentially a variation of the above. Nodes periodically move a random (increasing) step size with a 15s delay before testing 200 random paths. This test also monitors bandwidth usage.

![mobility2_arrival_progress](/assets/images/2021-06-26/mobility2_arrival_progress.svg)
![mobility2_traffic_progress](/assets/images/2021-06-26/mobility2_traffic_progress.svg)

The main feature to note is that, asside from having terrible reliability in this test, `v0.3.16` uses a ridiculous amount of bandwidth when mobility is involved. With `v0.4rc3`, the bandwith use drops to at or below around 10KBps, depending on how mobile things are. I'm fairly certain that most of this bandwith is still a reaction to mobility events in the network, because (as we're about to see) the bandwith use a pretty low in static networks.

#### Scalability1

The `scalability1` test set involves running the network over line, tree, or square grid networks. The line and tree networks start at 50 nodes and increase to 300. The grid network starts at 49 nodes (7x7) and increases the side length by 1 at each step, up to 298 nodes (17x17). This test waits for about 5 minutes before pinging 200 paths (slowly, over an additional 5 minutes), and measures both packet delivery rate and network utilization.

![scalability1-line](/assets/images/2021-06-26/scalability1-line.svg)
![scalability1-rtree](/assets/images/2021-06-26/scalability1-rtree.svg)
![scalability1-grid](/assets/images/2021-06-26/scalability1-grid4.svg)

There's not a whole lot to say here, `v0.4rc3` is just an improvement across the board. Note that it's a little surprising how the bandwidth use *decreases* as the network grows. This may be an artifact of how the test works. Each network measures reliability by pinging a fixed number of paths (200). The bandwidth used by these pings counts towards the test results. In the line network, increasing the network size also increases the path length at an equal rate, so the bandwidth use per node stays about the same. In the grid and rtree networks, path length doesn't increases as rapidly as the number of nodes, so the bandwith from the 200 test pings is increasing slower than the network size, which results in decreased average bandwidth use per node. In the future, it may be interesting to run some variation of this test without the pings, to get a better measurement of how the idle protocol traffic scales.

### Conclusion

The upcoming v0.4 release changes how packets are routed through the network. It's hard to predict exactly how this will affect network performance, but benchmarks in simulated networks may give us some insight into what we can expect. If the above benchmarks are at least qualitatively accurate, then we have reason to be optimistic about performance in the next release.

