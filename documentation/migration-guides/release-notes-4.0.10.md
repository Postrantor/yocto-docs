---
title: Release notes for Yocto-4.0.10 (Kirkstone)
---
# Security Fixes in Yocto-4.0.10

- binutils: Fix `2023-1579`
- cargo : Ignore `2022-46176`
- connman: Fix `2023-28488`
- curl: Fix `2023-27533`
- ffmpeg: Fix `2022-48434`
- freetype: Fix `2023-2004`
- ghostscript: Fix :cve_mitre:[2023-29979]
- git: Fix `2023-25652`
- go: Fix `2022-41722`
- go: Ignore `2022-41716`
- libxml2: Fix `2023-28484`
- libxpm: Fix `2022-44617`
- linux-yocto: Ignore `2021-3759`
- nasm: Fix `2022-44370`
- python3-cryptography: Fix `2023-23931`
- qemu: Ignore `2023-0664`
- ruby: Fix `2023-28755`
- screen: Fix `2023-24626`
- shadow: Fix `2023-29383`
- tiff: Fix `2022-4645`
- webkitgtk: Fix `2022-32888`
- xserver-xorg: Fix `2023-1393`

# Fixes in Yocto-4.0.10

- bitbake: bin/utils: Ensure locale en_US.UTF-8 is available on the system
- build-appliance-image: Update to kirkstone head revision
- cmake: add CMAKE_SYSROOT to generated toolchain file
- glibc: stable 2.35 branch updates.
- kernel-devsrc: depend on python3-core instead of python3
- kernel: improve initramfs bundle processing time
- libarchive: Enable acls, xattr for native as well as target
- libbsd: Add correct license for all packages
- libpam: Fix the xtests/tst-pam_motd\[1\|3] failures
- libxpm: upgrade to 3.5.15
- linux-firmware: upgrade to 20230404
- linux-yocto/5.15: upgrade to v5.15.108
- migration-guides: add release-notes for 4.0.9
- oeqa/utils/metadata.py: Fix running oe-selftest running with no distro set
- openssl: Move microblaze to linux-latomic config
- package.bbclass: correct check for /build in copydebugsources()
- poky.conf: bump version for 4.0.10
- populate_sdk_base: add zip options
- populate_sdk_ext.bbclass: set `METADATA_REVISION` override
- run-postinsts: Set dependency for ldconfig to avoid boot issues
- update-alternatives.bbclass: fix old override syntax
- wic/bootimg-efi: if fixed-size is set then use that for mkdosfs
- wpebackend-fdo: upgrade to 1.14.2
- xorg-lib-common: Add variable to set tarball type
- xserver-xorg: upgrade to 21.1.8

# Known Issues in Yocto-4.0.10

- N/A

# Contributors to Yocto-4.0.10

- Archana Polampalli
- Arturo Buzarra
- Bruce Ashfield
- Christoph Lauer
- Deepthi Hemraj
- Dmitry Baryshkov
- Frank de Brabander
- Hitendra Prajapati
- Joe Slater
- Kai Kang
- Kyle Russell
- Lee Chee Yang
- Mark Hatle
- Martin Jansa
- Mingli Yu
- Narpat Mali
- Pascal Bach
- Pawan Badganchi
- Peter Bergin
- Peter Marko
- Piotr Łobacz
- Randolph Sapp
- Ranjitsinh Rathod
- Ross Burton
- Shubham Kulkarni
- Siddharth Doshi
- Steve Sakoman
- Sundeep KOKKONDA
- Thomas Roos
- Virendra Thakur
- Vivek Kumbhar
- Wang Mingyu
- Xiangyu Chen
- Yash Shinde
- Yoann Congal
- Yogita Urade
- Zhixiong Chi

# Repositories / Downloads for Yocto-4.0.10

poky

