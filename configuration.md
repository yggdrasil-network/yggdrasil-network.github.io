---
sitemap: true
---

# Configuration

Yggdrasil can be run with a dynamically generated configuration, using sane-ish default settings, with `yggdrasil --autoconf`. In this mode, Yggdrasil will automatically attempt to peer with other nodes on the same subnet, but it also generates a random set of keys each time it is started, and therefore a random IP address.

In most cases, a static configuration simplifies most setups - it allows you to maintain the same IP address, configure static peers and various other options that will persist across restarts.

Yggdrasil supports configuration in either HJSON or JSON format. HJSON is the default preferred format, as it has comments, although JSON support is available due to ease of manipulation.

## Generating Configuration

If you installed Yggdrasil through one of the platform packages (i.e. macOS, Debian, RPM) then a default configuration file may already exist in `/etc/yggdrasil.conf`.

Otherwise, you can generate a configuration file in the following ways:

- **Generate HJSON**: `yggdrasil -genconf > /etc/yggdrasil.conf`
- **Generate JSON**: `yggdrasil -genconf -json > /etc/yggdrasil.conf`

## Using Configuration

Yggdrasil can accept a configuration file either through `stdin` or by being given a path on the filesystem to a configuration file:

- **Using stdin**: `yggdrasil -useconf < /etc/yggdrasil.conf`
- **Using file:** `yggdrasil -useconffile /etc/yggdrasil.conf`

## Normalising Configuration

If you want to see the original format of the configuration file, or convert between HJSON and JSON formats, you can use the `-normaliseconf` option, e.g.

- **Convert from HJSON to JSON**: `yggdrasil -normaliseconf -useconffile /etc/yggdrasil.conf -json`
- **Convert from JSON to HJSON**: `yggdrasil -normaliseconf -useconffile /etc/yggdrasil.conf`

Normalising the configuration also adds any missing configuration items with their default values. This can be useful when upgrading to a newer version of Yggdrasil that adds new configuration options. Many of our distribution packages normalise the configuration automatically during upgrade.

## Configuration Layout

A new configuration file has the following format. Please note that some of the default values will vary from platform to platform.

