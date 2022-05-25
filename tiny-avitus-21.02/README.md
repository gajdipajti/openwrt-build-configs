# Tiny-Avitus build config for OpenWRT 21.02.x

* Built on: Ubuntu 22.04
* Tested on: TPLink TL-WR841ND v9
* Firmware version: Avitus 21.02.3 r16554-1d4dea6d4f / LuCI openwrt-21.02 branch git-22.083.69138-0a0ce2a
* Kernel version: 5.4.188

## 0. Notes

*Tried gcc-7, gcc-8, and gcc-10; but **gcc-8** gave me the smallest image.*

Errata:

* Because of high memory usage it is recommended to stop the **uhttpd** service after initial configuration.

## 1. Feeds changes

* packages: [gajdipajti/packages](https://github.com/gajdipajti/packages/tree/openwrt-21.02) (but the main repository can be also used)
* telephony: removed

#### 2.2.2. Selected devices

* TP-Link [TL-WR740N](https://openwrt.org/toh/tp-link/tl-wr740n) [v4](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr740n_v4.20)
* TP-Link [TL-WR741ND](https://openwrt.org/toh/tp-link/tl-wr741nd) none selected
* TP-Link [TL-WR841ND](https://openwrt.org/toh/tp-link/tl-wr841nd) [v7](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v7), [v8](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v8), [v9](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v9), [v11](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v11)
* TP-Link [TL-WR941N](https://openwrt.org/toh/tp-link/tl-wr941nd) [v3](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr941nd_v3)

#### 2.2.3. Changes from default (but compare it yourself)

* Target: ath79 + subtarget: tiny
* Target Images: squashfs with 1024 Block size
* Global build settings: small changes, mostly stripping and removing IPv6
* Image configuration:
  * Version configuration options: Release distribution: Avitus; Support URL: this GitHub repository
* Base: -opkg, -ca-bundle, +dropbear:ecc,ecc_full
* Administration: +muninlite
* Kernel modules:
  * Network Support: -kmod-ppp, +kmod-wireguard
  * W1-support: +kmod-w1, +kmod-w1-master-gpio, +kmod-w1-slave-therm
* Libraries: -libuclient, +libqrencode
* Luci:
  * Modules: +luci-base, +minify*, Translations: {hungarian, english}, +luci-mod-admin-full, +luci-dashboard +luci-mod-network, +luci-mod-status, +luci-mod-system
  * Applications: +luci-app-firewall, +luci-app-wireguard
  * Themes: +luci-theme-bootstrap
  * Protocols: +luci-proto-wireguard
* Network:
  * VPN: +wireguard-tools
  * Webservers: +uhttpd, +uhttpd-mod-ubus
  * WirelessAPD: -wpad-basic-wolfssl, +wpad-mini
  * -uclient-fetch
* Utilities: +qrencode
