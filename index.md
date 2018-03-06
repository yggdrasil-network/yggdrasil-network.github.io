## What is it?

This is a toy implementation of an encrypted IPv6 network, with many good ideas stolen from [cjdns](https://github.com/cjdelisle/cjdns), which was written to test a particular routing scheme that was cobbled together one random afternoon.
It's notably not a shortest path routing scheme, with the goal of scalable name-independent routing on dynamic networks with an internet-like topology.
It's named Yggdrasil after the world tree from Norse mythology, because that seemed like the obvious name given how it works.
For a longer, rambling version of this readme with more information, see: [doc](doc/README.md).
A very early incomplete draft of a [whitepaper](doc/Whitepaper.md) describing the protocol is also available.

This is a toy / proof-of-principle, so it's not even alpha quality software--any nontrivial update is likely to break backwards compatibility with no possibility for a clean upgrade path.
You're encouraged to play with it, but it is strongly advised not to use it for anything mission critical.
