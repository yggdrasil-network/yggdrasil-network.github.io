# About

Yggdrasil is an encrypted IPv6 network running in the [`fd00::/8` address range](https://en.wikipedia.org/wiki/Unique_local_address).
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
An address is assigned from the `fd00::/8` prefix, according to the following:

1. Begin with `0xfd` as the first byte of the address.
2. Count the number of leading `1` bits in the NodeID.
3. Set the lower 7 bits of the second byte of the address to the number of leading `1` bits in the NodeID (7 bit unsigned integer, at most 127).
4. Append the NodeID to the remaining bits of the address, truncating the leading `1` bits and the first `0` bit, to a total address size of 128 bits.

The first bit of the second byte is used to flag if an address is for a router (`fd00::/9`), or part of an advertised prefix (`fd80::/9`), where each router owns a `/64` prefix with the 9th bit of the address set to 1.
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
In particular, nodes try to maximize: `<bandwidth to next hop> / <expected length of path to destination>`, where the denominator is equal to 1 (from the link from the current node to the next hop) + the expected distance from the next hop to the destination (from the [metric space](https://en.wikipedia.org/wiki/Metric_space)).

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
The `uptime` in this definition is equal to the time that a neighbor has been advertising *the same* coords for a node.

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
For a combination of performance and testing reasons, the usual iterative-parallel lookups are not used for searches in the DHT, and a recursive-serial approach is taken instead (similar to e.g. some implementations of [Chord](https://en.wikipedia.org/wiki/Chord_(DHT))).

To summarize the entire procedure, given only a node's IP address, the goal is to find a route to the destination.
That happens through 3 steps:

1. The address is unpacked into the known bits of a NodeID and a bitmask to signal which bits of the NodeID are known (the unknown bits are ignored).
2. A DHT search is performed, which normally results in a response from the node closest in the DHT keyspace to the target NodeID. The response contains the node's curve25519 key, which is checked to match the NodeID (and therefore the address), as well as the node's coordinates. Incorrect responses are ignored.
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
