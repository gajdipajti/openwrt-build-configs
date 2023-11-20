# Tiny-Cyrus build config for OpenWRT 19.07.11

> This is NOT and official OpenWRT release. I am just a user, who wants to keep some of his devices usable.

* Built on: Ubuntu 20.04.4
* Tested on: TPLink TL-WR740N v4; Past issues: *TL-WR941ND v3* -> unstable wifi
* Firmware version: Cyrus 19.07.11 r11435-15432053ab / LuCI openwrt-19.07 branch git-22.115.68448-712bc8e
* Kernel version: 4.14.275

## 0. Notes

I wasn't able to ssh into some of my old *19.07.10* devices after switching to Ubuntu 22.04 because the rsa host keys were not supported anymore. I tried [cherry-picking some fixes](https://github.com/openwrt/openwrt/pull/9910) however at that time I did not realize that the *openwrt-19.07* branch is [end-of-life](https://lists.infradead.org/pipermail/openwrt-announce/2022-April/000027.html) and closed.

At the end I decided to cherry-pick 5 dropbear commits and create my own tagged release. And again, just to emphasize:

> This is NOT and official OpenWRT release. I am just a user, who wants to keep some of his devices usable.

### 0.1. Changelog

Changes from v19.07.10..v19.07.11:

* [**openwrt**](https://github.com/openwrt/openwrt/compare/openwrt-19.07...gajdipajti:openwrt-19.07): Cherry-picked dropbear fixes and changes from the *openwrt-21.03* branch to support new hostkeys.
* **packages**: Cherry-picked muinlite changes from *master* to support new features.
* **feeds**: changed feeds from git.openwrt.org to github.com. Removed *telephony* and *freifunk*.
* other contributions.

### 0.2. Errata

* Due to a change in openssh you cannot connect to this version of dropbear. The error message is: ```no matching host key type found. Their offer: ssh-rsa``` To fix this add ```HostKeyAlgorithms +ssh-rsa``` to the ```/etc/ssh/ssh_config``` on your client (Ubuntu, Fedora, ...)
  * This is fixed by cherry-picked dropbear commits.
* A possible WiFi instability on a TL-WR941ND v3 device.
  * Maybe it is related to a channel collision. I don't know how to fix it.
  * After switching the device to tiny-avitus-21.03 there was no error.

### 0.3. Accessing old version

* [tiny-1.1](https://github.com/gajdipajti/openwrt-build-configs/tree/tiny-v1.17)

## 1. How to clone the openwrt repository to build v19.07.11

```bash
git clone git@github.com:gajdipajti/openwrt.git --branch v19.07.11 --single-branch
```

## 2. Selected devices

* TP-Link [TL-WR740N](https://openwrt.org/toh/tp-link/tl-wr740n) [v4](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr740n_v4.20)
* TP-Link [TL-WR741ND](https://openwrt.org/toh/tp-link/tl-wr741nd) none selected
* TP-Link [TL-WR841ND](https://openwrt.org/toh/tp-link/tl-wr841nd) [v7](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v7), [v8](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v8), [v9](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v9), [v11](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v11)
* TP-Link [TL-WR941N](https://openwrt.org/toh/tp-link/tl-wr941nd) [v3](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr941nd_v3) - however I don't recommend using this device, see errata

## 3. Changes from default (but compare it yourself)

* Target: ar7xxx/ar9xxx + subtarget: tiny
* Target Images: squashfs with 1024 Block size
* Global build settings: small changes, mostly stripping and removing IPv6
* Image configuration:
  * Version configuration options: Release distribution: Cyrus; Support URL: this GitHub repository
* Base: -opkg, +dropbear:ecc, full_ecc, chacha20poly1305, curve25519, ed25519
* Administration: +muninlite
* Kernel modules:
  * Network Support: -kmod-ppp, +kmod-wireguard
  * W1-support: +kmod-w1, +kmod-w1-gpio-custom, +kmod-w1-master-gpio, +kmod-w1-slave-therm
  * Wireless Drivers: -ath-dfs, -mac80211-mesh
* Libraries: -libuclient, +libqrencode
* Luci:
  * Modules: +luci-base, +minify*, Translations: {hungarian, english}, +luci-mod-admin-full, +luci-mod-network, +luci-mod-status, +luci-mod-system
  * Applications: +luci-app-firewall, +luci-app-wireguard
  * Themes: +luci-theme-bootstrap
  * Protocols: +luci-proto-wireguard
* Network:
  * VPN: +wireguard, +wireguard-tools
  * Webservers: +uhttpd, +uhttpd-mod-ubus
* Utilities:
  * +logrotate
  * +qrencode
