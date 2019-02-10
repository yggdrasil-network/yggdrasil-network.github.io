---
tags: dontlink
sitemap: true
---

# Linux

Yggdrasil is well supported on Linux.

## Notes

- Should work with any kernel that includes `tun` and/or `tap`.
- The maximum MTU size supported on Linux is 65535 in TUN mode and 65521 in TAP mode.
- Binary `.deb` packages are available for Debian, Ubuntu, elementaryOS and similar distributions.
- Binary `.rpm` packages are available for Red Hat Enterprise Linux, Fedora, CentOS and similar distributions.
- IPv6 needs to be enabled in order for Yggdrasil to work - IPv6 is usually enabled by default, but if not, enable using `sysctl -w net.ipv6.conf.all.disable_ipv6=0` or similar.
- If using TUN/TAP then `/dev/net/tun` should be present on your system.

## Debian, Ubuntu, elementaryOS (with systemd)

Debian binary packages exist to simplify the installation of Yggdrasil. These
will also work on any Debian-based distribution.

### From CircleCI

Visit our [Builds](builds.md) page and download the relevant `.deb` file, then
install it on your system. If you want to install the latest `.deb` from the
`master` branch:
```
curl -so- "https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts?branch=master&filter=successful" | \
egrep -o "https.*yggdrasil\-.*$(dpkg --print-architecture).deb" | \
while read line; do curl -O $line && dpkg -i $(basename $line); done
```
Configuration will be generated automatically into `/etc/yggdrasil.conf` when
the package is installed, and the Yggdrasil service will automatically be
installed into systemd and started.

### From an Internet repository

To start with, add the repository key to your apt keyring:
```
sudo apt-key adv --keyserver pool.sks-keyservers.net --recv-keys 569130E8CA20FBC4CB3FDE555898470A764B32C9
```

Add the repository:
```
echo 'deb http://neilalexander.s3.eu-west-2.amazonaws.com/deb/ debian yggdrasil' | sudo tee /etc/apt/sources.list.d/yggdrasil.list
sudo apt-get update
```

**Note**: The repository also works over HTTPS - you will need to `sudo apt-get install apt-transport-https` and then edit the above URL so that it starts with `https://` instead of `http://` in `/etc/apt/sources.list.d/yggdrasil.list`.

Install Yggdrasil:
```
sudo apt-get install yggdrasil
```
Configuration will be generated automatically into `/etc/yggdrasil.conf` when
the package is installed, and the Yggdrasil service will automatically be
installed into systemd and started.

## Red Hat Enterprise Linux, Fedora, CentOS (with systemd)

RPM binary packages exist to simplify the installation of Yggdrasil. These
will also work on any other RPM-based distribution.

### From CircleCI

Visit our [Builds](builds.md) page and download the relevant `.rpm` file, then
install it on your system:
```
sudo groupadd --system yggdrasil
sudo rpm -i yggdrasil...rpm
```
Configuration will be generated automatically into `/etc/yggdrasil.conf` when
the package is installed, and the Yggdrasil service will automatically be
installed into systemd and started.

### From an Internet repository

To start with, download the repository key:
```
sudo rpm --define="%_hkp_keyserver http://pool.sks-keyservers.net" --import 0x5898470A764B32C9
```

Add the repository:
```
sudo cat > /etc/yum.repos.d/yggdrasil.repo << EOF
[yggdrasil]
name = Yggdrasil
baseurl = https://neilalexander.s3.eu-west-2.amazonaws.com/rpm/
gpgcheck=1
EOF
```

Create the `yggdrasil` group:
```
sudo groupadd --system yggdrasil
```

Install Yggdrasil:
```
sudo dnf install yggdrasil
```
Enable and start the service after install/upgrade:
```
sudo systemctl enable yggdrasil
sudo systemctl start yggdrasil
```

## Other Distributions

[Download the latest Yggdrasil binary](https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts) and install it:
```
sudo cp ~/yggdrasil-x.x.xx-linux-amd64 /usr/local/bin/yggdrasil
sudo chmod +x /usr/local/bin/yggdrasil
```
Alternatively, compile Yggdrasil from source (below) and install:
```
sudo cp /path/to/yggdrasil-go/yggdrasil /usr/local/bin/yggdrasil
sudo chmod +x /usr/local/bin/yggdrasil
```

## Generate configuration

If you do not have a configuration file, you should generate configuration
before starting Yggdrasil:
```
sudo yggdrasil -genconf > /etc/yggdrasil.conf
```

## Run Yggdrasil

### Run once

Open a shell and start Yggdrasil using your generated configuration:
```
sudo nohup yggdrasil -useconffile /etc/yggdrasil.conf &
```
Alternatively, start Yggdrasil in auto-configuration mode:
```
sudo nohup yggdrasil -autoconf &
```

### Run with systemd

systemd service scripts are included in the `contrib/systemd/` folder so that it
runs automatically in the background (using `/etc/yggdrasil.conf` for
configuration).

Copy the service files into `/etc/systemd/system`, copy `yggdrasil` into your
`$PATH`, i.e. `/usr/bin`, and then enable the service:
```
systemctl enable yggdrasil
systemctl start yggdrasil
```

Once installed as a systemd service, you can read the `yggdrasil` output:
```
systemctl status yggdrasil
journalctl -u yggdrasil
```

## Build instructions

Linux has most of the tools needed to build Yggdrasil from source - you just
need to install Go 1.11 or later.

1. Install Go 1.11 or later, [godeb](https://github.com/niemeyer/godeb) is
recommended for Debian-based distributions).
1. Open a shell, clone the repository and build:
```
cd /path/to
git clone https://github.com/yggdrasil-network/yggdrasil-go
cd yggdrasil-go
./build
```
1. The resulting `yggdrasil` file is your build - optionally install it into
your system, as above.
