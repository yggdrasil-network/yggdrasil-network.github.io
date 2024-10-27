---
sitemap: true
tags: dontlink
---

# Configuration Reference

The configuration file contains the following options:

### `Peers`

A list of outbound peering connections to make. Peers are specified in URL format. The following types of peers are supported:

* `tcp://1.1.1.1:1234` (TCP)
* `tls://1.1.1.1:1234` (TCP+TLS)
* `quic://1.1.1.1:1234` (QUIC+TLS)
* `socks://2.2.2.2:2345/1.1.1.1:1234` (TCP via the SOCKS proxy at `2.2.2.2:2345`)
* `sockstls://2.2.2.2:2345/1.1.1.1:1234` (TLS via the SOCKS proxy at `2.2.2.2:2345`)
* `unix:///path/to/sock.sock` (UNIX)
* `ws://1.1.1.1:1234` or `ws://1.1.1.1:1234/path` (WebSockets, Yggdrasil 0.5.7 or later only)
* `wss://1.1.1.1:1234` or `wss://1.1.1.1:1234/path` (WebSockets+TLS, Yggdrasil 0.5.7 or later only)

Additional settings can optionally be added as query-string parameters to the end of the URL:

* `password=PASSWORD` — set this only for peers that require a shared secret/password to connect
* `key=PUBLICKEY` — pin the specified public key for this peer, this will cause the connection to fail if the remote side's key does not match
* `maxbackoff=DURATION` — control what the maximum backoff/retry time will be if the peering goes down, format like `30s` for seconds or `1m` for minutes

### `InterfacePeers`

Like `Peers` above, but sorted into sections representing the outbound network interface used to establish the peering connection. This is only useful on hosts that require a special multi-homed configuration, otherwise you should use `Peers` instead.

### `Listen`

A list of listeners to open for accepting incoming connections. Instead of supplying the remote address, you should instead supply a bind address. This would either be `0.0.0.0` for IPv4, `::` for IPv4+IPv6 or the IP address of a network interface on your machine. The following listener types are supported:

* `tcp://[::]:1234` (TCP)
* `tls://[::]:1234` (TCP+TLS)
* `quic://[::]:1234` (QUIC+TLS)
* `unix:///path/to/sock.sock` (UNIX)
* `ws://[::]:444` (WebSockets, Yggdrasil 0.5.7 or later only)

### `MulticastInterfaces`

Controls which interfaces to enable or disable multicast peer discovery on. The default varies by platform.

Each multicast interface block has the following options:

* `Regex` — match the names of specific interfaces, i.e. `eth.*` for matching all network interfaces starting with `eth`
* `Beacon` — controls whether this node should advertise its presence to nearby devices
* `Listen` — controls whether this node should attempt to connect to other nearby nodes that are advertising their presence
* `Port` — sets the port number for the TLS listener that is automatically opened for each matched interface, or `0` for a random port
* `Password` — optionally sets a password, only other nodes that have the same password configured will discover and connect to each other automatically
* `Priority` — controls whether peerings made to a node over this interface should take predecence over peerings made *to the same node* over other interfaces, lower numbers are higher priority, i.e. for preferring ethernet over Wi-Fi

### `AllowedPublicKeys`

A list of public keys from which your node will allow incoming peering connections.

If public keys are specified, whitelisting is enabled and only nodes with those public keys will be able to peer. If no public keys are specified in this section then peering connections will be allowed as per the `Listen` and/or `MulticastInterfaces` configuration.

**NOTE:** This option does not control who can send you traffic over the Yggdrasil Network. For that you need an IPv6 firewall.

### `IfName`

Determines which TUN interface to use. The default is set to `auto` which will try to set up a TUN automatically. If set to `none`, TUN will be disabled and the node will run in headless router-only mode.

On Linux, you can use this setting to give your Yggdrasil TUN interface a unique/persistent name, i.e. `ygg0`, if desired.

### `IfMTU`

The MTU of the interface.

### `NodeInfoPrivacy`

Whether or not the node info should automatically include build information, i.e. the operating system and architecture and the Yggdrasil build version. If privacy is enabled, the node info will not contain this information.

### `NodeInfo`

A free-form section that the node operator can use to put JSON-formatted metadata that may be made available to other nodes.
