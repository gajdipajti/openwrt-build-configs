# openwrt-build-configs

A storage for my custom openwrt build config files, for small devices. Just to keep track of the changes. You can reuse them, change them, end even make suggestions how to lower the footprint and add more functionality. The main idea is that each build should have **Luci**, **wireguard**, **muninlite** for ease of configuration, access and monitoring.

I recommend using the Cyrus-19.07.10 build, or for better ssh the DavianThule-19.07.10.999. The Avitus-21.02.3 is also good, but I personally recommend stopping the **uhttpd** webserver service from Luci (or disabling it completely).

## 1. How to build

On my build computer I use zfs snapshots between each steps, so that I can rollback whenever I want to start from a previous state. This defeats the git version control system, but makes tests easier. References [1](https://gist.github.com/chankruze/dee8c2ba31c338a60026e14e3383f981), [2](https://openwrt.org/docs/guide-developer/toolchain/use-buildsystem)

Steps in general:

1. ```git clone https://git.openwrt.org/openwrt/openwrt.git```
2. ```cd openwrt```
3. ```git checkout -b v19.07.10``` # Or any other tagged release. I usually rebuild only tagged versions: *v18.06.1*, *v19.07.6*, *...*
4. Make adjustments to the feeds.conf.default.
5. ```./scripts/feeds update -a```
6. ```./scripts/feeds install -a```
7. Copy the build config file to the repository as .config
8. ```make menuconfig```
9. Make your changes.
10. ```make``` or ```make -j5``` # Depending on the number of cores (cores+1).

Or 1&3 in one step from GitHub:
* ```git clone --branch openwrt-19.07 git@github.com:openwrt/openwrt.git```

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

* Avitus testing: the previous Avitus config won't fit. I don't recommend using this.
  * [Avitus OpenWRT 22.03.0-rc1](./tiny-avitus-22.03/README.md)
* Avitus build configuration: ath79/tiny, -IPv6, -PPP, -opkg, -wolfssl, -ca-bundles, +Luci, +uhttp, +wireguard, +muninlite, +w1.
  * [Avitus OpenWRT 21.02.3](./tiny-avitus-21.02/README.md) - This build is the most up-to-date which can run on the devices. However **uhttpd** needs to be stopped or disabled after configuration.
  * [Avitus OpenWRT 19.07.10](./tiny-avitus-19.07/README.md)
* Cyrus build configuration: ar7xxx/tiny, -IPv6, -PPP, -opkg, +Luci, +uhttp, +wireguard, +muninlite, +w1.
  * [Cyrus OpenWRT 19.07.10](./tiny-cyrus-19.07/README.md) - I recommend using this build.
  * [Cyrus OpenWRT 18.06.9](./tiny-cyrus-18.06/README.md)

***Note:** Cyrus builds can be considered end-of-life, as no new release will happen from OpenWRT. I recommend using the device images built on the 19.07.10 tag as they are the most stable and up-to-date.*

> ***Note:** Switching between builds: [link](https://openwrt.org/docs/guide-user/installation/ar71xx.to.ath79)

## 3. External resources

These are the repositories that I am using for the build. If there is an errata which is not fixed in the openwrt repository (**eol**), then the fix is cherry-picked into my branch. No other change. In case of packages, I cherry-picked the **muninlite** changes.

* [openwrt/openwrt](https://github.com/openwrt/openwrt)
* [gajdipajti/cherry-pick-fs-2275](https://github.com/gajdipajti/openwrt/tree/cherry-pick-fs-2275) - cherry picked dropbear:ecc rebuild fix for the openwrt-19.07 branch.
* [gajdipajti/cherry-pick-efc533c](https://github.com/gajdipajti/openwrt/tree/cherry-pick-eccfix) - cherry picked dropbear enable ecc host key use for the openwrt-18.06 branch.
* [openwrt/packages](https://github.com/openwrt/packages)
* [gajdipajti/packages](https://github.com/gajdipajti/packages) - cherry picked muninlite for openwrt-18.06 and openwrt-19.07 
