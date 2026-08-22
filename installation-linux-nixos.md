---
tags: dontlink
sitemap: true
---

# Installing on NixOS

Yggdrasil is supported on NixOS. Is available in the stable channels unstable channels of nixpkgs with a [NixOS module](https://github.com/NixOS/nixpkgs/blob/master/nixos/modules/services/networking/yggdrasil.nix) for easy configuration.

## Preparation

Before configuring Yggdrasil, you will need to get a `PrivateKey` first. To get it, simply run `nix-shell -p yggdrasil --run "yggdrasil -genconf -json | yggdrasil -useconf -exportkey"`, and copy the output to your clipboard, it should start with `-----BEGIN PRIVATE KEY-----`.

Now proceed to the configuration.

## NixOS Configuration

```nix
{
  services.yggdrasil = {
    enable = true;
    settings = {
      # Warning: Do not put private keys directly in the Nix store as they would
      # be world-readable!
      # There is many other ways you can set `privateKeyPath`,
      # this way is not recommended but is the simplest one.
      # If you like to publish your NixOS configurations to a git server,
      # you should be using sops-nix or Agenix instead.
      privateKeyPath = "${pkgs.writeText "yggdrasil-private-key" ''
        -----BEGIN PRIVATE KEY-----
        <PASTE HERE your ~65 char length Private Key generated with the command above>
        -----END PRIVATE KEY-----
      ''}";
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
