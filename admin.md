---
tags: dontlink
---

# Admin Interface

The admin socket provides an interface to query and configure Yggdrasil during runtime. By default, Yggdrasil listens for admin connections on `localhost:9001`.

## Admin Socket

The Yggdrasil admin socket uses JSON for request and response formats.

The structure of a typical request is as below:
```
{
  "request": "XXX",
  "foo": "bar",
  "baz": "qux"
}
```
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
- *Always* has a `request` field, which contains the body of the original request
- *Always* has a `status` field, which is either `"success"` or `"error"`
- *Optionally* has a `response` field, which contains the response data from the request
- *Optionally* has an `"error"` field, which contains error text

### Request

The `"request"` field contains a verb that describes which request to perform. 

#### `getDHT`

Returns known nodes in the DHT.
- `last_seen` contains the number of seconds since the DHT record was last updated
- `bucket` contains the ID of the bucket that the DHT record is stored in
- `coords` contains the coordinates of the node on the spanning tree
- `peer_only` shows if the node is a peer

#### `getPeers`

Returns information about active peer sessions.
- `bytes_sent` contains the number of bytes sent to that peer
- `bytes_recvd` contains the number of bytes received from that peer
- `port` contains the local switch port number for that peer 
- `uptime` contains the number of seconds since the peer connection was established

#### `getSwitchPeers`

Returns information about switch peers, including 
