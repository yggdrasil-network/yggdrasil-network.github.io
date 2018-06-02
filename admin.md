# Admin API

The admin socket provides an interface to query and configure Yggdrasil during runtime. By default, Yggdrasil listens for admin connections on `localhost:9001`.

## Control Utility

The `yggdrasilctl` utility provides a human-friendly CLI interface to the Yggdrasil admin socket. It can connect to both local and remote Yggdrasil instances, and accepts the same verbs as below. Each field is specified in the `field=value` format.

Examples:
```
yggdrasilctl getDHT
yggdrasilctl addPeer uri=tcp://a.b.c.d:e
yggdrasilctl setTunTap name=auto mtu=65535 tap_mode=false
````

To perform an action on a remote Yggdrasil node, specify the `-endpoint` parameter:
```
yggdrasilctl -endpoint=10.0.0.1:9001 getPeers
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
- `last_seen` (`uint32`) contains the number of seconds since the DHT record was last updated
- `bucket` (`uint8`) contains the ID of the bucket that the DHT record is stored in
- `coords` (`string`) contains the coordinates of the node on the spanning tree
- `peer_only` (`bool`) shows if the node is a peer

#### `getPeers`

Expects no additional request fields.

Returns one or more records containing information about active peer sessions. The first record typically refers to the current node. 

For each IPv6 address:
- `bytes_sent` (`uint64`) contains the number of bytes sent to that peer
- `bytes_recvd` (`uint64`) contains the number of bytes received from that peer
- `port` (`uint8`) contains the local switch port number for that peer 
- `uptime` (`uint32`) contains the number of seconds since the peer connection was established

#### `addPeer`

Expects:
- `uri` (`string`) for the peer to added, in standard URI format as used in the configuration file, i.e. `tcp://a.b.c.d:e`

Adds a new peer.

Returns:
- Zero or more successful `string` peer URIs in the `"added"` section
- Zero or more failed `string` peer URIs in the `"not_added"` section

#### `removePeer`

Expects:
- `port` (`uint8`) for the port of the peer to remove

Removes an existing peer.

Returns:
- Zero or more successful `string` ports in the `"removed"` section
- Zero or more failed `string` ports in the `"not_removed"` section 

#### `getSwitchPeers`

Expects no additional request fields.

Returns zero or more records containing information about switch peers.

For each port number:
- `coords` (`string`) contains the coordinates of the node on the spanning tree
- `ip` (`string`) contains the IPv6 address of the node 

#### `getSelf`

Expects no additional request fields.

Returns exactly one record containing information about the current Yggdrasil node.

For the current IPv6 address:
- `coords` (`string`) contains the coordinates of the node on the spanning tree

#### `getSessions`

Expects no additional request fields.

Returns zero or more records containing information about open sessions between the current Yggdrasil node and other nodes. Open sessions indicate that traffic has been exchanged with the remote node recently.

For each IPv6 address:
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

#### `getAllowedBoxPubs`

Expects no additional request fields.

Returns zero or more strings containing the allowed box public keys.

If zero strings are returned then it is implied that all connections are permitted.

#### `addAllowedBoxPub`

Expects:
- `box_pub_key` (`string`) for the public key to add

Adds a new allowed box pub key.

Returns:
- Zero or more successful `string` box pub keys in the `"added"` section
- Zero or more failed `string` box pub keys in the `"not_added"` section

#### `removeAllowedBoxPub`

Expects:
- `box_pub_key` (`string`) for the public key to remove

Removes an existing box pub key.

Returns:
- Zero or more successful `string` box pub keys in the `"removed"` section
- Zero or more failed `string` box pub keys in the `"not_removed"` section
