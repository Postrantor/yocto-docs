---
title: Release notes for Yocto-4.0.10 (Kirkstone)
---
# Security Fixes in Yocto-4.0.10

- binutils: Fix `2023-1579`{.interpreted-text role="cve"}, `2023-1972`{.interpreted-text role="cve"}, :cve_mitre:[2023-25584]{.title-ref}, :cve_mitre:[2023-25585]{.title-ref} and :cve_mitre:[2023-25588]{.title-ref}
- cargo : Ignore `2022-46176`{.interpreted-text role="cve"}
- connman: Fix `2023-28488`{.interpreted-text role="cve"}
- curl: Fix `2023-27533`{.interpreted-text role="cve"}, `2023-27534`{.interpreted-text role="cve"}, `2023-27535`{.interpreted-text role="cve"}, `2023-27536`{.interpreted-text role="cve"} and `2023-27538`{.interpreted-text role="cve"}
- ffmpeg: Fix `2022-48434`{.interpreted-text role="cve"}
- freetype: Fix `2023-2004`{.interpreted-text role="cve"}
- ghostscript: Fix :cve_mitre:[2023-29979]{.title-ref}
- git: Fix `2023-25652`{.interpreted-text role="cve"} and `2023-29007`{.interpreted-text role="cve"}
- go: Fix `2022-41722`{.interpreted-text role="cve"}, `2022-41724`{.interpreted-text role="cve"}, `2022-41725`{.interpreted-text role="cve"}, `2023-24534`{.interpreted-text role="cve"}, `2023-24537`{.interpreted-text role="cve"} and `2023-24538`{.interpreted-text role="cve"}
- go: Ignore `2022-41716`{.interpreted-text role="cve"}
- libxml2: Fix `2023-28484`{.interpreted-text role="cve"} and `2023-29469`{.interpreted-text role="cve"}
- libxpm: Fix `2022-44617`{.interpreted-text role="cve"}, `2022-46285`{.interpreted-text role="cve"} and `2022-4883`{.interpreted-text role="cve"}
- linux-yocto: Ignore `2021-3759`{.interpreted-text role="cve"}, `2021-4135`{.interpreted-text role="cve"}, `2021-4155`{.interpreted-text role="cve"}, `2022-0168`{.interpreted-text role="cve"}, `2022-0171`{.interpreted-text role="cve"}, `2022-1016`{.interpreted-text role="cve"}, `2022-1184`{.interpreted-text role="cve"}, `2022-1198`{.interpreted-text role="cve"}, `2022-1199`{.interpreted-text role="cve"}, `2022-1462`{.interpreted-text role="cve"}, `2022-1734`{.interpreted-text role="cve"}, `2022-1852`{.interpreted-text role="cve"}, `2022-1882`{.interpreted-text role="cve"}, `2022-1998`{.interpreted-text role="cve"}, `2022-2078`{.interpreted-text role="cve"}, `2022-2196`{.interpreted-text role="cve"}, `2022-2318`{.interpreted-text role="cve"}, `2022-2380`{.interpreted-text role="cve"}, `2022-2503`{.interpreted-text role="cve"}, `2022-26365`{.interpreted-text role="cve"}, `2022-2663`{.interpreted-text role="cve"}, `2022-2873`{.interpreted-text role="cve"}, `2022-2905`{.interpreted-text role="cve"}, `2022-2959`{.interpreted-text role="cve"}, `2022-3028`{.interpreted-text role="cve"}, `2022-3078`{.interpreted-text role="cve"}, `2022-3104`{.interpreted-text role="cve"}, `2022-3105`{.interpreted-text role="cve"}, `2022-3106`{.interpreted-text role="cve"}, `2022-3107`{.interpreted-text role="cve"}, `2022-3111`{.interpreted-text role="cve"}, `2022-3112`{.interpreted-text role="cve"}, `2022-3113`{.interpreted-text role="cve"}, `2022-3115`{.interpreted-text role="cve"}, `2022-3202`{.interpreted-text role="cve"}, `2022-32250`{.interpreted-text role="cve"}, `2022-32296`{.interpreted-text role="cve"}, `2022-32981`{.interpreted-text role="cve"}, `2022-3303`{.interpreted-text role="cve"}, `2022-33740`{.interpreted-text role="cve"}, `2022-33741`{.interpreted-text role="cve"}, `2022-33742`{.interpreted-text role="cve"}, `2022-33743`{.interpreted-text role="cve"}, `2022-33744`{.interpreted-text role="cve"}, `2022-33981`{.interpreted-text role="cve"}, `2022-3424`{.interpreted-text role="cve"}, `2022-3435`{.interpreted-text role="cve"}, `2022-34918`{.interpreted-text role="cve"}, `2022-3521`{.interpreted-text role="cve"}, `2022-3545`{.interpreted-text role="cve"}, `2022-3564`{.interpreted-text role="cve"}, `2022-3586`{.interpreted-text role="cve"}, `2022-3594`{.interpreted-text role="cve"}, `2022-36123`{.interpreted-text role="cve"}, `2022-3621`{.interpreted-text role="cve"}, `2022-3623`{.interpreted-text role="cve"}, `2022-3629`{.interpreted-text role="cve"}, `2022-3633`{.interpreted-text role="cve"}, `2022-3635`{.interpreted-text role="cve"}, `2022-3646`{.interpreted-text role="cve"}, `2022-3649`{.interpreted-text role="cve"}, `2022-36879`{.interpreted-text role="cve"}, `2022-36946`{.interpreted-text role="cve"}, `2022-3707`{.interpreted-text role="cve"}, `2022-39188`{.interpreted-text role="cve"}, `2022-39190`{.interpreted-text role="cve"}, `2022-39842`{.interpreted-text role="cve"}, `2022-40307`{.interpreted-text role="cve"}, `2022-40768`{.interpreted-text role="cve"}, `2022-4095`{.interpreted-text role="cve"}, `2022-41218`{.interpreted-text role="cve"}, `2022-4139`{.interpreted-text role="cve"}, `2022-41849`{.interpreted-text role="cve"}, `2022-41850`{.interpreted-text role="cve"}, `2022-41858`{.interpreted-text role="cve"}, `2022-42328`{.interpreted-text role="cve"}, `2022-42329`{.interpreted-text role="cve"}, `2022-42703`{.interpreted-text role="cve"}, `2022-42721`{.interpreted-text role="cve"}, `2022-42722`{.interpreted-text role="cve"}, `2022-42895`{.interpreted-text role="cve"}, `2022-4382`{.interpreted-text role="cve"}, `2022-4662`{.interpreted-text role="cve"}, `2022-47518`{.interpreted-text role="cve"}, `2022-47519`{.interpreted-text role="cve"}, `2022-47520`{.interpreted-text role="cve"}, `2022-47929`{.interpreted-text role="cve"}, `2023-0179`{.interpreted-text role="cve"}, `2023-0394`{.interpreted-text role="cve"}, `2023-0461`{.interpreted-text role="cve"}, `2023-0590`{.interpreted-text role="cve"}, `2023-1073`{.interpreted-text role="cve"}, `2023-1074`{.interpreted-text role="cve"}, `2023-1077`{.interpreted-text role="cve"}, `2023-1078`{.interpreted-text role="cve"}, `2023-1079`{.interpreted-text role="cve"}, `2023-1095`{.interpreted-text role="cve"}, `2023-1118`{.interpreted-text role="cve"}, `2023-1249`{.interpreted-text role="cve"}, `2023-1252`{.interpreted-text role="cve"}, `2023-1281`{.interpreted-text role="cve"}, `2023-1382`{.interpreted-text role="cve"}, `2023-1513`{.interpreted-text role="cve"}, `2023-1829`{.interpreted-text role="cve"}, `2023-1838`{.interpreted-text role="cve"}, `2023-1998`{.interpreted-text role="cve"}, `2023-2006`{.interpreted-text role="cve"}, `2023-2008`{.interpreted-text role="cve"}, `2023-2162`{.interpreted-text role="cve"}, `2023-2166`{.interpreted-text role="cve"}, `2023-2177`{.interpreted-text role="cve"}, `2023-22999`{.interpreted-text role="cve"}, `2023-23002`{.interpreted-text role="cve"}, `2023-23004`{.interpreted-text role="cve"}, `2023-23454`{.interpreted-text role="cve"}, `2023-23455`{.interpreted-text role="cve"}, `2023-23559`{.interpreted-text role="cve"}, `2023-25012`{.interpreted-text role="cve"}, `2023-26545`{.interpreted-text role="cve"}, `2023-28327`{.interpreted-text role="cve"} and `2023-28328`{.interpreted-text role="cve"}
- nasm: Fix `2022-44370`{.interpreted-text role="cve"}
- python3-cryptography: Fix `2023-23931`{.interpreted-text role="cve"}
- qemu: Ignore `2023-0664`{.interpreted-text role="cve"}
- ruby: Fix `2023-28755`{.interpreted-text role="cve"} and `2023-28756`{.interpreted-text role="cve"}
- screen: Fix `2023-24626`{.interpreted-text role="cve"}
- shadow: Fix `2023-29383`{.interpreted-text role="cve"}
- tiff: Fix `2022-4645`{.interpreted-text role="cve"}
- webkitgtk: Fix `2022-32888`{.interpreted-text role="cve"} and `2022-32923`{.interpreted-text role="cve"}
- xserver-xorg: Fix `2023-1393`{.interpreted-text role="cve"}

