### Introduction

Yggdrasil is an early-stage implementation of a fully encrypted IPv6 network. Although Yggdrasil shares many similarities with [cjdns](https://github.com/cjdelisle/cjdns), it also employs a different routing algorithm based on a globally-agreed spanning tree. In theory, an Yggdrasil network should scale well even up to internet-like topologies. 

#### Project Status

The project is currently in very early stages but it is being actively developed. Yggdrasil is considered to be pre-alpha at this point. It is entirely possible that breaking changes may still happen but generally it works well and has proven to be reasonably stable so far.

Currently we don't have any specific releases, but binaries are automatically built each time that we commit to the Git repository for a number of platforms.

Feel free to play with them, stress-test them or even use them in your own environments. However, be prepared to upgrade frequently, and don't run anything mission-critical over it!

#### Implementation

Yggdrasil is written in Go. The codebase is fairly small and easy to navigate. It has been tested on a number of platforms (including Linux, Windows, macOS, NetBSD, FreeBSD, OpenBSD and Ubiquiti EdgeOS) although with various minor caveats. See the [Platforms](platforms.md) page for more information. 
