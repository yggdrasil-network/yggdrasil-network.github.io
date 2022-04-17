---
layout: post
title:  "Enthusiastic about Yggdrasil"
date:   2021-10-01 16:00:00 +0200
author: tomz
---

*Guest post from [TomZ](https://read.cash/@TomZ/mesh-networking-with-yggdrasil-e300d18e)*

A couple of months ago I learned of a new technology called Yggdrasil. This is a so called overlay network that uses mesh-networking. And I think this is very exciting for the broader Internet world.

To understand what the value of Yggdrasil is, let's take a look at what we use today for networking. We use the Internet Protocol, or IP to route TCP over. Together "TCP/IP". When IP version 4 was [introduced](https://en.wikipedia.org/wiki/IPv4) in 1981 it had a certain design which is largely still unchanged today. We essentially have roads that underpin the Internet of today that are principally unchanged for 40 years now.

As 1981 was really the beginnings of the wider commercial Internet, we can translate that to roads that are like basic cobblestones, meant for horse and carriage to ride over. Roads not wide enough for two cars to pass each other by. Definitely nothing fancy like traffic lights yet.

In contrast to our analogy, our real roads supporting our modern cars have evolved.  Many lanes. Lines and indications. Better surface. Traffic signs and lights, to name a few. The roads also evolved in concert with the cars that drive on them.

On the Internet the opposite happened, near zero changes to the basic road system. Limited number of destinations, really bad security etc. Yet, we have a pretty decent Internet today! What gives?

All innovations that actually did happen on the Internet have been done while the roads (IPv4) stayed basically the same. Instead those changes happened in what moves on top of them. For instance, innovative people have created massive private networks to sidestep some limitations on the main one (using [NAT](https://en.wikipedia.org/wiki/Network_address_translation)).

This may sound harsh to the engineers back then, but it was a different time.  Mobile Internet was simply impossible in the mind of the engineers in 1981. What do you expect, put your computer on a cart and stick the cable in each room as you ride by? Don't be silly.

The result of this is that we have to deal with concepts 4 decades old to do new things. Unchanged since 1981 is that an IP-address is determined by location.  If I go from my WIFI and walk out the house, my phone gets a new Internet address. The original assumption was that a machine could not move.  The fallout is immediate.  Changing IP address means you are a different person to the remote server. Connections will time-out waiting for answers from the previous address, which no longer has anyone there. The app on the phone ends up creating a new connection from a new IP address.

To avoid exactly this problem when driving around while the phone hops from one cell-tower to
the other, the mobile phone providers' internal network has been setup with quite some overhead to ensure you have one address. A private address from that phone company, not a real one the wider Internet knows you by, mind you.

So, 40 years of innovations and changes of environment have been forced to happen all without actually changing the basic layer 3; the Internet Protocol. And this is very noticeable in our security (the 3-letter agencies that listen in) and ultimately in our user experience. Not to mention cost due to lots of extra innovations that have been added which need to be maintained.

# IP-v6

At one point, in 1995, the "next" version of the Internet Protocols (IP) was published: [IPv6](https://en.wikipedia.org/wiki/IPv6). Many items that are wrong with IPv4 have been fixed in that version. Stuff I won't mention here other than the most well known, which is the [number of addresses](https://en.wikipedia.org/wiki/IPv4_address_exhaustion) world wide. Suffice to say it fixes a lot of **other** issues as well.

Yet, after 25 years we can't say that the usage of IPv4 has diminished and that IPv6 has stolen marketshare from it. This begs the questions, why is this and how to improve?

IPv6 has as its goal the all-out and complete replacement of IPv4. Due to its nature it is not really possible for an IPv6 user to access the IPv4 network without having both available on his computer. As a result, most machines that use v6 also are connected to v4 as long as there are v4-only services. This is why adding more IPv6 doesn't actually remove a dependency on IPv4 infrastructure.

Even worse, just having the IPv6 software on your machine isn't enough to start using it. Your ISP must give you access and they can't do that without their hardware supporting it. Keep in mind that they will have to also support v4 until the last of the v4 user stops using that.

A lot of time has passed while IPv6 has been available and normally replacement of hardware gradually causes an upgrade. But due to the above mentioned reasons, that is not possible here. The end result that we still are stuck with the cobblestone roads of IPv4 for the majority of our networking.

Where IPv6 failed to replace it so far is that it assumed people would stop using one and switch to the other.  All devices, all at once.  Turns out, that simply doesn't happen.

# Can Yggdrasil do better?

Yggdrasil is a peer to peer network. The system weaves itself into a mesh-network without any central administration.  
Yggdrasil is currently also a so-called "overlay network". You can tie together Yggdrasil nodes via IPv4 or IPv6, public or private, and likely in future nodes can connect over more innovative networking.

This means any upgrade path is going to be much easier. The major blocker why IPv6 didn't succeed to penetrate is simply avoided with Yggdrasil.
I can start a node and hook into the Yggdrasil network over my existing network connection. Public or private. No need for any hardware upgrades, no need to call your ISP.

When we look at the wider Internet, providing an upgrade path that is low effort and decentralized is a good first step, a necessity even. But that doesn't mean success. It would display hubris to assume that after building it, people will just start using it. No, we need to keep pushing and expand the ecosystem f
or this kind of solution.  
Without such a push we are sure to see te big 5 Internet companies to keep inching us to an ever more corporate owned Internet. So how can we exploit Yggdrasil to the fullest in order to really sell it as a solution?

# Benefits

The Internet today is mostly a star-system. Each computer, phone or fridge connects to a central place. Those connect to a central place again etc. For instance if I want to send an email to my neighbor, it will likely cross several state or country lines to reach the person that is just next door. This is the result of a star system. People connect to central hubs.

The natural result of us having used a star design is that it is more speedy to connect to a server far away as long as they are near the center of the star systems. This results in the design we see today that most people "talk" to each other over the Internet by both of them connecting to Twitter, Facebook or similar big companies. Such is the cheaper solution with today's Internet.

Yggdrasil is a mesh, making its network fundamentally different. My Yggdrasil node will seek out and connect to all other ygg-aware machines nearby. For instance everyone in the classroom. The result of this is that when I send an yggmail, it will go via the most direct path. The data will avoid going to the "center" and back. A mesh network allows more direct connections.

Mesh networks optimize for shortest path, peer-to-peer connections. Yggdrasil specifically makes it easy for people to use the Internet in a much more personal way. Run your Nextcloud at home and whitelist you laptop's Yggdrasil IP-address. Wherever you are in the world you can now access your private service with basically no administrator hurdle.

What Yggdrasil mesh networking then provides is a new method of doing Internet that makes it easy to avoid central servers, it makes it easy to avoid moving your data via those big Internet central hotspots. The old model is still possible if you are stubborn. But why would you, when the ones that benefit from the old star system are the 1% and the governments because then they can monitor all you do.

The icing on the cake is that all data sent via the Yggdrasil network is always encrypted end-to-end. And this is major feature that Yggdrasil gives all users out-of-the box.

Encryption to keep what you do on the Internet private is long known to be a good idea. The problem is that it is hard. Ask a random techy and they likely don't even know that if you use HTTPS-only websites, that your ISP still can monitor every single website you visit. The website is leaked  as part of the [SNI](https://en.wikipedia.org/wiki/Server_Name_Indication) standard.

In contrast, using the Yggdrasil design of encrypting the connection, a plain simple HTTP connection is more safe from prying eyes. There is no leaking of website names. Usage of website certificates may keep being useful because they still provide identity and chain-of-trust protection, but with Yggdrasil won't leak to monitoring parties.

Protecting private Internet data is quite important, as the 2013 Snowden datadrops showed. But while it is getting better, it is the last gap that is really hard to close. In 2017 [49% of the websites](https://thehill.com/policy/technology/271152-study-finds-about-half-of-web-traffic-is-encrypted) had a certificate (provided encrypted data). Up majorly since 2013. But in 2021 we [only reached 80%](https://hostingtribunal.com/blog/ssl-stats/). Activation is slowing. We will never reach 100% this way.

Encryption at the connection-layer is the only way to reach 100%. And please realize that the web is only one part of the Internet. There are many other parts that are today having much less than 80% encryption. [DNS is a prime example](https://blog.powerdns.com/2019/02/07/the-big-dns-privacy-debate-at-fosdem/) that can be used to snoop on people and which is largely open to monitoring.

While privacy and a more personal Internet are very visible, they are not the only benefits you get from using Yggdrasil.  Remember that I mentioned IPv6 fixing a number of things in v4? This was a big reason it was created in the first place. Well, Yggdrasil gives your device a world reachable IPv6 address.  Machines get assigned an address based on some cryptography. You can fetch a new address every time you connect or you can spend a while mining to get a more private address for personal use as your address that stays with you wherever you go.

# Future

One of the more interesting choices that the creators of Yggdrasil made is that it aims to reuse known Internet technologies. Other software needs almost no special support for stuff to just work.

Off-the-shelf webservers, webbrowsers and essentials like firewalls all work with Yggdrasil without modification. Anything that uses internet sockets over TCP will continue working and indeed there are people running many existing tools on the Yggdrasil network today.

To understand the problem of any new competing network getting popular, look at it like a new road-system. Even the IPv4 and the IPv6 networks are incompatible (can't drive from one to the other). Adding Yggdrasil means we now have 3 incompatible road systems. The good thing is that because we already had 2 for so long, most software is already adapted to this concept. A webserver will happily serve pages on all 3 networks. A webbrowser will simply pick the best available choice.

The software itself is still marked as "alpha-level", which is mostly due to the fact that breaking changes are expected before it's meant for a wider audience. I understand that the longer term goals are to stabilize and document ([RFC](https://en.wikipedia.org/wiki/Request_for_Comments)) the protocol and maybe even get a kernel module for Linux as that would be the best way to make it fast and cheap on resources.

I personally think that the biggest advantage is in the usecases that are really hard today which Yggdrasil makes easy. Running a website, [Nextcloud](https://nextcloud.com/) or [Matrix](https://matrix.org/discover/) home-server are impossible for the majority to do privately. The main choice today is to get a contract with one of the virtual-private-server suppliers, but with the rising number of people running their own Raspberry PI or media computer at home, this is less than attractive.  Yggdrasil makes this easy. Run your own Nextcloud on a home-computer or Pi and pair your devices once to get access worldwide.

Not everything is ideal with Yggdrasil. The team picked an IPv6 range that is reserved for private use. The advantage is that this doesn't conflict with the global Internet. But the downside is that using the DNS system isn't really possible. For instance when I added my Yggdrasil IP to the DNS, my entire website was marked as offline by [letsencrypt](https://letsencrypt.org/). Longer term we need to find a good DNS solution, one that will be supported by most of the applications in use today.

Some examples of how we can make Yggdrasil become more useful:

* Webbrowsers now only mark HTTPS connections as "secure". On Yggdrasil
  all connections are encrypted, just maybe not trusted.
* More peer-to-peer tools that explicitly support Yggdrasil would be good.  
  For instance Nextcloud could use the remote IP address, or even the Yggdrasil public key,
  to allow or disallow access. Such a simple filter would help make world-reachable
  deployments more secure.
* Network routers can run Yggdrasil to make end-user configuration much simpler. It would be nice
  to work with those teams to get more support in their distro's.

# Conclusion

The Internet was started by people that had the saying that the Internet sees censorship as damage and routes around it. But we can't really claim that this is the case today.

Yggdrasil uses mesh networking and peer-to-peer connections with encrypt-everything by default. It solves the mobile problem and makes IPv6 available to all devices you could want to connect.

Those simple and basic building blocks are needed to give the push that the existing peer to peer movement needs to actually break through and reach mass audiences.

We looked at webbrowsing that had severe problems with privacy and security and since Snowden, 8 years ago, we still have a long way to go to fix this on the plain Internet. Instead of fixing each and every protocol and service, what would work better is a base-layer upgrade and Yggdrasil provides such an upgrade.

A Internet base-layer upgrade paves a real path towards a system that the original Internet designers actually were aiming for, where censorship is local and can be routed around.

There are various challenges left and the software itself is definitely going to need more work, according to the authors. At the risk of a small interruption every now and then this does allow the wider audience to work on making this future a reality. It already today allows people deploy and use private cloud solutions furthering the original promise of the Internet.