- Repository Location: :yocto_[git:%60/poky](git:%60/poky)\`
- Branch: :yocto_[git:%60kirkstone](git:%60kirkstone) \</poky/log/?h=kirkstone\>\`
- Tag: :yocto_[git:%60yocto-4.0.10](git:%60yocto-4.0.10) \</poky/log/?h=yocto-4.0.10\>\`
- Git Revision: :yocto_[git:%60f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f](git:%60f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f) \</poky/commit/?id=f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f\>\`
- Release Artefact: poky-f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f
- sha: 8820aeac857ce6bbd1c7ef26cadbb86eca02be93deded253b4a5f07ddd69255d
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/poky-f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/poky-f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/poky-f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/poky-f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f.tar.bz2)

openembedded-core

- Repository Location: :oe_[git:%60/openembedded-core](git:%60/openembedded-core)\`
- Branch: :oe_[git:%60kirkstone](git:%60kirkstone) \</openembedded-core/log/?h=kirkstone\>\`
- Tag: :oe_[git:%60yocto-4.0.10](git:%60yocto-4.0.10) \</openembedded-core/log/?h=yocto-4.0.10\>\`
- Git Revision: :oe_[git:%60d2713785f9cd2d58731df877bc8b7bcc71b6c8e6](git:%60d2713785f9cd2d58731df877bc8b7bcc71b6c8e6) \</openembedded-core/commit/?id=d2713785f9cd2d58731df877bc8b7bcc71b6c8e6\>\`
- Release Artefact: oecore-d2713785f9cd2d58731df877bc8b7bcc71b6c8e6
- sha: 78e084a1aceaaa6ec022702f29f80eaffade3159e9c42b6b8985c1b7ddd2fbab
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/oecore-d2713785f9cd2d58731df877bc8b7bcc71b6c8e6.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/oecore-d2713785f9cd2d58731df877bc8b7bcc71b6c8e6.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/oecore-d2713785f9cd2d58731df877bc8b7bcc71b6c8e6.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/oecore-d2713785f9cd2d58731df877bc8b7bcc71b6c8e6.tar.bz2)

meta-mingw

- Repository Location: :yocto_[git:%60/meta-mingw](git:%60/meta-mingw)\`
- Branch: :yocto_[git:%60kirkstone](git:%60kirkstone) \</meta-mingw/log/?h=kirkstone\>\`
- Tag: :yocto_[git:%60yocto-4.0.10](git:%60yocto-4.0.10) \</meta-mingw/log/?h=yocto-4.0.10\>\`
- Git Revision: :yocto_[git:%60a90614a6498c3345704e9611f2842eb933dc51c1](git:%60a90614a6498c3345704e9611f2842eb933dc51c1) \</meta-mingw/commit/?id=a90614a6498c3345704e9611f2842eb933dc51c1\>\`
- Release Artefact: meta-mingw-a90614a6498c3345704e9611f2842eb933dc51c1
- sha: 49f9900bfbbc1c68136f8115b314e95d0b7f6be75edf36a75d9bcd1cca7c6302
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/meta-mingw-a90614a6498c3345704e9611f2842eb933dc51c1.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/meta-mingw-a90614a6498c3345704e9611f2842eb933dc51c1.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/meta-mingw-a90614a6498c3345704e9611f2842eb933dc51c1.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/meta-mingw-a90614a6498c3345704e9611f2842eb933dc51c1.tar.bz2)

meta-gplv2

- Repository Location: :yocto_[git:%60/meta-gplv2](git:%60/meta-gplv2)\`
- Branch: :yocto_[git:%60kirkstone](git:%60kirkstone) \</meta-gplv2/log/?h=kirkstone\>\`
- Tag: :yocto_[git:%60yocto-4.0.10](git:%60yocto-4.0.10) \</meta-gplv2/log/?h=yocto-4.0.10\>\`
- Git Revision: :yocto_[git:%60d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a](git:%60d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a) \</meta-gplv2/commit/?id=d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a\>\`
- Release Artefact: meta-gplv2-d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a
- sha: c386f59f8a672747dc3d0be1d4234b6039273d0e57933eb87caa20f56b9cca6d
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/meta-gplv2-d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/meta-gplv2-d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/meta-gplv2-d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/meta-gplv2-d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a.tar.bz2)

bitbake

- Repository Location: :oe_[git:%60/bitbake](git:%60/bitbake)\`
- Branch: :oe_[git:%602.0](git:%602.0) \</bitbake/log/?h=2.0\>\`
- Tag: :oe_[git:%60yocto-4.0.10](git:%60yocto-4.0.10) \</bitbake/log/?h=yocto-4.0.10\>\`
- Git Revision: :oe_[git:%600c6f86b60cfba67c20733516957c0a654eb2b44c](git:%600c6f86b60cfba67c20733516957c0a654eb2b44c) \</bitbake/commit/?id=0c6f86b60cfba67c20733516957c0a654eb2b44c\>\`
- Release Artefact: bitbake-0c6f86b60cfba67c20733516957c0a654eb2b44c
- sha: 4caa94ee4d644017b0cc51b702e330191677f7d179018cbcec8b1793949ebc74
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/bitbake-0c6f86b60cfba67c20733516957c0a654eb2b44c.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/bitbake-0c6f86b60cfba67c20733516957c0a654eb2b44c.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/bitbake-0c6f86b60cfba67c20733516957c0a654eb2b44c.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/bitbake-0c6f86b60cfba67c20733516957c0a654eb2b44c.tar.bz2)

yocto-docs

- Repository Location: :yocto_[git:%60/yocto-docs](git:%60/yocto-docs)\`
- Branch: :yocto_[git:%60kirkstone](git:%60kirkstone) \</yocto-docs/log/?h=kirkstone\>\`
- Tag: :yocto_[git:%60yocto-4.0.10](git:%60yocto-4.0.10) \</yocto-docs/log/?h=yocto-4.0.10\>\`
- Git Revision: :yocto_[git:%608388be749806bd0bf4fccf1005dae8f643aa4ef4](git:%608388be749806bd0bf4fccf1005dae8f643aa4ef4) \</yocto-docs/commit/?id=8388be749806bd0bf4fccf1005dae8f643aa4ef4\>\`
