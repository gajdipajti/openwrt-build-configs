# Tiny-Cyrus build config for OpenWRT 18.06.9

* Built on: Ubuntu 20.04.4
* Tested on: TPLink TL-WR841ND v9, TL-WR941ND v3
* Firmware version: Cyrus 18.06.9 r8077-7cbbab7246 / LuCI openwrt-18.06 branch (git-20.319.49209-ab22243)
* Kernel version: 4.9.243

## 0. Notes

Errata:

* Due to a change in openssh you cannot connect to this version of dropbear. The error message is: ```no matching host key type found. Their offer: ssh-rsa``` To fix this add ```HostKeyAlgorithms +ssh-rsa``` to the ```/etc/ssh/ssh_config``` on your client (Ubuntu, Fedora, ...)
  1. This should fixed by enabling **dropbear:ecc** build option. However the keyfile is not checked when built from **openwrt-18.06** branch.
  2. Fixed by the commit: [efc533c](https://github.com/openwrt/openwrt/commit/efc533cc2ff7ec99595727c4990b1fed006794ea)
  3. This commit is backported to [gajdipajti/cherry-pick-efc533c](https://github.com/gajdipajti/openwrt/tree/cherry-pick-eccfix) branch (branched from **openwrt-18.06**).
  4. Maybe it is fixed.

*This OpenWRT branch is **end-of-life**. This bug won't be fixed on the **openwrt-18.06** branch. However you can use my cherry-picked branch [gajdipajti/cherry-pick-efc533c](https://github.com/gajdipajti/openwrt/tree/cherry-pick-eccfix) to fix the errata.*

```sh
git clone --branch cherry-pick-efc533c git@github.com:gajdipajti/openwrt.git
```

## 1. Feeds changes

* packages: [gajdipajti/packages](https://github.com/gajdipajti/packages/tree/openwrt-18.06) (backported muninlite from master)
* telephony: removed

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
* Base: -opkg, +dropbear:ecc
* Administration: +muninlite
* Kernel modules:
  * Network Support: -kmod-ppp, +kmod-wireguard
  * W1-support: +kmod-w1, +kmod-w1-gpio-custom, +kmod-w1-master-gpio, +kmod-w1-slave-therm
  * Wireless Drivers: -ath-dfs, -mac80211-mesh
* Libraries: -libuclient, +libqrencode
* Luci:
  * Modules: +luci-base, +minify*, Translations: {hungarian, english}, +luci-mod-admin-full
  * Applications: +luci-app-firewall, +luci-app-upnp, +luci-app-wireguard
  * Themes: +luci-theme-bootstrap
  * Protocols: +luci-proto-wireguard
* Network:
  * VPN: +wireguard
  * Webservers: +uhttpd, +uhttpd-mod-ubus
  * -uclient-fetch
* Utilities: +qrencode