---
title: Release notes for Yocto-4.1.4 (Langdale)
---
# Security Fixes in Yocto-4.1.4

- cve-extra-exclusions/linux-yocto: Ignore `2020-27784`{.interpreted-text role="cve"}, `2021-3669`{.interpreted-text role="cve"}, `2021-3759`{.interpreted-text role="cve"}, `2021-4218`{.interpreted-text role="cve"}, `2022-0480`{.interpreted-text role="cve"}, `2022-1184`{.interpreted-text role="cve"}, `2022-1462`{.interpreted-text role="cve"}, `2022-2308`{.interpreted-text role="cve"}, `2022-2327`{.interpreted-text role="cve"}, `2022-26365`{.interpreted-text role="cve"}, `2022-2663`{.interpreted-text role="cve"}, `2022-2785`{.interpreted-text role="cve"}, `2022-3176`{.interpreted-text role="cve"}, `2022-33740`{.interpreted-text role="cve"}, `2022-33741`{.interpreted-text role="cve"}, `2022-33742`{.interpreted-text role="cve"}, `2022-3526`{.interpreted-text role="cve"}, `2022-3563`{.interpreted-text role="cve"}, `2022-3621`{.interpreted-text role="cve"}, `2022-3623`{.interpreted-text role="cve"}, `2022-3624`{.interpreted-text role="cve"}, `2022-3625`{.interpreted-text role="cve"}, `2022-3629`{.interpreted-text role="cve"}, `2022-3630`{.interpreted-text role="cve"}, `2022-3633`{.interpreted-text role="cve"}, `2022-3635`{.interpreted-text role="cve"}, `2022-3636`{.interpreted-text role="cve"}, `2022-3637`{.interpreted-text role="cve"}, `2022-3646`{.interpreted-text role="cve"} and `2022-3649`{.interpreted-text role="cve"}
- cve-extra-exclusions/linux-yocto 5.15: Ignore `2022-3435`{.interpreted-text role="cve"}, `2022-3534`{.interpreted-text role="cve"}, `2022-3564`{.interpreted-text role="cve"}, `2022-3564`{.interpreted-text role="cve"}, `2022-3619`{.interpreted-text role="cve"}, `2022-3640`{.interpreted-text role="cve"}, `2022-42895`{.interpreted-text role="cve"}, `2022-42896`{.interpreted-text role="cve"}, `2022-4382`{.interpreted-text role="cve"}, `2023-0266`{.interpreted-text role="cve"} and `2023-0394`{.interpreted-text role="cve"}
- epiphany: Fix `2023-26081`{.interpreted-text role="cve"}
- git: Ignore `2023-22743`{.interpreted-text role="cve"}
- go: Fix `2022-41722`{.interpreted-text role="cve"}, `2022-41723`{.interpreted-text role="cve"}, `2022-41724`{.interpreted-text role="cve"}, `2022-41725`{.interpreted-text role="cve"} and `2023-24532`{.interpreted-text role="cve"}
- harfbuzz: Fix `2023-25193`{.interpreted-text role="cve"}
- libmicrohttpd: Fix `2023-27371`{.interpreted-text role="cve"}
- libxml2: Fix `2022-40303`{.interpreted-text role="cve"} and `2022-40304`{.interpreted-text role="cve"}
- openssl: Fix `2023-0464`{.interpreted-text role="cve"}, `2023-0465`{.interpreted-text role="cve"} and `2023-0466`{.interpreted-text role="cve"}
- python3-setuptools: Fix `2022-40897`{.interpreted-text role="cve"}
- qemu: Fix `2022-4144`{.interpreted-text role="cve"}
- screen: Fix `2023-24626`{.interpreted-text role="cve"}
- shadow: Ignore `2016-15024`{.interpreted-text role="cve"}
- tiff: Fix `2022-48281`{.interpreted-text role="cve"}, `2023-0795`{.interpreted-text role="cve"}, `2023-0796`{.interpreted-text role="cve"}, `2023-0797`{.interpreted-text role="cve"}, `2023-0798`{.interpreted-text role="cve"}, `2023-0799`{.interpreted-text role="cve"}, `2023-0800`{.interpreted-text role="cve"}, `2023-0801`{.interpreted-text role="cve"}, `2023-0802`{.interpreted-text role="cve"}, `2023-0803`{.interpreted-text role="cve"} and `2023-0804`{.interpreted-text role="cve"}
- vim: Fix `2023-1127`{.interpreted-text role="cve"}, `2023-1170`{.interpreted-text role="cve"}, `2023-1175`{.interpreted-text role="cve"}, `2023-1264`{.interpreted-text role="cve"} and `2023-1355`{.interpreted-text role="cve"}
- xdg-utils: Fix `2022-4055`{.interpreted-text role="cve"}
- xserver-xorg: Fix for `2023-1393`{.interpreted-text role="cve"}

