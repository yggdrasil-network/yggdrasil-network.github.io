---
tags: dontlink
---

# NodeInfo

Yggdrasil supports advertising a small amount of information publicly to the network. The entire stanza should not exceed 16KB. You should not publish any information that you do not want to be collected by network crawlers or deem to be private. NodeInfo can take any format, although this document outlines some common types for interoperability - please stick to these formats where possible.

If your configuration file is in HJSON format (as is standard), then you should specify your NodeInfo in HJSON format and it will be converted to JSON automatically by Yggdrasil when requested. If your configuration file is in JSON format, then you should also specify your NodeInfo in JSON format.

The below examples are all in HJSON format.

## Well-known types

#### Node build tags

These options are automatically populated into NodeInfo therefore you do not need to configure them. These can be unshared by setting the `NodeInfoPrivacy` configuration setting to `true` or by configuring the below values to `null`.

```
NodeInfo:
{
  buildname: yggdrasil
  buildversion: 0.3.3
  buildplatform: darwin
  buildarch: amd64
}
```

#### Node name

A friendly name for the node, which may be displayed on the maps or in GUI clients. This is often specified in fully-qualified domain name (FQDN) format, although this is not essential and free-flowing text can be used instead.

```
NodeInfo:
{
  name: some-name.net
}
```

#### Public peering details

If you wish to advertise your node as a public node, then you can provide information about how other users can peer with it over other networks.

The first level of the `"public"` key should contain the network class, and then each network class should be a list of connection strings in Yggdrasil configuration format.

```
NodeInfo:
{
  public:
  {
    internet: [
      tcp://a.b.c.d:e
      tcp://[a:b:c::d]:e
    ]
    tor: [
      socks://localhost:a/b.c.d.e:f
    ]
  }
}
```

Currently known classes are `"internet"`, `"tor"`, `"i2p"` and `"cjdns"`.
