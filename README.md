# openwrt-build-configs

A storage for my custom openwrt build config files, for small devices. Just to keep track of the changes. You can reuse them, change them, end even make suggestions how to lower the footprint and add more functionality.

I recommend using the Cyrus-19.07.10 build, but the Avitus-21.02.3 is also good. But for Avitud I personally recommend stopping the uhttpd service.

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
  * [Avitus OpenWRT 22.03.0-rc1](./tiny-avitus-22.03/README.md)
  * [Avitus OpenWRT 21.02.3](./tiny-avitus-21.02/README.md)
  * [Avitus OpenWRT 19.07.10](./tiny-avitus-19.07/README.md)
* Cyrus build configuration: ar7xxx/tiny, -IPv6, -PPP, -opkg, +Luci, +uhttp, +wireguard, +muninlite, +w1.
  * [Cyrus OpenWRT 19.07.10](./tiny-cyrus-19.07/README.md)
  * [Cyrus OpenWRT 18.06.9](./tiny-cyrus-18.06/README.md)
* ApolloDiomedes - same as Avitus, but backported dropbear commits.
* DavianThule - same as Cyrus, but backported dropbear commits.

***Note:** Cyrus builds can be considered end-of-life, as no new release will happen from OpenWRT. I recommend using the device images built on the 19.07.10 tag as they are the most stable and up-to-date.*

> ***Note:** Switching between builds: [link](https://openwrt.org/docs/guide-user/installation/ar71xx.to.ath79)