# Fixes in Yocto-4.1.4

- apt: re-enable version check
- base-files: Drop localhost.localdomain from hosts file
- binutils: Fix nativesdk ld.so search
- bitbake: bin/utils: Ensure locale en_US.UTF-8 is available on the system
- bitbake: cookerdata: Drop dubious exception handling code
- bitbake: cookerdata: Improve early exception handling
- bitbake: cookerdata: Remove incorrect SystemExit usage
- bitbake: fetch/git: Fix local clone url to make it work with repo
- bitbake: toaster: Add refreshed oe-core and poky fixtures
- bitbake: toaster: fixtures/README: django 1.8 -\> 3.2
- bitbake: toaster: fixtures/gen_fixtures.py: update branches
- bitbake: utils: Allow to_boolean to support int values
- bmap-tools: switch to main branch
- build-appliance-image: Update to langdale head revision
- buildtools-tarball: Handle spaces within user \$PATH
- busybox: move hwclock init earlier in startup
- cargo.bbclass: use offline mode for building
- cpio: Fix wrong CRC with ASCII CRC for large files
- cracklib: update github branch to \'main\'
- cups: add/fix web interface packaging
- cups: check `PACKAGECONFIG`{.interpreted-text role="term"} for pam feature
- cups: use BUILDROOT instead of DESTDIR
- cve-check: Fix false negative version issue
- devtool/upgrade: do not delete the workspace/recipes directory
- dhcpcd: Fix install conflict when enable multilib.
- ffmpeg: fix build failure when vulkan is enabled
- filemap.py: enforce maximum of 4kb block size
- gcc-shared-source: do not use \${S}/.. in deploy_source_date_epoch
- glibc: Add missing binutils dependency
- go: upgrade to 1.19.7
- image_types: fix multiubi var init
- image_types: fix vname var init in multiubi_mkfs() function
- iso-codes: upgrade to 4.13.0
- kernel-devsrc: fix mismatched compiler warning
- lib/oe/gpg_sign.py: Avoid race when creating .sig files in detach_sign
- lib/resulttool: fix typo breaking resulttool log \--ptest
- libcomps: Fix callback function prototype for PyCOMPS_hash
- libdnf: upgrade to 0.70.0
- libgit2: update license information
- libmicrohttpd: upgrade to 0.9.76
- linux-yocto-rt/5.15: upgrade to -rt59
- linux-yocto/5.15: upgrade to v5.15.108
- linux: inherit pkgconfig in kernel.bbclass
- lttng-modules: upgrade to v2.13.9
- lua: Fix install conflict when enable multilib.
- mdadm: Fix raid0, 06wrmostly and 02lineargrow tests
- mesa-demos: packageconfig weston should have a dependency on wayland-protocols
- meson: Fix wrapper handling of implicit setup command
- meson: remove obsolete RPATH stripping patch
- migration-guides: update release notes
- oeqa ping.py: avoid busylooping failing ping command
- oeqa ping.py: fail test if target IP address has not been set
- oeqa rtc.py: skip if read-only-rootfs
- oeqa/runtime: clean up deprecated backslash expansion
- oeqa/sdk: Improve Meson test
- oeqa/selftest/cases/package.py: adding unittest for package rename conflicts
- oeqa/selftest/cases/runqemu: update imports
- oeqa/selftest/prservice: Improve debug output for failure
- oeqa/selftest/reproducible: Split different packages from missing packages output
- oeqa/selftest: OESelftestTestContext: convert relative to full path when newbuilddir is provided
- oeqa/targetcontrol: do not set dump_host_cmds redundantly
- oeqa/targetcontrol: fix misspelled RuntimeError
- oeqa/targetcontrol: remove unused imports
- oeqa/utils/commands: fix usage of undefined EPIPE
- oeqa/utils/commands: remove unused imports
- oeqa/utils/qemurunner: replace hard-coded user \'root\' in debug output
- oeqs/selftest: OESelftestTestContext: replace the os.environ after subprocess.check_output
- package.bbclass: check packages name conflict in do_package
- pango: upgrade to 1.50.13
- piglit: Fix build time dependency
- poky.conf: bump version for 4.1.4
- populate_sdk_base: add zip options
- populate_sdk_ext: Handle spaces within user \$PATH
- pybootchart: Fix extents handling to account for cpu/io/mem pressure changes
- pybootchartui: Fix python syntax issue
- report-error: catch Nothing `PROVIDES`{.interpreted-text role="term"} error
- rpm: Fix hdr_hash function prototype
- run-postinsts: Set dependency for ldconfig to avoid boot issues
- runqemu: respect `IMAGE_LINK_NAME`{.interpreted-text role="term"}
- runqemu: Revert \"workaround for APIC hang on pre 4.15 kernels on qemux86q\"
- scripts/lib/buildstats: handle top-level build_stats not being complete
- selftest/recipetool: Stop test corrupting tinfoil class
- selftest/runtime_test/virgl: Disable for all Rocky Linux
- selftest: devtool: set `BB_HASHSERVE_UPSTREAM`{.interpreted-text role="term"} when setting `SSTATE_MIRRORS`{.interpreted-text role="term"}
- selftest: runqemu: better check for ROOTFS: in the log
- selftest: runqemu: use better error message when asserts fail
- shadow: Fix can not print full login timeout message
- staging/multilib: Fix manifest corruption
- staging: Separate out different multiconfig manifests
- sudo: upgrade to 1.9.13p3
- systemd.bbclass: Add /usr/lib/systemd to searchpaths as well
- systemd: add group sgx to udev package
- systemd: fix wrong nobody-group assignment
- timezone: use \'tz\' subdir instead of \${WORKDIR} directly
- toolchain-scripts: Handle spaces within user \$PATH
- tzcode-native: fix build with gcc-13 on host
- tzdata: upgrade to 2023c
- tzdata: use separate `B`{.interpreted-text role="term"} instead of `WORKDIR`{.interpreted-text role="term"} for zic output
- u-boot: Map arm64 into map for u-boot dts installation
- uninative: Upgrade to 3.9 to include glibc 2.37
- vala: Fix install conflict when enable multilib.
- vim: add missing pkgconfig inherit
- vim: set modified-by to the recipe `MAINTAINER`{.interpreted-text role="term"}
- vim: upgrade to 9.0.1429
- xcb-proto: Fix install conflict when enable multilib.

