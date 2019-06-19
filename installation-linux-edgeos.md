---
tags: dontlink
sitemap: true
---

# Installing on Ubiquiti EdgeOS

Yggdrasil is supported on the Ubiquiti EdgeRouter using the
[vyatta-yggdrasil](https://github.com/neilalexander/vyatta-yggdrasil) package.

Perform installation steps over SSH by connecting to the EdgeRouter as the
`ubnt` user, e.g. `ssh ubnt@192.168.1.1`, or another admin-level user if
configured.

## Package install from an Aptitude repository

Start by adding the GPG key:
```
curl -o- https://neilalexander.s3.dualstack.eu-west-2.amazonaws.com/deb/key.txt | sudo apt-key add -
```

Then add the repository:
```
configure
set system package repository yggdrasil distribution edgerouter
set system package repository yggdrasil components yggdrasil
set system package repository yggdrasil url http://neilalexander.s3.dualstack.eu-west-2.amazonaws.com/deb/
commit
save
```

Then install the `vyatta-yggdrasil` package:
```
sudo apt-get update
sudo apt-get install vyatta-yggdrasil
```

Yggdrasil can then be updated in the future by updating the packages:
```
sudo apt-get update
sudo apt-get upgrade
```

## One-off package install from CircleCI

If you do not want to configure the repository above, you can perform a one-off
installation by [downloading the latest vyatta-yggdrasil `.deb`
package](https://circleci.com/api/v1.1/project/github/yggdrasil-network/yggdrasil-go/latest/artifacts) and then install it:
```
curl -O https://xx-xxxxxxxxx-gh.circle-artifacts.com/x/vyatta-yggdrasil-x.x.xxx-mipsel.deb
sudo dpkg -i vyatta-yggdrasil-x.x.xxx-mipsel.deb
```

## After installation

Read the [EdgeRouter platform page](platform-edgerouter.md) page for information
on how to configure Yggdrasil and guidance on modifying the configuration.
