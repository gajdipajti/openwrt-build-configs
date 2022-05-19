# openwrt-build-configs
A storage for my custom openwrt build config files, for small devices. Just to keep track of the changes. You can reuse them, change them, end even make suggestions how to lower the footprint and add more functionality.

## 1. How to build

On my build computer I use zfs snapshots between each steps, so that I can rollback whenever I want to a previous state. This defeats the git version control system, but if it works... (I don't use git that often). References [1](https://gist.github.com/chankruze/dee8c2ba31c338a60026e14e3383f981), [2](https://openwrt.org/docs/guide-developer/toolchain/use-buildsystem)

I usually rebuild only tagged versions: *v18.06.1*, *v19.07.6*, *...*

1. ```git clone https://git.openwrt.org/openwrt/openwrt.git```
2. ```cd openwrt```
3. ```git checkout -b v19.07.10``` # Or any other tagged release.
4. Make adjustments to the feeds.conf.default.
5. ```./scripts/feeds update -a```
6. ```./scripts/feeds install -a```
7. Copy the build config file to the repository as .config
8. ```make menuconfig```
9. Make your changes.
10. ```make``` or ```make -j5``` # Depending on the number of cores.

### 1.1. Build systems

Reference: [3](https://openwrt.org/docs/guide-developer/toolchain/install-buildsystem)

Ubuntu 22.04:

```sh
sudo apt install build-essential gawk gcc-multilib flex git gettext libncurses5-dev libssl-dev python3-distutils zlib1g-dev
```

Ubuntu 20.04.4:

```sh
sudo apt install build-essential ccache ecj fastjar file g++ gawk \
gettext git java-propose-classpath libelf-dev libncurses5-dev \
libncursesw5-dev libssl-dev python python2.7-dev python3 unzip wget \
python-distutils-extra python3-setuptools python3-dev rsync subversion \
swig time xsltproc zlib1g-dev 
```

## 2. Available configurations

* Avitus build configuration: ath79/tiny, -IPv6, -PPP, -opkg, +Luci, +uhttp, +wireguard, *+muninlite (if possible)*
* Cyrus build configuration: ar7xxx/tiny, -IPv6, -PPP, -opkg, +Luci, +uhttp, +wireguard, +muninlite, +w1.

***Note:** Cyrus builds can be considered end-of-life, as no new release will happen from OpenWRT. I recommend using the device images built on the 19.07.10 tag as they are the most stable and up-to-date.*

### 2.1. Tiny-Avitus build config for OpenWRT 22.03.x

Built on: Ubuntu 22.04

#### 2.1.1. Feeds changes

* telephony: removed

#### 2.1.2. Selected devices

* TP-Link [TL-WR740N](https://openwrt.org/toh/tp-link/tl-wr740n) [v4](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr740n_v4.20)
* TP-Link [TL-WR741ND](https://openwrt.org/toh/tp-link/tl-wr741nd) none selected
* TP-Link [TL-WR841ND](https://openwrt.org/toh/tp-link/tl-wr841nd) [v7](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v7), [v8](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v8), [v9](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v9), [v11](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v11)
* TP-Link [TL-WR941N](https://openwrt.org/toh/tp-link/tl-wr941nd) [v3](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr941nd_v3)

#### 2.1.3. Changes from default (but compare it yourself)

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

### 2.2. Tiny-Avitus build config for OpenWRT 21.02.x

Built on: Ubuntu 22.04

*Tried gcc-7, gcc-8, and gcc-10; but **gcc-8** gave me the smallest image.*

#### 2.2.1. Feeds changes

* packages: https://github.com/gajdipajti/packages/tree/openwrt-21.02 (backported muninlite from master)
* telephony: removed

#### 2.2.2. Selected devices

* TP-Link [TL-WR740N](https://openwrt.org/toh/tp-link/tl-wr740n) [v4](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr740n_v4.20)
* TP-Link [TL-WR741ND](https://openwrt.org/toh/tp-link/tl-wr741nd) none selected
* TP-Link [TL-WR841ND](https://openwrt.org/toh/tp-link/tl-wr841nd) [v7](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v7), [v8](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v8), [v9](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v9), [v11](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v11)
* TP-Link [TL-WR941N](https://openwrt.org/toh/tp-link/tl-wr941nd) [v3](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr941nd_v3)

#### 2.2.3. Changes from default (but compare it yourself)

> It could be built, but it was also oversized at first. Has not been flashed to any device yet. Maybe it will forget the configuration as there is not enough free space.

* Target: ath79 + subtarget: tiny
* Target Images: squashfs with 1024 Block size
* Global build settings: small changes, mostly stripping and removing IPv6
* Image configuration:
  * Version configuration options: Release distribution: Avitus; Support URL: this GitHub repository
* Base: -opkg, -ca-bundle, +dropbear:ecc,ecc_full,ed25519, busybox:applets
  * Finding Utilities: -egrep, -fgrep
  * Int Utilities: -halt, -poweroff
  * Linux System Utilities: -swapoff, -swapon
  * Miscellaneous Utilities: -less
  * Shells: ash optimize for size
* Administration: +muninlite
* Kernel modules:
  * Network Support: -kmod-ppp, +kmod-wireguard
* Libraries: -libuclient, +libqrencode
* Luci:
  * Modules: +luci-base, +minify*, Translations: {hungarian, english}+luci-mod-admin-full, +luci-dashboard +luci-mod-network, +luci-mod-status, +luci-mod-system
  * Applications: +luci-app-firewall, +luci-app-wireguard
  * Themes: +luci-theme-bootstrap
  * Protocols: +luci-proto-wireguard
* Network:
  * VPN: +wireguard-tools
  * Webservers: +uhttpd, +uhttpd-mod-ubus
  * WirelessAPD: -wpad-basic-wolfssl, +wpad-mini
  * -uclient-fetch
* Utilities: +qrencode

### 2.3. Tiny-Avitus build config for OpenWRT 19.07.10

*This OpenWRT branch is **end-of-life**. I don't backport security fixes and changes as I don't have a fork of the main repository. I only build tagged releases with custom configuration.  If I adjust my build configuration, then I will upload the changes just for comparison.*

Built on: Ubuntu 20.04.4; Not tested!

#### 2.3.1. Feeds changes

* packages: https://github.com/gajdipajti/packages/tree/openwrt-19.07 (backported muninlite from master)
* telephony: removed
* freifunk: removed

#### 2.3.2. Selected devices

* TP-Link [TL-WR740N](https://openwrt.org/toh/tp-link/tl-wr740n) [v4](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr740n_v4.20)
* TP-Link [TL-WR741ND](https://openwrt.org/toh/tp-link/tl-wr741nd) none selected
* TP-Link [TL-WR841ND](https://openwrt.org/toh/tp-link/tl-wr841nd) [v7](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v7), [v8](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v8), [v9](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v9), [v11](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v11)
* TP-Link [TL-WR941N](https://openwrt.org/toh/tp-link/tl-wr941nd) [v3](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr941nd_v3)

#### 2.3.3. Changes from default (but compare it yourself)

> It could be built, but it was also oversized at first. Has not been flashed to any device yet. Maybe it will forget the configuration as there is not enough free space.

* Target: ath79 + subtarget: tiny
* Target Images: squashfs with 1024 Block size
* Global build settings: small changes, mostly stripping and removing IPv6
* Image configuration:
  * Version configuration options: Release distribution: Avitus; Support URL: this GitHub repository
* Base: -opkg, -ca-bundle, -busybox:
  * Finding Utilities: -egrep, -fgrep
  * Int Utilities: -halt, -poweroff
  * Linux System Utilities: -swapoff, -swapon
  * Miscellaneous Utilities: -less
  * Shells: ash optimize for size
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

### 2.4. Tiny-Cyrus build config for OpenWRT 19.07.10

*This OpenWRT branch is **end-of-life**. I don't backport security fixes and changes as I don't have a fork of the main repository. I only build tagged releases with custom configuration.  If I adjust my build configuration, then I will upload the changes just for comparison.*

* Built on: Ubuntu 20.04.4; Tested on: TPLink TL-WR841ND v8, v9;
* Firmware version: Cyrus 19.07.10 r11427-9ce6aa9d8d / LuCI openwrt-19.07 branch git-22.099.58928-786ebc9
* Kernel version: 4.14.275

#### 2.4.1. Feeds changes

* packages: https://github.com/gajdipajti/packages/tree/openwrt-19.07 (backported muninlite from master)
* telephony: removed
* freifunk: removed

#### 2.4.2. Selected devices

* TP-Link [TL-WR740N](https://openwrt.org/toh/tp-link/tl-wr740n) [v4](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr740n_v4.20)
* TP-Link [TL-WR741ND](https://openwrt.org/toh/tp-link/tl-wr741nd) none selected
* TP-Link [TL-WR841ND](https://openwrt.org/toh/tp-link/tl-wr841nd) [v7](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v7), [v8](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v8), [v9](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v9), [v11](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v11)
* TP-Link [TL-WR941N](https://openwrt.org/toh/tp-link/tl-wr941nd) [v3](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr941nd_v3)

#### 2.4.3. Changes from default (but compare it yourself)

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

### 2.5. Tiny-Cyrus build config for OpenWRT 18.06.9

*This OpenWRT branch is **end-of-life**. I don't backport security fixes and changes as I don't have a fork of the main repository. I only build tagged releases with custom configuration. If I adjust my build configuration, then I will upload the changes just for comparison.*

* Built on: Ubuntu 20.04.4; Tested on: TPLink TL-WR841ND v9;
* Firmware version: Cyrus 18.06.9 r8077-7cbbab7246 / LuCI openwrt-18.06 branch (git-20.319.49209-ab22243)
* Kernel version: 4.9.243

#### 2.5.1. Feeds changes

* packages: https://github.com/gajdipajti/packages/tree/openwrt-18.06 (backported muninlite from master)
* telephony: removed

#### 2.5.2. Selected devices

* TP-Link [TL-WR740N](https://openwrt.org/toh/tp-link/tl-wr740n) [v4](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr740n_v4.20)
* TP-Link [TL-WR741ND](https://openwrt.org/toh/tp-link/tl-wr741nd) none selected
* TP-Link [TL-WR841ND](https://openwrt.org/toh/tp-link/tl-wr841nd) [v7](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v7), [v8](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v8), [v9](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v9), [v11](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr841n_v11)
* TP-Link [TL-WR941N](https://openwrt.org/toh/tp-link/tl-wr941nd) [v3](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr941nd_v3)

#### 2.5.3. Changes from default (but compare it yourself)

* Target: ar7xxx/ar9xxx + subtarget: tiny
* Target Images: squashfs with 1024 Block size
* Global build settings: small changes, mostly stripping and removing IPv6
* Image configuration:
  * Version configuration options: Release distribution: Cyrus; Support URL: this GitHub repository
* Base: -opkg, +dropbear:ecc, -busybox:[swapon, swapoff]
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
