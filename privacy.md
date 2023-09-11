---
sitemap: true
dontlink: true
---

# Privacy

In order to release Yggdrasil on certain platforms, we are obliged to release a Privacy Policy which discusses information usage within Yggdrasil. This document was last updated on the 14th December 2021.

### Overview

Yggdrasil is a decentralised application with no central point of registration or communication. You **do not** need to register an account to use Yggdrasil, nor do you need to supply any personal information to the application or to the developers.

Yggdrasil deploys **end-to-end encryption** for all traffic crossing the network. This ensures that your traffic will not be readable or modifiable by third parties, including by any other node that is carrying traffic on your behalf. The integrity of this encryption is guaranteed only if you take suitable precautions to protect your private key, which is typically stored in your Yggdrasil configuration file. You must not share it with anyone.

### Network Identity

Your identity on the Yggdrasil Network is derived from your cryptographic keypair, which is generated and stored locally on your device. The public key is used to derive your network IPv6 address, which will be used whenever you send or receive any IP traffic across the Yggdrasil Network.

You can discard your network IPv6 address/network identity and assume a new one at any time by discarding your keypair and generating a new one.

### Node Information

Yggdrasil supports publishing node information by means of the `NodeInfo` section in the configuration file. Node information can be queried by any remote node on the network, even if that remote node is not otherwise exchanging IP traffic with your node.

By default, the `NodeInfo` may also contain information about the version of Yggdrasil being run as well as the operating system and processor architecture. You can choose not to publish this information by setting the `NodeInfoPrivacy` option in your configuration file. Otherwise, `NodeInfo` is entirely optional and you can leave it empty. On iOS, this is shown under "Node Info" in the Settings page.

You are not obliged to share anything via node information. Any information entered into this section is publicly accessible and may be stored by network crawlers or by any other network user. You should therefore not publish any sensitive information in your `NodeInfo`.

### Peerings

When you establish a peering, your node's IPv6 address and public key are made visible to any peers to which you are directly connected. If peering over the Internet or another network, the remote peer may also be able to see your real IP address, and these may be logged by the remote node.

If you are concerned about the storage or collection of logging information on a public node, you should contact the operator of that node. If you are in any way concerned about the safety of a public node, or whether a given public node is administered in good faith, you should not attempt to connect to it.

### Yggdrasil for iOS

With regards to our iOS application when distributed via TestFlight or the App Store specifically, information collected through TestFlight or the App Store pertaining to usage demographics and/or crash analytics may be made available to the developers of the iOS application by Apple, as outlined in [Apple's own Privacy Policy](https://www.apple.com/uk/legal/privacy/en-ww/).

No other information is collected. The Yggdrasil application itself, as well as the accompanying VPN network extension, will not collect any personal or analytic information whatsoever.