```
{
  # List of connection strings for outbound peer connections in URI format,
  # e.g. tcp://a.b.c.d:e or socks://a.b.c.d:e/f.g.h.i:j. These connections
  # will obey the operating system routing table, therefore you should
  # use this section when you may connect via different interfaces.
  Peers: [
    tcp://a.b.c.d:xxxxx
    socks://e.f.g.h:xxxxx/a.b.c.d:xxxxx
    tls://a.b.c.d:xxxxx
  ]

  # List of connection strings for outbound peer connections in URI format,
  # arranged by source interface, e.g. { "eth0": [ tcp://a.b.c.d:e ] }.
  # Note that SOCKS peerings will NOT be affected by this option and should
  # go in the "Peers" section instead.
  InterfacePeers: {
    "eth0": [
      tcp://a.b.c.d:xxxxx
      tls://a.b.c.d:xxxxx
    ]
  }

  # Listen addresses for incoming connections. You will need to add
  # listeners in order to accept incoming peerings from non-local nodes.
  # Multicast peer discovery will work regardless of any listeners set
  # here. Each listener should be specified in URI format as above, e.g.
  # tcp://0.0.0.0:0 or tcp://[::]:0 to listen on all interfaces.
  Listen: [
    tcp://[::]:xxxxx
    tls://[::]:xxxxx
  ]

  # Listen address for admin connections. Default is to listen for local
  # connections either on TCP/9001 or a UNIX socket depending on your
  # platform. Use this value for yggdrasilctl -endpoint=X. To disable
  # the admin socket, use the value "none" instead.
  AdminListen: tcp://localhost:9001

  # Regular expressions for which interfaces multicast peer discovery
  # should be enabled on. If none specified, multicast peer discovery is
  # disabled. The default value is .* which uses all interfaces.
  MulticastInterfaces:
  [
    .*
  ]

  # List of peer encryption public keys to allow incoming TCP peering
  # connections from. If left empty/undefined then all connections will
  # be allowed by default. This does not affect outgoing peerings, nor
  # does it affect link-local peers discovered via multicast.
  AllowedEncryptionPublicKeys: []

  # Your public encryption key. Your peers may ask you for this to put
  # into their AllowedEncryptionPublicKeys configuration.
  EncryptionPublicKey: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

  # Your private encryption key. DO NOT share this with anyone!
  EncryptionPrivateKey: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

  # Your public signing key. You should not ordinarily need to share
  # this with anyone.
  SigningPublicKey: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

  # Your private signing key. DO NOT share this with anyone!
  SigningPrivateKey: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

  # The port number to be used for the link-local TCP listeners for the
  # configured MulticastInterfaces. This option does not affect listeners
  # specified in the Listen option. Unless you plan to firewall link-local
  # traffic, it is best to leave this as the default value of 0. This
  # option cannot currently be changed by reloading config during runtime.
  LinkLocalTCPPort: 0

  # Local network interface name for TUN/TAP adapter, or "auto" to select
  # an interface automatically, or "none" to run without TUN/TAP.
  IfName: auto

  # Set local network interface to TAP mode rather than TUN mode if
  # supported by your platform - option will be ignored if not.
  IfTAPMode: false

  # Maximux Transmission Unit (MTU) size for your local TUN/TAP interface.
  # Default is the largest supported size for your platform. The lowest
  # possible value is 1280.
  IfMTU: 65535

  # The session firewall controls who can send/receive network traffic
  # to/from. This is useful if you want to protect this node without
  # resorting to using a real firewall. This does not affect traffic
  # being routed via this node to somewhere else. Rules are prioritised as
  # follows: blacklist, whitelist, always allow outgoing, direct, remote.
  SessionFirewall:
  {
    # Enable or disable the session firewall. If disabled, network traffic  
    # from any node will be allowed. If enabled, the below rules apply.
    Enable: false

    # Allow network traffic from directly connected peers.
    AllowFromDirect: true

    # Allow network traffic from remote nodes on the network that you are  
    # not directly peered with.
    AllowFromRemote: true

    # Allow outbound network traffic regardless of AllowFromDirect or  
    # AllowFromRemote. This does allow a remote node to send unsolicited  
    # traffic back to you for the length of the session.
    AlwaysAllowOutbound: false

    # List of public keys from which network traffic is always accepted,  
    # regardless of AllowFromDirect or AllowFromRemote.
    WhitelistEncryptionPublicKeys: []

    # List of public keys from which network traffic is always rejected,  
    # regardless of the whitelist, AllowFromDirect or AllowFromRemote.
    BlacklistEncryptionPublicKeys: []
  }

  # Allow tunneling non-Yggdrasil traffic over Yggdrasil. This effectively
  # allows you to use Yggdrasil to route to, or to bridge other networks,
  # similar to a VPN tunnel. Tunnelling works between any two nodes and
  # does not require them to be directly peered.
  TunnelRouting:
  {
    # Enable or disable tunnel routing.
    Enable: false

    # IPv6 subnets belonging to remote nodes, mapped to the node's public  
    # key, e.g. { "aaaa:bbbb:cccc::/e": "boxpubkey", ... }
    IPv6RemoteSubnets: {}

    # IPv6 subnets belonging to this node's end of the tunnels. Only traffic  
    # from these ranges (or the Yggdrasil node's IPv6 address/subnet)  
    # will be tunnelled.
    IPv6LocalSubnets: []

    # IPv4 subnets belonging to remote nodes, mapped to the node's public  
    # key, e.g. { "a.b.c.d/e": "boxpubkey", ... }
    IPv4RemoteSubnets: {}

    # IPv4 subnets belonging to this node's end of the tunnels. Only traffic  
    # from these ranges will be tunnelled.
    IPv4LocalSubnets: []
  }

  # Advanced options for tuning the switch. Normally you will not need
  # to edit these options.
  SwitchOptions:
  {
    # Maximum size of all switch queues combined (in bytes).
    MaxTotalQueueSize: 4194304
  }

  # By default, nodeinfo contains some defaults including the platform,
  # architecture and Yggdrasil version. These can help when surveying
  # the network and diagnosing network routing problems. Enabling
  # nodeinfo privacy prevents this, so that only items specified in
  # "NodeInfo" are sent back if specified.
  NodeInfoPrivacy: false

  # Optional node info. This must be a { "key": "value", ... } map
  # or set as null. This is entirely optional but, if set, is visible
  # to the whole network on request.
  NodeInfo: {}
}
```

Note that any field not specified in the configuration will use its default value, which may be random from run to run in the case of ports or keys.

## Configuration Options

