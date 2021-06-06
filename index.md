---
tags: dontlink
sitemap: true
---

# Yggdrasil

Yggdrasil is an early-stage implementation of a fully end-to-end encrypted IPv6 network. It is lightweight, self-arranging, supported on multiple platforms and allows pretty much any IPv6-capable application to communicate securely with other Yggdrasil nodes. Yggdrasil does not require you to have IPv6 Internet connectivity - it also works over IPv4.

Although Yggdrasil shares many similarities with [cjdns](https://github.com/cjdelisle/cjdns), it employs a different routing algorithm based on a globally-agreed spanning tree and greedy routing in a metric space, and aims to implement some novel local backpressure routing techniques. In theory, Yggdrasil should scale well on networks with internet-like topologies. Take a look at the [About](about.md) page for a detailed overview.

## Project Status

The project is currently in early stages but it is being actively developed. We have recently released [version 0.3.15](changelog.md) and are still regularly working on updates.

Yggdrasil is written in Go. The codebase is fairly small and easy to navigate. It has been tested on a number of platforms (including Linux, Windows, macOS, FreeBSD, OpenBSD and Ubiquiti EdgeRouter).

A small number of users have been using and stress-testing Yggdrasil quite heavily for a number of purposes, including but not limited to secure remote access (SSH and VNC), access to Matrix, Jabber and IRC servers and even some video streams, large file transfers and performance tests. We have a number of [internal services](services.md) available, contributed and operated by the community.

It is entirely possible that occasional breaking changes may still happen in future versions but generally Yggdrasil works well and has proven to be reasonably stable so far, with recent builds reaching weeks of uptime without crashes or failures.

Binaries are [automatically built](https://circleci.com/gh/yggdrasil-network/yggdrasil-go) each time that we commit to the Git repository for a number of platforms. Feel free to [download them](https://github.com/yggdrasil-network/yggdrasil-go/releases), play with them, stress-test them or even use them in your own environments. However, be prepared to upgrade frequently and we wouldn't recommend relying on Yggdrasil for anything mission-critical at this stage!

## Get Involved

If you are interested in or would like to get involved in the Yggdrasil project, please feel free to join us!

- Set up Yggdrasil on your [own computer or router](installation.md) and join the network - the bigger the network grows, the better understanding we will get of how Yggdrasil can scale!
- Come and chat to us in our [Matrix channel](https://matrix.to/#/#yggdrasil:matrix.org) (or even [read our channel backlog](https://view.matrix.org/room/!DwmKuvGvRKciqyFcxv:matrix.org/))
- Take a look at our [GitHub](https://github.com/yggdrasil-network/yggdrasil-go), and in particular, the [Open Issues](https://github.com/yggdrasil-network/yggdrasil-go/issues) which contain bugs waiting to be fixed, problems waiting to be solved and plenty of in-depth discussions about our implementation or design choices
