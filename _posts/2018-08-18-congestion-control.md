---
layout: post
title:  "Congestion Control"
date:   2018-08-18 22:10:00 -0500
author: Arceliar
---

### Why can't I, hold all these packets?

Earlier blog posts have explained, to some extent, how we know where a packet in Yggdrasil needs to go.
As G. I. Joe taught me as a child, knowing is half the battle.
Now we need to deal with the other half.

[Packet switch](https://en.wikipedia.org/wiki/Packet_switching) networks allow links to be used by multiple unrelated streams of traffic at the same time, to better utilize network links.
That's what makes them ~~better than~~ different from [circuit switch](https://en.wikipedia.org/wiki/Circuit_switching) networks like the [Public Switched Telephone Network](https://en.wikipedia.org/wiki/Public_switched_telephone_network).
This leads to a problem: what do we do if we receive multiple packets that need to be sent over the same link?
How do we decide which one to send first?
What do we do with the rest of them?

It seems like we should start sending the first packet we process, since at that point we don't *know* that the other packets need to use the same link.
If we drop them, then that's the end of the story, and this would be a much shorter post.
What we actually do is buffer them, to better utilize the available bandwidth of the link, but that just leads to further questions: what does the buffer look like? How large is it? What do we do if it's already full (assuming some concept of full is well defined)?

This post attempts to explain Yggdrasil's congestion control mechanism, why past approaches were abandoned, and our possible future plans are.

### FIFO, LIFO, front or tail drop?

The first thing to try is not to implement any explicit buffering in Yggdrasil.
Packets are received from a socket, we look up where the packet needs to go next, and then we send on that socket.
This immediately leads to blocking network operations and poor performance, so we need separate read and write threads (goroutines, in our case).
Initially, we used buffered channels and non-blocking channel sends.
This means that, instead of the reader goroutine writing to the socket to send, it would pass it to a channel which a dedicated writer goroutine would read from.
The problem with this approach is that Go channels with non-blocking sends are [FIFO](https://en.wikipedia.org/wiki/FIFO_(computing_and_electronics)) and [tail dropped](https://en.wikipedia.org/wiki/Tail_drop).
This means traffic continues to flood the channel until it fills up, and then begins dropping.
TCP doesn't notice the dropped packets until later traffic gets sent, which has to first wait for the entire go channel to empty (in addition to the OS's TCP buffers).
This is just [bufferbloat](https://en.wikipedia.org/wiki/Bufferbloat) appearing in another part of the network stack.

So instead, we switched to having the writer goroutine first read packets out of the channel and put them into a local slice, which it uses as a [LIFO stack](https://en.wikipedia.org/wiki/Stack_(abstract_data_type)) with head drop.
This means the newest packets are always sent first, and if the stack grows too large, then the oldest packets are dropped.
This kills two birds with one stone.
First, latency goes back to being something reasonable -- packets tend to either get sent quickly, or get burred in the stack so long that they drop (either because the stack drops them, or because they arrive at the destination so far out-of-order that the session layer drops them).
Secondly, LIFO order causes congestion to reorder packets, which reasonable applications recognize as a sign of congestion, and throttle down in response.

There are, however, a couple of down sized to the LIFO stack approach. A malicious or stupid node can continue to flood traffic, which will force packets from other streams to the bottom of the stack and cause them to drop, so a single stack per peer connection isn't a great long-term strategy.
Secondly, single traffic streams that *do* play nice have trouble utilizing the full bandwidth of a link.
Connecting two machines with GbE, I observe a line rate of about 928 Mbps, but running a benchmark over Ygg with LIFO stacks gives an unstable value around 850 Mbps.
Third, there's some weird behavior if we have multiple connections (e.g. over different interfaces) to the same physical peer node.
When we add a packet to the queue for one interface or another, we're committed to sending to that interface, even if another one sits idle.
This means we usually use one interface, and ignore the others, which is bad if we happen to pick a stupid interface to use (e.g. we send over ad-hoc wifi with a weak signal, instead of GbE).
That's how things behaved by the start of the v0.2 release cycle, but a lot of changes have occurred since then.

### The only winning move

We can try to game theory our way out of this one.
What we want is for multiple streams of traffic to be handled independently, to the extent that we can do so, given that the OS still manages TCP buffers out of our reach.
Then, we can reward different traffic streams to prioritize based on lowest bandwidth (i.e. size of queue / age of oldest packet in queue, with a separate queue per traffic stream).
Now we let traffic streams compete for bandwidth.
The winning strategy, to get more bandwidth during times of congestion, is to attempt to use *less* bandwidth, which I argue is exactly the behavior we want to encourage.
Streams of traffic that play nice get a fair share of bandwidth, which includes pretty much every sane TCP implementation, and streams that flood go to timeout.

### Yggdrasil's congestion control

The current congestion control mechanism in Yggdrasil is back to using FIFO queues, with one queue per `coords`+`sessionHandle` pair (which translates to a separate queue per pair of communicating nodes), and a fixed total queue size of 4 MB (just to give it some kind of maximum).

The packet forwarding procedure is then broken into two parts. The first part, when a packet is received, is:
1. Packets are received from a peer over a TCP connection and sent to a centralized switch worker goroutine.
2. The switch checks that there's at least 1 peer which is closer to the destination than we are. If not, then it passes the packet to the router (which either accepts it, if it's addressed to us, or drops the packet).
3. The switch iterates over *idle* peer connections and forwards to the one that's closest to the destination, as long as it's closer to the destination than we are.
4. If no closer peers have an idle connection, then the packet is queued (in FIFO order), with each stream of traffic (identified by destination coords, which currently embed the IPv6 flow ID field) getting its own queue.
5. If the total size of all queues is greater than the allowed maximum (currently hard-coded at 4 MB), then a random byte is selected. The first packet from the queue which contains this byte is dropped -- so front drops occur with odds proportional to the relative size of queues. This is repeated until the total queue size falls below the allowed maximum. A queue is deleted if the last packet is dropped.

Then, whenever a peer connection finishes sending a packet, the following procedure is used:
1. The connection notifies the switch that it's idle.
2. The switch iterates over queues, skipping ones for which the peer is not closer to the destination, and finds the queue for which `<age of first packet in queue>/<size of queue in bytes>` is highest (i.e. the bandwidth it's trying to use is lowest).
3. If a queue was selected, then the oldest packet from this queue is sent. If the queue is now empty, then it is deleted.
4. If no queue was found, then the connection is added to the switch's idle list, so it can be considered immediately for incoming packets (the first part of the procedure, above).

In my tests, this is sufficient to e.g. have a VoIP conversation or stream live video over a link that another node pair is flooding (via iperf3 or similar).
If there are multiple connections to one peer, then traffic is sent over whichever one happens to be free (which is more likely in proportion to the speed of the link).
My (admittedly limited) tests have seen this give about the same performance as if only the fastest link was used.
As before, connecting two devices by ethernet, the hardware gets 928 Mbps.
Yggdrasil manages about 926 Mbps after these changes, so overhead and unnecessary throttling from congestion control *should* be negligible compared to the tree's added stretch, which itself *should* be negligible in large networks, when compared to the cost of protocol traffic that would be needed by most other routing schemes.
I can live with this.

### Future work: backpressure

Yggdrasil's congestion control mechanism wasn't designed arbitrarily, there's somewhere I want to go with this.
[Backpressure routing](https://en.wikipedia.org/wiki/Backpressure_routing) as a utility-maximizing routing scheme based entirely around queue sizes.
If nodes communicate changes in queue sizes with their neighbors (at some point, e.g. whenever another packet or keep-alive traffic is sent), then we can use a combination of the tree distance metric + queue sizes (in packets) to *route around congestion*.
Furthermore, if tuned properly, backpressure routing is [basically optimal](https://arxiv.org/abs/1008.4895) in terms of the latency/bandwidth trade-off.
This means low-bandwidth traffic from low-latency applications could take the most direct route available, while bulk data transfers would use multiple and/or longer routes.

It's difficult to overstate how nice this would be, *if* this doesn't confuse existing applications, but I'm not certain how well it will really work.
It is entirely possible that real applications, like anything using TCP, may hate the way backpressure routed traffic looks, and throttle down in response.
Then we're back to everything using the shortest route it can find, where low-bandwidth traffic is already given priority, so all we'd accomplish by sending queues sizes around is wasting bandwidth.
Still, because we won't really know without trying, adding the required new packet types and testing backpressure is tentatively planned for a future release.

### Conclusion

Yggdrasil has gone through a number of different congestion control mechanisms since the TCP link layer was introduced.
The current congestion control mechanism rewards traffic streams which utilize less bandwidth by prioritizing them higher than streams using more bandwidth.
Cooperative streams obtain a fair share of bandwidth, while streams which attempt to use more than their fair share are given lower priority, and are forced to throttle down as a result.
When packet drops become necessary, a random drop mechanism is used which penalizes large queues the most, which should signal congestion to the worst offenders.
Much of this is a precursor to backpressure routing, which, if it works out in practice as well as it does on paper, should give the network a nearly-optimal latency/bandwidth trade-off.

Of course, you shouldn't just take my word for any of this.
Every good experiment is reproducible.
In an upcoming post, I plan to go over some performance studies, to show off some of the areas of Yggdrasil described so far, with instructions for how to reproduce the tests, and comparisons to other approaches where possible.