- `Listen`
    - A list of strings in the form `[ "tcp://listenAddress:listenPort", "tls://listenAddress:listenPort", ... ]`, on which to listen for TCP or TLS connections from peers.
    - Note that, due to Go language design choices, `[::]` listens on IPv4 and IPv6 on most platforms, while an empty IP or `0.0.0.0` listens only to IPv4.
    - Note that a `tcp://` listener can only accept `tcp://` peer connections, and a `tls://` listener can only accept `tls://` peer connections
- `AdminListen`
    - Port to listen on for the admin socket, specified in URI format, i.e. `tcp://localhost:9001`.
    - On supported platforms, the admin socket can listen on a UNIX domain socket instead, i.e. `unix:///var/run/yggdrasil.sock`.
    - The default is to listen on the loopback interface (`tcp://localhost:9001`) which ensures that only local connections to the admin socket are allowed.
    - Note that if you change the listen address to a non-loopback address, this may allow other hosts on the network to manage the Yggdrasil process. This probably isn't desirable.
- `Peers`
    - A list of strings in the form `[ "tcp://peerAddress:peerPort", "tls://peerAddress:peerPort", "socks://proxyAddress:proxyPort/peerAddress:peerPort", ... ]` of peers to connect to.
    - Peer hostnames can be specified either using IPv4 addresses, IPv6 addresses or DNS names.
    - Each entry should begin with `tcp://` or `socks://proxyAddress:proxyPort/`.
- `InterfacePeers`
    - Like peers above, but arranged using specific interface names: `{ "eth0": [ "tcp://peerAddress:peerPort", "tls://peerAddress:peerPort", "socks://proxyAddress:proxyPort/peerAddress:peerPort", ... ], "eth1": [], ... }` of peers to connect to.
    - Note that a `tcp://` peer connection can only connect to a `tcp://` listener, and a `tls://` peer connection can only connect to a `tls://` listener
- `AllowedEncryptionPublicKeys`
    - A list of strings in the form `["key", "key", ...]`, where `key` is each node's `EncryptionPublicKey` key which you would like to allow connections from.
    - This option allows you to restrict which other nodes can connect to your Yggdrasil node as a peer. It applies to incoming TCP connections.
    - If the list is left empty, or the option is not specified, then Yggdrasil will automatically accept connections from any other node.
    - Note that multicast link-local peerings (see below) will always override this option if enabled.
- `EncryptionPublicKey`
    - A hexadecimal string representing the node's public Curve25519 key.
    - A node's ID in the DHT is a (sha-512) hash of this public key.
    - A node's IP address is derived from the ID.
- `EncryptionPrivateKey`
    - A hexadecimal string representing the node's private Curve25519 key.
    - This is a private key, **don't share it with anyone**.
- `SigningPublicKey`
    - A hexadecimal string representing a node's public Ed25519 key.
    - Used primarily for signatures in the greedy routing scheme.
- `SigningPrivateKey`
    - A hexadecimal string representing the node's private Ed25519 key.
    - This is a private key, **don't share it with anyone**.
- `MulticastInterfaces`
    - A list of regex strings for matching which interfaces to enable multicast peer discovery on. Interfaces that don't match any of the provided regexes are ignored.
    - The default value (`.*`) matches all interfaces.
    - This is also useful if you want to prevent accidental peering over a layer 2 VPN running on top of Yggdrasil.
    - These interfaces will be listened on UDP port 9001 which needs to be opened in firewall. For example with UFW: `ufw allow from fe80::/10 to any port 9001 proto udp`
- `IfName`
    - The name of the `tun` or `tap` network interface to create or use. Applications send packets over this interface to use the network.
    - On most platforms, an empty string or the default `"auto"` will create a new interface automatically.
    - You can also specify `"none"` as the interface name, in which case Yggdrasil will run as a router only without opening a network interface. This effectively allows Yggdrasil to carry traffic for other nodes without exposing the system to the network.
    - The behaviour of this option is different on different operating systems. Some quick notes:
        - On Linux, any suitable interface name can be specified.
        - On FreeBSD, OpenBSD and NetBSD, a full path to the TAP interface should be specified, i.e. `"/dev/tap0"`.
        - On macOS, a `utun` device is automatically assigned by the operating system, therefore you cannot specify a name.
        - On Windows, a network adapter friendly name (like `"Local Area Connection 2"`) can be specified to choose a specific adapter. Use "Network Adapters" in Control Panel to see and/or rename adapters.
