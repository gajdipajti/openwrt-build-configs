# openwrt-build-configs

A storage for my custom openwrt build config files, for small devices. Just to keep track of the changes. You can reuse them, change them, end even make suggestions how to lower the footprint and add more functionality. The main idea is that each build should have **Luci**, **wireguard**, **muninlite** for ease of configuration, access and monitoring.

I recommend using the **Cyrus-19.07.10** build, or for better ssh the **Cyrus-19.07.11** build from my github repository. The **Avitus-21.02.3** is also good, but I personally recommend stopping the **uhttpd** webserver service from Luci (or disabling it completely).

## 0. Personal notes about the openwrt-19.07 branch

I wasn't able to ssh into some of my old *19.07.10* devices after switching to Ubuntu 22.04 because the rsa host keys were not supported anymore. I tried [cherry-picking some fixes](https://github.com/openwrt/openwrt/pull/9910) however at that time I did not realize that the *openwrt-19.07* branch is [end-of-life](https://lists.infradead.org/pipermail/openwrt-announce/2022-April/000027.html) and closed.

At the end I decided to cherry-pick 5 dropbear commits and create my own tagged release. And again, just to emphasize:

> This is NOT and official OpenWRT release. I am just a user, who wants to keep some of his devices usable.

### 0.1. Errata

* Due to a change in openssh you cannot connect to this version of dropbear. The error message is: ```no matching host key type found. Their offer: ssh-rsa``` To fix this add ```HostKeyAlgorithms +ssh-rsa``` to the ```/etc/ssh/ssh_config``` on your client (Ubuntu, Fedora, ...)
  * **FIX**: Cherry pick 5 dropbear changes and tag is as v19.07.11
  * [changes](https://github.com/openwrt/openwrt/compare/openwrt-19.07...gajdipajti:openwrt-19.07)

### 0.2. How to access old configs

* [tiny-1.1](https://github.com/gajdipajti/openwrt-build-configs/tree/tiny-v1.17)

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

### 1.1. For v19.07.11 see the readme in subfolders

> This is NOT and official OpenWRT release.

1. ```git clone git@github.com:gajdipajti/openwrt.git --branch v19.07.11 --single-branch```
2. ```cd openwrt```
3. not needed
4. not needed
5. ...

### 1.2. Build systems

Reference: [3](https://openwrt.org/docs/guide-developer/toolchain/install-buildsystem)

Ubuntu 24.04: 

```sh
sudo apt install build-essential clang flex bison g++ gawk gcc-multilib g++-multilib gettext git libncurses5-dev libssl-dev python3-setuptools rsync swig unzip zlib1g-dev file wget
```

Ubuntu 22.04.5:

*Note: This is the last LTS version that can build the openwrt-19.07.* branch.*

```sh
sudo apt install build-essential gawk gcc-multilib flex git gettext libncurses5-dev libssl-dev python3-distutils zlib1g-dev python2
```

Ubuntu 20.04.4:

```sh
sudo apt install build-essential ccache ecj fastjar file g++ gawk gettext git java-propose-classpath libelf-dev libncurses5-dev libncursesw5-dev libssl-dev python python2.7-dev python3 unzip wget python-distutils-extra python3-setuptools python3-dev rsync subversion swig time xsltproc zlib1g-dev 
```

## 2. Available configurations

* Avitus testing: the previous Avitus config won't fit. I don't recommend using this.
  * [Avitus OpenWRT 22.03.0-rc1](./tiny-avitus-22.03/README.md)
* Avitus build configuration: **ath79/tiny**, -IPv6, -PPP, -opkg, -wolfssl, -ca-bundles, +Luci, +uhttp, +wireguard, +muninlite, +w1.
  * [Avitus OpenWRT 21.02.3](./tiny-avitus-21.02/README.md) - This build is the most up-to-date which can run on the devices. However **uhttpd** needs to be stopped or disabled after configuration.
  * [Avitus OpenWRT 19.07.11](./tiny-avitus-19.07/README.md)
* Cyrus build configuration: **ar7xxx/tiny**, -IPv6, -PPP, -opkg, +Luci, +uhttp, +wireguard, +muninlite, +w1.
  * [Cyrus OpenWRT 19.07.11](./tiny-cyrus-19.07/README.md) - I recommend using this build.
  * [Cyrus OpenWRT 18.06.9](./tiny-cyrus-18.06/README.md)

***Note:** Cyrus builds can be considered end-of-life, as no new release will happen from OpenWRT. I recommend using the device images built on the 19.07.11 tag as they are the most stable and up-to-date.*

> ***Note:** Switching between builds: [link](https://openwrt.org/docs/guide-user/installation/ar71xx.to.ath79)

## 3. External resources

These are the repositories that I am using for the build. If there is an errata which is not fixed in the **openwrt/openwrt** repository (**eol**), then possible fixs are cherry-picked into my branches. No other change. In case of packages, I cherry-picked the **muninlite** changes.

* [openwrt/openwrt](https://github.com/openwrt/openwrt)
* [gajdipajti/openwrt-19.07](https://github.com/gajdipajti/openwrt/tree/openwrt-19.07) - with **v19.07.11** tag
* [gajdipajti/cherry-pick-fs-2275](https://github.com/gajdipajti/openwrt/tree/cherry-pick-fs-2275) - cherry picked dropbear:ecc rebuild fix for the **openwrt-19.07** branch. - but it is not enough.
* [gajdipajti/cherry-pick-efc533c](https://github.com/gajdipajti/openwrt/tree/cherry-pick-eccfix) - cherry picked dropbear enable ecc host key use for the **openwrt-18.06** branch. - but it is not working as expected.
* [openwrt/packages](https://github.com/openwrt/packages) - it includes the cherry-picked **muninlite** commits from **master** for the **openwrt-19.07** branch.
* [gajdipajti/packages](https://github.com/gajdipajti/packages) - cherry picked muninlite for **openwrt-18.06** and **openwrt-19.07** 
