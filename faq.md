---
sitemap: true
---

# FAQ

### Why Yggdrasil?

Yggdrasil was created in order to build a decentralised routing scheme for mesh networks that can potentially operate at a global scale, motivated in particular by significant performance and scaling issues that were present in [cjdns](https://github.com/cjdelisle/cjdns) at the time. However, Yggdrasil is not a fork — it is a separate standalone project. See the [About](about.md) page for more information around the problems we are trying to solve.

### Is Yggdrasil safe?

All traffic sent across the Yggdrasil Network is encrypted end-to-end, such that it cannot be decrypted or read by any intermediate nodes, and can only be decrypted by the recipient for which it was intended.

However, it is still a public network, therefore you should take adequate precautions when using Yggdrasil, such as using a firewall and not exposing services you do not want others to be able to reach.

We also feel that it is important to highlight that, even though Yggdrasil is using industry-standard cryptography, our codebase has not been officially externally audited.

### Is Yggdrasil stable?

Yggdrasil is relatively stable and very rarely crashes, although our official stance is that it is still alpha software and comes with all of the usual warnings. You should expect things to not be wholly smooth in all cases and therefore you should probably not run any mission-critical or life-and-death workloads over Yggdrasil at this time.

You should also make sure that you stay up-to-date as often as possible with the latest versions to ensure that your node continues to be compatible as we make protocol changes.

### Is Yggdrasil anonymous?

No, it is not a goal of the Yggdrasil project to provide anonymity. Direct peers over the Internet will be able to see your IP address and may be able to use this information to determine your location or identity. Multicast-discovered peerings on the same network will typically expose your device MAC address. Other nodes on the network may be able to discern some information about which nodes you are peered with.

### Will my machine be exposed to other users of the network?

Typically yes, you will be routable from other nodes on the network. If you want to restrict incoming connections to your machine, you should use an IPv6 firewall. The steps for this will vary from platform to platform.

#### Linux (with `ip6tables`)

Assuming your TUN/TAP adapter is named `tun0`:

```
ip6tables -A INPUT -i tun0 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
ip6tables -A INPUT -i tun0 -m conntrack --ctstate INVALID -j DROP
ip6tables -A INPUT -i tun0 -j DROP
```

#### Windows (with Windows Firewall)

Windows, by default, should classify the TAP adapter as a "Public Network". Configure Windows Firewall to prevent incoming connections on Public networks.

#### macOS (with built-in firewall)

macOS has an application firewall, therefore any firewall policies applied on other interfaces will also apply to the Yggdrasil interface.

### Can I run an Yggdrasil router without a TUN interface?

Yes, you can set the `IfName` configuration setting to `"none"`. This will prevent Yggdrasil from creating a TUN interface.

You will be able to create and accept peering connections and will continue to route traffic on behalf of your peers, but you will not be reachable from within the Yggdrasil Network, nor will you be able to send traffic into the network either. In this mode, other users on the network will receive no response if they try to reach or ping your node addresses.

### Does Yggdrasil require IPv6?

Your system must be IPv6-enabled in order to send or receive traffic to/from the Yggdrasil Network, which just about all modern operating systems are.

While Yggdrasil does transport only IPv6 traffic internally, you do not need an IPv6 internet connection to peer with other Yggdrasil users. You can peer with other Yggdrasil nodes over either IPv4 or IPv6.

### Will my node route traffic on behalf of other nodes?

If you have peerings to more than one node, your node can potentially route traffic on behalf of other network users. If you have only peerings to a single node, you will typically not route traffic, although you may still handle some occasional protocol messages. Keep this in mind if you are restricted by data usage caps.

### Will Yggdrasil conflict with my network routing?

Yggdrasil uses the `0200::/7` range, which is a range deprecated by the IETF. It has been deprecated since 2004, pending changes to an RFC which simply never materialised all these years later. It was decided to use this range instead of `fc00::/7` (which is more typically allocated to private networks) in order to prevent conflicts with existing ULA ranges.

As long as you are not using this deprecated address range on your network, you will not experience any routing conflicts.

### Can the network be crawled?

Currently it is possible to crawl the network by querying the debug query handlers of remote nodes. This is how the [network map](http://51.15.204.214) and popularity contest are generated today, allowing us to observe how the network scales. However, these endpoints are not required for the network to operate and will definitely be removed in a future version — certainly before a potential version 1.0 release.

### I've just installed Yggdrasil and I can't ping anyone. What have I missed?

Yggdrasil requires that you configure either a static peer to another Yggdrasil node, or that you discover another Yggdrasil node *on the same subnet* using multicast discovery (which is enabled by default). Without peers, you will be isolated from the rest of the network. If you have not added or discovered any peers, you will not be able to reach beyond your own node.

You can check if you have any peers by running `yggdrasilctl getPeers` - peer on port 0 is your own node, ports 1 and above are your active peers.

Stuck for peers? Try adding a [public peer](https://github.com/yggdrasil-network/public-peers).

### Does Yggdrasil add or remove peerings automatically?

Peerings may be automatically established to nodes on the same subnet if multicast peering is enabled on both nodes, however, Yggdrasil will never add or remove remote peerings automatically over other networks like the Internet.

### I've installed the Yggdrasil Debian package and now I can't find the logs

The Debian package installs the Yggdrasil service into systemd, therefore you can query systemd for the logs:

- `systemctl status yggdrasil`
- `journalctl -u yggdrasil`

### I've modified the configuration file but nothing has changed

Yggdrasil only loads the configuration at startup. Restart the Yggdrasil process or service to load the new configuration. If you are troubleshooting peering connections, check the logging output and/or examine the output of `yggdrasilctl getPeers` to see if any peers are connected.

### I'm running Yggdrasil on a machine that is directly reachable from the Internet. Does this mean anyone can peer with me?

The default Yggdrasil configuration does not listen on any external ports for incoming peering connections. In order to accept peering connections from other nodes, you must add a listener entry to the `Listen` section of the configuration file. You can bind to any TCP port of your choosing in either plain TCP (e.g. `tcp://[::]:12345`) or in TLS (e.g. `tls://[::]:54321`) mode.

You can either limit incoming connections to your host using a firewall by limiting or denying connections to the port specified in your `Listen` configuration option. This is useful if you want to limit peerings from certain IP ranges or on certain interfaces.

Alternatively, you can limit who can peer with you by modifying the `AllowedPublicKeys` option in your Yggdrasil configuration. When this list is populated, only the nodes with the specified public keys will be able to establish a peering connection with your node. This is useful if you want to ensure that you can only peer from your own devices or to allow peerings from certain users. When this list is empty, any remote node is allowed to peer with you.

### I am running Yggdrasil from behind a NAT. Will this affect my connectivity?

To accept incoming peerings, you will probably need to configure port forwarding on your router/gateway. Yggdrasil listens on the port number specified in the `Listen` setting, so forward this port to the machine that runs Yggdrasil.

To use outbound peerings, that is, static peers that have been configured in your `Peers` setting, you will likely not need to change anything.

Regardless of whether your peering connections are inbound or outbound, you will be able to accept incoming connections on your Yggdrasil IPv6 addresses. This makes Yggdrasil an effective way to accept incoming connections when you reside behind NATs that you do not control, i.e. carrier-grade NATs (CGNAT).

### Why does my Yggdrasil adapter have an unusually high MTU?

Yggdrasil peerings are typically stream-based and therefore don't suffer from fragmentation issues when pushing large amounts of data. By using the largest possible MTU supported by a platform, we can send much more data in each write and the TCP connection will take care of the rest. This also helps somewhat in the reduction of TCP-over-TCP amplification, as there are less control messages to be amplified.

This also uses less CPU, as we can send more data for every system call on the TUN/TAP adapter or network socket. System calls often result in context switches by the operating system and are expensive operations, therefore by using an MTU of up to 65535, we can save as many as 42 context switches for each packet - a substantial performance improvement!

### Does Yggdrasil work alongside an existing VPN?

Yes, Yggdrasil will work alongside many VPNs on most platforms.

Please note that some VPN software (for example, [Mullvad VPN](https://mullvad.net/en/help/using-mullvad-vpn-app/#killswitch) and Cloudflare WARP) tries to prevent traffic leaking by adding firewall rules or blackhole routing table entries. This can interfere with Yggdrasil in some cases. You will need to reconfigure your VPN software to not drop traffic in this way or contact the vendor of the VPN software for support, as we are unable to prevent this from within Yggdrasil.

### Does Yggdrasil work with networks like Tor or I2P?

Yggdrasil can peer over Tor or I2P. See [/public-peers/tree/master/other](https://github.com/yggdrasil-network/public-peers/tree/master/other) for public peers.

### Is there any benefit to being the "root node" of the network spanning tree?

No. At worst, the root node may be used in worst-case-scenario paths between
other nodes in the absence of being able to determine better routes, but this
is not advantageous.
