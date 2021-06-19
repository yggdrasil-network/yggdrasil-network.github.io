---
layout: post
title:  "Preparing for Yggdrasil v0.4"
date:   2021-06-19 21:00:00 +0000
author: Neil Alexander, Arceliar
---

### Version 0.4 is coming soon

In the coming weeks, we will be preparing to release Yggdrasil v0.4. This is a significant change from the v0.3 branch with an all-new protocol implementing an improved routing scheme.

This release brings some new and significant benefits:

* **Improved mobility performance** — For nodes that move around or change peerings frequently. This was largely impractical with v0.3 as the sessions would have to time out and a new search repeated.
* **Spanning tree changes are now less disruptive** — Previously it was common for sessions to fail or for traffic to be dropped if the root or parent coordinates changed. This is no longer the case as tree routing is largely only used for bootstrapping DHT paths and determining source routes.
* **Opportunistic source routing** — Session traffic will now use source routing if available, to ensure that the overall connection quality of sessions is preserved. If a source-routed path fails, the traffic will revert to DHT forwarding seamlessly. 

However, there are also a number of user-impacting changes coming in this release to be aware of, as we have worked to simplify the codebase and reduce complexity. 

#### Protocol changes

Yggdrasil v0.4 contains a number of breaking changes to the protocol. That means that v0.4 nodes **will not** peer with v0.3 nodes. We will be wiping the public peers list around the time of release as a result and asking users to re-submit their information once they have upgraded their public nodes.

#### IPv6 address changes

In v0.3, IPv6 addresses on the network were generated as a hash of your curve25519 keys (the `EncryptionPublicKey` configuration option). This was made possible due to the iterative search nature of the DHT. In v0.4, the new DHT is based on ed25519 keys instead and therefore we have had to switch to generating IPv6 addresses from the ed25519 keys instead.

This is sadly unavoidable. We understand that this is a rather disruptive change, especially for those who operate public services. However, we believe that the added robustness of the new routing scheme and DHT is more than worth the disruption. We will also be clearing the public services list and asking service operators to re-submit their details after upgrading.

#### Session firewall deprecated

We decided to remove the session firewall from Yggdrasil v0.4. It's no longer straight-forward to implement in the new codebase and we believe that it often lulled users into a false sense of security. While it may have given the impression of being stateful, it was much more rudimentary. For example, if the user allowed only outbound connections, it would still be possible for the remote side to send traffic back to you for the length of the session. It was also possible to extend this window just by sending more session traffic.

With the new version, the `SessionFirewall` options are no longer present in the configuration and will not take effect. You should look to use your operating system firewall instead if you need to control traffic coming to your node. If you intend to operate a node solely as an Yggdrasil router and do not need to send/receive Yggdrasil traffic from that node directly, you can also disable the TUN adapter by setting `IfName` to `"none"` in the configuration file.

#### Tunnel routing deprecated

We also took the decision to remove tunnel routing from v0.4. We know that this has been a somewhat popular feature with some users, but it ultimately was the source of a significant number of bugs within v0.3. It increased the complexity of the TUN module substantially and often also didn't behave in the way that users expected, particularly those who were used to configuring Wireguard already. 

With the new version, the `TunnelRouting` configuration options are no longer present and will not take effect either. It's still possible to tunnel over Yggdrasil by using a number of other technologies: GRE, IPIP, Wireguard and others, using the Yggdrasil IPs as endpoint addresses for the tunnels. We recommend tunnelling one of these protocols over Yggdrasil instead.

#### Release candidates

