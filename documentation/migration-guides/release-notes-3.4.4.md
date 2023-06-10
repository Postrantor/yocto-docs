---
tip: translate by openai@2023-06-07 22:38:38
...
---
title: Release notes for 3.4.4 (honister)
-----------------------------------------

# Security Fixes in 3.4.4

- tiff: fix `2022-0865`{.interpreted-text role="cve"}, `2022-0891`{.interpreted-text role="cve"}, `2022-0907`{.interpreted-text role="cve"}, `2022-0908`{.interpreted-text role="cve"}, `2022-0909`{.interpreted-text role="cve"} and `2022-0924`{.interpreted-text role="cve"}

> 修复 CVE-2022-0865、CVE-2022-0891、CVE-2022-0907、CVE-2022-0908、CVE-2022-0909 和 CVE-2022-0924。

- xz: fix [CVE-2022-1271](https://security-tracker.debian.org/tracker/CVE-2022-1271)
- unzip: fix [CVE-2021-4217](https://security-tracker.debian.org/tracker/CVE-2021-4217)
- zlib: fix `2018-25032`{.interpreted-text role="cve"}
- grub: ignore `2021-46705`{.interpreted-text role="cve"}

# Fixes in 3.4.4

- alsa-tools: Ensure we install correctly
- bitbake.conf: mark all directories as safe for git to read
- bitbake: knotty: display active tasks when printing keepAlive() message
- bitbake: knotty: reduce keep-alive timeout from 5000s (83 minutes) to 10 minutes
- bitbake: server/process: Disable gc around critical section
- bitbake: server/xmlrpcserver: Add missing xmlrpcclient import
- bitbake: toaster: Fix `IMAGE_INSTALL`{.interpreted-text role="term"} issues with \_append vs :append
- bitbake: toaster: fixtures replace gatesgarth
- build-appliance-image: Update to honister head revision
- conf.py/poky.yaml: Move version information to poky.yaml and read in conf.py
- conf/machine: fix QEMU x86 sound options
- devupstream: fix handling of `SRC_URI`{.interpreted-text role="term"}
- documentation: update for 3.4.4 release
- externalsrc/devtool: Fix to work with fixed export funcition flags handling
- gmp: add missing COPYINGv3
- gnu-config: update `SRC_URI`{.interpreted-text role="term"}
- libxml2: fix CVE-2022-23308 regression
- libxml2: move to gitlab.gnome.org
- libxml2: update to 2.9.13
- libxshmfence: Correct `LICENSE`{.interpreted-text role="term"} to HPND
- license_image.bbclass: close package.manifest file
- linux-firmware: correct license for ar3k firmware
- linux-firmware: upgrade 20220310 -\> 20220411
- linux-yocto-rt/5.10: update to -rt61
- linux-yocto/5.10: cfg/debug: add configs for kcsan
- linux-yocto/5.10: split vtpm for more granular inclusion
- linux-yocto/5.10: update to v5.10.109
- linux-yocto: nohz_full boot arg fix
- oe-pkgdata-util: Adapt to the new variable override syntax
- oeqa/selftest/devtool: ensure Git username is set before upgrade tests
- poky.conf: bump version for 3.4.4 release
- pseudo: Add patch to workaround paths with crazy lengths
- pseudo: Fix handling of absolute links
- sanity: Add warning for local hasheqiv server with remote sstate mirrors
- scripts/runqemu: Fix memory limits for qemux86-64
- shadow-native: Simplify and fix syslog disable patch
- tiff: Add marker for CVE-2022-1056 being fixed
- toaster: Fix broken overrides usage
- u-boot: Inherit pkgconfig
- uninative: Upgrade to 3.6 with gcc 12 support
- vim: Upgrade 8.2.4524 -\> 8.2.4681
- virglrenderer: update `SRC_URI`{.interpreted-text role="term"}
- webkitgtk: update to 2.32.4
- wireless-regdb: upgrade 2022.02.18 -\> 2022.04.08

# Known Issues

There were a couple of known autobuilder intermittent bugs that occurred during release testing but these are not regressions in the release.

# Contributors to 3.4.4

- Alexandre Belloni
- Anuj Mittal
- Bruce Ashfield
- Chee Yang Lee
- Dmitry Baryshkov
- Joe Slater
- Konrad Weihmann
- Martin Jansa
- Michael Opdenacker
- Minjae Kim
- Peter Kjellerstedt
- Ralph Siemsen
- Richard Purdie
- Ross Burton
- Tim Orling
- Wang Mingyu
- Zheng Ruoqin

# Repositories / Downloads for 3.4.4

poky

- Repository Location: :yocto\_[git:%60/poky](git:%60/poky)\`
- Branch: :yocto\_[git:%60honister](git:%60honister) \</poky/log/?h=honister\>\`
- Tag: :yocto\_[git:%60yocto-3.4.4](git:%60yocto-3.4.4) \</poky/tag/?h=yocto-3.4.4\>\`
- Git Revision: :yocto\_[git:%60780eeec8851950ee6ac07a2a398ba937206bd2e4](git:%60780eeec8851950ee6ac07a2a398ba937206bd2e4) \</poky/commit/?id=780eeec8851950ee6ac07a2a398ba937206bd2e4\>\`

> Git 版本：:yocto_[git:%60780eeec8851950ee6ac07a2a398ba937206bd2e4](git:%60780eeec8851950ee6ac07a2a398ba937206bd2e4) \</poky/commit/?id=780eeec8851950ee6ac07a2a398ba937206bd2e4\>\`

- Release Artefact: poky-780eeec8851950ee6ac07a2a398ba937206bd2e4
- sha: 09558927064454ec2492da376156b716d9fd14aae57196435d742db7bfdb4b95
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/poky-780eeec8851950ee6ac07a2a398ba937206bd2e4.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/poky-780eeec8851950ee6ac07a2a398ba937206bd2e4.tar.bz2), [http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/poky-780eeec8851950ee6ac07a2a398ba937206bd2e4.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/poky-780eeec8851950ee6ac07a2a398ba937206bd2e4.tar.bz2)

> 下载位置：[http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/poky-780eeec8851950ee6ac07a2a398ba937206bd2e4.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/poky-780eeec8851950ee6ac07a2a398ba937206bd2e4.tar.bz2)，[http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/poky-780eeec8851950ee6ac07a2a398ba937206bd2e4.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/poky-780eeec8851950ee6ac07a2a398ba937206bd2e4.tar.bz2)

openembedded-core

- Repository Location: :oe\_[git:%60/openembedded-core](git:%60/openembedded-core)\`
- Branch: :oe\_[git:%60honister](git:%60honister) \</openembedded-core/log/?h=honister\>\`
- Tag: :oe\_[git:%60yocto-3.4.4](git:%60yocto-3.4.4) \</openembedded-core/tag/?h=yocto-3.4.4\>\`
- Git Revision: :oe\_[git:%601a6f5e27249afb6fb4d47c523b62b5dd2482a69d](git:%601a6f5e27249afb6fb4d47c523b62b5dd2482a69d) \</openembedded-core/commit/?id=1a6f5e27249afb6fb4d47c523b62b5dd2482a69d\>\`

> Git 版本：<openembedded-core/commit/?id=1a6f5e27249afb6fb4d47c523b62b5dd2482a69d>（git:%601a6f5e27249afb6fb4d47c523b62b5dd2482a69d）

- Release Artefact: oecore-1a6f5e27249afb6fb4d47c523b62b5dd2482a69d
- sha: b8354ca457756384139a579b9e51f1ba854013c99add90c0c4c6ef68421fede5
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/oecore-1a6f5e27249afb6fb4d47c523b62b5dd2482a69d.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/oecore-1a6f5e27249afb6fb4d47c523b62b5dd2482a69d.tar.bz2), [http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/oecore-1a6f5e27249afb6fb4d47c523b62b5dd2482a69d.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/oecore-1a6f5e27249afb6fb4d47c523b62b5dd2482a69d.tar.bz2)

> 下载位置：[http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/oecore-1a6f5e27249afb6fb4d47c523b62b5dd2482a69d.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/oecore-1a6f5e27249afb6fb4d47c523b62b5dd2482a69d.tar.bz2)，[http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/oecore-1a6f5e27249afb6fb4d47c523b62b5dd2482a69d.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/oecore-1a6f5e27249afb6fb4d47c523b62b5dd2482a69d.tar.bz2)

meta-mingw

- Repository Location: :yocto\_[git:%60/meta-mingw](git:%60/meta-mingw)\`
- Branch: :yocto\_[git:%60honister](git:%60honister) \</meta-mingw/log/?h=honister\>\`
- Tag: :yocto\_[git:%60yocto-3.4.4](git:%60yocto-3.4.4) \</meta-mingw/tag/?h=yocto-3.4.4\>\`
- Git Revision: :yocto\_[git:%60f5d761cbd5c957e4405c5d40b0c236d263c916a8](git:%60f5d761cbd5c957e4405c5d40b0c236d263c916a8) \</meta-mingw/commit/?id=f5d761cbd5c957e4405c5d40b0c236d263c916a8\>\`

> Git 版本：yocto_[git：f5d761cbd5c957e4405c5d40b0c236d263c916a8](git:f5d761cbd5c957e4405c5d40b0c236d263c916a8) \</meta-mingw/commit/?id=f5d761cbd5c957e4405c5d40b0c236d263c916a8\>\`

- Release Artefact: meta-mingw-f5d761cbd5c957e4405c5d40b0c236d263c916a8
- sha: d4305d638ef80948584526c8ca386a8cf77933dffb8a3b8da98d26a5c40fcc11
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/meta-mingw-f5d761cbd5c957e4405c5d40b0c236d263c916a8.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/meta-mingw-f5d761cbd5c957e4405c5d40b0c236d263c916a8.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/meta-mingw-f5d761cbd5c957e4405c5d40b0c236d263c916a8.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/meta-mingw-f5d761cbd5c957e4405c5d40b0c236d263c916a8.tar.bz2)

> 下载位置：[http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/meta-mingw-f5d761cbd5c957e4405c5d40b0c236d263c916a8.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/meta-mingw-f5d761cbd5c957e4405c5d40b0c236d263c916a8.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/meta-mingw-f5d761cbd5c957e4405c5d40b0c236d263c916a8.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/meta-mingw-f5d761cbd5c957e4405c5d40b0c236d263c916a8.tar.bz2)

meta-gplv2

- Repository Location: :yocto\_[git:%60/meta-gplv2](git:%60/meta-gplv2)\`
- Branch: :yocto\_[git:%60honister](git:%60honister) \</meta-gplv2/log/?h=honister\>\`
- Tag: :yocto\_[git:%60yocto-3.4.4](git:%60yocto-3.4.4) \</meta-gplv2/tag/?h=yocto-3.4.4\>\`
- Git Revision: :yocto\_[git:%60f04e4369bf9dd3385165281b9fa2ed1043b0e400](git:%60f04e4369bf9dd3385165281b9fa2ed1043b0e400) \</meta-gplv2/commit/?id=f04e4369bf9dd3385165281b9fa2ed1043b0e400\>\`

> Git 版本：yocto_[git:`f04e4369bf9dd3385165281b9fa2ed1043b0e400`](git:%60f04e4369bf9dd3385165281b9fa2ed1043b0e400%60) </meta-gplv2/commit/?id=f04e4369bf9dd3385165281b9fa2ed1043b0e400>

- Release Artefact: meta-gplv2-f04e4369bf9dd3385165281b9fa2ed1043b0e400
- sha: ef8e2b1ec1fb43dbee4ff6990ac736315c7bc2d8c8e79249e1d337558657d3fe
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/meta-gplv2-f04e4369bf9dd3385165281b9fa2ed1043b0e400.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/meta-gplv2-f04e4369bf9dd3385165281b9fa2ed1043b0e400.tar.bz2), [http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/meta-gplv2-f04e4369bf9dd3385165281b9fa2ed1043b0e400.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/meta-gplv2-f04e4369bf9dd3385165281b9fa2ed1043b0e400.tar.bz2)

> 下载位置：[http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/meta-gplv2-f04e4369bf9dd3385165281b9fa2ed1043b0e400.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/meta-gplv2-f04e4369bf9dd3385165281b9fa2ed1043b0e400.tar.bz2)，[http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/meta-gplv2-f04e4369bf9dd3385165281b9fa2ed1043b0e400.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/meta-gplv2-f04e4369bf9dd3385165281b9fa2ed1043b0e400.tar.bz2)

bitbake

- Repository Location: :oe\_[git:%60/bitbake](git:%60/bitbake)\`
- Branch: :oe\_[git:%601.52](git:%601.52) \</bitbake/log/?h=1.52\>\`
- Tag: :oe\_[git:%60yocto-3.4.4](git:%60yocto-3.4.4) \</bitbake/tag/?h=yocto-3.4.3\>\`
- Git Revision: :oe\_[git:%60c2d8f9b2137bd4a98eb0f51519493131773e7517](git:%60c2d8f9b2137bd4a98eb0f51519493131773e7517) \</bitbake/commit/?id=c2d8f9b2137bd4a98eb0f51519493131773e7517\>\`

> Git 版本：<bitbake/commit/?id=c2d8f9b2137bd4a98eb0f51519493131773e7517>（git:`c2d8f9b2137bd4a98eb0f51519493131773e7517`）

- Release Artefact: bitbake-c2d8f9b2137bd4a98eb0f51519493131773e7517
- sha: a8b6217f2d63975bbf49f430e11046608023ee2827faa893b15d9a0d702cf833
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/bitbake-c2d8f9b2137bd4a98eb0f51519493131773e7517.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/bitbake-c2d8f9b2137bd4a98eb0f51519493131773e7517.tar.bz2), [http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/bitbake-c2d8f9b2137bd4a98eb0f51519493131773e7517.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/bitbake-c2d8f9b2137bd4a98eb0f51519493131773e7517.tar.bz2)

> 下载位置：[http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/bitbake-c2d8f9b2137bd4a98eb0f51519493131773e7517.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-3.4.4/bitbake-c2d8f9b2137bd4a98eb0f51519493131773e7517.tar.bz2)，[http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/bitbake-c2d8f9b2137bd4a98eb0f51519493131773e7517.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-3.4.4/bitbake-c2d8f9b2137bd4a98eb0f51519493131773e7517.tar.bz2)

yocto-docs

- Repository Location: :yocto\_[git:%60/yocto-docs](git:%60/yocto-docs)\`
- Branch: :yocto\_[git:%60honister](git:%60honister) \</yocto-docs/log/?h=honister\>\`
- Tag: :yocto\_[git:%60yocto-3.4.4](git:%60yocto-3.4.4) \</yocto-docs/tag/?h=yocto-3.4.4\>\`
- Git Revision: :yocto\_[git:%605ead7d39aaf9044078dff27f462e29a8e31d89e4](git:%605ead7d39aaf9044078dff27f462e29a8e31d89e4) \</yocto-docs/commit/?5ead7d39aaf9044078dff27f462e29a8e31d89e4\>\`

> Git 版本：yocto_[git:%605ead7d39aaf9044078dff27f462e29a8e31d89e4](git:%605ead7d39aaf9044078dff27f462e29a8e31d89e4) \</yocto-docs/commit/?5ead7d39aaf9044078dff27f462e29a8e31d89e4\>\
