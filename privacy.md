---
sitemap: true
---

# Privacy

In order to release Yggdrasil on certain platforms, we are obliged to release a Privacy Policy which discusses information usage within Yggdrasil. This document was last updated on the 9th January 2019.

### Overview

Yggdrasil is a decentralised application with no central point of registration or communication. You **do not** need to register an account to use Yggdrasil, nor do you need to supply any personal information to the developers.

Yggdrasil deploys **end-to-end encryption** for all traffic crossing the network. This ensures that your traffic will not be readable or modifiable by third parties. The integrity of this encryption is guaranteed only if you take suitable precautions to protect your private keys. Do not share them with anyone.

### Network Identity

Your identity on the Yggdrasil Network is derived from your encryption and signing keypairs, which are stored locally on your device. Your public encryption key is used to derive your network IPv6 address, which will be used whenever you send or receive traffic across the Yggdrasil Network.

You can discard your network IPv6 address/network identity and assume a new one at any time by discarding your keypairs and generating a new configuration with fresh keys.

### Node Information

Yggdrasil supports node information configuration through the `NodeInfo` configuration section. By default, the `NodeInfo` may also contain information about the version of Yggdrasil being run, on which operating system and architecture. You can choose not to publish this information using the `NodeInfoPrivacy` option in your configuration. Any other `NodeInfo` is entirely optional. You are not obliged to share any information. 

On iOS, this is shown under "Node Info" in the Settings page. Any information entered into this section is published to the network and may be stored by network crawlers or by any other network user. You should therefore not publish any sensitive information in your `NodeInfo`. 

### Peerings

When you establish a peering, your network IPv6 address and public key are made visible to any peers to which you are directly connected. If peering over the Internet or another network, the remote peer may also be able to see your real IP address. If you are concerned about the storage or collection of information from a public node, you should contact the operator of that node. 

### Yggdrasil for iOS

With regards to our iOS application, information collected through TestFlight or the App Store pertaining to usage demographics and/or crash reports may be made available to the developers of the iOS application by Apple, as outlined in [Apple's own Privacy Policy](https://www.apple.com/uk/legal/privacy/en-ww/). 

No other information is collected. The Yggdrasil application (and the accompanying VPN network extension) itself does not collect any information whatsoever.