There are [release candidate builds available](https://github.com/yggdrasil-network/yggdrasil-go/releases) if you want to try out v0.4 today. We recommend though that you take a backup of your configuration before upgrading or installing any packages and be aware that you will not be able to peer with or access services from the v0.3 network.

At this point the release candidates are using a developmental protocol number. We will bump the protocol version on the final release candidate, but until then, any nodes running v0.4 release candidates should be considered to be experimental only.

#### Technical Details

##### Routing

The core routing logic has been redesigned and written into a [separate library](https://github.com/Arceliar/ironwood). This began as a toy hard-state reimplementation of Yggdrasil's routing logic to test a new DHT design, but it eventually became a soft-state implementation using generational hard state -- basically, nodes periodically set up a new network and throw away the old one, but everything acts like a hard-state protocol within the life of any one generation of the network.

Similar tree and DHT structures were reimplemented in [pinecone](https://github.com/matrix-org/pinecone), so if you [grok](https://en.wikipedia.org/wiki/Grok) the [SNEK](https://matrix.org/blog/2021/05/06/introducing-the-pinecone-overlay-network) then this should seem very familiar.

###### Treespace

The spanning tree works largely the same way as before. The only significant differences are with the root selection and updates: the root is the node with the lowest ed25519 public key, rather than the highest sha512sum hash of the public key, and the root updates the timestamp for its spanning tree announcements every 30 *minutes* (previously 30 seconds) with a timeout after 60 *minutes* (previously 60 seconds). Parent selection uses whatever non-looping path has advertised the best root & timestamp combination the longest, i.e. the path that sent the update the fastest, unless that path was unstable, in which case any flapping should push the network towards the fastest stable path.

As before, each node uses the path from the root to itself as the node's distance label. Given the distance labels of two nodes, the distance between them can easily be calculated (it's the sum of the distance from each of them to their last common ancestor in the tree). This is used to do greedy routing in a metric space, but this is only used to find paths for protocol traffic. User traffic uses one of the following two routing schemes.

###### Keyspace

Each node need to be able to contact any other node given only the node's IPv6 address (in the Yggdrasil address range). To do that, we use a distributed hash table (DHT). The new DHT for v0.4 is very different from the old DHT (which was based on Chord).

The new DHT takes advantage of the fact that node identifiers are simply ed25519 public keys, and that the root of the tree is the node with the lowest key. Since every node knows a path to the root, and the root is at one of the edges of keyspace, we don't need to wrap keyspace to form a Chord-like ring. So the new DHT is simply a line of nodes, ordered from the lowest key to the highest key, beginning with the root of the tree.

Each node is responsible for setting up a path from itself to its predecessor in the line. The predecessor uses that path to route traffic to the node. In addition to this, intermediate nodes store a routing table entry for the path. So if node B sets up a path to node A, which node A uses to forward traffic to B, then every node in the path A->B also has a routing table entry for a path that routes traffic towards B. If a link in the path times out, then the nodes on either end of the broken link send an explicit notification that the path is broken, which is what allows the DHT to quickly detect and react to mobility events.

Packets are forwarded towards the key that's highest without being higher than the destination ("The Price Is Right" rules). These routing decisions are made by any node along a path, not only the nodes at the endpoints of a path. So if A is the predecessor of B, then A need not handle traffic to B — traffic which happens to cross any node on the path A->B will flow towards B without reaching A. The root, as well as all peers and all ancestors of peers, act as additional DHT paths that nodes know about "for free" (since they were learned by necessity as part of the spanning tree setup).

To discover their keyspace neighbors, nodes with no predecessor (or with a predecessor based on an outdated version of the tree) periodically send a bootstrap packet. The bootstrap packet is routed via the DHT until it hits a dead end — at the node's predecessor. The bootstrap contains the sender's treespace distance label, which the predecessor uses to send a bootstrap acknowledgement message. The acknowledgement includes the predecessors treespace distance label, which the original node uses to set up a path for the DHT.

Because the new DHT rules are based around forwarding, rather than lookups, DHT searches (and the crawling operations that come with them) are no longer part of the network. Instead of looking up a path to a node, nodes simply forward traffic towards the destination key via the DHT. While establishing a session, the nodes set up a source route and transparently switch to it in the background.

To figure out how many DHT entries are needed in the network, consider the following:

1. Each node sets up at most 1 DHT path to other nodes (its predecessor).
2. Each path has 1 DHT entry in the routing table of each node in the path.
3. The longest possible path between two nodes is one which goes through the root.
4. In a stable network, nodes select a parent which minimizes the latency of the path from the root.
5. If we assume latency is proportional to hop count, then this minimizes the hop count.
6. This means the longest path we expect on the tree is equal to at most the diameter of the network `d`.
7. This means the longest possible path between two nodes via treespace is `2d`.
8. Therefore, in an `n`-node network, there are `O(nd)` DHT entries across all nodes, or an average of `O(d)` entries per node (but with no bounds on how that's distributed across nodes).
9. Internet-like (scale-free) graphs are observed to have a diameter that scales slowly with network size, most likely `d~logn` (or possibly `d~loglogn`).
10. Therefore, we expect `O(nlogn)` total DHT routing table entries in a large internet-like network, or `O(logn)` average state per node (with no bounds on the variance).

That works out to the same average state per node as in most popular DHT implementations, so this may scale OK in practice. However, we expect that distribution to be skewed, with a large number of nodes having very few entries, which plausibly could mean that some small fraction of nodes have `O(n)` routing table entries in the worst case. These nodes are the same nodes that would need to carry per-path keep-alive traffic if intermediate nodes did not store routing state, so in reality we're trading one resource for another (possibly higher memory use in exchange for lower bandwidth consumption).

###### Source routing

The DHT seems quite reliable in benchmarks (using e.g. [meshnet-lab](https://github.com/mwarning/meshnet-lab)), but it is not efficient: paths through the DHT keyspace typically have higher stretch than paths through treespace. In addition, while coord flapping and node join/leave events cause less disruption for the new DHT, that's not the same as no disruption. To combat this, nodes transparently switch to source routing, and fall back to routing on the DHT only when no source route is known or when a source route reaches a dead end.

To be specific, when a node A sends traffic to node B, A adds B to a list of nodes it cares about. If B also cares about A, then when B receives traffic from A, B sends a notification packet back to A (containing B's treespace distance label). If A receives a notification from a node they care about (in this case, B), then A sends a pathfinding packet via the tree. At each hop along the way, nodes along the path add the port number to the previous node to a reverse route at the end of the packet. When B receives the pathfinding packet, they send an acknowledgement back to A, using the reverse route back to A as a source route. The acknowledgement builds up its own reverse route, which represents a path back to B. When A receives the acknowlegement, A stores the reverse route as its source route to B. Subsequent traffic is sent to B via that source route, with infrequent (1/minute) checks for a new source route. The same process occurs in reverse, with A sending a notification to B, when A receives DHT-routed traffic from B.

If node A's source routed traffic to B hits a dead end, due to e.g. a link failure in the network, then the source route is stripped from the packet, and the packet is routed the rest of the way over the DHT. When B receives the DHT-routed packet from A, this immediately and automatically causes B to send a new notify (possibly with B's new treespace distance label) back to A, which leads to A discovering a new source route to B.

This gives us the best of both worlds. In a stable network, traffic is source routed along the familiar treespace routes from v0.3.X and earlier. If the path between A and B is stable, but the network is not, then the source route continues to work while the tree and DHT try to catch up to changes in network state. If the destination node is mobile, then source routed traffic gets as far as it can before falling back to the DHT, which often puts the packet close to the destination (minimizing the stretch added by the DHT fallback).

##### Encryption

The encryption and session logic has seen some minor changes as well. Since node IDs are now based on ed25519 keys, we no longer have permanent curve25519 keys to use for the Diffie–Hellman key exchange. Also, in older versions, we would perform one ephemeral key exchange, and then keep using the same key pair for the life of the session. The new version uses a ratcheting system, where keys are rotated after each round trip (or whenever a sender nonce overflows). This should offer better forward secrecy than the previous code, though it's still subject to change in future versions (if we find something off-the-shelf that we're happier with).

#### Conclusion

We are looking forward to releasing Yggdrasil v0.4 and are optimistic that the benefits will significantly outweigh any disruption caused at this stage. We've also made a number of other fixes and developed both iOS and Android apps, which we will talk more about soon.

We will be continuing to perfect the release candidates and will make announcements both in our [Matrix channel](https://matrix.to/#/#yggdrasil:matrix.org) and on the blog around the time of the release. Please stay tuned for updates!

As always, please bear in mind that Yggdrasil is not production-grade software and we ask you to continue to [report problems to us on GitHub](https://github.com/yggdrasil-network/yggdrasil-go/issues).
