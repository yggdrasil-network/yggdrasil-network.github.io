---
tags: dontlink
---

# Introduction

Yggdrasil is an early-stage implementation of a fully encrypted IPv6 network. Although Yggdrasil shares many similarities with [cjdns](https://github.com/cjdelisle/cjdns), it also employs a different routing algorithm based on a globally-agreed spanning tree. In theory, an Yggdrasil network should scale well even up to internet-like topologies. Take a look at the [About](about.md) page for a detailed overview.

## Project Status

The project is currently in very early stages but it is being actively developed. Yggdrasil is considered to be pre-alpha at this point. It is entirely possible that breaking changes may still happen but generally it works well and has proven to be reasonably stable so far.

Currently we don't have any specific releases, but binaries are [automatically built](https://circleci.com/gh/yggdrasil-network/yggdrasil-go) each time that we commit to the Git repository for a number of platforms. Feel free to [download them](https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts), play with them, stress-test them or even use them in your own environments. However, be prepared to upgrade frequently and we wouldn't recommend relying on Yggdrasil for anything mission-critical at this stage!

## Implementation

Yggdrasil is written in Go. The codebase is fairly small and easy to navigate. It has been tested on a number of platforms (including Linux, Windows, macOS, NetBSD, FreeBSD, OpenBSD and Ubiquiti EdgeOS) although with various minor caveats. See the [Platforms](platforms.md) page for more information.

## Get Involved

If you are interested in getting involved in the Yggdrasil project, please join us!

- Come and chat to us in our [Matrix channel](https://matrix.to/#/#yggdrasil:matrix.org) (or even [read our channel backlog](https://view.matrix.org/room/!DwmKuvGvRKciqyFcxv:matrix.org/))
- Take a look at our [GitHub](https://github.com/yggdrasil-network/yggdrasil-go), and in particular, the [Open Issues](https://github.com/yggdrasil-network/yggdrasil-go/issues) which contain bugs waiting to be fixed, problems waiting to be solved and discussions about our implementation or design choices
