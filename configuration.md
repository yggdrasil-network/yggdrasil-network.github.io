# Configuration

Yggdrasil can be run with a dynamically generated configuration, using sane-ish default settings, with `yggdrasil --autoconf`. In this mode, Yggdrasil will automatically attempt to peer with other nodes on the same subnet, but it also generates a random set of keys each time it is started, and therefore a random IP address.

In most cases, a static configuration simplifies most setups - it allows you to maintain the same IP address, configure static peers and various other options that will persist across restarts.

Configuration can be provided to Yggdrasil in HJSON format either through `stdin` (using `yggdrasil --useconf < path/to/configuration.json`) or through a path to a configuration file (using `yggdrasil --useconffile path/to/configuration.json`).

A new configuration file may be generated with `yggdrasil --genconf > path/to/configuration.json`, which looks something like:

```
{
  # Listen address for peer connections (default is to listen for all
  # connections over IPv4 and IPv6)
  Listen: "[::]:45744"

  # Listen address for admin connections (default is to listen only
  # for local connections)
  AdminListen: "[::1]:9001"

  # List of connection strings for static peers (i.e. tcp://a.b.c.d:e)
  Peers: []

  # List of peer BoxPubs to allow UDP incoming TCP connections from
  # (if left empty/undefined then connections will be allowed by default)
  AllowedBoxPubs: []

  # Your public encryption key (your peers may ask you for this to put
  # into their AllowedBoxPubs configuration)
  BoxPub: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

  # Your private encryption key (do not share this with anyone!)
  BoxPriv: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

  # Your public signing key
  SigPub: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

  # Your private signing key (do not share this with anyone!)
  SigPriv: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

  # Enable or disable automatic peer discovery on the same LAN using multicast
  Multicast: true

  # Regex for which interfaces multicast peer discovery should be enabled on
  LinkLocal: ""

  # Local network interface name for TUN/TAP adapter, or "auto", or "none"
  IfName: auto

  # Set local network interface to TAP mode rather than TUN mode (if supported
  # by your platform, option will be ignored if not)
  IfTAPMode: false

  # Maximux Transmission Unit (MTU) size for your local network interface
  IfMTU: 65535

  # Extended options for interoperability with other networks
  Net:
  { 
    # Experimental options for configuring peerings over Tor 
    Tor:
    {
      OnionKeyfile: ""
      ControlAddr: ""
      Enabled: false
    }
 
    # Experimental options for configuring peerings over I2P 
    I2P:
    {
      Keyfile: ""
      Addr: ""
      Enabled: false
    }
  }
}
```

Note that any field not specified in the configuration will use its default value, which may be random from run to run in the case of ports or keys.

## Configuration Options

- `Listen`
    - A string, in the form of `"ip:port"`, on which to listen for connections from peers (both TCP and UDP).
    - Note that, due to Go language design choices, `[::]` listens on IPv4 and IPv6 on most platforms, while an empty IP or `0.0.0.0` listens only to IPv4.
    - The default is to listen on all addresses (`[::]`) with a random port.
- `AdminListen`
    - Port to listen on for the (TCP) admin socket.
    - The default is to listen on the loopback interface (`localhost:9001`) which ensures that only local connections to the admin socket are allowed.
    - Note that if you change the listen address to a non-loopback address, this will allow other hosts on the network to manage the Yggdrasil process. This probably isn't desirable. 
- `Peers`
    - A list of strings in the form `["peerAddress:peerPort", "peerAddress:peerPort", ...]` of peers to connect to.
    - Peer hostnames can be specified either using IPv4 addresses, IPv6 addresses or DNS names.
    - Each entry may optionally begin with `tcp://`, `udp://` or `socks://proxyAddress:proxyPort/` to manually force a connection over a specific protocol.
    - If unspecified, the default is to connect over TCP.
