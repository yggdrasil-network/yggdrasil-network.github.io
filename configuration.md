# Configuration

Yggdrasil can be run with a dynamically generated configuration, using sane-ish default settings, with `yggdrasil --autoconf`.

There are cases where a static configuration simplifies some setups.
Configuration is provided to `stdin` in json format, such as from a file using `yggdrasil --useconf < path/to/configuration.json`.
A new configuration file may be generated with `yggdrasil --genconf > path/to/configuration.json`, which looks something like:

```json
{
  "Listen": "[::]:51743",
  "AdminListen": "[::1]:9001",
  "Peers": [],
  "BoxPub": "some hex string",
  "BoxPriv": "some hex string",
  "SigPub": "some hex string",
  "SigPriv": "some longer hex string",
  "Multicast": true,
  "LinkLocal": "",
  "IfName": "auto",
  "IfTAPMode": false,
  "IfMTU": 65535,
  "Net": {
    "Tor": {
      "OnionKeyfile": "",
      "ControlAddr": "",
      "Enabled": false
    },
    "I2P": {
      "Keyfile": "",
      "Addr": "",
      "Enabled": false
    }
  }
}
```

Note that any field not specified in the configuration will use its default value, which may be random from run to run in the case of ports or keys.

## Configuration Options

- `Listen`
    - A string, in the form of `"ip:port"`, on which to listen (both TCP and UDP).
    - Note that, due to Go language design choices, `[::]` listens on IPv4 and IPv6 on most platforms, while an empty IP or `0.0.0.0` listens only to IPv4.
    - The default is to listen on all addresses (`[::]`) with a random port.
- `AdminListen`
    - Port to listen on for the (TCP) admin socket.
- `Peers`
    - A list of strings in the form `["peerAddress:peerPort"]` of peers to connect to.
    - Each entry may optionally begin with `tcp://`, `udp://` or `socks://proxyAddress:proxyPort/` to manually force a connection over a specific protocol.
    - If unspecified, the default is to connect over TCP.
- `BoxPub`
    - A hexadecimal string representing the node's public Curve25519 key.
    - A node's ID in the DHT is a (sha-512) hash of this public key.
    - A node's IP is derived from the ID.
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
    - If true (default), link-local multicast peering is enabled.
    - Link-local multicast listens for UDP announcement messages on `[ff02::114]:9001`.
    - Link-local multicast peers are added as TCP peers.
- `LinkLocal`
    - A regex string.
    - Link-local multicast peering only connects over interfaces matching this regex.
    - The default value (an empty string) matches all interfaces.
    - This is useful if you want to prevent accidental peering over a layer 2 VPN running on top of yggdrasil.
- `IfName`
    - The name of the `tun` or `tap` network interface to create or use.
    - Applications send packets over this interface to use the network.
    - On most platforms, an empty string or the default `"auto"` will create a new interface automatically.
- `IfTAPMode`
    - If true, then the interface will be a `tap` device instead of a `tun`.
    - Default value is platform specific, and some platforms support only `tun` or `tap` mode.
    - Note that the network only transports IPv6 packets, so frames sent to or received from a `tap` are decapsulated or encapsulated at the end points of a connection.
- `IfMTU`
    - The MTU of the `tun`/`tap` interface.
    - Defaults to the maximum value supported on each platform, up to `65535`.
    - PMTU discovery will limit the MTU to the lower of the MTUs used by the end points of a connection.
    - If traffic is routed over UDP links, PMTU may be further reduced to prevent fragmentation.
- `Net`
    - Additional configuration options for overlay networks (Tor, I2P).
    - Still under development.
    - Note that a `socks` connection should be sufficient to use any network which is reachable over a socks proxy.

# Use Cases

## Manually Connecting to Peers

By default, only link-local auto-peering is enabled.
This connects devices that are connected directly to eachother at layer 2, including devices on the same LAN, directly connected by ethernet, or configured to use the same ad-hoc wifi network.

As the network uses ordinary TCP and UDP, it is possible to connect over other networks, provided that the connecting node knows the address and port to connect to, and that the connection is not blocked by a NAT or firewall.

By default, connections to peers are made over TCP.
This tends to have lower CPU usage than connecting over UDP, which leads to higher bandwidth on CPU-constrained single-board computers (Raspberry Pi and its ilk).

UDP connections can be made by specifying `udp://` in the connection string.
These tend to require more CPU, due to lower PMTU, but otherwise mostly works the same.
If two nodes that want to connect are both stuck behind NATs, then it should generally be possible to hole punch if each node specifies a `udp://` connection to the other.

As a transport of last resort, it is possible to route through a `socks://proxyAddr:proxyPort:/` connection.
This uses TCP over the specified proxy, and can be used to e.g. ssh tunnel out from a network with a particularly restrictive firewall.
This can also be used to connect over Tor, particularly for `.onion` hidden service addresses.

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

Something like the following should be sufficient to advertise a prefix and a route to `fd00::/8` with radvd to a network attached to the `eth0` interface:

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