- `IfTAPMode`
    - If true, then the interface will be a `tap` device (Layer 2) instead of a `tun` (Layer 3) device.
    - Default value is platform specific, and some platforms support only `tun` or `tap` mode.
    - Note that the network only transports IPv6 packets, so frames sent to or received from a `tap` are decapsulated or encapsulated at the end points of a connection.
    - In TAP mode, Yggdrasil automatically answers Neighbor Discovery Packet (NDP) requests on behalf of Yggdrasil IPv6 addresses.
    - In TAP mode, it may be possible to bridge with the Yggdrasil `tap` adapter, e.g. to allow container bridging.
- `IfMTU`
    - The MTU of the `tun`/`tap` interface.
    - Defaults to the maximum value supported on each platform, up to `65535` on Linux/macOS/Windows, `32767` on FreeBSD, `16384` on OpenBSD, `9000` on NetBSD, etc.
    - Yggdrasil automatically assists in Path MTU Discovery (PMTU) and will limit the MTU of a given connection between two hosts to the lower of the MTUs used by each endpoint. The operating system is made aware of these MTUs using ICMP.
- `SessionFirewall`
    - The session firewall lets you control the traffic sent to/from that node. It is useful if you want to act as a router without allowing access to your host, or if you only want to allow access from a specific set of nodes.
    - Contains the following configuration options:
        - `Enable`
            - Disables or enables the session firewall.
            - If enabled, the rules from the below options apply. If disabled, all traffic is allowed.
        - `AllowFromDirect`
            - Decides whether or not traffic should be allowed from nodes that are directly peered to you.
        - `AllowFromRemote`
            - Decides whether or not traffic should be allowed from nodes that are elsewhere on the network and *not* directly peered to you.
        - `AlwaysAllowOutbound`
            - Decides whether to allow traffic to be sent to any node on the network, as long the connection is initiated from your node.
            - Note that once you open a session with a remote node, they *can* send traffic back to you for the lifetime of the session.
        - `WhitelistEncryptionPublicKeys`
            - A list of node's `EncryptionPublicKey` keys that are always allowed to exchange traffic with this host, both incoming or outgoing.
            - Takes priority over the above options, but is overridden by the blacklist below.
        - `BlacklistEncryptionPublicKeys`
            - A list of node's `EncryptionPublicKey` keys that are never allowed to communicate with this node under any circumstances, both incoming or outgoing.
            - Takes priority over all other options.
- `TunnelRouting`
    - Crypto-key tunnel routing allows you to tunnel either IPv4 or IPv6 traffic to remote nodes over Yggdrasil. This is similar to using a VPN.
    - Contains the following configuration options:
        - `Enable`
            - Enables crypto-key routing.
            - If enabled, the following crypto-key routes will be used by Yggdrasil. If disabled, the below options have no effect.
        - `IPv6RemoteSubnets`
            - A list of routes in the form `{ "aaaa:bbbb:cccc::/e": "EncryptionPublicKey", ... }`
            - For each entry, an IPv6 route entry will be created that sends traffic destined for `aaaa:bbbb:cccc::/e` to the node with the specified `EncryptionPublicKey` (effectively your "remote" ranges).
        - `IPv6LocalSubnets`
            - A list of allowed source subnets in the form `[ "aaaa:bbbb:cccc::/e" ]`
            - Specifies a list of source IPv6 addresses which are allowed to be sent over the tunnel (essentially your "local" ranges).
            - Traffic from the Yggdrasil node's IPv6 address and routed subnet are always allowed.
        - `IPv4RemoteSubnets`
            - A list of routes in the form `{ "a.b.c.d/e": "EncryptionPublicKey", ... }`
            - For each entry, an IPv4 route entry will be created that sends traffic destined for `a.b.c.d/e` to the node with the specified `EncryptionPublicKey` (effectively your "remote" ranges).
        - `IPv4LocalSubnets`
            - A list of allowed source subnets in the form `[ "a.b.c.d/e" ]`
            - Specifies a list of source IPv4 addresses which are allowed to be sent over the tunnel (essentially your "local" ranges).
- `SwitchOptions`
    - Switch options are advanced tuning settings. Ordinarily you should not attempt to change these from their defaults.
    - Contains the following configuration options:
        - `MaxTotalQueueSize`
            - The maximum allowed size, in bytes, of all local switch queues combined. Default is `4194304` (or 4MB).
