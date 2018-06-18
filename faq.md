# FAQ

## Is Yggdrasil safe?

All traffic sent across the Yggdrasil network is encrypted end-to-end. Assuming that our crypto is solid, it cannot be decrypted or read by any intermediate nodes, and can only be decrypted by the recipient for which it was intended. However, please note that Yggdrasil has not been officially externally audited.

## Is Yggdrasil stable?

Our official stance is that it is still alpha software. Expect things to not be wholly smooth, and expect to have to upgrade often to the latest builds. That said, there is a small community of users who have not experienced any stability problems so far. Yggdrasil very rarely crashes.

## Does Yggdrasil work on my platform?

Quite likely! Take a look at the [Platforms](platforms.md) page - you'll find platform-specific notes there.

## Does Yggdrasil require IPv6?

Your system must be IPv6-capable, which just about all modern operating systems are.

While Yggdrasil does transport only IPv6 traffic internally, you do not need an IPv6 internet connection to peer with other Yggdrasil users. You can peer with other Yggdrasil nodes over either IPv4 or IPv6.

## Will Yggdrasil conflict with my network routing?

Yggdrasil uses the `0200::/7` range, which is a range deprecated by the IETF. It has been deprecated since 2004, pending changes to an RFC which simply never materialised 14 years later. It was decided to use this range instead of `fc00::/7` (which is more typically allocated to private networks) in order to prevent conflicts with existing ULA ranges.

As long as you are not using this deprecated address range on your network, you will not experience any routing conflicts.

## I've just installed Yggdrasil and I can't ping anyone. What have I missed?

Yggdrasil requires that you configure either a static peer to another Yggdrasil node, or that you discover another Yggdrasil node *on the same subnet* using multicast discovery (which is enabled by default). If you have not added or discovered any peers, you will not be able to reach beyond your own node.

You can check if you have any peers by running `yggdrasilctl getPeers` - peer on port 0 is your own node, ports 1 and above are your active peers.

Stuck for peers? Try adding a [public peer](https://github.com/yggdrasil-network/public-peers).

## I've installed the Yggdrasil Debian package and now I can't find the logs.

The Debian package installs the Yggdrasil service into systemd, therefore you can query systemd for the logs:
- `systemctl status yggdrasil`
- `journalctl -u yggdrasil`

## I've modified the configuration file but nothing has changed.

Yggdrasil only loads the configuration at startup. Restart the Yggdrasil process or service to load the new configuration.

## I'm running Yggdrasil on a machine that is reachable from the Internet. Does this mean anyone can peer with me?

Without any further configuration, yes. However, you can limit who can peer with you by modifying the `AllowedEncryptionPublicKeys` configuration option. When this list is empty, any remote node is allowed to peer with you.

To restrict incoming peerings to certain nodes, you should first ask the operators of those nodes for their `EncryptionPublicKey` and then add those public keys into your own `AllowedEncryptionPublicKeys` setting. 

## I've changed my `AdminListen` port and now `yggdrasilctl` doesn't work.

`yggdrasilctl` will assume that your admin port is on `localhost:9001`. If you have changed it, simply pass this option through to `yggdrasilctl`, i.e.
```
yggdrasilctl -endpoint=127.0.0.1:12345
```

## I want to run an Yggdrasil router to provide connectivity for other people, but I don't want them to be able to reach my own machine.

You can set the `IfName` configuration setting to `none`. This will load Yggdrasil, but will not create a TUN/TAP adapter, meaning that your host will not be exposed to the Yggdrasil network.

## I want to allow outgoing connections from my machine but prevent unwanted incoming connections.

Generally this requires you to use a firewall. The steps for this will vary from platform to platform.

### Linux (with `ip6tables`)
Assuming your TUN/TAP adapter is `tun0`:
```
ip6tables -A INPUT -i tun0 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
ip6tables -A INPUT -i tun0 -m conntrack --ctstate INVALID -j DROP
ip6tables -A INPUT -i tun0 -j DROP
```

### Windows (with Windows Firewall)
Windows, by default, will classify the TAP adapter as a "Public Network". Configure Windows Firewall to prevent incoming connections on Public networks.

Note that this does mean that your node won't be able to send any traffic to the Yggdrasil either - it will act purely as an intermediate router.

### macOS (with built-in firewall)
macOS has an application firewall, therefore any firewall policies applied on other interfaces will also apply to the Yggdrasil interface.

## Why does my Yggdrasil adapter have an unusually high MTU?

Yggdrasil peerings are typically stream-based and therefore don't suffer from fragmentation issues when pushing large amounts of data. By using the largest possible MTU supported by a platform, we can send much more data for every TCP control message. This also helps somewhat in the reduction of TCP-over-TCP amplification, as there are less control messages to be amplified.

