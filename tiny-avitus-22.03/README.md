# Tiny-Avitus build config for OpenWRT 22.03.x

Built on: Ubuntu 22.04

## 1. Feeds changes

* telephony: removed

## 2. Selected devices

* TP-Link [TL-WR740N](https://openwrt.org/toh/tp-link/tl-wr740n) [v4](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr740n_v4.20)
* TP-Link [TL-WR741ND](https://openwrt.org/toh/tp-link/tl-wr741nd) none selected
* TP-Link [TL-WR841ND](https://openwrt.org/toh/tp-link/tl-wr841nd) [v7](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v7), [v8](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v8), [v9](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v9), [v11](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v11)
* TP-Link [TL-WR941N](https://openwrt.org/toh/tp-link/tl-wr941nd) [v3](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr941nd_v3)

## 3. Changes from default (but compare it yourself)

> Initial build, but it could barely fit. I had to remove wireguard and muninlite support. I had to strip busybox a bit (poweroff, halt, swapon, swapoff,fgrep,egrep).
>
> It might forget the configuration, because of free space limitations.

* Target: ath79 + subtarget: tiny
* Target Images: squashfs with 1024 Block size
* Global build settings: small changes, mostly stripping and removing IPv6, -SECCOMP
* Image configuration:
  * Version configuration options: Release distribution: Avitus; Support URL: this GitHub repository
* Base: -opkg, -ca-bundle, busybox:applets
  * Finding Utilities: -egrep, -fgrep
  * Int Utilities: -halt, -poweroff
  * Linux System Utilities: -swapoff, -swapon
  * Miscellaneous Utilities: -less
  * Shells: ash optimize for size
* Administration: +muninlite
* Kernel modules:
  * Network Support: -kmod-ppp, +kmod-wireguard
* Luci: (the current state)
  * Collections: nothing selected
  * Modules: +luci-base, +minify*, Translations: hungarian, +luci-compat, +luci-mod-admin-full, +luci-dashboard +luci-mod-network, +luci-mod-rpc, +luci-mod-status, +luci-mod-system
  * Applications: +luci-app-firewall, +luci-app-wireguard
  * Protocols: +luci-proto-wireguard
  * Themes: +luci-theme-bootstrap
  * Libraries: -libuclient
* Network:
  * VPN: +wireguard-tools
  * Webservers: +uhttpd
  * WirelessAPD: -wpad-basic-wolfssl, +wpad-mini
  * -uclient-fetch