---
tags: dontlink
sitemap: true
---

# Installing on OpenWrt

`yggdrasil` v0.4+ packages are available in OpenWrt since 17.01!

Since v0.5.1, the module has changed significantly to support Yggdrasil as a native interface.

To configure Yggdrasil with the LuCI web interface, install the package `luci-proto-yggdrasil`.

## Configuration

Use the LuCI web interface to configure Yggdrasil - in "Interfaces", add a new interface and set the protocol to Yggdrasil. Then, follow the configuration steps in the settings menu - in particular, the main tab and "Peers" which allows to edit peers and connections.

Alternatively, you can configure Yggdrasil with just a command line, by editing /etc/config/network.

An example of an Yggdrasil configuration, with interface 'ygg0', is presented below:
```
config interface 'ygg0'  # Should be appended to the existing configuration
		option proto 'yggdrasil'
		list listen_address 'tls://127.0.0.1:9090'  # (Repeatable) List of ports to listen for peers on
		option private_key 'YOUR_PRIVATE_KEY'  # Private key. Leave empty to generate an anonymous one every run, or set to "auto" to auto-generate
		option node_info_privacy '1'  # Enable node info privacy. Set to 0 or omit to disable.
		option node_info '{"a": "b"}'  # Custom node info fields, formatted as JSON
		list allowed_public_key 'ALLOWED_PUBLIC_KEY'  # (Repeatable) Public key whitelist. Omit to allow all.

config yggdrasil_ygg0_interface  # (Repeatable) Multicast interfaces. ygg0 is replaced by the network name.
		list interface 'br-lan'  # (Repeatable) Interfaces to listen on.
		list interface 'phy0-ibss0'
		option beacon '1'  # Send a discovery beacon
		option listen '1'  # Listen for discovery beacons
		option port '9090'  # Auto-discovery ports
		option password 'INTERFACE_PASSWORD'  # Password. Needs to be the same across peers to connect.

config yggdrasil_ygg0_peer  # (Repeatable) Peers.
		option address 'tls://1.2.3.4:5678'  # Peer address.
		option interface 'eth0'  # Interfacce to restrict peer to. Omit to attempt on all.

config yggdrasil_ygg0_peer
		option address 'tls://9.10.11.12:1314'
```

## OpenWrt Yggdrasil on Matrix

Please stop by `#yggdrasil-openwrt:matrix.org` to join the community development channel.


## NodeInfo enabled by default

The Yggdrasil daemon will provide some basic NodeInfo information to the mesh by default.  
This can be disabled by configuring the section in `/etc/config/network` or LuCI admin.  

This means the following example can be queried by others in the network:

```
"NodeInfo": {
  "kernel": "4.14.123",
  "hostname": "OpenWrt",
  "model": "Globalscale Marvell ESPRESSOBin Board",
  "board_name": "globalscale,espressobin"
}
```
