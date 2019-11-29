---
layout: post
title:  "Addressing and Name-Independent Routing"
date:   2018-07-28 18:00:00 -0500
author: Arceliar
---

### By any other name

In an earlier post, I talked about Yggdrasil's use of a tree-based metric space for name-dependent routing.
The purpose of this post is to explain how Yggdrasil's name-independent routing is implemented.

Name-dependent routing means that addressing follows network topology: if the state of the network changes, then a machine's address can change.
For example, in the CIDR-based internet, at least if you're the kind of person who reads random blogs about experimental routing schemes, then you've probably run into the dynamic addressing problem.
If you're offline for too long, or your ISP does any non-trivial maintenance or upgrade work on their network, or sometimes for no visible reason, IP addresses tend to change.
Sure, you can pay extra for a static address, but this is one of the cases where ISPs have a genuinely good excuse to charge extra: assigning a static IP may require the ISPs routers to keep an additional route to you, which would consume excessive resources if everyone needed static addresses.

If addresses are dynamic, and you want to host some kind of service, then you probably want to attach a static identifier to the host.
This usually means [Dynamic DNS](https://en.wikipedia.org/wiki/Dynamic_DNS).
This *also* tends to wrap the problem up with the separate problem of assigning human-readable identifiers and securing this layer of abstraction [complicates things](https://en.wikipedia.org/wiki/Domain_Name_System_Security_Extensions).
The real issue is that we simultaneously use an IP address as an *address*, specifying *where* we want to send a packet, and as a *name*, specifying *who* the packet is for.

### Addressing in Yggdrasil

So, what if we didn't do that?

Yggdrasil uses name-independent routing, which means identifiers (IPv6 addresses) and network state are independent.
As a consequence, we can treat addresses as some flat / opaque identifier.
Yggdrasil uses the `200::/7` region of the IPv6 network space.
This region was set aside for [NSAP](https://en.wikipedia.org/wiki/NSAP_address)-mapped IPv6 addresses in [RFC1888](https://tools.ietf.org/html/rfc1888), which was (AFAIK) never used and eventually deprecated in [RFC4048](https://tools.ietf.org/html/rfc4048) (with further explanation in [RFC4548](https://tools.ietf.org/html/rfc4548)).
The address space is currently unused and, we believe, unlikely to be reassigned for the foreseeable future.
As such, we think it's reasonable to use this address space for Yggdrasil, to avoid conflicts with existing use of the more obvious [ULA](https://en.wikipedia.org/wiki/Unique_local_address) range (which we used previously).

Within `200::/7`, we partition the network into `200::/8` and `300::/8`.
Each node running Yggdrasil as assigned a `/128` address in `200::/8`, and an optional matching `/64` prefix in `300::/8`.
Other than the change of 1 bit for `2->3`, the rest of the first 64 bits of the address and prefix are identical.
Having a `/64` address range allows a Yggdrasil node to operate as a router, where it advertises a route (using normal IPv6 tools, like radvd) allowing the rest of its LAN to reach Yggdrasil without needing to run the code.
This allows unsupported and legacy devices (phones, network printers, game consoles, old OSs, etc.) to reach the network, or low-power / battery-powered devices (IoT) to off-load cryptography and routing logic onto something with fewer constraints.

A node's address is constructed from its `NodeID`, described in the next section, but for now just think of it as a 512-bit unsigned integer.
The process is pretty simple: the first byte is always `0x02` (or `0x03` for a `/64` prefix), to fall within the allowed address range.
The next byte is an unsigned 8 bit integer, and equals the number of leading `1` bits in the `NodeID` (if you brute force a `NodeID` with many leading 1 bits, this number will be larger).
The rest of the address (or `/64` prefix) is equal to the `NodeID`, but with the leading `1` bits and the first leading `0` bit removed (since you know the first bit after the last `1` must be a `0`), and truncated to a maximum of `/128` bits for an address (or `/64` for a prefix).
By optionally brute-forcing a large number of leading `1` bits, a node can increase the difficulty of address and prefix collisions.
This is basically a workaround for the fact that 128 bit IPv6 addresses (and much moreso, 64-bit prefixes) are too short *really* to be immune to collisions.
Fixing that is on my wish-list for IPv7, if for some reason that ever happens.

### NodeIDs and locators

Other than when packets enter and leave the network, Yggdrasil is oblivious to IPv6 addresses.
The untruncated equivalent, a `NodeID`, is used instead.
A node's `NodeID` is equal to a sha512sum of the node's curve25519 public cryptographic key.
Traffic, including protocol traffic, is encrypted in Yggdrasil, so spoofing an ID is equivalent to generating a private key for which the derived public key happens to hash to the right `NodeID` -- presumably a somewhat harder problem than finding a sha512sum collision.
This is mostly influenced by [cjdns](https://github.com/cjdelisle/cjdns)'s use of a double-sha512sum truncated to 128 bits as an addressing scheme, but with a single round of sha512 and a more complicated truncation rule.

Meanwhile, to actually route a packet through the network, we need to know not only *to whom* it is going, but *where* it is going -- name-independent routing implies some form of locator identifier separation (similar in purpose to [LISP](https://en.wikipedia.org/wiki/Locator/Identifier_Separation_Protocol)).
In Yggdrasil, a locator consists of a node's position in the spanning tree.
This includes two parts: the root of the tree, and a path from the root to the destination node.
Since the same root is implied, only the path portion, referred to as `coords` in the code, needs to be attached to each packet.
This path is represented by a list of interface numbers.
Each node assigns an interface to each of their peers, with 0 corresponding to their self in the current implementation, which are stored as a list of 64-bit unsigned integers.
This list is then encoded as a list of 8-bit unsigned integers, with each 64-bit integer represented by a [variable-length quantity](https://en.wikipedia.org/wiki/Variable-length_quantity) -- in practice, this means 1 byte per hop in the path from the root to the destination.
These `coords` are then used as distance labels, wherein the `coords` of any two nodes are sufficient to compute their distance apart on the tree.

### DHT FTW

Now that each node has a `NodeID` and a locator with a set of `coords`, we need a way to use them.
Specifically, when given a node's `200::/7` IPv6 address, we need to figure out the node's coords.

To do that, we first unpack the IPv6 address into a `NodeID` and a bitmask signifying which bits of the `NodeID` are known (since some were truncated away when compressing it into an IPv6 address).
Then, to map a `NodeID` onto `coords`, we use a [Distributed Hash Table](https://en.wikipedia.org/wiki/Distributed_hash_table) (DHT).
In particular, we use a [Kademlia](https://en.wikipedia.org/wiki/Kademlia)-like DHT with no proper key:value store -- we only store information about nodes in each keyspace bucket.

In practice, what this means is the following.
Each node has a 512-bit `NodeID`.
For each number of possible bits that could match in the address (0 to 511), the node stores a list of other nodes (called a bucket), which includes node keys (to derive `NodeID`s) and `coords` (so you can actually send packets to them).
These buckets store information about nodes for which exactly that number of leading bits match, so bucket 3 stores info about nodes where exactly the first 3 bits match.
More generally, we can define the distance between `NodeID`s, in some abstract keyspace, as being the `xor` of the two `NodeID`s, and each bucket covers some range of keyspace, but it turns out to be easiest to align these things so the bucket edges match bits of the `NodeID`s.
Then, for every possible `NodeID` that *could* be in the network, either a node has that `NodeID`, or else it should know someone closer to that `NodeID` in one of its buckets.
Nodes can then search for a given `NodeID` by iteratively asking nodes they know about for a list of nodes that are closer to the destination in keyspace.
Like any good Kademlia-based DHT, this requires a logarithmic number of steps, and uses a logarithmic amount of memory for each node's local table.
The leading `1` suppression from address generation tends to lead to some additional steps, as it encourages nodes to cluster in the same region of keyspace, which acts as a form of sybil attack mitigation (it becomes computationally more expensive to become a keyspace neighbor any particular node -- let alone *all* the keyspace neighbors of that node -- if everyone's address starts with a bunch of extra `1` bits.).
The currently implementation's bucket size is 2 nodes, plus any directly connected peers (always tracking all peers helps from a bootstrapping perspective, and we know about them already from the switch layer, so we can include them in the DHT at basically no cost).

It's important to note that packets are routed based on `coords`, with no regard to the structure of the DHT.
This is in contrast to [cjdns](https://github.com/cjdelisle/cjdns)'s old dht-based pathfinder, where source routing and switch label splicing [cause the path a packet takes to mirror the path followed in the DHT](https://github.com/cjdelisle/cjdns/blob/cjdns-v20.2/dht/dhtcore/ReplySerializer.c#L75-L78) (and if the DHT learns paths from other DHT entires, then extra stretch may compound as the network grows).

### The connection handshake

Once a node learns the destination's `coords` and key from the DHT lookup, it can initiate a session exchange.
The session exchange involves sending a session ping packet the destination, which includes the sender's public key, `coords`, and an ephemeral session public key (used for [forward secrecy](https://en.wikipedia.org/wiki/Forward_secrecy)).
The destination then responds with a pong, in the same format.
The nodes cache this information, and use the cached `coords` and ephemeral derived secret key to communicate.
So full connection process looks something like the following:

1. A node wants to send a packet to a node, which is addressed to `A`.
2. The node unpacks the address to get an approximate `NodeID` and bitmask.
3. The node creates a search for the `NodeID`, and initially fills it with information from the node's own portion of the DHT.
4. The node repeatedly sends a lookup to the closest node in the search, adding any responses to the search, until it reaches the closest node to the destination `NodeID` (or hits a dead end).
5. The node checks that the destination `NodeID` and bitmask match the `NodeID` of the closest node (if not, then it means the destination node doesn't exist / is offline / is unreachable while the network re-converges due some disruption).
6. The node sends a session ping to the destination.
7. The node receives a session pong from the destination, learning their public ephemeral key.
8. The nodes can now send regular IPv6 traffic to each other, encrypted with the ephemeral shared secret, using the session's cached `coords` to avoid future lookups (unless the session is unresponsive for too long, in which case any new sends will also trigger a ping, or a new DHT lookup if the pings fail).

### Conclusion

Yggdrasil provides name-independent routing with cryptographically generated addresses.
Static addressing means you can keep the same address as long as you want, even if you move to a new location, or generate a new address (by generating new keys) whenever you want.
Cryptographically generated addressing means it's impractical (if not outright impossible) to spoof addresses, so you get end-to-end encryption, with automatic key exchange and forward secrecy, and with some degree of collision resistance, without any dependence on public key infrastructure.
This also means [reflection](https://en.wikipedia.org/wiki/Denial-of-service_attack#Reflected_/_spoofed_attack) and [amplification](https://en.wikipedia.org/wiki/Denial-of-service_attack#Amplification) attacks, which are only possible [because of IP address spoofing](https://blog.cloudflare.com/the-root-cause-of-large-ddos-ip-spoofing/), or attacks based on [route hijacking](https://blog.cloudflare.com/bgp-leaks-and-crypto-currencies/), should be at least as hard as finding a truncated sha512sum collision or breaking the cryptographic primitives.
Oh, and the routing tables are ridiculously small -- we could exhaust the entire `200::/8` part of the address space and the average routing table would only be ~240 nodes + directly connected peers.

The main cost you pay for this is an extra delay during the initial DHT lookup, which is sort-of like having a slower (because it's `O(log(n))`-ish steps) DNS lookup, except the names aren't human readable.
The DHT takes a little while to converge, so *incoming* connections also tend to fail immediately after node startup, until your keyspace neighbors have a chance to hear about you, but the lookups for *outgoing* connections tend to work almost immediately.
Also, while the current DHT *should* be fine for peer-to-peer type applications, I wouldn't want to be the keyspace neighbor of an especially popular server, as this would mean a large number of DHT lookups would go through my node.
A caching DHT could, in principle, be able to address that issue without changes to the protocol, but that's not a very high priority given the size of the current network.

Lastly, IPv6 anycast *may* work with the current implementation, but which anycast node the lookup directs you to probably has nothing to do with the network topology, so it would make sense to add an extra lookup at the end of a search to return all `coords` where a particular `NodeID` can be found.
This would require slightly the DHT to include identical `NodeIDs`, and adding an additional DHT lookup step at the end to get a list of all `coords` (presumably selecting the closest `coords` returned by the lookup).
I'm not certain if this can be done in a backwards compatible way, so this will require further investigation at some point.

