---
sitemap: true
tags: dontlink
---

# Developers

Yggdrasil is written in Go. All of our source code is [available on GitHub](https://github.com/yggdrasil-network/yggdrasil-go). The codebase is fairly small and easy to navigate.

We welcome contributions, either in the form of [Pull Requests](https://github.com/yggdrasil-network/yggdrasil-go/pulls) or [Issues](https://github.com/yggdrasil-network/yggdrasil-go/issues). As this is a largely hobbyist project at this stage, it may take some time for us to get around to reviewing PRs/issues.

If you'd like to talk to us about the project or the source code, please join our [Matrix channel](https://matrix.to/#/#yggdrasil:matrix.org). 

## Platforms

Yggdrasil has been tested on a number of platforms (including Linux, Windows, macOS, FreeBSD, OpenBSD and Ubiquiti EdgeRouter). There are also packages available for a number of distributions/platforms.

## Build Artifacts

Build artifacts from our CI are made available (for 30 days after building) for the following GitHub branches:

- [`master`](builds.md) (stable) builds
- [`develop`](builds-develop.md) (unstable) builds, for testing soon-to-be-released changes
- [`future`](builds-future.md) (testing) builds, likely to be broken or incompatible with the stable release


## Algorithms / Spec

All Yggdrasil nodes have their own public/private key to enable identity checking and encryption. The public key is converted to the node's IPv6 address in a simple lossy-compression algoritm which is defined in code [here](https://github.com/yggdrasil-network/yggdrasil-go/blob/99227b60cedfb278b1b6d6ac9ae965f47fce5ccf/src/address/address.go#L51), but a simpler to read Python implementation follows;


``` python
import bitarray
import bitarray.util
import ipaddress

def ygg_pk2ipv6(pkstr: str) -> ipaddress.IPv6Address:
    pkbits: bitarray.bitarray = bitarray.util.hex2ba(pkstr)
    if len(pkbits) != 256:
        raise RuntimeError('Wrong number of bits %d in public key "%s"' %
                           (len(pkbits), pkstr))
    nzerobits: int = pkbits.index(bitarray.bitarray('1'))
    restbits: bitarray.bitarray = pkbits[nzerobits+1:]
    ipbits: bitarray.bitarray = (bitarray.bitarray('00000010') +
                                 bitarray.util.int2ba(nzerobits, length=8) +
                                 ~restbits[:112])
    return ipaddress.IPv6Address(ipbits.tobytes())
```

English description:

Imagine that your public key is concatenation of the following bit sequences: N * 0b, 1b, REST, or in other words, N zero bits followed by single non-zero bit, followed by remaining bits.
Then your node IPv6 address is the sequence of the following bits:

8 bits: 02h in hex (or 00000010b in binary) as hardcoded prefix,
8 bits: N (number of zero bits in the beginning of your public key,
112 bits: first 112 bits of REST, inverted.
