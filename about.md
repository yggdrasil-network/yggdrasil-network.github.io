---
sitemap: true
logo: iconoir/sparks.svg
---

# About

Yggdrasil is an experimental compact routing scheme that is **fully decentralised** and only requires a small amount of state to work. It is predominanently a shortest-path scheme, whereby the network will attempt to find the most direct path to the destination.

<img src="/assets/images/about/first.svg" style="max-height: 12em; max-width: 22em; margin: 1em; float: right; clear: both;" />

Nodes are equal participants and connect to each other using **peering connections** which carry network traffic. Peerings can be set up over any IP network — whether that's a direct wired or wireless link, a local area network or even the Internet. In some cases, peerings can also be set up automatically by nearby devices on the same network using multicast discovery.

All nodes on an Yggdrasil network are routers and will automatically pass traffic to help it get closer to its destination where possible. This means that, even in a network that is only sparsely connected, **all nodes will be reachable** by all other nodes on that network. It doesn't even matter if a node is behind a NAT — once a peering is established, traffic flows in both directions over that peering.

Yggdrasil is also designed to tolerate changes in the network. For example, if a link fails, the network will self-heal and use other links to route traffic where available. This makes it **suitable for use in mesh networks**, where the network topology can and often will change.

Each node on the network has a location-independent **cryptographic identity** and, in our [current experimental implementation](implementation.md), stable IPv6 addresses are generated from this key. This allows IPv6-supporting applications to work over Yggdrasil largely without modification. The address is fully mobile and stays with the node as it moves around the network.

### Why Yggdrasil?

Many networks that exist today are hierarchical in nature, require extensive manual configuration and often rely on a certain degree of centralisation in order to scale. This often makes it difficult or impractical to set up networks quickly on an ad-hoc basis and so most people are heavily reliant on their Internet Service Providers (ISPs).

On the other hand, Yggdrasil requires **very little configuration** in order to work and full multi-hop networks can be built up very quickly and easily using Yggdrasil.

Nodes do not need to be assigned an address from a centralised authority; they can **generate their own cryptographic identity** and, more importantly, they can keep this address as they roam. Once peering connections are established, routing information is propagated quickly and automatically throughout the network.

This ability to provide full end-to-end routability between all network nodes means that Yggdrasil is potentially an enabling technology for true edge computing scenarios, as well as real-world mesh networks, as it **does not rely on the Internet** to function.

### How does Yggdrasil compare to other projects?

Yggdrasil is often compared to other projects attempting to create anonymous overlays, such as Tor, I2P, Lokinet and others. These projects are very different to Yggdrasil, in part because they try to guarantee anonymity. Yggdrasil does not aim to provide and **does not guarantee anonymity**. These projects are also intentionally overlay-by-design rather than by convenience, whereas Yggdrasil only exists as an overlay network today because it is an easy way to test the design.

Yggdrasil is frequently compared with VPN projects such as Wireguard, Tailscale, Nebula and Zerotier. Although it is possible to use Yggdrasil to build up private networks and/or point-to-point VPN links, this isn't explicitly our primary goal. It's also important to understand that connecting any single node of a private network to another network (such as a public peer) will result in **both networks being bridged together**.

Finally, Yggdrasil has **no native exit nodes**, nor does it have any concept of exit nodes, for providing access to the public Internet or to other networks. This can be achieved using proxies or other tunnelling solutions on top of Yggdrasil but this is not something we explicitly aim to provide.

### What is the status of the project?

Yggdrasil is currently an **alpha-level research project**, with on-going development but actively maintained. Our expectation is that a future beta-quality release should know enough to be compatible in the face of wire format changes, and reasonably feature complete. A “stable” 1.0 release, if it ever happens, would probably be feature complete, with no expectation of future wire format changes, and free of known critical bugs.

The true goal of this project is to test the scalability of the Yggdrasil routing scheme and we are doing so with our [overlay network implementation](implementation.md). Studying the behaviour of the network in the real world is most easily achieved with a large number of participants running the software and joining the public test network. We've done our best to support [as many platforms as possible](installation.md) and have a number of [public peers](https://github.com/yggdrasil-network/public-peers) that you can connect to in order to join the network, so please feel free to experiment.

That said, we **recommend against running any mission-critical workloads** over Yggdrasil and it may be dangerous to rely solely on Yggdrasil for any life-or-death situation. There may be failure modes that we don't yet know about yet!

The project is likely to reach a number of possible outcomes:

1. The project may reach a reasonably stable state but never attract a large enough number of users
1. The project may attract a large enough number of users but reveal inherent design flaws in the process (a learning exercise for a future project or protocol version perhaps)
1. The project may end up working perfectly even as the network grows, in which case it will become worthwhile to look at writing better-optimised implementations and/or moving the important parts into other projects
