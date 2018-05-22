---
tags: dontlink
---

# Admin Interface

The admin socket provides an interface to query and configure Yggdrasil during runtime. By default, Yggdrasil listens for admin connections on `localhost:9001`.

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
- *Must* have a `"request"` field - a string value containing the verb of the request
- *Can* have a `"keepalive"` field - a `true` or `false` value stating whether the connection should be kept alive for further requests (if not specified, Yggdrasil will close the admin connection after returning a response)
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
- *Always* has a `"request"` field, which contains the body of the original request
- *Always* has a `"status"` field, which is either `"success"` or `"error"`
- *Optionally* has a `"response"` field, which contains the response data from the request
- *Optionally* has an `"error"` field, which contains error text

### Request Types

The `"request"` field contains a verb that describes which request to perform. 

#### `getDHT`

Expects no additional request fields.

Returns known nodes in the DHT.
- `last_seen` contains the number of seconds since the DHT record was last updated
- `bucket` contains the ID of the bucket that the DHT record is stored in
- `coords` contains the coordinates of the node on the spanning tree
- `peer_only` shows if the node is a peer

#### `getPeers`

Expects no additional request fields.

Returns information about active peer sessions, sorted by IPv6 address.
- `bytes_sent` contains the number of bytes sent to that peer
- `bytes_recvd` contains the number of bytes received from that peer
- `port` contains the local switch port number for that peer 
- `uptime` contains the number of seconds since the peer connection was established

#### `getSwitchPeers`

Expects no additional request fields.

Returns information about switch peers, sorted by port number.
- `coords` contains the coordinates of the node on the spanning tree
- `ip` contains the IPv6 address of the node 
