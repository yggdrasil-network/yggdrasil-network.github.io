---
tags: dontlink
sitemap: true
---

# Admin API

The admin socket provides an interface to query and configure Yggdrasil during runtime. By default, Yggdrasil listens for admin connections on `unix:///var/run/yggdrasil.sock` for Linux, OpenBSD, FreeBSD and MacOS, and on `localhost:9001` for Windows and other operating systems.

## Control Utility

The `yggdrasilctl` utility provides a human-friendly CLI interface to the Yggdrasil admin socket. It can connect to both local and remote Yggdrasil instances, and accepts the same verbs as below. Every field is specified in the `field=value` format.

Examples include:
```
yggdrasilctl getDHT
yggdrasilctl getPeers
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
yggdrasilctl -json getPeers
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
- `key` (`string`) contains the `PublicKey` of the remote node
- `port` (`int`)
- `rest` (`int`)

#### `getPeers`

Expects no additional request fields.

Returns one or more records containing information about active peer sessions. The first record typically refers to the current node.

For each IPv6 address:
- `key` (`string`) contains the `PublicKey` of the remote node
- `port` (`uint8`) contains the local switch port number for that peer
- `coords` (`[]int`) contains the coordinates of the node on the spanning tree
- `remote` (`string`) contains the peering URI of the remote peer

#### `getSelf`

Expects no additional request fields.

Returns exactly one record containing information about the current Yggdrasil node.

For the current IPv6 address:
- `key` (`string`) contains the `EncryptionPublicKey` of the current node
- `build_name` (`string`) contains the build name, if available (e.g. `yggdrasil`, `yggdrasil-develop`)
- `build_version` (`string`) contains the build version, if available (e.g. `0.3.0`, `0.2.7-0091`)
- `coords` (`[]int`) contains the coordinates of the node on the spanning tree
- `subnet` (`string`) contains the routed IPv6 subnet for this host

#### `getSessions`

Expects no additional request fields.

Returns zero or more records containing information about open sessions between the current Yggdrasil node and other nodes. Open sessions indicate that traffic has been exchanged with the remote node recently.

For each IPv6 address:
- `key` (`string`) contains the `EncryptionPublicKey` of the remote node

#### `getTunTap`

Expects no additional request fields.

Returns exactly one record containing information about the current node's TUN/TAP adapter.

For each adapter:
- `mtu` (`uint8`) contains the MTU of the local TUN/TAP adapter

#### `getMulticastInterfaces`

Expects no additional request fields.

Returns zero or more strings containing the enabled multicast peering interfaces.

If zero strings are returned then it is implied that multicast peering is not allowed on any interface.

#### `getNodeInfo`

Expects:
- `key=` `string`, hex-encoded public key of the remote node to ping, in the same format as e.g. verbose output from a `getDHT` response

Asks a remote node to respond with their nodeinfo.

Returns a `nodeinfo` section with the nodeinfo. This can be in any format, containing any keys.