# Known Issues in Yocto-4.1.4

- N/A

# Contributors to Yocto-4.1.4

- Alexander Kanavin
- Andrew Geissler
- Arturo Buzarra
- Bhabu Bindu
- Bruce Ashfield
- Carlos Alberto Lopez Perez
- Chee Yang Lee
- Chris Elledge
- Christoph Lauer
- Dmitry Baryshkov
- Enrico Jörns
- Fawzi KHABER
- Frank de Brabander
- Frederic Martinsons
- Geoffrey GIRY
- Hitendra Prajapati
- Jose Quaresma
- Kenfe-Mickael Laventure
- Khem Raj
- Marek Vasut
- Martin Jansa
- Michael Halstead
- Michael Opdenacker
- Mikko Rapeli
- Ming Liu
- Mingli Yu
- Narpat Mali
- Pavel Zhukov
- Peter Marko
- Piotr Łobacz
- Randy MacLeod
- Richard Purdie
- Robert Yang
- Romuald JEANNE
- Romuald Jeanne
- Ross Burton
- Siddharth
- Siddharth Doshi
- Soumya
- Steve Sakoman
- Sudip Mukherjee
- Tim Orling
- Tobias Hagelborn
- Tom Hochstein
- Trevor Woerner
- Wang Mingyu
- Xiangyu Chen
- Zoltan Boszormenyi

# Repositories / Downloads for Yocto-4.1.4

poky