- `AllowedBoxPubs`
    - A list of strings in the form `["boxpub", "boxpub", ...]`, where `boxpub` is each node's `BoxPub` key which you would like to allow connections from.
    - This option allows you to restrict which other nodes can connect to your Yggdrasil node as a peer. It applies to incoming TCP connections and both incoming and outgoing UDP connections.
    - If the list is left empty, or the option is not specified, then Yggdrasil will automatically accept connections from any other node.
    - Note that multicast link-local peerings (see below) will always override this option if enabled.
- `BoxPub`
    - A hexadecimal string representing the node's public Curve25519 key.
    - A node's ID in the DHT is a (sha-512) hash of this public key.
    - A node's IP address is derived from the ID.
- `BoxPriv`
    - A hexadecimal string representing the node's private Curve25519 key.
    - This is a private key, don't share it.
- `SigPub`
    - A hexadecimal string representing a node's public Ed25519 key.
    - Used primarily for signatures in the greedy routing scheme.
- `SigPriv`
    - A hexadecimal string representing the node's private Ed25519 key.
    - This is a private key, don't share it.
- `Multicast`
    - If true (default), link-local multicast peering is enabled. This will attempt to discover other Yggdrasil nodes running on the same network and peer with them automatically, effectively creating a "zero-config" peering setup.
    - Link-local multicast listens for UDP announcement messages on `[ff02::114]:9001`.
    - Upon discovery, link-local multicast peers are added as TCP peers.
- `LinkLocal`
    - A regex string.
    - Link-local multicast peering only connects over interfaces matching this regex.
    - The default value (an empty string) matches all interfaces.
    - This is useful if you want to prevent accidental peering over a layer 2 VPN running on top of Yggdrasil.
- `IfName`
    - The name of the `tun` or `tap` network interface to create or use. Applications send packets over this interface to use the network.
    - On most platforms, an empty string or the default `"auto"` will create a new interface automatically.
    - You can also specify `"none"` as the interface name, in which case Yggdrasil will run as a router only without opening a network interface. This effectively allows Yggdrasil to carry traffic for other nodes without exposing the system to the network.
    - The behaviour of this option is different on different operating systems. Some quick notes:
        - On Linux, any suitable interface name can be specified.
        - On FreeBSD, OpenBSD and NetBSD, a full path to the TAP interface should be specified, i.e. `"/dev/tap0"`. 
        - On macOS, a utun device is automatically assigned by the operating system, therefore you cannot specify a name.
        - On Windows, a network adapter friendly name (like `"Local Area Connection 2"`) can be specified to choose a specific adapter. Use "Network Adapters" in Control Panel to see and/or rename adapters.
- `IfTAPMode`
    - If true, then the interface will be a `tap` device (Layer 2) instead of a `tun` (Layer 3) device.
    - Default value is platform specific, and some platforms support only `tun` or `tap` mode.
    - Note that the network only transports IPv6 packets, so frames sent to or received from a `tap` are decapsulated or encapsulated at the end points of a connection.
    - In TAP mode, Yggdrasil automatically answers Neighbor Discovery Packet (NDP) requests on behalf of Yggdrasil IPv6 addresses. 
- `IfMTU`
    - The MTU of the `tun`/`tap` interface.
    - Defaults to the maximum value supported on each platform, up to `65535` on Linux/macOS/Windows, `32767` on FreeBSD, `16384` on OpenBSD, `9000` on NetBSD, etc.
    - Yggdrasil automatically assists in Path MTU Discovery (PMTU) and will limit the MTU of a given connection between two hosts to the lower of the MTUs used by each endpoint. The operating system is made aware of these MTUs using ICMP.
    - If traffic is routed over UDP links, PMTU may be further reduced to prevent unnecessary packet fragmentation.
- `Net`
    - Additional configuration options for overlay networks (Tor, I2P).
    - Still under development.
    - Note that a `socks` connection should be sufficient to use any network which is reachable over a SOCKS proxy.

# Use Cases

## Manually Connecting to Peers

By default, only link-local auto-peering is enabled. This connects devices that are connected directly to each other at layer 2, including devices on the same LAN, directly connected by ethernet or configured to use the same ad-hoc wireless network.