# Fixes in Yocto-4.0.10

- bitbake: bin/utils: Ensure locale en_US.UTF-8 is available on the system
- build-appliance-image: Update to kirkstone head revision
- cmake: add CMAKE_SYSROOT to generated toolchain file
- glibc: stable 2.35 branch updates.
- kernel-devsrc: depend on python3-core instead of python3
- kernel: improve initramfs bundle processing time
- libarchive: Enable acls, xattr for native as well as target
- libbsd: Add correct license for all packages
- libpam: Fix the xtests/tst-pam_motd\[1\|3\] failures
- libxpm: upgrade to 3.5.15
- linux-firmware: upgrade to 20230404
- linux-yocto/5.15: upgrade to v5.15.108
- migration-guides: add release-notes for 4.0.9
- oeqa/utils/metadata.py: Fix running oe-selftest running with no distro set
- openssl: Move microblaze to linux-latomic config
- package.bbclass: correct check for /build in copydebugsources()
- poky.conf: bump version for 4.0.10
- populate_sdk_base: add zip options
- populate_sdk_ext.bbclass: set `METADATA_REVISION`{.interpreted-text role="term"} with an `DISTRO`{.interpreted-text role="term"} override
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

- Repository Location: :yocto\_[git:%60/poky](git:%60/poky)\`
- Branch: :yocto\_[git:%60kirkstone](git:%60kirkstone) \</poky/log/?h=kirkstone\>\`
- Tag: :yocto\_[git:%60yocto-4.0.10](git:%60yocto-4.0.10) \</poky/log/?h=yocto-4.0.10\>\`
- Git Revision: :yocto\_[git:%60f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f](git:%60f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f) \</poky/commit/?id=f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f\>\`
- Release Artefact: poky-f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f
- sha: 8820aeac857ce6bbd1c7ef26cadbb86eca02be93deded253b4a5f07ddd69255d
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/poky-f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/poky-f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/poky-f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/poky-f53ab3a2ff206a130cdc843839dd0ea5ec4ad02f.tar.bz2)

openembedded-core

- Repository Location: :oe\_[git:%60/openembedded-core](git:%60/openembedded-core)\`
- Branch: :oe\_[git:%60kirkstone](git:%60kirkstone) \</openembedded-core/log/?h=kirkstone\>\`
- Tag: :oe\_[git:%60yocto-4.0.10](git:%60yocto-4.0.10) \</openembedded-core/log/?h=yocto-4.0.10\>\`
- Git Revision: :oe\_[git:%60d2713785f9cd2d58731df877bc8b7bcc71b6c8e6](git:%60d2713785f9cd2d58731df877bc8b7bcc71b6c8e6) \</openembedded-core/commit/?id=d2713785f9cd2d58731df877bc8b7bcc71b6c8e6\>\`
- Release Artefact: oecore-d2713785f9cd2d58731df877bc8b7bcc71b6c8e6
- sha: 78e084a1aceaaa6ec022702f29f80eaffade3159e9c42b6b8985c1b7ddd2fbab
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/oecore-d2713785f9cd2d58731df877bc8b7bcc71b6c8e6.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/oecore-d2713785f9cd2d58731df877bc8b7bcc71b6c8e6.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/oecore-d2713785f9cd2d58731df877bc8b7bcc71b6c8e6.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/oecore-d2713785f9cd2d58731df877bc8b7bcc71b6c8e6.tar.bz2)

meta-mingw

- Repository Location: :yocto\_[git:%60/meta-mingw](git:%60/meta-mingw)\`
- Branch: :yocto\_[git:%60kirkstone](git:%60kirkstone) \</meta-mingw/log/?h=kirkstone\>\`
- Tag: :yocto\_[git:%60yocto-4.0.10](git:%60yocto-4.0.10) \</meta-mingw/log/?h=yocto-4.0.10\>\`
- Git Revision: :yocto\_[git:%60a90614a6498c3345704e9611f2842eb933dc51c1](git:%60a90614a6498c3345704e9611f2842eb933dc51c1) \</meta-mingw/commit/?id=a90614a6498c3345704e9611f2842eb933dc51c1\>\`
- Release Artefact: meta-mingw-a90614a6498c3345704e9611f2842eb933dc51c1
- sha: 49f9900bfbbc1c68136f8115b314e95d0b7f6be75edf36a75d9bcd1cca7c6302
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/meta-mingw-a90614a6498c3345704e9611f2842eb933dc51c1.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/meta-mingw-a90614a6498c3345704e9611f2842eb933dc51c1.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/meta-mingw-a90614a6498c3345704e9611f2842eb933dc51c1.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/meta-mingw-a90614a6498c3345704e9611f2842eb933dc51c1.tar.bz2)

meta-gplv2

- Repository Location: :yocto\_[git:%60/meta-gplv2](git:%60/meta-gplv2)\`
- Branch: :yocto\_[git:%60kirkstone](git:%60kirkstone) \</meta-gplv2/log/?h=kirkstone\>\`
- Tag: :yocto\_[git:%60yocto-4.0.10](git:%60yocto-4.0.10) \</meta-gplv2/log/?h=yocto-4.0.10\>\`
- Git Revision: :yocto\_[git:%60d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a](git:%60d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a) \</meta-gplv2/commit/?id=d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a\>\`
- Release Artefact: meta-gplv2-d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a
- sha: c386f59f8a672747dc3d0be1d4234b6039273d0e57933eb87caa20f56b9cca6d
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/meta-gplv2-d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/meta-gplv2-d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/meta-gplv2-d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/meta-gplv2-d2f8b5cdb285b72a4ed93450f6703ca27aa42e8a.tar.bz2)

bitbake

- Repository Location: :oe\_[git:%60/bitbake](git:%60/bitbake)\`
- Branch: :oe\_[git:%602.0](git:%602.0) \</bitbake/log/?h=2.0\>\`
- Tag: :oe\_[git:%60yocto-4.0.10](git:%60yocto-4.0.10) \</bitbake/log/?h=yocto-4.0.10\>\`
- Git Revision: :oe\_[git:%600c6f86b60cfba67c20733516957c0a654eb2b44c](git:%600c6f86b60cfba67c20733516957c0a654eb2b44c) \</bitbake/commit/?id=0c6f86b60cfba67c20733516957c0a654eb2b44c\>\`
- Release Artefact: bitbake-0c6f86b60cfba67c20733516957c0a654eb2b44c
- sha: 4caa94ee4d644017b0cc51b702e330191677f7d179018cbcec8b1793949ebc74
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/bitbake-0c6f86b60cfba67c20733516957c0a654eb2b44c.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.0.10/bitbake-0c6f86b60cfba67c20733516957c0a654eb2b44c.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/bitbake-0c6f86b60cfba67c20733516957c0a654eb2b44c.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.0.10/bitbake-0c6f86b60cfba67c20733516957c0a654eb2b44c.tar.bz2)

yocto-docs

- Repository Location: :yocto\_[git:%60/yocto-docs](git:%60/yocto-docs)\`
- Branch: :yocto\_[git:%60kirkstone](git:%60kirkstone) \</yocto-docs/log/?h=kirkstone\>\`
- Tag: :yocto\_[git:%60yocto-4.0.10](git:%60yocto-4.0.10) \</yocto-docs/log/?h=yocto-4.0.10\>\`
- Git Revision: :yocto\_[git:%608388be749806bd0bf4fccf1005dae8f643aa4ef4](git:%608388be749806bd0bf4fccf1005dae8f643aa4ef4) \</yocto-docs/commit/?id=8388be749806bd0bf4fccf1005dae8f643aa4ef4\>\`
