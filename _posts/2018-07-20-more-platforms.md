---
layout: post
title:  "Bringing Yggdrasil to more platforms"
date:   2018-07-20 19:10:00 +0100
author: Neil Alexander
---

### Write once...

When I first joined the Yggdrasil project, it only targeted a single platform -
Linux. For proving a concept, this is fine, but it was never going to be
reasonable to expect everyone who may want to run Yggdrasil to also run Linux.

Yggdrasil is a project which cares very deeply about scale. Born out of the lack
of scalability in cjdns, the routing scheme is designed to work well even on
very large networks with lots of users or nodes, but testing this without real
users is actually very difficult. Of course we can run simulations and model the
sorts of behaviour we might expect, but ideally we need to be able to examine
how the routing scheme behaves in the wild - with real people, on real
computers over real networks. We need to meet our users where they are.

### ... Run anywhere?

I made it my mission to bring Yggdrasil to as many platforms as possible. It
started with macOS support because I wanted to be able to run Yggdrasil on my
own computers at home. From there I branched out to OpenBSD, FreeBSD and NetBSD,
and even to Windows. I even spent some time trying to simplify the installation
process on Debian Linux by producing Debian packages. Since then we've been able
to convert those same packages to RPMs with some success. We've targeted ARM
and MIPS platforms as well, including the Ubiquiti EdgeRouter - another device
I happen to own and like.

The use of the Go programming language actually helped a lot here, because as
it turns out, Google have gone through great lengths to make Go code relatively
portable across platforms and architectures. It bundles a custom standard
library, and the Go compiler can cross-compile effortlessly for a wide range of
platforms and architectures by simply passing in environment variables like
`GOARCH` and `GOOS` to the compiler. There's no tinkering about with
architecture-specific toolchains - it's built-in for free.

Cross-compiling, however, is only part of the battle. Every platform comes with
unique behaviours and interfaces which need to be accounted for in our code.
The most troublesome part was by far the TUN/TAP adapter, a crucial component
for allowing the node to actually communicate with the Yggdrasil network instead
of just being a passive router.

### Reuse, reuse, reuse

