---
tags: dontlink
sitemap: true
---

# Admin API

The admin socket provides an interface to query and configure Yggdrasil during runtime. By default, Yggdrasil listens for admin connections on `localhost:9001`.

## Control Utility

The `yggdrasilctl` utility provides a human-friendly CLI interface to the Yggdrasil admin socket. It can connect to both local and remote Yggdrasil instances, and accepts the same verbs as below. Every field is specified in the `field=value` format.

Examples include:
```
yggdrasilctl getDHT
yggdrasilctl addPeer uri=tcp://a.b.c.d:e
yggdrasilctl getPeers
yggdrasilctl removePeer port=4
yggdrasilctl setTunTap name=auto mtu=65535 tap_mode=false
````

To get a list of supported commands:
```
yggdrasilctl list
```

To perform an action on a remote Yggdrasil node, specify the `-endpoint` parameter:
```
yggdrasilctl -endpoint=tcp://10.0.0.1:9001 getPeers
yggdrasilctl -endpoint=unix:///var/run/yggdrasil.sock getDHT
```

To get the JSON response body instead of a "friendly" output, specify the `-json` parameter:
```
yggdrasilctl -json getSwitchPeers
```

To draw a map of a node's view of the network, install Graphviz onto your system and use `dot`:
```
yggdrasilctl dot | dot -Tpng -o map.png
```

## Admin Socket

The Yggdrasil admin socket uses JSON for request and response formats.

A request must be:
- A complete JSON stanza
- Followed by a newline `\n` character

Once a valid request is received, the response stanza is returned.

### Request

The structure of a typical request is as below:
```
{
  "request": "XXX",
  "foo": "bar",
  "baz": "qux"
}
```

A request:
- *Must* have a `"request"` (`string`) field - a value containing the verb of the request
- *Can* have a `"keepalive"` (`bool`) field - a `true` or `false` value stating whether the connection should be kept alive for further requests (if not specified, Yggdrasil will close the admin connection after returning a response)
- *Must* have any other required fields for the request
- *Can* have any other optional fields for the request

### Response

A typical response is structured like this:
```
{
  "request":
  {
    "request": "XXX",
    "foo": "bar",
    "baz": "qux"
  },

  "response":
  {
  },

  "status": "success"
}
```

A response:
- *Always* has a `"request"` (`string`) field, which contains the body of the original request
- *Always* has a `"status"` (`string`) field, which is either `"success"` or `"error"`
- *Optionally* has a `"response"` section, which contains the response data from the request
- *Optionally* has an `"error"` (`string`) field, which contains error text

### Request Types

The `"request"` field contains a verb that describes which request to perform.

#### `getDHT`

Expects no additional request fields.

Returns known nodes in the DHT.
- `box_pub_key` (`string`) contains the `EncryptionPublicKey` of the remote node
- `coords` (`string`) contains the coordinates of the node on the spanning tree
- `last_seen` (`uint32`) contains the number of seconds since the DHT record was last updated

#### `getPeers`

Expects no additional request fields.

Returns one or more records containing information about active peer sessions. The first record typically refers to the current node.

For each IPv6 address:
- `box_pub_key` (`string`) contains the `EncryptionPublicKey` of the remote node
- `bytes_sent` (`uint64`) contains the number of bytes sent to that peer
- `bytes_recvd` (`uint64`) contains the number of bytes received from that peer
- `endpoint` (`string`) contains the connected IPv4/IPv6 address and port of the peering
- `port` (`uint8`) contains the local switch port number for that peer
- `uptime` (`float64`) contains the number of seconds since the peer connection was established

#### `addPeer`

Expects:
- `uri` (`string`) for the peer to added, in standard URI format as used in the configuration file, i.e. `tcp://a.b.c.d:e`

Adds a new peer.

Returns:
- Zero or more successful `string` peer URIs in the `"added"` section
- Zero or more failed `string` peer URIs in the `"not_added"` section

#### `removePeer`

Expects:
- `port` (`uint8`) for the port of the peer to remove, this can be looked up using `getPeers` or `getSwitchPorts`

Removes an existing peer.

Returns:
- Zero or more successful `string` ports in the `"removed"` section
- Zero or more failed `string` ports in the `"not_removed"` section

#### `getSwitchPeers`

Expects no additional request fields.

Returns zero or more records containing information about switch peers.

For each port number:
- `box_pub_key` (`string`) contains the `EncryptionPublicKey` of the remote node
- `bytes_sent` (`uint64`) contains the number of bytes sent to the remote node
- `bytes_recvd` (`uint64`) contains the number of bytes received from the remote node
- `coords` (`string`) contains the coordinates of the node on the spanning tree
- `endpoint` (`string`) contains the connected IPv4/IPv6 address and port of the peering
- `ip` (`string`) contains the IPv6 address of the remote node

#### `getSelf`

Expects no additional request fields.

Returns exactly one record containing information about the current Yggdrasil node.