- Repository Location: :yocto\_[git:%60/poky](git:%60/poky)\`
- Branch: :yocto\_[git:%60langdale](git:%60langdale) \</poky/log/?h=langdale\>\`
- Tag: :yocto\_[git:%60yocto-4.1.4](git:%60yocto-4.1.4) \</poky/log/?h=yocto-4.1.4\>\`
- Git Revision: :yocto\_[git:%603e95f268ce04b49ba6731fd4bbc53b1693c21963](git:%603e95f268ce04b49ba6731fd4bbc53b1693c21963) \</poky/commit/?id=3e95f268ce04b49ba6731fd4bbc53b1693c21963\>\`
- Release Artefact: poky-3e95f268ce04b49ba6731fd4bbc53b1693c21963
- sha: 54798c4b519f5e11f409e1fd074bea1bc0a1b80672aa60dddbac772c8e4d838b
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.1.4/poky-3e95f268ce04b49ba6731fd4bbc53b1693c21963.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.1.4/poky-3e95f268ce04b49ba6731fd4bbc53b1693c21963.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.1.4/poky-3e95f268ce04b49ba6731fd4bbc53b1693c21963.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.1.4/poky-3e95f268ce04b49ba6731fd4bbc53b1693c21963.tar.bz2)

openembedded-core

- Repository Location: :oe\_[git:%60/openembedded-core](git:%60/openembedded-core)\`
- Branch: :oe\_[git:%60langdale](git:%60langdale) \</openembedded-core/log/?h=langdale\>\`
- Tag: :oe\_[git:%60yocto-4.1.4](git:%60yocto-4.1.4) \</openembedded-core/log/?h=yocto-4.1.4\>\`
- Git Revision: :oe\_[git:%6078211cda40eb018a3aa535c75b61e87337236628](git:%6078211cda40eb018a3aa535c75b61e87337236628) \</openembedded-core/commit/?id=78211cda40eb018a3aa535c75b61e87337236628\>\`
- Release Artefact: oecore-78211cda40eb018a3aa535c75b61e87337236628
- sha: 1303d836bae54c438c64d6b9f068eb91c32be4cc1779e89d0f2d915a55d59b15
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.1.4/oecore-78211cda40eb018a3aa535c75b61e87337236628.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.1.4/oecore-78211cda40eb018a3aa535c75b61e87337236628.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.1.4/oecore-78211cda40eb018a3aa535c75b61e87337236628.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.1.4/oecore-78211cda40eb018a3aa535c75b61e87337236628.tar.bz2)

meta-mingw

- Repository Location: :yocto\_[git:%60/meta-mingw](git:%60/meta-mingw)\`
- Branch: :yocto\_[git:%60langdale](git:%60langdale) \</meta-mingw/log/?h=langdale\>\`
- Tag: :yocto\_[git:%60yocto-4.1.4](git:%60yocto-4.1.4) \</meta-mingw/log/?h=yocto-4.1.4\>\`
- Git Revision: :yocto\_[git:%60b0067202db8573df3d23d199f82987cebe1bee2c](git:%60b0067202db8573df3d23d199f82987cebe1bee2c) \</meta-mingw/commit/?id=b0067202db8573df3d23d199f82987cebe1bee2c\>\`
- Release Artefact: meta-mingw-b0067202db8573df3d23d199f82987cebe1bee2c
- sha: 704f2940322b81ce774e9cbd27c3cfa843111d497dc7b1eeaa39cd694d9a2366
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.1.4/meta-mingw-b0067202db8573df3d23d199f82987cebe1bee2c.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.1.4/meta-mingw-b0067202db8573df3d23d199f82987cebe1bee2c.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.1.4/meta-mingw-b0067202db8573df3d23d199f82987cebe1bee2c.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.1.4/meta-mingw-b0067202db8573df3d23d199f82987cebe1bee2c.tar.bz2)

bitbake

- Repository Location: :oe\_[git:%60/bitbake](git:%60/bitbake)\`
- Branch: :oe\_[git:%602.2](git:%602.2) \</bitbake/log/?h=2.2\>\`
- Tag: :oe\_[git:%60yocto-4.1.4](git:%60yocto-4.1.4) \</bitbake/log/?h=yocto-4.1.4\>\`
- Git Revision: :oe\_[git:%605b105e76dd7de3b9a25b17b397f2c12c80048894](git:%605b105e76dd7de3b9a25b17b397f2c12c80048894) \</bitbake/commit/?id=5b105e76dd7de3b9a25b17b397f2c12c80048894\>\`
- Release Artefact: bitbake-5b105e76dd7de3b9a25b17b397f2c12c80048894
- sha: 2cd6448138816f5a906f9927c6b6fdc5cf24981ef32b6402312f52ca490edb4f
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.1.4/bitbake-5b105e76dd7de3b9a25b17b397f2c12c80048894.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.1.4/bitbake-5b105e76dd7de3b9a25b17b397f2c12c80048894.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.1.4/bitbake-5b105e76dd7de3b9a25b17b397f2c12c80048894.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.1.4/bitbake-5b105e76dd7de3b9a25b17b397f2c12c80048894.tar.bz2)

yocto-docs

- Repository Location: :yocto\_[git:%60/yocto-docs](git:%60/yocto-docs)\`
- Branch: :yocto\_[git:%60langdale](git:%60langdale) \</yocto-docs/log/?h=langdale\>\`
- Tag: :yocto\_[git:%60yocto-4.1.4](git:%60yocto-4.1.4) \</yocto-docs/log/?h=yocto-4.1.4\>\`
- Git Revision: :yocto\_[git:%60da685fc5e69d49728e3ffd6c4d623e7e1745059d](git:%60da685fc5e69d49728e3ffd6c4d623e7e1745059d) \</yocto-docs/commit/?id=da685fc5e69d49728e3ffd6c4d623e7e1745059d\>\`
