# Tiny-Cyrus build config for OpenWRT 18.06.9

* Built on: Ubuntu 20.04.4
* Tested on: TPLink TL-WR841ND v9
* Firmware version: Cyrus 18.06.9 r8077-7cbbab7246 / LuCI openwrt-18.06 branch (git-20.319.49209-ab22243)
* Kernel version: 4.9.243

*This OpenWRT branch is **end-of-life**.*

## 0. Notes

For this custom build I won't fork the **openwrt-18.06** branch to backport fixes. Cyrus is only built using tagged openwrt releases, but with custom configuration and feeds.

Errata:

* Due to a change in openssh you cannot connect to this version of dropbear. The error message is: ```no matching host key type found. Their offer: ssh-rsa``` To fix this add ```HostKeyAlgorithms +ssh-rsa``` to the ```/etc/ssh/ssh_config``` on your client (Ubuntu, Fedora, ...)
  * This is fixed by enabling **dropbear:ecc** build option. However the keyfile is not checked in the config.

### 0.1. Elliptic Curve Cryptography fix

```diff
diff --git a/package/network/services/dropbear/Makefile b/package/network/services/dropbear/Makefile
index 9127651ef9..c81ccb0177 100644
--- a/package/network/services/dropbear/Makefile
+++ b/package/network/services/dropbear/Makefile
@@ -60,6 +60,7 @@ define Package/dropbear/description
 endef

 define Package/dropbear/conffiles
+$(if $(CONFIG_DROPBEAR_ECC),/etc/dropbear/dropbear_ecdsa_host_key)
 /etc/dropbear/dropbear_rsa_host_key
 /etc/config/dropbear
 endef
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