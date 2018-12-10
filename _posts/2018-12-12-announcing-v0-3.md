---
layout: post
title:  "Announcing Yggdrasil Network v0.3"
date:   2018-12-12 00:00:00 -0000
author: Neil Alexander
---

### It's finally here

At the end of 2017, Yggdrasil's first commit was uploaded to GitHub - a project
to explore whether it was possible to build a decentralised, end-to-end
encrypted and scalable compact routing scheme modelled around the concept of a
global spanning tree. Many concept routing schemes that we have seen to date
seem to have problems with scalability - after the network exceeds a certain
size, they either fail to perform or they start to rely on centralised points in
order to consolidate routing information. We wanted to build something that
would not be subject to these limitations and to maintain decentralisation as
far as possible. To our knowledge, this hasn't quite been achieved before.

Throughout the course of 2018, Yggdrasil has gone from being a very early-stage
project supporting only a single platform to a feature-strong and relatively
stable project which now runs on many supported platforms. Although we currently
still haven't advanced from the "alpha" label, our network has grown to exceed
70 nodes across the world (and growing slowly but steadily), with a good portion
of these users coming on-board and contributing their own
[services](services.md) to the network and using the network for their own
purposes. We've even had a small amount of publicity - [Toronto
Mesh](https://tomesh.net) have been exploring using Yggdrasil on their city-wide
mesh net, and even [presented some Yggdrasil
fundamentals](https://www.nuug.no/aktiviteter/20181009-mesh/) to the Norwegian
Unix User Group (NUUG) back in October.

So far, we believe that Yggdrasil is well on track to delivering on its promises
to build a fully end-to-end encrypted, self-arranging IPv6 network. We also
believe that Yggdrasil should be scalable on paper; we have somewhat proven
this in simulations, but the real proof will come in how the Yggdrasil Network
scales up in the real world, on real hardware, across real links. Having users
helping us to test brings us closer to our goal and enhances our understanding
of how our software will behave on large-scale network graphs.

Version 0.3 has been quite some time coming - we released version 0.2.7 on the
13th October and we have been working since then on what will make it into this
release. Even though it feels in some ways that version 0.3 is a relatively
small evolutionary release, it's actually by far our biggest release yet. We've
included quite a large list of fixes, changes and even new features and over
2000 lines of code changed. We've taken a lot of feedback from our users about
their use-cases and pain points, and we've collected topographical data from
various contributor nodes to try and get a good view of what the network looks
like. We've even experienced some rather large topology changes and enjoyed
relatively good network stability throughout.

For much of the time that we were developing v0.3, we had thought that there
would end up being protocol-breaking changes and that this would render v0.3
incompatible with nodes running previous versions. I am happy to announce that
we have **not** needed to introduce breaking changes at this stage and currently
the network has been running a mix of both older and newer developmental nodes
without any particular issues.

### New features

You'll be able to see the full list of modifications that have been made in our
[changelog](changelog.md), but I'd like to take the time to discuss some of our
bigger changes.

Perhaps the largest user-facing change is the introduction of Crypto-Key Routing
for traffic tunnelling, allowing you to effectively use Yggdrasil as a VPN for
both IPv4 and IPv6 traffic between any two given points on the network. This
tunnelled traffic enjoys the same benefits as regular Yggdrasil IPv6 traffic in
that it is end-to-end encrypted and our many optimisations assist in preventing
TCP-over-TCP anomalies that often arise in other solutions. I wrote an
introductory [blog post](_posts/2018-11-06-crypto-key-routing.md) back at the
beginning of November about CKR, which explains some more about how to
configure it and how it works.

In the background, we've made a change from using a Kademlia-based DHT to a
Chord-based DHT. This has some advantages in that a node can bootstrap and start
working with far less state information than was needed before. Additional
state, which we learn about automatically through searches, helps to speed up
DHT lookups. We also believe that using Chord can help us to reduce some idle
DHT chatter on the network in the future, and to help nodes converge more
quickly when they change their coordinates on the network.

We've fixed a reasonable number of bugs and crashes, including in the DHT,
switch and ICMPv6 code, and have made a number of additions to the admin socket
in order to support new functionality and to make parameter naming more
consistent throughout.

### Upgrading

Our CI pipeline automatically produces builds for all supported platforms and
these will become available on our [Builds page](builds.md). In addition, our S3
repository for Debian and RPM-based distributions will also be updated with the
new package releases.

New macOS .pkg installers are now available as a part of the v0.3 release too,
so installing and upgrading on macOS is now significantly easier than before.
You can find these installers on the [Builds page](builds.md) also.

On other platforms, simply download the latest binary for your platform and drop
it into place. Remember to take a backup of your configuration and normalise it,
which will add any new options for features in v0.3:
```
cp /path/to/yggdrasil.conf /var/backups/yggdrasil.conf
yggdrasil -useconffile /var/backups/yggdrasil.conf -normaliseconf > /path/to/yggdrasil.conf
```

### What's next?

Our work is far from over. We still have a list of things that can potentially
be rolled into future releases and we will be looking to see what we should
prioritise for our next version.

As always, we'll be hanging around in our Matrix/IRC channel too, so please feel
free to join and chat to us:

- `#yggdrasil:matrix.org` on Matrix
- `chat.freenode.net` channel `#yggdrasil` on IRC

A big thanks to our contributors, particularly those who have worked on creating
packages for Yggdrasil and bringing it to their distributions of choice, and to
all of the users who use Yggdrasil, contributing services and providing feedback
to us on a regular basis!