For the current IPv6 address:
- `box_pub_key` (`string`) contains the `EncryptionPublicKey` of the current node
- `build_name` (`string`) contains the build name, if available (e.g. `yggdrasil`, `yggdrasil-develop`)
- `build_version` (`string`) contains the build version, if available (e.g. `0.3.0`, `0.2.7-0091`)
- `coords` (`string`) contains the coordinates of the node on the spanning tree
- `subnet` (`string`) contains the routed IPv6 subnet for this host

#### `getSessions`

Expects no additional request fields.

Returns zero or more records containing information about open sessions between the current Yggdrasil node and other nodes. Open sessions indicate that traffic has been exchanged with the remote node recently.

For each IPv6 address:
- `box_pub_key` (`string`) contains the `EncryptionPublicKey` of the remote node
- `bytes_sent` (`uint64`) contains the number of bytes sent across that session
- `bytes_recvd` (`uint64`) contains the number of bytes received across that session
- `coords` (`string`) contains the coordinates of the remote node on the spanning tree
- `mtu` (`uint8`) contains the negotiated session MTU between the local end and the remote end of the session
- `was_mtu_fixed` (`bool`) shows whether or not the MTU has been adjusted since the session was opened to compensate for read errors

#### `getTunTap`

Expects no additional request fields.

Returns exactly one record containing information about the current node's TUN/TAP adapter.

For each adapter:
- `tap_mode` (`bool`) shows whether or not the interface is in TAP mode (if `false` then TUN mode is implied)
- `mtu` (`uint8`) contains the MTU of the local TUN/TAP adapter

#### `getAllowedEncryptionPublicKeys`

Expects no additional request fields.

Returns zero or more strings containing the allowed box public keys.

If zero strings are returned then it is implied that all connections are permitted.

#### `addAllowedEncryptionPublicKey`

Expects:
- `box_pub_key=` `string` for the public key to add

Adds a new allowed box pub key.

Returns:
- Zero or more successful `string` box pub keys in the `"added"` section
- Zero or more failed `string` box pub keys in the `"not_added"` section

#### `removeAllowedEncryptionPublicKey`

Expects:
- `box_pub_key=` `string` for the public key to remove

Removes an existing box pub key.

Returns:
- Zero or more successful `string` box pub keys in the `"removed"` section
- Zero or more failed `string` box pub keys in the `"not_removed"` section

#### `getMulticastInterfaces`

Expects no additional request fields.

Returns zero or more strings containing the enabled multicast peering interfaces.

If zero strings are returned then it is implied that multicast peering is not allowed on any interface.

#### `getRoutes`

Expects no additional request fields.

Returns zero or more records where the subnet (`string`) is mapped to the public key (`string`).

#### `addRoute`

Expects:
- `subnet=` `string` for the subnet to route
- `box_pub_key=` `string` for the public key to route to

Adds a new crypto-key route.

Returns:
- Zero or more successful `string` routes in the `"added"` section
- Zero or more failed `string` routes in the `"not_added"` section

#### `removeRoute`

Expects:
- `subnet=` `string` for the subnet to remove the route for
- `box_pub_key=` `string` for the public key that is routed to

Removes an existing crypto-key route.

Returns:
- Zero or more successful `string` routes in the `"removed"` section
- Zero or more failed `string` routes in the `"not_removed"` section

#### `getSourceSubnets`

Expects no additional request fields.

Returns zero or more records for allowed crypto-key routing source subnets (`string`).

#### `addSourceSubnet`

Expects:
- `subnet=` `string` for the subnet to allow traffic from

Adds a new crypto-key source subnet.

Returns:
- Zero or more successful `string` source subnets in the `"added"` section
- Zero or more failed `string` source subnets in the `"not_added"` section

#### `removeSourceSubnet`

Expects:
- `subnet=` `string` for the subnet to remove

Removes an existing crypto-key source subnet.

Returns:
- Zero or more successful `string` source subnets in the `"removed"` section
- Zero or more failed `string` source subnets in the `"not_removed"` section

#### `dhtPing`

Expects:
- `box_pub_key=` `string`, hex-encoded public key of the remote node to ping, in the same format as e.g. verbose output from a `getDHT` response
- `coords=` `string`, location of the remote node in the network, in the same format as e.g. a `getDHT` response
- `target=` `string`, hex-encoded 512-bit NodeID to ask about, affects what the response from the remote node will be, optional

Asks a remote node to respond with information from the DHT.

Returns a `nodes` section with information about each node included in the DHT lookup response, indexed by IPv6.

For each IPv6 address, this includes:
- `box_pub_key` (`string`) contains the `EncryptionPublicKey` of the remote node
- `coords` (`string`) contains the coordinates of the node on the spanning tree

#### `getNodeInfo`

Expects:
- `box_pub_key=` `string`, hex-encoded public key of the remote node to ping, in the same format as e.g. verbose output from a `getDHT` response
- `coords=` `string`, location of the remote node in the network, in the same format as e.g. a `getDHT` response

Asks a remote node to respond with their nodeinfo.

Returns a `nodeinfo` section with the nodeinfo. This can be in any format, containing any keys.
