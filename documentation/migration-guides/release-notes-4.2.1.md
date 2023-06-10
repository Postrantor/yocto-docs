---
tip: translate by openai@2023-06-07 23:23:50
...
---
title: Release notes for Yocto-4.2.1 (Mickledore)
---
# Security Fixes in Yocto-4.2.1

- connman: Fix `2023-28488`{.interpreted-text role="cve"}
- linux-yocto: Ignore `2023-1652`{.interpreted-text role="cve"} and `2023-1829`{.interpreted-text role="cve"}
- ghostscript: Fix `2023-28879`{.interpreted-text role="cve"}
- qemu: Ignore `2023-0664`{.interpreted-text role="cve"}
- ruby: Fix `2022-28738`{.interpreted-text role="cve"} and `2022-28739`{.interpreted-text role="cve"}
- tiff: Fix `2022-4645`{.interpreted-text role="cve"}
- xwayland: Fix `2023-1393`{.interpreted-text role="cve"}

# Fixes in Yocto-4.2.1

- apr: upgrade to 1.7.3
- bind: upgrade to 9.18.13
- build-appliance-image: Update to mickledore head revision
- cargo: Fix build on musl/riscv
- cpio: fix appending to archives larger than 2GB
- cracklib: upgrade to 2.9.11
- cve-update-nvd2-native: added the missing http import
- dev-manual: init-manager.rst: add summary
- dhcpcd: use git instead of tarballs
- docs: add support for mickledore (4.2) release
- gawk: Add skipped.txt to emit test to ignore
- gawk: Disable known ptest fails on musl
- gawk: Remove redundant patch
- glib-networking: Add test retry to avoid failures
- glib-networking: Correct glib error handling in test patch
- gtk4: upgrade to 4.10.3
- kernel-devsrc: depend on python3-core instead of python3
- kernel-fitimage: Fix the default dtb config check
- kernel: improve initramfs bundle processing time
- libarchive: Enable acls, xattr for native as well as target
- libhandy: upgrade to 1.8.2
- libnotify: remove dependency dbus
- libpam: Fix the xtests/tst-pam_motd\[1\|3\] failures
- libpcap: upgrade to 1.10.4
- libsdl2: upgrade to 2.26.5
- libxml2: Disable icu tests on musl
- license.bbclass: Include `LICENSE`{.interpreted-text role="term"} in the output when it fails to parse
- linux-firmware: upgrade to 20230404
- machine/qemuarm\*: don\'t explicitly set vmalloc
- maintainers.inc: Fix email address typo
- maintainers.inc: Move repo to unassigned
- man-pages: upgrade to 6.04
- manuals: document `SPDX_CUSTOM_ANNOTATION_VARS`{.interpreted-text role="term"}
- manuals: expand init manager documentation
- mesa: upgrade to 23.0.3
- migration-guides: add release-notes for 4.1.4
- migration-guides: fixes and improvements to 4.2 release notes
- migration-guides: release-notes-4.0.9.rst: add missing `SPDX`{.interpreted-text role="term"} info
- migration-guides: release-notes-4.2: add doc improvement highlights
- mpg123: upgrade to 1.31.3
- mtools: upgrade to 4.0.43
- oeqa/utils/metadata.py: Fix running oe-selftest running with no distro set
- overview-manual: development-environment: update text and screenshots
- overview-manual: update section about source archives
- package_manager/ipk: fix config path generation in \_create_custom_config()
- pango: upgrade to 1.50.14
- perl: patch out build paths from native binaries
- poky.conf: bump version for 4.2.1 release
- populate_sdk_ext.bbclass: redirect stderr to stdout so that both end in LOGFILE
- populate_sdk_ext.bbclass: set `METADATA_REVISION`{.interpreted-text role="term"} with an `DISTRO`{.interpreted-text role="term"} override
- python3targetconfig.bbclass: Extend PYTHONPATH instead of overwriting
- qemu: Add fix for powerpc instruction fallback issue
- qemu: Update ppc instruction fix to match revised upstream version
- quilt: Fix merge.test race condition
- recipes: Default to https git protocol where possible
- ref-manual: add \"Mixin\" term
- ref-manual: classes.rst: document devicetree.bbclass
- ref-manual: classes: kernel: document automatic defconfig usage
- ref-manual: classes: kernel: remove incorrect sentence opening
- ref-manual: remove unused and obsolete file
- ref-manual: system-requirements.rst: fix AlmaLinux variable name
- ref-manual: variables.rst: add wikipedia shortcut for \"getty\"
- ref-manual: variables.rst: document `KERNEL_DANGLING_FEATURES_WARN_ONLY`{.interpreted-text role="term"}
- ref-manual: variables.rst: don\'t mention the `INIT_MANAGER`{.interpreted-text role="term"} \"none\" option
- release-notes-4.2: remove/merge duplicates entries
- release-notes-4.2: update RC3 changes
- release-notes-4.2: update known issues and Repositories/Downloads
- releases.svg: fix and explain duration of Hardknott 3.3
- ruby: upgrade to 3.2.2
- rust: upgrade to 1.68.2
- selftest/distrodata: clean up exception lists in recipe maintainers test
- systemd-systemctl: fix instance template WantedBy symlink construction
- texinfo: upgrade to 7.0.3
- unfs3: fix symlink time setting issue
- update-alternatives.bbclass: fix old override syntax
- vala: upgrade to 0.56.6
- waffle: upgrade to 1.7.2
- weston: add xwayland to `DEPENDS`{.interpreted-text role="term"} for `PACKAGECONFIG`{.interpreted-text role="term"} xwayland
- wpebackend-fdo: upgrade to 1.14.2
- xserver-xorg: upgrade to 21.1.8
- xwayland: upgrade to 23.1.1

