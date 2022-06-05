# Tiny-Cyrus build config for OpenWRT 19.07.10

* Built on: Ubuntu 20.04.4
* Tested on: TPLink TL-WR841ND v8, v9; *TL-WR941ND v3* - but had issues.
* Firmware version: Cyrus 19.07.10 r11427-9ce6aa9d8d / LuCI openwrt-19.07 branch git-22.099.58928-786ebc9
* Kernel version: 4.14.275

## 0. Notes

Errata:

* Due to a change in openssh you cannot connect to this version of dropbear. The error message is: ```no matching host key type found. Their offer: ssh-rsa``` To fix this add ```HostKeyAlgorithms +ssh-rsa``` to the ```/etc/ssh/ssh_config``` on your client (Ubuntu, Fedora, ...)
  * This can be fixed by enabling **dropbear:ecc** build option.
  * However there is a [bug](https://github.com/openwrt/openwrt/issues/6157) in the dropbear makefile which can be hit when rebuilding with different ecc configuration.
  * It can be fixed by cherry-picking the [fix](https://github.com/openwrt/openwrt/commit/289d532ddd9427a9071d85966d38fff9d78837bd) when you run into the dropbear compilation error. After this start the build again.
  * Or you can use my branch.
* **TL-WR941ND v3** had WiFi stability issues. Maybe it was due to channel collision.

*This OpenWRT branch is **[end-of-life](https://lists.infradead.org/pipermail/openwrt-announce/2022-April/000027.html)**. This bug [won't be fixed on the **openwrt-19.07**](https://github.com/openwrt/openwrt/pull/9910) branch. However you can use my cherry-picked branch [gajdipajti/cherry-pick-fs-2275](https://github.com/gajdipajti/openwrt/tree/cherry-pick-fs-2275) to fix the errata.*

```sh
git clone --branch cherry-pick-fs-2275 git@github.com:gajdipajti/openwrt.git
```

## 1. Feeds changes

* packages: [gajdipajti/packages](https://github.com/gajdipajti/packages/tree/openwrt-19.07) (backported muninlite from master)
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
* Base: -opkg, +dropbear:ecc, full_ecc, compression
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