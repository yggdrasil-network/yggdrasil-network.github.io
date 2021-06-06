---
sitemap: true
---

# FAQ

### Is Yggdrasil safe?

All traffic sent across the Yggdrasil network is encrypted end-to-end. Assuming that our crypto is solid, it cannot be decrypted or read by any intermediate nodes, and can only be decrypted by the recipient for which it was intended. However, please note that Yggdrasil has not been officially externally audited.

### Is Yggdrasil stable?

Our official stance is that it is still alpha software. Expect things to not be wholly smooth, and expect to have to upgrade often to the latest builds. That said, there is a small community of users who have not experienced any stability problems so far. Yggdrasil very rarely crashes.

### Is Yggdrasil anonymous?

It is not a goal of the Yggdrasil project to provide anonymity. Your direct peers may be able to determine your location if, for example, you are peering over the Internet.

### Does Yggdrasil require IPv6?

Your system must be IPv6-capable, which just about all modern operating systems are.

While Yggdrasil does transport only IPv6 traffic internally, you do not need an IPv6 internet connection to peer with other Yggdrasil users. You can peer with other Yggdrasil nodes over either IPv4 or IPv6.

### Will Yggdrasil conflict with my network routing?

Yggdrasil uses the `0200::/7` range, which is a range deprecated by the IETF. It has been deprecated since 2004, pending changes to an RFC which simply never materialised 14 years later. It was decided to use this range instead of `fc00::/7` (which is more typically allocated to private networks) in order to prevent conflicts with existing ULA ranges.

As long as you are not using this deprecated address range on your network, you will not experience any routing conflicts.

### Can the network be crawled?

Currently it is possible to crawl the network to reveal the spanning tree relationships by querying nodes in the DHT. This is how the [network map](http://51.15.204.214) and popularity contest are generated today. However, it is considered a design weakness that this is even possible currently and will hopefully be fixed in the future.

### Can I run a crawler?

Please don't run your own network crawler. Crawlers generate a lot of network-wide protocol traffic and effect is amplified by each additional crawler. If you really really *really* want information about the network today, use an [existing data source within the network](http://[301:4541:2f84:1188:216:3eff:feb6:65a3]:3001/nodeinfo.json) instead of crawling yourself.

### I've just installed Yggdrasil and I can't ping anyone. What have I missed?

Yggdrasil requires that you configure either a static peer to another Yggdrasil node, or that you discover another Yggdrasil node *on the same subnet* using multicast discovery (which is enabled by default). If you have not added or discovered any peers, you will not be able to reach beyond your own node.

You can check if you have any peers by running `yggdrasilctl getPeers` - peer on port 0 is your own node, ports 1 and above are your active peers.

Stuck for peers? Try adding a [public peer](https://github.com/yggdrasil-network/public-peers).

### I've installed the Yggdrasil Debian package and now I can't find the logs.

The Debian package installs the Yggdrasil service into systemd, therefore you can query systemd for the logs:
- `systemctl status yggdrasil`
- `journalctl -u yggdrasil`

### I've modified the configuration file but nothing has changed.

Yggdrasil only loads the configuration at startup. Restart the Yggdrasil process or service to load the new configuration.

### I'm running Yggdrasil on a machine that is directly reachable from the Internet. Does this mean anyone can peer with me?

Without any further configuration, yes.

However, you can either limit incoming connections to your host using a firewall by limiting or denying connections to the port specified in your `Listen` configuration option. This is useful if you want to limit peerings from certain IP ranges or on certain interfaces.

Alternatively, you can limit who can peer with you by modifying the `AllowedEncryptionPublicKeys` option in your Yggdrasil configuration. When this list is empty, any remote node is allowed to peer with you.

To restrict incoming peerings to certain nodes, you should first ask the operators of those nodes for their `EncryptionPublicKey` and then add those public keys into your own `AllowedEncryptionPublicKeys` list. From that point forward, only nodes with those public keys will be allowed to peer with you.

### I am running Yggdrasil from behind a NAT. Will this affect my connectivity?

To accept incoming peerings, you will probably need to configure port forwarding on your router/gateway. Yggdrasil listens on the port number specified in the `Listen` setting, so forward this port to the machine that runs Yggdrasil.

To use outbound peerings, that is, static peers that have been configured in your `Peers` setting, you will likely not need to change anything.

### Why does my Yggdrasil adapter have an unusually high MTU?

Yggdrasil peerings are typically stream-based and therefore don't suffer from fragmentation issues when pushing large amounts of data. By using the largest possible MTU supported by a platform, we can send much more data in each write, and the TCP connection will take care of the rest. This also helps somewhat in the reduction of TCP-over-TCP amplification, as there are less control messages to be amplified.

This also uses less CPU, as we can send more data for every system call on the TUN/TAP adapter or network socket. System calls often result in context switches by the operating system and are expensive operations, therefore by using an MTU of up to 65535, we can save as many as 42 context switches for each packet - a substantial performance improvement!

### I've changed my `AdminListen` port and now `yggdrasilctl` doesn't work.

`yggdrasilctl` will assume that your admin port is on `localhost:9001`. If you have changed it, simply pass your configured endpoint through to `yggdrasilctl`, i.e.
```
yggdrasilctl -endpoint=127.0.0.1:12345
```

### I want to run an Yggdrasil router to provide connectivity for other people, but I don't want them to be able to reach my own machine.

You can set the `IfName` configuration setting to `"none"`. This will load Yggdrasil, but will not create a TUN/TAP adapter, meaning that your host will not be exposed to the Yggdrasil network. This does of course mean that you won't be able to send any traffic from that node to Yggdrasil either!

### Does Yggdrasil work alongside an existing VPN?

Yes, Yggdrasil should not interfere with existing VPNs. VPN traffic can be sent while using Yggdrasil.

### Does Yggdrasil work with networks like Tor or I2P?

Yggdrasil can peer over Tor or I2P. See [/public-peers/tree/master/other](https://github.com/yggdrasil-network/public-peers/tree/master/other) for public peers.

### I want to allow outgoing connections from my machine but prevent unwanted incoming connections.

Generally this requires you to use a firewall. The steps for this will vary from platform to platform.

#### Linux (with `ip6tables`)
Assuming your TUN/TAP adapter is `tun0`:
```
ip6tables -A INPUT -i tun0 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
ip6tables -A INPUT -i tun0 -m conntrack --ctstate INVALID -j DROP
ip6tables -A INPUT -i tun0 -j DROP
```

#### Windows (with Windows Firewall)
Windows, by default, will classify the TAP adapter as a "Public Network". Configure Windows Firewall to prevent incoming connections on Public networks.

#### macOS (with built-in firewall)
macOS has an application firewall, therefore any firewall policies applied on other interfaces will also apply to the Yggdrasil interface.

### Is there any benefit to being the "root node" of the network?

No. At worst, the root node may be used in worst-case-scenario paths between
other nodes in the absence of being able to determine better routes, but this
is not advantageous. 
