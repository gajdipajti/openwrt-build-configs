# Tiny-Cyrus build config for OpenWRT 19.07.10

* Built on: Ubuntu 20.04.4
* Tested on: TPLink TL-WR841ND v8, v9;
* Firmware version: Cyrus 19.07.10 r11427-9ce6aa9d8d / LuCI openwrt-19.07 branch git-22.099.58928-786ebc9
* Kernel version: 4.14.275

*This OpenWRT branch is possibly **end-of-life** (?).*

## 0. Notes

For this custom build I won't fork the **openwrt-19.07** branch to backport fixes. Cyrus is only built using tagged openwrt releases, but with custom configuration and feeds. 
But you can check the **DavianThule** custom build which is built from the forked **openwrt-19.07** branch to fix the errata.

Errata:

* Due to a change in openssh you cannot connect to this version of dropbear. The error message is: ```no matching host key type found. Their offer: ssh-rsa``` To fix this add ```HostKeyAlgorithms +ssh-rsa``` to the ```/etc/ssh/ssh_config``` on your client (Ubuntu, Fedora, ...)

## 1. Feeds changes

* packages: https://github.com/gajdipajti/packages/tree/openwrt-19.07 (backported muninlite from master)
* telephony: removed
* freifunk: removed

## 2. Selected devices

* TP-Link [TL-WR740N](https://openwrt.org/toh/tp-link/tl-wr740n) [v4](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr740n_v4.20)
* TP-Link [TL-WR741ND](https://openwrt.org/toh/tp-link/tl-wr741nd) none selected
* TP-Link [TL-WR841ND](https://openwrt.org/toh/tp-link/tl-wr841nd) [v7](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v7), [v8](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v8), [v9](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v9), [v11](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v11)
* TP-Link [TL-WR941N](https://openwrt.org/toh/tp-link/tl-wr941nd) [v3](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr941nd_v3)

## 3. Changes from default (but compare it yourself)

* Target: ar7xxx/ar9xxx + subtarget: tiny
* Target Images: squashfs with 1024 Block size
* Global build settings: small changes, mostly stripping and removing IPv6
* Image configuration:
  * Version configuration options: Release distribution: Cyrus; Support URL: this GitHub repository
* Base: -opkg, -busybox:[swapon, swapoff]
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
  * -uclient-fetch
* Utilities: +qrencode