- `NodeInfo`
    - Public information about your node. This information can be requested by any node and may be particularly useful for crawlers and network surveys.
    - There are no set options - you can freely enter any valid HJSON or JSON (whichever your configuration file is using).
    - An example of some ways in which you may populate nodeinfo:
    ```
NodeInfo:
{
    name: hostname.y.domain.com
    contact: email@domain.com
    location: Place, Country
}
    ```

# Use Cases

## Manually Connecting to Peers

By default, only link-local auto-peering is enabled. This connects devices that are connected directly to each other at layer 2, including devices on the same LAN, directly connected by ethernet or configured to use the same ad-hoc wireless network.

As the network uses ordinary TCP, it is possible to connect over other networks, such as the Internet or WAN links, provided that the connecting node knows the address and port to connect to and that the connection is not blocked by a NAT or firewall. If the node resides behind a NAT, then port forwarding may be required in order to accept incoming connections.

By default, connections to peers are made over TCP. It is possible to route through a `socks://proxyAddr:proxyPort/` connection.
This uses TCP over the specified SOCKS proxy, and can be used to tunnel out from a network with a particularly restrictive firewall, for example, using SSH tunneling.
This can also be used to [connect over Tor](https://github.com/yggdrasil-network/public-peers/blob/master/other/tor.md), particularly for `.onion` hidden service addresses, although this does have a number of performance issues and is not generally recommended.

If you are unable to find nodes in the nearby area, a best effort is made to maintain a list of [Public Peers](https://github.com/yggdrasil-network/public-peers) for new users looking to join or test the network.

## Advertising a Prefix

While it is generally encouraged that nodes run the software locally, to provide end-to-end cryptographic sessions and participate in routing, this is not always practical.
Some network devices will inevitably be unable to run user code, but may still provide IPv6 connectivity.
Users may also prefer to avoid running the software on an otherwise compatible system, perhaps to provide guest access or to avoid any overhead to battery powered devices.
To that end, it is each node is assigned a `/64` prefix in parallel to their address.
A node acting as a router may advertise this prefix just as they would any other ordinary IPv6 network.

This may be best illustrated by example.
Suppose a node has generated the address: `200:1111:2222:3333:4444:5555:6666:7777`.
Then the node may also use addresses from the prefix: `300:1111:2222:3333::/64` (note the `200` changed to `300`, a separate `/8` is used for prefixes, but the rest of the first 64 bits are the same).

On Linux, something like the following should be sufficient to advertise a prefix and a route to `200::/7` using radvd to a network attached to the `eth0` interface:

1. Enable IPv6 forwarding (e.g. `sysctl -w net.ipv6.conf.all.forwarding=1` or add it to sysctl.conf).

2. `ip addr add 300:1111:2222:3333::1/64 dev eth0` or similar, to assign an address for the router to use in that prefix, where the LAN is reachable through `eth0`.

3. Install/run `radvd` with something like the following in `/etc/radvd.conf`:
```
interface eth0
{
        AdvSendAdvert on;
        prefix 300:1111:2222:3333::/64 {
            AdvOnLink on;
            AdvAutonomous on;
        };
        route 200::/7 {};
};
```

Note that a `/64` prefix has fewer bits of address space available to check against the node's ID, which in turn means hash collisions are more likely.
As such, it is unwise to rely on addresses as a form of identify verification for the `300::/8` address range.

## Generating Stronger Addresses (and Prefixes)

While 128 bits is long enough to make collisions technically impractical, if not outright impossible, it's not unreasonable to think that 64 bits may be attackable at some point if not now.
Without going too far into the details, addresses are a truncated hash of a node's public key, with leading `1` bits accumulated and suppressed (along with the inevitable first `0` bit).
Thanks to the accumulator, it is possible to brute force generate keys which include more bits of the node's ID in the node's IPv6 address, thereby making collisions more difficult.
This can partially mitigate the fact that IPv6 addresses are only 128 bits long, and, more importantly, that prefixes are a mere 64 bits, 16 bits of which are sacrificed to the `200::/7` prefix and 1-byte accumulator in either case.

In short, if you plan to advertise a prefix, or if you want your address to be exceptionally difficult to collide with, then it is strongly advised that you burn some CPU cycles generating a harder-to-collide set of keys, using the following tool:

```
go run misc/genkeys.go
```

This continually generates new keys and prints them out each time a new best set of keys is discovered.
These keys may then be manually added to the configuration file.