As the network uses ordinary TCP and UDP, it is possible to connect over other networks, such as the Internet or WAN links, provided that the connecting node knows the address and port to connect to and that the connection is not blocked by a NAT or firewall. If the node resides behind a NAT, then port forwarding may be required in order to accept incoming connections.

By default, connections to peers are made over TCP. This tends to have lower CPU usage than connecting over UDP, which leads to higher bandwidth on CPU-constrained single-board computers (i.e. Raspberry Pi). UDP connections can be made by specifying `udp://` in the connection string. These tend to require more CPU, due to lower PMTU, but otherwise mostly works the same.

If two nodes that want to connect are both stuck behind NATs, then it should generally be possible to punch a hole through the NAT if each node specifies a `udp://` connection to the other.

As a last resort, it is possible to route through a `socks://proxyAddr:proxyPort:/` connection. This uses TCP over the specified SOCKS proxy, and can be used to tunnel out from a network with a particularly restrictive firewall, for example, using SSH tunnelling. This can also be used to connect over Tor, particularly for `.onion` hidden service addresses.

If you are unable to find nodes in the nearby area, a best effort is made to maintain a list of [Public Peers](https://github.com/yggdrasil-network/public-peers) for new users looking to join or test the network.

## Advertising a Prefix

While it is generally encouraged that nodes run the software locally, to provide end-to-end cryptographic sessions and participate in routing, this is not always practical.
Some network devices will inevitably be unable to run user code, but may still provide IPv6 connectivity.
Users may also prefer to avoid running the software on an otherwise compatible system, perhaps to provide guest access or to avoid any overhead to battery powered devices.
To that end, it is each node is assigned a `/64` prefix in parallel to their address.
A node acting as a router may advertise this prefix just as they would any other ordinary IPv6 network.

This may be best illustrated by example.
Suppose a node has generated the address: `fd00:1111:2222:3333:4444:5555:6666:7777`.
Then the node may also use addresses from the prefix: `fd80:1111:2222:3333::/64` (note the `fd00` changed to `fd80`, a separate `/9` is used for prefixes, but the rest of the first 64 bits are the same).

On Linux, something like the following should be sufficient to advertise a prefix and a route to `fd00::/8` using radvd to a network attached to the `eth0` interface:

1. Enable IPv6 forwarding (e.g. `sysctl -w net.ipv6.conf.all.forwarding=1` or add it to sysctl.conf).

2. `ip addr add fd80:1111:2222:3333::1/64 dev eth0` or similar, to assign an address for the router to use in that prefix, where the LAN is reachable through `eth0`.

3. Install/run `radvd` with something like the following in `/etc/radvd.conf`:
```
interface eth0
{
        AdvSendAdvert on;
        prefix fd80:1111:2222:3333::/64 {
            AdvOnLink on;
            AdvAutonomous on;
        };
        route fd00::/8 {};
};
```

Note that a `/64` prefix has fewer bits of address space available to check against the node's ID, which in turn means hash collisions are more likely.
As such, it is unwise to rely on addresses as a form of identify verification for the `fd80::/9` address range.

## Generating Stronger Addresses (and Prefixes)

While 128 bits is long enough to make collisions technically impractical, if not outright impossible, it's not unreasonable to think that 64 bits may be attackable at some point if not now.
Without going too far into the details, addresses are a truncated hash of a node's public key, with leading `1` bits accumulated and suppressed (along with the inevitable first `0` bit).
Thanks to the accumulator, it is possible to brute force generate keys which include more bits of the node's ID in the node's IPv6 address, thereby making collisions more difficult.
This can partially mitigate the fact that IPv6 addresses are only 128 bits long, and, more importantly, that prefixes are a mere 64 bits, 16 bits of which are sacrificed to the `fd00::/8` prefix and 1-byte accumulator in either case.

In short, if you plan to advertise a prefix, or if you want your address to be exceptionally difficult to collide with, then it is strongly advised that you burn some CPU cycles generating a harder-to-collide set of keys, using the following tool:

```
GOPATH=$PWD go run misc/genkeys.go
```

This continually generates new keys and prints them out each time a new best set of keys is discovered.
These keys must then be manually added to the configuration file.