Admittedly, we didn't write all of the TUN/TAP code ourselves. Instead we made
use of a library called [Water](https://github.com/songgao/water) which exposes
a much simpler interface. However, this library is certainly not without
inadequacies - it was necessary for us to fork this library to add platform
support for BSD, interface name support for Windows and to fix a deadlock bug
with closing the TUN/TAP adapter on macOS. (Apparently the author is not
terribly receptive to pull requests, so to this day, we continue to use our own
fork.)

Of course, a couple of fixed bugs and introduced features in Water wasn't all
that remained to be done...

### macOS

Getting Yggdrasil to work on macOS was actually relatively straight-forward. TUN
adapters on macOS are a little different to on other platforms - they are
implemented through a `utun` interface. Luckily, Water already had support for
this, but Yggdrasil didn't have a mechanism for configuring the interface
address, prefix or MTU. On macOS, all of this is possible by using an
`AF_SYSTEM` socket, so I wrote code that would fill this gap, plagiarising a
number of structs from the XNU source code in the process.

The macOS `utun` driver only supports creating TUN (Layer 3) devices and has no
support whatsoever for TAP (Layer 2) devices. There is an open-source
`tuntaposx` driver which implements both, but this would rely on the driver
being installed when `utun` does the job perfectly out of the box. Therefore,
Yggdrasil will only allow TUN mode when run on macOS.

### BSD

In many ways, Darwin (the operating system which underpins macOS) is not
dissimilar to BSD at all. In fact, there are many user-space components which
were derived or imported directly from BSD. It made sense that adding support
for FreeBSD, NetBSD and OpenBSD would be relatively simple as a result.

However, Water didn't have support for BSD, so at this point we started to
modify the Water library to include support for it. TUN/TAP on BSD is actually
much simpler than on macOS - rather than having to request a new `utun` device
from within the process, you can simply open a read/write descriptor to one of
the TUN or TAP nodes in `/dev`. The changes needed to Water were actually [very
simple](https://github.com/yggdrasil-network/water/blob/master/syscalls_bsd.go)
in the end, but did end up being really quite different from macOS.

Originally I anticipated that setting the interface address, prefix and MTU
would be just like macOS too, but this turned out not to be the case.
Whereas macOS uses an `AF_SYSTEM` socket, BSD expects you to simply perform
syscalls directly onto the TUN/TAP device node. More code for BSD was born.
However, like all system calls, these are difficult to debug, and I haven't yet
figured out the secret formula to getting these `ioctl` calls to work right. I
was forced to implement an ugly workaround - when the `ioctl` fails, it simply
falls back to calling `ifconfig` to do the work for us. Hardly ideal, but at
least functional.

The MTU proved troublesome too. Whereas Linux and macOS both support a maximum
MTU of 65535 on TUN devices (65521 for Linux TAP devices to make way for the
14-byte Ethernet header), the maximum supported MTUs on the BSDs were much
lower. FreeBSD managed only half of the above MTU with a maximum of 32767 bytes.
OpenBSD was half of that again with a maximum of 16384. NetBSD, just in the
spirit of being awkward, is different again, with a mere 9000 byte maximum. I
assume there are historical and possibly important reasons for the varying
defaults, but they remain a mystery to me at the moment.

One final place that caught me out is that, in TUN mode, OpenBSD and FreeBSD
(but *not* NetBSD) both include a "Protocol Information" header to all traffic
going in and out of the TAP adapter. Incidentally, macOS does not do this, and
on Linux, this behaviour can be disabled. Nevertheless, on OpenBSD and FreeBSD,
this behaviour seems to be mandatory. Yggdrasil would need to recognise and
strip this header, although at this time, this remains an [unfixed
problem](https://github.com/yggdrasil-network/yggdrasil-go/issues/31) as it will
require additional changes in the Water library. Currently, the library is not
aware of this and simply fails to process the packets as it believes they are
malformed. As a workaround, these operating systems were later supported through
TAP mode, which was only introduced when we added support for...

### Windows

Yes, Windows. The real outlier, sharing almost nothing in common with Linux,
macOS or BSD, apart from some very historic POSIX compliance in the early
incarnations of Windows NT which have almost certainly since lapsed. Windows
actually presented a rather unique challenge. Through the help of the OpenVPN
developers, there is an NDIS-compliant TAP driver available for Windows, and
Water even had support for it too, but it is the only platform out of our
current supported list that actually only supports TAP and doesn't support TUN.

Whereas TUN adapters typically emulate a Layer 3 routed interface, like those
used with VPNs and encapsulated tunnels, TAP adapters actually mimic a real
Layer 2 network interface - Ethernet and all. These adapters behave almost as if
the program responsible for them is connected by a virtual network cable,
requiring that every frame sent in and out is a valid Ethernet frame, with MAC
address and all.

Yggdrasil, up until this point, had not made any particular accommodation for
TAP devices, but that would not be sufficient on Windows. I was faced with two
new challenges.

#### Ethernet frames

Given that the TAP adapter would feed us entire Ethernet frames instead of just
IP packets, I started by writing code that would be dissect the Ethernet headers
and to unwrap the IP packet within, and also encapsulate any IP packets received
across the Yggdrasil network within a new Ethernet frame. Critically, Yggdrasil
never transports Ethernet headers across our encrypted sessions - only raw IP
packets, therefore the Ethernet encapsulation needs to be dealt with at the
endpoint itself.

Encapsulation and decapsulation takes place transparently if Water reports that
the adapter is a TAP device. Yggdrasil must take a note of the MAC address of
the frames coming from the TAP adapter and must create headers for the response
frames too with a MAC address of its own (a rather arbitrarily-chosen
`02:00:00:00:00:02` if you were wondering), which allows the host to believe
that the traffic coming in from the TAP adapter was sent from a real Ethernet
device.

But this alone is not enough to encourage Windows to send traffic over the TAP
adapter.

#### ICMPv6

Because the prefix length on the adapter address covers the entire Yggdrasil
address space, this leads the host to believe that the entire network is
reachable within a single broadcast domain. In the real world, this would be
like every other host being directly attached to the same switch. This is
obviously not the case, but in order to avoid having to add additional static
routes (which may be fragile or even difficult to automatically add or remove,
especially on Windows) we instead lie to the host to make it believe that every
other node is directly connected.

When a packet is sent towards an Yggdrasil IP address, a request is sent out on
the Ethernet interface to ask which MAC address holds that IP address. In IPv4,
this happens using Address Resolution Protocol (ARP), but in IPv6, this is
actually a feature of ICMPv6 called Neighbour Discovery (ND).

Windows (or any other platform, for that matter) won't actually send any real
traffic to a Layer 2 interface for the destination until it is received a
suitable ARP or ND response for the destination. Windows starts by sending out
an ND request for the destination address, therefore Yggdrasil must respond. I
wrote code that would do exactly that - we check if the destination address
matches our address space (`0200::/7`) and if it does, we send back an ND
message claiming that the destination address is reachable at
`02:00:00:00:00:02` (our virtual MAC address from before). At this point,
Windows starts sending traffic towards that MAC address and all is good.

Now that Yggdrasil can understand Ethernet headers, and it can sufficiently
lie to the host operating system to make it believe that an Yggdrasil address is
directly connected, we can now exchange traffic over a TAP adapter. Doing
this actually opened up TAP support properly on Linux and BSD too!

### Ubiquiti EdgeRouter

There is nothing particularly remarkable about the EdgeOS platform -
it's just a modified Debian distribution, but instead, running on a MIPS CPU
instead of an x86-based CPU. This meant that cross-compiling Yggdrasil for MIPS
Linux was largely sufficient to get Yggdrasil to work on the EdgeRouter.

However, EdgeOS also comes with an overlay configuration system (the Vyatta
configuration system, in fact). For friendliness, it made sense to create a
Vyatta configuration wrapper for Yggdrasil so that it can be configured in the
same way as everything else on the EdgeRouter.

These wrappers are merely scripts that either modify the `yggdrasil.conf` file
or start/stop the Yggdrasil process. The work for this is in a [separate
repository](https://github.com/neilalexander/vyatta-yggdrasil) but this results
in a single `.deb` package that can provide EdgeOS support.

### Conclusion

That concludes the journey that I have taken to bring Yggdrasil to a wider range
of platforms. Of course there are still platforms that are still to be targeted,
and some platforms are better tested than others, but it is a significant step
for the project to be able to run on more operating systems than just Linux. We
would really appreciate if you can provide us with any feedback you have about
running Yggdrasil on your own systems - as always, issues can be raised on our
[GitHub repository](https://github.com/yggdrasil-network/yggdrasil-go/issues).
