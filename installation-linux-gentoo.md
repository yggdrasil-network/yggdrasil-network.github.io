---
tags: dontlink
sitemap: true
---

# Installing on Gentoo Linux

Yggdrasil is hosted in the Gentoo GURU repository as the
[net-p2p/yggdrasil-go](https://gitweb.gentoo.org/repo/proj/guru.git/tree/net-p2p/yggdrasil-go)
package.

## Package install from the GURU repository

The GURU repository has to be enabled using `eselect repository`.

```
sudo eselect repository enable guru
```

And then synchronized with `emerge`:

```
sudo emerge --sync
```

Now, the `net-p2p/yggdrasil-go` package is masked and marked as unstable using
the `~amd64` keyword. So we have to unmask it by accepting the keyword, there
are two alternatives.

1. If `/etc/portage/package.accept_keywords` is a directory:

```
echo "net-p2p/yggdrasil-go ~amd64" | sudo tee /etc/portage/package.accept_keywords/yggdrasil-go
```

2. If `/etc/portage/package.accept_keywords` is a file:

```
echo "net-p2p/yggdrasil-go ~amd64" | sudo tee -a /etc/portage/package.accept_keywords
```

Now `net-p2p/yggdrasil-go` can be merged and installed with:

```
sudo emerge --ask net-p2p/yggdrasil-go
```

And you're good to go!

## Making configuration changes

Modify the `/etc/yggdrasil.conf` file and then either reload the config:

1. OpenRC
```
rc-service yggdrasil reload
```

2. systemd
```
systemctl reload yggdrasil
```

... or restart the Yggdrasil daemon altogether:

1. OpenRC
```
rc-service yggdrasil restart
```

2. systemd
```
systemctl restart yggdrasil
```

## After installation

Read the [Linux platform page](platform-linux.md) page for further
information about Linux platform support.
