---
tags: dontlink
sitemap: true
---

# Installing on NixOS

Yggdrasil is supported on NixOS. Is available in the stable channels unstable channels of nixpkgs with a [NixOS module](https://github.com/NixOS/nixpkgs/blob/master/nixos/modules/services/networking/yggdrasil.nix) for easy configuration.

## Preparation

Before configuring Yggdrasil, you will need to get a `PrivateKey` first. To get it, simply run `nix-shell -p yggdrasil --run "yggdrasil -genconf -json"`, and copy the `PrivateKey` generated for you.

Now proceed to the configuration.

## NixOS Configuration

```nix
{
  services.yggdrasil = {
    enable = true;
    settings = {
      PrivateKey = "<PrivateKey_generated_in_the_preparation";
      Peers = [
        # Example peers list, modify it according to your needs
        "tcp://1.1.1.1:1234?password=1234"
        "tls://1.1.1.1:1235"
        "quic://1.1.1.1:1234"
        "ws://1.1.1.1:444"
      ];
      Listen = [
        # Example listen list, modify it according to your needs
        "tcp://0.0.0.0:1234"
        "tls://0.0.0.0:1235"
        "quic://0.0.0.0:1234"
      ];
      InterfacePeers = { };
      MulticastInterfaces = [
        {
          Regex = ".*";
          Beacon = true;
          Listen = true;
          Password = "";
        }
      ];
      AllowedPublicKeys = [ ];
      IfName = "auto";
      IfMTU = 65535;
      NodeInfoPrivacy = false;
      NodeInfo = null;
    };
  };
}
```

## After configuration

Now rebuild your system using `nixos-rebuild switch` or
`nixos-rebuild switch --flake .` if you are using Nix Flakes.

## After installation

Read the [Linux platform page](configuration.md) page and the
[services.yggdrasil.settings](https://search.nixos.org/options?channel=unstable&show=services.yggdrasil.settings&query=services.yggdrasil.settings) NixOS option
for further information about Linux platform support.