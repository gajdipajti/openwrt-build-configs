# openwrt-build-configs
Just a storage openwrt build config files, for small devices. Just to keep track.

## Tiny-Cyrus build config for OpenWRT 19.07.x

Built on: Ubuntu 20.04.4

Feeds changes:

* packages: https://github.com/gajdipajti/packages/tree/openwrt-19.07 (backported muninlite from master)
* telephony: removed
* freifunk: removed

Supported devices:

* TP-Link [TL-WR740N](https://openwrt.org/toh/tp-link/tl-wr740n) [v4](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr740n_v4.20)
* TP-Link [TL-WR741ND](https://openwrt.org/toh/tp-link/tl-wr741nd) 
* TP-Link [TL-WR841ND](https://openwrt.org/toh/tp-link/tl-wr841nd) [v7](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v7), [v8](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v8), [v9](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v9), [v11](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v11) 
* TP-Link [TL-WR941N](https://openwrt.org/toh/tp-link/tl-wr941nd) [v3](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr941nd_v3)

Changes from default (but compare it yourself):

* Target: ar7xxx/ar9xxx + subtarget: tiny
* Target Images: squashfs with 1024 Block size
* Global build settings: small changes, mostly stripping and removing IPv6
* Image configuration:
  * Version configuration options: Release distribution: Cyrus; Support URL: this GitHub repository
* Base: -opkg, dropbear enable compression, busybox+top, -openwrt-keyring
* Administration: +muninlite
* Kernel modules:
  * Network Support: -kmod-ppp, +kmod-wireguard
  * W1-support: +kmod-w1, +kmod-w1-gpio-custom, +kmod-w1-master-gpio, +kmod-w1-slave-therm
* Luci: (the current state)
  * Collections: nothing selected
  * Modules: +luci-base, +minify*, Translations: hungarian, +luci-compat, +luci-mod-admin-full, +luci-mod-network, +luci-mod-rpc, +luci-mod-status, +luci-mod-system
  * Applications: +luci-app-firewall, +luci-app-wireguard
  * Themes: +luci-theme-bootstrap
  * Protocols: "luci-proto-wireguard
  * Libraries: nothing changed
* Network:
  * VPN: +wireguard, +wireguard-tools
  * Webservers: +uhttpd