# Known Issues in Yocto-4.2.1

- N/A

# Contributors to Yocto-4.2.1

- Alex Kiernan
- Alexander Kanavin
- Arslan Ahmad
- Bruce Ashfield
- Chen Qi
- Dmitry Baryshkov
- Enrico Jörns
- Jan Vermaete
- Joe Slater
- Johannes Schrimpf
- Kai Kang
- Khem Raj
- Kyle Russell
- Lee Chee Yang
- Luca Ceresoli
- Markus Volk
- Martin Jansa
- Martin Siegumfeldt
- Michael Halstead
- Michael Opdenacker
- Ming Liu
- Otavio Salvador
- Pawan Badganchi
- Peter Bergin
- Peter Kjellerstedt
- Piotr Łobacz
- Richard Purdie
- Ross Burton
- Steve Sakoman
- Thomas Roos
- Virendra Thakur
- Wang Mingyu
- Yoann Congal
- Zhixiong Chi

# Repositories / Downloads for Yocto-4.2.1

poky

- Repository Location: :yocto\_[git:%60/poky](git:%60/poky)\`
- Branch: :yocto\_[git:%60mickledore](git:%60mickledore) \</poky/log/?h=mickledore\>\`
- Tag: :yocto\_[git:%60yocto-4.2.1](git:%60yocto-4.2.1) \</poky/log/?h=yocto-4.2.1\>\`

- Git Revision: :yocto\_[git:%60c5c69f78fc7ce4ba361363c14352e4264ce7813f](git:%60c5c69f78fc7ce4ba361363c14352e4264ce7813f) \</poky/commit/?id=c5c69f78fc7ce4ba361363c14352e4264ce7813f\>\`

> Git版本：:yocto_<poky/commit/?id=c5c69f78fc7ce4ba361363c14352e4264ce7813f>（git:`c5c69f78fc7ce4ba361363c14352e4264ce7813f`）
- Release Artefact: poky-c5c69f78fc7ce4ba361363c14352e4264ce7813f
- sha: 057d7771dceebb949a79359d7d028a733a29ae7ecd98b60fefcff83fecb22eb7

- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/poky-c5c69f78fc7ce4ba361363c14352e4264ce7813f.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/poky-c5c69f78fc7ce4ba361363c14352e4264ce7813f.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/poky-c5c69f78fc7ce4ba361363c14352e4264ce7813f.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/poky-c5c69f78fc7ce4ba361363c14352e4264ce7813f.tar.bz2)

> 下载位置：[http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/poky-c5c69f78fc7ce4ba361363c14352e4264ce7813f.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/poky-c5c69f78fc7ce4ba361363c14352e4264ce7813f.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/poky-c5c69f78fc7ce4ba361363c14352e4264ce7813f.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/poky-c5c69f78fc7ce4ba361363c14352e4264ce7813f.tar.bz2)

openembedded-core

- Repository Location: :oe\_[git:%60/openembedded-core](git:%60/openembedded-core)\`
- Branch: :oe\_[git:%60mickledore](git:%60mickledore) \</openembedded-core/log/?h=mickledore\>\`
- Tag: :oe\_[git:%60yocto-4.2.1](git:%60yocto-4.2.1) \</openembedded-core/log/?h=yocto-4.2.1\>\`

- Git Revision: :oe\_[git:%6020cd64812d286c920bd766145ab1cd968e72667e](git:%6020cd64812d286c920bd766145ab1cd968e72667e) \</openembedded-core/commit/?id=20cd64812d286c920bd766145ab1cd968e72667e\>\`

> Git版本：<openembedded-core/commit/?id=20cd64812d286c920bd766145ab1cd968e72667e>（git：`20cd64812d286c920bd766145ab1cd968e72667e`）
- Release Artefact: oecore-20cd64812d286c920bd766145ab1cd968e72667e
- sha: 877fb909af7aa51e1c962d33cfe91ba3e075c384716006aa1345b4bcb15a48ef

- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/oecore-20cd64812d286c920bd766145ab1cd968e72667e.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/oecore-20cd64812d286c920bd766145ab1cd968e72667e.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/oecore-20cd64812d286c920bd766145ab1cd968e72667e.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/oecore-20cd64812d286c920bd766145ab1cd968e72667e.tar.bz2)

> 下载位置：[http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/oecore-20cd64812d286c920bd766145ab1cd968e72667e.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/oecore-20cd64812d286c920bd766145ab1cd968e72667e.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/oecore-20cd64812d286c920bd766145ab1cd968e72667e.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/oecore-20cd64812d286c920bd766145ab1cd968e72667e.tar.bz2)

meta-mingw

- Repository Location: :yocto\_[git:%60/meta-mingw](git:%60/meta-mingw)\`
- Branch: :yocto\_[git:%60mickledore](git:%60mickledore) \</meta-mingw/log/?h=mickledore\>\`
- Tag: :yocto\_[git:%60yocto-4.2.1](git:%60yocto-4.2.1) \</meta-mingw/log/?h=yocto-4.2.1\>\`

- Git Revision: :yocto\_[git:%60cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e](git:%60cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e) \</meta-mingw/commit/?id=cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e\>\`

> - Git 修订：yocto_[git:`cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e`](git:`cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e`) \</meta-mingw/commit/?id=cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e\>\`
- Release Artefact: meta-mingw-cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e
- sha: 69ccc3ee503b5c35602889e85d28df64a5422ad0f1e55c96c94135b837bb4a1c

- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/meta-mingw-cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/meta-mingw-cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/meta-mingw-cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/meta-mingw-cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e.tar.bz2)

> 下载位置：[http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/meta-mingw-cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/meta-mingw-cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/meta-mingw-cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/meta-mingw-cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e.tar.bz2) 下载地址：[http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/meta-mingw-cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/meta-mingw-cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/meta-mingw-cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/meta-mingw-cc9fd0a988dc1041035a6a6cafb2d1237ef38d8e.tar.bz2)

bitbake

- Repository Location: :oe\_[git:%60/bitbake](git:%60/bitbake)\`
- Branch: :oe\_[git:%602.4](git:%602.4) \</bitbake/log/?h=2.4\>\`
- Tag: :oe\_[git:%60yocto-4.2.1](git:%60yocto-4.2.1) \</bitbake/log/?h=yocto-4.2.1\>\`

- Git Revision: :oe\_[git:%60d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c](git:%60d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c) \</bitbake/commit/?id=d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c\>\`

> Git 修订：<bitbake/commit/?id=d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c>（git:`d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c）
- Release Artefact: bitbake-d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c
- sha: 5edcb97cb545011226b778355bb840ebcc790552d4a885a0d83178153697ba7a

- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/bitbake-d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/bitbake-d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/bitbake-d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/bitbake-d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c.tar.bz2)

> 下载位置：[http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/bitbake-d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/bitbake-d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/bitbake-d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/bitbake-d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c.tar.bz2)

简体中文：下载位置：[http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/bitbake-d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.2.1/bitbake-d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/bitbake-d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.2.1/bitbake-d97d62e2cbe4bae17f0886f3b4759e8f9ba6d38c.tar.bz2)

yocto-docs

- Repository Location: :yocto\_[git:%60/yocto-docs](git:%60/yocto-docs)\`
- Branch: :yocto\_[git:%60mickledore](git:%60mickledore) \</yocto-docs/log/?h=mickledore\>\`
- Tag: :yocto\_[git:%60yocto-4.2.1](git:%60yocto-4.2.1) \</yocto-docs/log/?h=yocto-4.2.1\>\`

- Git Revision: :yocto\_[git:%606b04269bba72311e83139cc88b7a3539a5d832e8](git:%606b04269bba72311e83139cc88b7a3539a5d832e8) \</yocto-docs/commit/?id=6b04269bba72311e83139cc88b7a3539a5d832e8\>\`

> Git 修订：yocto_[git:%606b04269bba72311e83139cc88b7a3539a5d832e8](git:%606b04269bba72311e83139cc88b7a3539a5d832e8) \</yocto-docs/commit/?id=6b04269bba72311e83139cc88b7a3539a5d832e8\>
