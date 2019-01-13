---
sitemap: true
---

### Network Setup

This page is meant to contain general advice, some of which may not necessarily be specific to Yggdrasil, about connecting nodes together.
If a new person finds the project and wants to set something up, this page should give them a rough overview of what kind of options exist for linking nodes, under what circumstances each approach makes sense, any caveats to be aware of, and (when possible) links to examples of how set things up.

### Common Issues

Yggdrasil uses [link-local IPv6 multicast](https://en.wikipedia.org/wiki/Multicast_address#IPv6) (sent to UDP port 9001 at the `ff02::114` address) for peer discovery, and establishes connections over TCP  (using a configurable port, set to a random value when a new configuration is generated).
When trying to connect nodes, the most common issues you're likely to face are [firewalls](https://en.wikipedia.org/wiki/Firewall_(computing)) and [NAT](https://en.wikipedia.org/wiki/Network_address_translation)s.
In particular, it seems pretty easy to forget about link-local multicast when setting up firewall rules, so if two nodes aren't discovering eachother when it really seems like they should, then that's something to check.
Otherwise, if you want to be able to accept incoming connections, you'll want to make sure that your firewall allows TCP connections to the port you have configured, and you'll need to forward that port through any NATs you may have (this would include basically any IPv4 connection via a home router).

### Peering via a Local Area Network

Nodes connected to the same [link-layer network](https://en.wikipedia.org/wiki/Link_layer), such as a typical home [Ethernet](https://en.wikipedia.org/wiki/Ethernet)+[Wi-Fi](https://en.wikipedia.org/wiki/Wi-Fi) [Local Area Network](https://en.wikipedia.org/wiki/Local_area_network), should generally discover each other automatically via multicast peer discovery.
The main reasons to peer in this manner are:
1. To test the network. This is the easiest setup (typically, no changes to Yggdrasil's configuration are needed, though some firewall rules may need updating).
2. To use Yggdrasil's end-to-end encryption. This prevents eavesdropping and IP address spoofing by other nodes in the LAN.
3. To route through nodes in the LAN that may have access to other networks. In particular, it often makes sense to set up a "gateway" node in the LAN. Such a gateway node is then configured to connect to other networks via the Internet, or has additional network interfaces that connect to a local mesh network (or even just to another LAN).

### Local Mesh Networks

Two nodes `A` and `C` that can't connect directly to each other, such as if they're connected to separate LANs, can be bridged by a third node `B` and communicate by relaying traffic through it.
Yggdrasil's goal isn't to make sure that this always happens using the optimal path through the network, it merely tries to make sure that it always finds *some* path, and that the paths it finds tend to be pretty good in practice for the kinds of networks that tend to show up "naturally".

There are a few different ways to do this, but a few specific options to consider are:
1. Connect a device in one LAN to a second LAN.
2. Directly connect two devices from different LANs via a cable, without either of them joining the other's LAN.
3. Connect devices from multiple LANs to a shared switch, so you can bridge many networks with a single extra wired interface per gateway.
4. Connect nearby devices using Wi-Fi in [IBSS](https://en.wikipedia.org/wiki/Service_set_(802.11_network)#Basic_service_sets_(BSSs)) ("ad hoc") or [802.11s](https://en.wikipedia.org/wiki/IEEE_802.11s) ("mesh point") mode.

The first three don't need much elaboration, except to note that you can't connect to multiple Wi-Fi access points at the same time, so using Wi-Fi in scenario 1 requires an extra radio per network that you want to join.
That is probably both the worst and the easiest way to link two LANs via Yggdrasil, if you have the extra hardware for it and can't get and ad-hoc/mesh network set up for some reason.
For case 4, my impression is mesh enthusiasts generally prefer 802.11s over IBSS.
Far more devices support Wi-Fi in IBSS/ad-hoc mode, but that support is often buggy.
Devices and drivers that bother to support 802.11s tend to have better support for it than a random device's ad-hoc support, so if you're planning on getting any new hardware, you're better served setting up an 802.11s network.
There are also more options to tune in 802.11s to try to get better performance.
Lastly, 802.11s is a mesh routing protocol on its own, but it only extends to small networks with a few nodes.
By limiting the maximum number of nodes or number of hops, but still allowing it to route, you could have 802.11s deal with local wireless issues while Yggdrasil deals with end-to-end encryption, address assignment, and scalability on the larger network, but this has admittedly not been tested yet, to the best of my knowledge.
All that said, Yggdrasil *can* be run on ad-hoc wireless, if you're limited to using existing hardware or intentionally choose to target the lowest common denominator, which may make sense if you're more concerned with providing *any* connectivity than providing *good* connectivity.

### Long-range Network Connections

Note that this section is based on speculation and hearsay, as I haven't been in a situation where setting up such links makes sense.

When building a local mesh network, it's likely that there could be cases where node density is too low or some regions are too remote to establish a connection "naturally" via other nodes in the mesh.
There may also be cases where routing through an excessive number of intermediate nodes leads to poor performance, such as connecting a sprawling mess of neighborhoods in a city or suburb.

In such cases, it may be beneficial to create long range wired or wireless links.
As before, wired is probably the better option, but tends to be more expensive and labor intensive to deploy.
If you're seriously considering a wired connection, you probably have the budget and the know-how to manage on your own, so go for it (and let us know what happens).

For the rest of us, [long-range Wi-Fi](https://en.wikipedia.org/wiki/Long-range_Wi-Fi) and other wireless point-to-point systems make the most sense.
Assuming you have the hardware to set something up, the main obstacle is the need for clear line of sight and an obstruction-free [Fresnel zone](https://en.wikipedia.org/wiki/Fresnel_zone), to minimize interference from reflections.
As for hardware, if you already have spare Wi-Fi equipment, then it's not too expensive or difficult to build a [cantenna](https://en.wikipedia.org/wiki/Cantenna), [WokFi](https://en.wikipedia.org/wiki/WokFi), or a [Yagi-Uda](https://en.wikipedia.org/wiki/Yagi%E2%80%93Uda_antenna).
If you want something off-the-shelf, and you don't mind using a proprietary protocol instead of Wi-Fi, then Ubiquiti's [LiteBeam](https://www.ubnt.com/airmax/litebeam-ac-gen2/) hardware looks quite nice from a price/performance standpoint, just that they don't follow the WiFi standard, so you'll need to buy them in pairs to set up point-to-point links.

### Peering via the Internet

There are times when the best option for connecting two nodes is to go over an existing network connection, specifically the Internet. This allows local mesh networks to reach each other, thereby making services available in one network reachable to the rest of the world, which helps to mitigate the chicken-and-egg problem between network services and network infrastructure. In the long run, it would be nice to replace these Internet links with dedicated links when possible, but this will remain the most economical option for connecting nodes on different continents for the foreseeable future.

When setting up Internet links, you generally want to plan how you connect to your node to play off of Yggdrasil's (and the Internet's) strengths. In particular, you want to do things that avoid backtracking, and avoid routing traffic through slower connections when possible. Basically, you don't want two home connections (with slow upload speeds) connecting to each other from opposite sides of the world, and then being asked to route traffic for the rest of the network.

Since the network is still pretty small, and finding someone to peer with can be difficult, [public peers](https://github.com/yggdrasil-network/public-peers) are available to make initial setup and testing easier. The idea is to set up a node, with a reasonably good Internet connection, that will accept incoming connections from anyone. Most of these are hosted on [Virtual Private Servers](https://en.wikipedia.org/wiki/Virtual_private_server) with some of the cheaper VPS hosts. These are fairly low cost to rent, and usually have much better upload speeds (and higher data caps, if any) than many home Internet connections.

When peering, it makes sense to pick some strategy that avoids most pathological cases. Some kind of a (soft) tiered system may work, where nodes with a good connection tend to peer over longer ranges, and nodes with poorer connections tend to peer nearby (possibly just a single connection to a nearby node). Roughly speaking, each node could select some region based on how good its connection is (perhaps globally, some part of a continent, a country or state, etc.), and then peer with as many nodes as possible, within that region, which have a connection approximately as good, or better than, the node's own connection. That gives the network a tightly connected "core" of nodes, which route traffic globally, somewhat less connected clusters of regional nodes, and sparsely connected edges from mesh locals, where physical mesh networks finally enter the picture. That combination of a dense core, sparse (tree-like) edges, and some transition region in between, is roughly what a scale-free graph looks like, which is one of the topologies where Yggdrasil looks most promising from tests on simulated networks.

### Peering via SOCKS5 Overlay (including Tor and I2P)

It is also possible to peer over a SOCKS5 proxy. If you peer over Tor/I2P, then you should know that you're *probably* less anonymous than if you were using some other kind of traffic. In particular, if it's possible to reach nodes in the outside world without going over any SOCKS peers, then for performance reasons that will almost certainly turn into the route that the rest of the network uses to reach you. This means *other users* can de-anonymize you by peering for better performance. Basically, you are at most as anonymous as your least anonymous peer, assuming that Yggdrasil has no bugs which would further de-anonymize you (PROTIP: this is a horrible assumption to make if you actually care about anonymity).

That said, there are some scenarios where peering over SOCKS can be useful:
1. You need to configure a SOCKS connection to reach anything at all. Your network administrator sounds either incompetent or evil. If it's the former, then maybe they'll let Yggdrasil traffic through.
2. You want to peer with some node that's unreachable for whatever reason, but you can SSH into them (maybe you need to chain through some gateway node first). There's probably a good reason why you can't connect directly, but if you're sure you're not going to get fired or something over it, then using SSH as a SOCKS proxy is a simple way to get the node on the network.
3. You use Tor/I2P but don't *really* care about being anonymous. Maybe there's some technical reason to want to use one (a particularly bad firewall?). Maybe you just want to make de-anonymizing you inconvenient enough that people are unlikely to bother, and don't mind paying some performance cost to do it, though I'd like to think that using a mesh network already gets to some of the way there. Maybe you support anonymity in general, recognize that those networks are useless if the only people using them are the ones who really need anonymity, and so you try to run random things over them to provide cover traffic. In any case, if you have other non-SOCKS peers, then you're unlikely to actually *use* the link after the network has stabilized, so it shouldn't hurt anything in the long run, but nodes may initially be stuck using the slow Tor/I2P link if they don't know any better. If you *don't* have other peers, then you should expect a worse connection than if you'd just peered over the open Internet. Note that you may also need to adjust the `ReadTimeout` setting in your config file, otherwise connections may drop due to random packet loss or latency jitter, but then you're also likely to see the connection stick around for a while even if the node goes offline. So you kind of get the worst of everything by peering this way.

### Other Consideration

When peering nodes, it probably makes sense to keep in mind how Yggdrasil routes.
Trying to explain that is beyond the scope of this page, but when you're adding a new peer, you should always consider the possibility that:

1. You might pick this peer as a parent, even if they're far away from you in the real world, just because they happen to give a lower latency path to the root.
2. The worst case scenario is that you route on the tree, so following the [minimax principle](https://en.wikipedia.org/wiki/Minimax), you should plan around the worst case scenario and try to minimize how bad things would be if you were stuck routing everything via the tree.
3. You avoid the worst case scenario by adding shortcuts between certain points in the network.

For example, consider this network:

```
  [...]
    |
    P
   / \
  /   \
 A--B--C
```

If `{A,B,C}` are a local mesh network, and `P` is on the fastest path to the root for all of these nodes, then `A` and `C` may route through `P` instead of through `B`.
The extra connection to `P` gives the network more redundancy, and may increase the total bandwidth available to the `{A,B,C}` network, particularly for node `B`, which sees two equally good paths to the outside world, but it can lead to worse performance *within* the `{A,B,C}` network.
If `P` is pretty close by, then this may not matter too much, but you probably wouldn't want to peer with `P` in Paris if `{A,B,C}` is in Sydney.
If the two gateway nodes, `A` and `C`, are peered with each other, then that mitigates the problem (other nodes in the mesh may still route via the `A <-> C` link in cases where it's not necessary, but it's nowhere near as bad as before).

Future Yggdrasil releases may improve things for that particular example, but the real point is that Yggdrasil scales *because* it doesn't try to find perfect routes to all nodes in the network.
There's bound to always be edge cases, where peering with one part of the network will make performance worse when contacting another, so try to plan around this by avoiding unnecessary long-range links, and try to add shortcuts to bypass them if long range links are necessary.

Another thing to note is that Yggdrasil tries to avoid switching parents until it's sure that the new would-be parent is consistently faster and seems to be stable.
As of writing, this takes about 2 *hours*, which is intentionally very slow.
Nodes favor keeping the same parent, even if it's not optimal, since an extra few hops *locally* probably don't matter as much as repeatedly suffering a few seconds of outage every time we think we see a better path. 
This is because changing parents is expensive for a node, and has a cost to all other nodes that included the first node in their ancestry, so we try to avoid it if we're not sure we won't need to immediately switch back when something in the network changes again.
The idea is that fixed infrastructure will (eventually) settle into the lowest latency paths to the root that are possible in the network (+- a few cases where a path has slightly lower latency, but has too much latency jitter, or the connection is prone to dropping).
Nodes currently require a new prospective parent to provide a faster path from the root for about 2 hours before switching, to make sure the link is reliable and not overly sensitive to temporary fluctuations from changes in network load.
This delay hasn't been optimized, so it may change (most likely shorten) in the future.

