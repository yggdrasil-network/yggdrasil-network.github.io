---
sitemap: true
---

# About

Yggdrasil is an encrypted IPv6 network running in the [`200::/7` address range](https://en.wikipedia.org/wiki/Unique_local_address).
It is an experimental/toy network, so failure is acceptable, as long as it's instructive to see how it breaks if/when everything falls apart.

IP addresses are derived from cryptographic keys, to reduce the need for public key infrastructure.
A form of locator/identifier separation (similar in goal to [LISP](https://en.wikipedia.org/wiki/Locator/Identifier_Separation_Protocol)) is used to map static identifiers (IP addresses) onto dynamic routing information (locators), using a [distributed hash table](https://en.wikipedia.org/wiki/Distributed_hash_table) (DHT).
Locators are used to approximate the distance between nodes in the network, where the approximate distance is the length of a real worst-case-scenario path through the network.
This is (arguably) easier to secure and requires less information about the network than commonly used routing schemes.

While not technically a [compact routing scheme](https://arxiv.org/abs/0708.2309), tests on real-world networks suggest that routing in this style incurs stretch comparable to the name-dependent compact routing schemes designed for static networks.
Compared to compact routing schemes, Yggdrasil appears to have smaller average routing table sizes, works on dynamic networks, and is name-independent.
It currently lacks the provable bounds of compact routing schemes, and there's a serious argument to be made that it cheats by stretching the definition of some of the above terms, but the main point to be emphasized is that *we're not looking for formal proofs and definitions, we just want something efficient in real networks*.
In that sense, Yggdrasil seems to be competitive on paper, and working well in practice so far.

## Addressing and NodeIDs

Yggdrasil uses a truncated version of a NodeID to assign addresses.
An address is assigned from the `200::/7` prefix, according to the following:

1. Begin with `0x02` as the first byte of the address, or `0x03` if it's a `/64` prefix.
2. Count the number of leading `1` bits in the NodeID.
3. Set the second byte of the address to the number of leading `1` bits in the NodeID (8 bit unsigned integer, at most 255).
4. Append the NodeID to the remaining bits of the address, truncating the leading `1` bits and the first `0` bit, to a total address size of 128 bits.

The last bit of the first byte is used to flag if an address is for a router (`200::/8`), or part of an advertised prefix (`300::/8`), where each router owns a `/64` that matches their address (except with the eight bit set to 1 instead of 0).
This allows the prefix to be advertised to the router's LAN, so unsupported devices can still connect to the network (e.g. network printers).

The NodeID is a [sha512sum](https://en.wikipedia.org/wiki/SHA-512) of a node's public encryption key.
Addresses are checked that they match NodeID, to prevent address spoofing.
As such, while a 128 bit IPv6 address is likely too short to be considered secure by cryptographer standards, there is a significant cost in attempting to cause an address collision.
Addresses can be made more secure by brute force generating a large number of leading `1` bits in the NodeID.

### Cryptography

Public key encryption is done using the `golang.org/x/crypto/nacl/box`, which uses [Curve25519](https://en.wikipedia.org/wiki/Curve25519), [XSalsa20](https://en.wikipedia.org/wiki/Salsa20), and [Poly1305](https://en.wikipedia.org/wiki/Poly1305) for key exchange, encryption, and authentication (interoperable with [NaCl](https://en.wikipedia.org/wiki/NaCl_(software))).
Permanent keys are used only for protocol traffic, with random nonces generated on a per-packet basis using `crypto/rand` from Go's standard library.
Ephemeral session keys (for [forward secrecy](https://en.wikipedia.org/wiki/Forward_secrecy)) are generated for encapsulated IPv6 traffic, using the same set of primitives, with random initial nonces that are subsequently incremented.
A list of recently received session nonces is kept (as a bitmask) and checked to reject duplicated packets, in an effort to block duplicate packets and replay attacks.
A separate set of keys are generated and used for signing with [Ed25519](https://en.wikipedia.org/wiki/Ed25519), which is used by the routing layer to secure construction of a spanning tree.

## Locators and Routing

Locators are generated using information from a spanning tree (described below).
The result is that each node has a set of [coordinates in a greedy metric space](https://en.wikipedia.org/wiki/Greedy_embedding).
These coordinates are used as a distance label.
Given the coordinates of any two nodes, it is possible to calculate the length of some real path through the network between the two nodes.
Traffic is forwarded using a [greedy routing](https://en.wikipedia.org/wiki/Small-world_routing#Greedy_routing) scheme, where each node forwards the packet to a one-hop neighbor that is closer to the destination (according to this distance metric) than the current node.
In particular, when a packet needs to be forward, a node will forward it to whatever peer is closest to the destination in the greedy [metric space](https://en.wikipedia.org/wiki/Metric_space) used by the network, provided that the peer is closer to the destination than the current node.
If no closer peers are idle, then the packet is queued in FIFO order, with separate queues per destination+crypto-session (so traffic from different nodes, to the same destination, is tracked separately).
Whenever the node finishes forwarding a packet to a peer, it checks the queues, and will forward the first packet from the queue with the maximum `<age of first packet>/<queue size in bytes>`, subject to the constraint that the peer is a valid next hop (i.e. closer to the destination than the current node).
If no non-empty queue is available, then the peer is added to the idle set, forward packets when the need arises.
This acts as a crude approximation of backpressure routing, where the remote queue sizes are assumed to be equal to the distance of a node from a destination (rather than communicating queue size information), and packets are never forwarded "backwards" through the network.
The queue selection strategy grants a larger fration of available bandwith to sessions that attempt to use less bandwidth, loosely based on the rationale behind some proposed solutions to the [cake-cutting](https://en.wikipedia.org/wiki/Fair_cake-cutting) problem.
The queue size is limited to 4 MB. If a packet is added to a queue and the total size of all queues is larger than this threshold, then a random queue is selected (with odds proportional to relative queue sizes), and the first packet from that queue is dropped, with the process repeated until the total queue size drops below the allowed threshold.

### Spanning Tree

A [spanning tree](https://en.wikipedia.org/wiki/Spanning_tree) is constructed with the tree rooted at the highest TreeID, where TreeID is equal to a sha512sum of a node's public [Ed25519](https://en.wikipedia.org/wiki/Ed25519) key (used for signing).
A node sends periodic advertisement messages to each neighbor.
The advertisement contains the coords that match the path from the root through the node, plus one additional hop from the node to the neighbor being advertised to.
Each hop in this advertisement includes a matching ed25519 signature.
These signatures prevent nodes from forging arbitrary routing advertisements.

The first hop, from the root, also includes a sequence number, which must be updated periodically.
A node will blacklist the current root (keeping a record of the last sequence number observed) if the root fails to update for longer than some timeout (currently hard coded at 1 minute).
Normally, a root node will update their sequence number for frequently than this (once every 30 seconds).
Nodes are throttled to ignore updates with a new sequence number for some period after updating their most recently seen sequence number (currently this cooldown is 10 seconds).
The implementation chooses to set the sequence number equal to the unix time on the root's clock, so that a new (higher) sequence number will be selected if the root is restarted and the clock is not set back.

Other than the root node, every other node in the network must select one of its neighbors to use as their parent.
This selection is done by maximizing: `<uptime + timeout> / <distance to the root>`.
Here, `uptime` is the time between when we first and last received a message from the node which advertised the node's current location in the tree (resetting to zero if the location changes), and timeout is the time we wait before dropping a root due to inactivity.
This essentially means the numerator is at least as long as the amount of time between when the neighbor was first seen at its present location, and when the advertisement from the neighbor becomes invalid due to root timeout.
Resetting the uptime with each coordinate change causes nodes to favor long-lived stable paths over short-lived unstable ones, for the purposes of tree construction (indirectly impacting route selection).

The distance metric between nodes is simply the distance between the nodes if they routed on the spanning tree.
This is equal to the sum of the distance from each node to the last common ancestor of the two nodes being compared.
The locator then consists of a root's key, timestamp, and coordinates representing each hop in the path from the root to the node.
In practice, only the coords are used for routing, while the root and timestamp, along with all the per-hop signatures, are needed to securely construct the spanning tree.

## Distributed Hash Table

A [Kademlia](https://en.wikipedia.org/wiki/Kademlia)-like Distributed Hash Table (DHT) is used as a distributed database that maps NodeIDs onto coordinates in the spanning tree metric space.
The DHT is Kademlia-like in that it uses the `xor` metric and structures the hash table into k-buckets (with 2 nodes per bucket in the normal case, plus some additional slots for keyspace neighbors and one-hop neighbors at the router level).

It differs from kademlia in that there are no values in the key:value store--it only stores information about DHT peers.
Furthermore, the network bootstraps by adding one-hop neighbors to the DHT, and (for subtle reasons) this strategy does not behave the same as bootstrapping off of a dedicated node.
To bootstrap successfully, when a bucket is full, old nodes (the least recently pinged) are removed to make room for new nodes.

To summarize the entire procedure, given only a node's IP address, the goal is to find a route to the destination.
That happens through 3 steps:

1. The address is unpacked into the known bits of a NodeID and a bitmask to signal which bits of the NodeID are known (the unknown bits are ignored).
2. A DHT search is performed, which normally results in a response from the node closest in the DHT keyspace to the target NodeID. The response contains the node's curve25519 key, which is checked to match the NodeID (and therefore the address), as well as the node's coordinates.
The NodeID is checked to make sure it matches the target IPv6 address.
3. Using the keys and coords from the above step, an ephemeral key exchange occurs between the source and destination nodes. These ephemeral session keys are used to encrypt any ordinary IPv6 traffic that may be encapsulated and sent between the nodes.

## Project Status and Plans

The current (Go) implementation is pre-alpha, so compatibility with future versions is neither guaranteed nor expected.
While users are discouraged from running anything truly critical on top of it, as of writing, it seems reliable enough for day-to-day use.

An "alpha" quality release should at least include the ability to detect incompatible versions and warn the users that an update may be needed.
A "beta" quality release should know enough to be compatible in the face of wire format changes, and reasonably feature complete.
A "stable" 1.0 release, if it ever happens, would probably be feature complete, with no expectation of future wire format changes, and free of known critical bugs.

Roughly speaking, there are a few obvious ways the project could turn out:

1. The developer(s) could lose interest before it goes anywhere.
2. The project could be reasonably complete (beta or stable), but never gain a significant number of users.
3. The network may grow large enough that fundamental (non-fixable) design problems appear, which is hopefully a learning experience, but the project may die as a result.
4. The network may grow large, but never hit any design problems, in which case we need to think about either moving the important parts into other projects ([cjdns](https://github.com/cjdelisle/cjdns)) or rewriting compatible implementations that are better optimized for the target platforms (e.g. a linux kernel module).

That last one is probably impossible, because the speed of light would *eventually* become a problem.
If the only thing limiting network growth turns out to be the underlying physics, then that arguably counts as a win.
