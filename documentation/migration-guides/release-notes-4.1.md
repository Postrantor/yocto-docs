---
tip: translate by openai@2023-06-07 23:19:40
...
---
title: Release notes for 4.1 (langdale)
---------------------------------------

# New Features / Enhancements in 4.1

- Linux kernel 5.19, glibc 2.36 and \~260 other recipe upgrades
- `make` 4.0 is now the minimum make version required on the build host. For host distros that do not provide it, this is included as part of the `buildtools`.

> `make` 4.0 现在是构建主机所需的最低 make 版本。对于不提供此版本的主机发行版，它包含在 `buildtools`。

- New layer setup tooling:

  - New `scripts/oe-setup-layers` standalone script to restore the layer configuration from a json file
  - New `bitbake-layers create-layers-setup` command to save the layer configuration to a json file
  - New `bitbake-layers save-build-conf` command to save the active build configuration as a template into a layer
- Rust-related enhancements:

  - Support for building rust for the target
  - Significant SDK toolchain build optimisation
  - Support for building native components in the SDK
  - Support `crate://` fetcher with `ref-classes-externalsrc`
- New core recipes:

  - `buildtools-make-tarball`
  - `icon-naming-utils` (previously removed)
  - `musl-locales`
  - `python3-editables` (originally in meta-python)
  - `python3-hatch-vcs`
  - `python3-hatchling` (originally in meta-oe)
  - `python3-lxml` (originally in meta-python)
  - `python3-pathspec` (originally in meta-python)
  - `python3-picobuild`
  - `sato-icon-theme` (previously removed)
- CVE checking enhancements:

  - New `CVE_DB_UPDATE_INTERVAL` variable to allow specifying the CVE database minimum update interval (and default to once per day)

> 新增 `CVE_DB_UPDATE_INTERVAL` 变量，允许指定 CVE 数据库的最小更新间隔(默认为每天一次)。

- Added JSON format to summary output
- Added support for Ignored CVEs
- Enable recursive CVE checking also for `do_populate_sdk`
- New `CVE_CHECK_SHOW_WARNINGS` variable to disable unpatched CVE warning messages
- The `ref-classes-pypi`

> 现在，`ref-classes-pypi` 默认为简体中文。

- Added current kernel CVEs to ignore list since we stay as close to the kernel stable releases as we can
- Optimisations to avoid dependencies on fetching
- Complementary package installation (as used in SDKs and images) no longer installs recommended packages, in order to avoid conflicts
- Dependency of -dev package on main package is now an `RRECOMMENDS` variable

> - -dev 包对主包的依赖现在是一个 `RRECOMMENDS` 变量轻松设置。

- Support for CPU, I/O and memory pressure regulation in BitBake
- Pressure data gathering in `ref-classes-buildstats` and rendering in `pybootchartgui`
- New Picobuild system for lightweight Python PEP-517 build support in the `ref-classes-python_pep517` class
- Many classes are now split into global and recipe contexts for better validation. For more information, see `Classes now split by usage context <migration-4.1-classes-split>`.

> 现在许多类已经按全局和配方上下文分割，以便更好地验证。有关更多信息，请参阅“现在按使用上下文分割的类 <migration-4.1-classes-split>”。

- Architecture-specific enhancements:

  - arch-armv8-4a.inc: add tune include for armv8.4a
  - tune-neoversen2: support tune-neoversen2 base on armv9a
  - riscv: Add tunes for rv64 without compressed instructions
  - gnu-efi: enable for riscv64
  - shadow-securetty: allow ttyS4 for amd-snowyowl-64
- Kernel-related enhancements:

  - linux-yocto/5.15: cfg/xen: Move x86 configs to separate file
  - linux-yocto/5.15: Enabled MDIO bus config
  - linux-yocto: Enable mdio for qemu
  - linux-yocto/5.15: base: enable kernel crypto userspace API
  - kern-tools: allow \'y\' or \'m\' to avoid config audit warnings
  - kernel-yocto.bbclass: say what `SRC_URI` entry is being dropped
  - kernel.bbclass: Do not overwrite recipe\'s custom postinst
  - kmod: Enable xz support by default
  - Run depmod(wrapper) against each compiled kernel when multiple kernels are enabled
  - linux-yocto-tiny: enable qemuarmv5/qemuarm64
- wic Image Creator enhancements:

  - Added dependencies to support erofs
  - Added `fspassno` parameter to partition to allow specifying the value of the last column (`fs_passno`) in `/etc/fstab`.
  - bootimg-efi: added support for loading devicetree files
  - Added `none` fstype for custom image (for use in conjunction with `rawcopy`)
- SDK-related enhancements:

  - `Support for using the regular build system as an SDK <sdk-manual/extensible:Setting up the Extensible SDK environment directly in a Yocto build>`

> 支持使用常规构建系统作为 SDK <sdk-manual/extensible: 直接在 Yocto 构建中设置可扩展 SDK 环境 >

- `ref-classes-image-buildinfo` class now also writes build information to SDKs
- New `SDK_TOOLCHAIN_LANGS` variable to control support of rust / go in SDK
- rust-llvm: enabled `ref-classes-nativesdk` variant
- python3-pluggy: enabled for `ref-classes-native`
- QEMU/runqemu enhancements:

  - qemux86-64: Allow higher tunes
  - runqemu: display host uptime when starting
  - runqemu: add `QB_KERNEL_CMDLINE` that can be set to \"none\" to avoid overriding kernel command line specified in dtb
- Image-related enhancements:

  - New variable `UBOOT_MKIMAGE_KERNEL_TYPE`
  - New variable `FIT_PAD_ALG` to control FIT image padding algorithm
  - New `KERNEL_DEPLOY_DEPEND` variable to allow disabling image dependency on deploying the kernel
  - `ref-classes-image_types`: isolate the write of UBI configuration to a `write_ubi_config` function that can be easily overridden

> - `ref-classes-image_types`：将 UBI 配置的写操作隔离到一个可以轻松覆盖的 `write_ubi_config` 函数中。

- openssh: add support for config snippet includes to ssh and sshd
- `ref-classes-create-spdx` option
- wpa-supplicant: build static library if not disabled via `DISABLE_STATIC`
- wpa-supplicant: package dynamic modules
- openssl: extract legacy provider module to a separate package
- linux-firmware: split out ath3k firmware
- linux-firmware: add support for building snapshots
- eudev: create static-nodes in init script
- udev-extraconf: new `MOUNT_BASE` variable allows configuring automount base directory
- udev-extraconf/mount.sh: use partition labels in mountpoint paths
- systemd: Set RebootWatchdogSec to 60s by default
- systemd: systemd-systemctl: Support instance conf files during enable
- weston.init: enable `xwayland` in weston.ini if `x11` is in `DISTRO_FEATURES`
- New `npm_registry` Python module to enable caching with nodejs 16+
- `ref-classes-npm`: replaced `npm pack` call with `tar czf` for nodejs 16+ compatibility and improved `do_configure` performance

> `- ` ref-classes-npm `: 为了兼容Nodejs 16+，用` tar czf `取代了` npm pack `调用，并且改善了` do_configure` 性能

- Enabled `ref-classes-bin-package` class to work properly in the native case
- Enabled `buildpaths <qa-check-buildpaths>` QA check as a warning by default
- New `OVERLAYFS_ETC_EXPOSE_LOWER`

> 新增 `OVERLAYFS_ETC_EXPOSE_LOWER`。

- New `OVERLAYFS_QA_SKIP` mounts

> 新的 OVERLAYFS_QA_SKIP 变量允许跳过 ref-classes-overlayfs 挂载的检查。

- New `PACKAGECONFIG` options for individual recipes:

  > - apr: xsi-strerror
  > - btrfs-tools: lzo
  > - connman: iwd
  > - coreutils: openssl
  > - dropbear: enable-x11-forwarding
  > - eudev: blkid, kmod, rule-generator
  > - eudev: manpages, selinux
  > - flac: avx, ogg
  > - gnutls: fips
  > - gstreamer1.0-plugins-bad: avtp
  > - libsdl2: libusb
  > - llvm: optviewer
  > - mesa: vulkan, vulkan-beta, zink
  > - perf: bfd
  > - piglit: glx, opencl
  > - python3: editline
  > - qemu: bpf, brlapi, capstone, rdma, slirp, uring, vde
  > - rpm: readline
  > - ruby: capstone
  > - systemd: no-dns-fallback, sysext
  > - tiff: jbig
  >
- ptest enhancements in `curl`, `json-c`, `libgcrypt`, `libgpg-error`, `libxml2`
- ptest compile/install functions now use `PARALLEL_MAKE` in ptest for significant speedup

> 现在，ptest 的编译/安装功能使用 `PARALLEL_MAKE`，以显着加速。

- New `TC_CXX_RUNTIME` variable to enable other layers to more easily control C++ runtime
- Set `BB_DEFAULT_UMASK` using ??= to make it easier to override
- Set `TCLIBC` using ??= to make them easier to override
- squashfs-tools: build with lzo support by default
- insane.bbclass: make `do_qa_staging` check shebang length for native scripts in all `SYSROOT_DIRS`
- utils: Add `create_cmdline_shebang_wrapper` function to allow recipes to easily create a wrapper to fix long shebang lines
- meson: provide relocation script and native/cross wrappers also for meson-native
- meson.bbclass: add cython binary to cross/native toolchain config
- New `musl-locales` recipe to provide a limited set of locale data for musl based systems
- gobject-introspection: use `OBJDUMP` environment variable so that objdump tool can be picked up from the environment

> 使用 `OBJDUMP` 环境变量，以便从环境中捡起 objdump 工具，gobject-introspection。

- The Python `zoneinfo` module is now split out to its own `python3-zoneinfo` package.
- busybox: added devmem 128-bit support
- vim: split xxd out into its own package
- New `ref-classes-github-releases` class to consolidate version checks for github-based packages
- `devtool reset` now preserves `workspace/sources` source trees in `workspace/attic/sources/` instead of leaving them in-place
- scripts/patchreview: Add commit to stored json data
- scripts/patchreview: Make json output human parsable
- `wpa-supplicant` recipe now uses the upstream `defconfig` modified based upon `PACKAGECONFIG` instead of a stale `defconfig` file

> 现在，`wpa-supplicant` 配方使用基于 `PACKAGECONFIG` 的上游 `defconfig`，而不是过时的 `defconfig` 文件

- bitbake: build: prefix the tasks with a timestamp in the log.task_order
- bitbake: fetch2/osc: Add support to query latest revision
- bitbake: utils: Pass lock argument in fileslocked
- bitbake: utils: Add enable_loopback_networking()

# Known Issues in 4.1

- The change to `migration-4.1-complementary-deps`.

> 变更为“migration-4.1-complementary-deps”意味着使用“ptest-pkgs”“IMAGE_FEATURES”构建的镜像不会自动安装“ptest-runner”，因为它是各个“-ptest”软件包的建议。这将在下一个点发行版中解决，可以通过明确安装“ptest-runner”到镜像来解决。已报告为：yocto_bugs:[bug 14928 \</show_bug.cgi?id=14928\>]。

- There is a known issue with eSDKs where sstate objects may be missing, resulting in packages being unavailable to install in the sysroot. This is due to image generation optimisations having unintended consequences in eSDK generation. This will be resolved in the next point release. Filed as :yocto_bugs:[bug 14626 \</show_bug.cgi?id=14626\>], which also details the fix.

> 在 eSDK 中有一个已知的问题，即 sstate 对象可能丢失，导致在 sysroot 中无法安装软件包。这是由于镜像生成优化产生的意外后果导致的 eSDK 生成。下一个点发布会解决这个问题。报告号：yocto_bugs:[bug 14626 \</show_bug.cgi?id=14626\>]，该报告中还详细说明了解决方案。

- The change to `migration-4.1-classes-split`.

> 更改到 `migration-4.1-classes-split` 无意中将 `ref-classes-externalsrc` 类移动到 `meta/classes-recipe`，而它不是特定于配方的，也可以在全局上下文中使用。该类将在下一个点发行版中移回 `meta/classes`。已提交为：yocto_bugs: [bug 14940 \</show_bug.cgi?id=14940\>]。

# Recipe License changes in 4.1

The following corrections have been made to the `LICENSE` values set by recipes:

- alsa-state: add GPL-2.0-or-later because of alsa-state-init file
- git: add GPL-2.0-or-later & BSD-3-Clause & MIT & BSL-1.0 & LGPL-2.1-or-later due to embedded code
- libgcrypt: dropped GPLv3 license after upstream changes
- linux-firmware: correct license for ar3k firmware (specific \"ar3k\" license)

# Security Fixes in 4.1

- bind: `2022-1183`

> 绑定：CVE-2022-1183、CVE-2022-2795、CVE-2022-2881、CVE-2022-2906、CVE-2022-3080、CVE-2022-38178

- binutils: `2019-1010204`

> binutils：CVE-2019-1010204，CVE-2022-38126，CVE-2022-38127，CVE-2022-38128，CVE-2022-38533

- busybox: `2022-30065`
- connman: `2022-32292`
- cups: `2022-26691`
- e2fsprogs: `2022-1304`
- expat: `2022-40674`
- freetype: `2022-27404`
- glibc: `2022-39046`
- gnupg: `2022-34903`
- grub2: `2021-3695`

> CVE-2021-3695、CVE-2021-3696、CVE-2021-3697、CVE-2022-28733、CVE-2022-28734、CVE-2022-28735

- inetutils: `2022-39028`
- libtirpc: `2021-46828`
- libxml2: `2016-3709` (ignored)
- libxslt: `2022-29824` (not applicable)
- linux-yocto/5.15: `2022-28796`
- logrotate: `2022-1348`
- lua: `2022-33099`
- nasm: `2020-18974` (ignored)
- ncurses: `2022-29458`
- openssl: `2022-1292`

> CVE-2022-1292，CVE-2022-1343，CVE-2022-1434，CVE-2022-1473，CVE-2022-2068，CVE-2022-2274，CVE-2022-2097

- python3: `2015-20107` (ignored)
- qemu: `2021-20255`

> QEMU：2021-20255(已忽略)，2019-12067(已忽略)，2021-3507，2022-0216，2022-2962，2022-35414

- rpm: `2021-35937`
- rsync: `2022-29154`
- subversion: `2021-28544`
- tiff: `2022-1210`

> - 提夫：“2022-1210”(不适用)，“2022-1622”，“2022-1623”(无效)，“2022-2056”，“2022-2057”，“2022-2058”，“2022-2953”，“2022-34526”。

- unzip: `2022-0529`
- vim: `2022-1381`

> -Vim: CVE-2022-1381、CVE-2022-1420、CVE-2022-1621、CVE-2022-1629、CVE-2022-1674、CVE-2022-1733、CVE-2022-1735、CVE-2022-1769、CVE-2022-1771、CVE-2022-1785、CVE-2022-1796、CVE-2022-1927、CVE-2022-1942、CVE-2022-2257、CVE-2022-2264、CVE-2022-2284、CVE-2022-2285、CVE-2022-2286、CVE-2022-2287、CVE-2022-2816、CVE-2022-2817、CVE-2022-2819、CVE-2022-2845、CVE-2022-2849、CVE-2022-2862、CVE-2022-2874、CVE-2022-2889、CVE-2022-2980、CVE-2022-2946、CVE-2022-2982、CVE-2022-3099、CVE-2022-3134、CVE-2022-3234、CVE-2022-3278。

- zlib: `2022-37434`

# Recipe Upgrades in 4.1

- acpica 20211217 -\> 20220331
- adwaita-icon-theme 41.0 -\> 42.0
- alsa-lib 1.2.6.1 -\> 1.2.7.2
- alsa-plugins 1.2.6 -\> 1.2.7.1
- alsa-ucm-conf 1.2.6.3 -\> 1.2.7.2
- alsa-utils 1.2.6 -\> 1.2.7
- asciidoc 10.1.4 -\> 10.2.0
- at-spi2-core 2.42.0 -\> 2.44.1
- autoconf-archive 2022.02.11 -\> 2022.09.03
- base-passwd 3.5.29 -\> 3.5.52
- bind 9.18.5 -\> 9.18.7
- binutils 2.38 -\> 2.39
- boost 1.78.0 -\> 1.80.0
- boost-build-native 4.4.1 -\> 1.80.0
- btrfs-tools 5.16.2 -\> 5.19.1
- cargo 1.59.0 -\> 1.63.0
- ccache 4.6 -\> 4.6.3
- cmake 3.22.3 -\> 3.24.0
- cmake-native 3.22.3 -\> 3.24.0
- coreutils 9.0 -\> 9.1
- createrepo-c 0.19.0 -\> 0.20.1
- cross-localedef-native 2.35 -\> 2.36
- curl 7.82.0 -\> 7.85.0
- diffoscope 208 -\> 221
- dmidecode 3.3 -\> 3.4
- dnf 4.11.1 -\> 4.14.0
- dos2unix 7.4.2 -\> 7.4.3
- dpkg 1.21.4 -\> 1.21.9
- dropbear 2020.81 -\> 2022.82
- efibootmgr 17 -\> 18
- elfutils 0.186 -\> 0.187
- ell 0.50 -\> 0.53
- enchant2 2.3.2 -\> 2.3.3
- erofs-utils 1.4 -\> 1.5
- ethtool 5.16 -\> 5.19
- eudev 3.2.10 -\> 3.2.11
- ffmpeg 5.0.1 -\> 5.1.1
- file 5.41 -\> 5.43
- flac 1.3.4 -\> 1.4.0
- fontconfig 2.13.1 -\> 2.14.0
- freetype 2.11.1 -\> 2.12.1
- gcc 11.3.0 -\> 12.2.0
- gcompat 1.0.0+1.1+gitX (4d6a5156a6eb...) -\> 1.0.0+1.1+gitX (c6921a1aa454...)
- gdb 11.2 -\> 12.1
- ghostscript 9.55.0 -\> 9.56.1
- git 2.35.4 -\> 2.37.3
- glibc 2.35 -\> 2.36
- glslang 1.3.204.1 -\> 1.3.216.0
- gnu-config 20211108+gitX -\> 20220525+gitX
- gnu-efi 3.0.14 -\> 3.0.15
- gnutls 3.7.4 -\> 3.7.7
- go 1.17.13 -\> 1.19
- go-helloworld 0.1 (787a929d5a0d...) -\> 0.1 (2e68773dfca0...)
- gpgme 1.17.1 -\> 1.18.0
- gptfdisk 1.0.8 -\> 1.0.9
- harfbuzz 4.0.1 -\> 5.1.0
- hdparm 9.63 -\> 9.64
- help2man 1.49.1 -\> 1.49.2
- hwlatdetect 2.3 -\> 2.4
- icu 70.1 -\> 71.1
- inetutils 2.2 -\> 2.3
- init-system-helpers 1.62 -\> 1.64
- iproute2 5.17.0 -\> 5.19.0
- iptables 1.8.7 -\> 1.8.8
- iw 5.16 -\> 5.19
- json-c 0.15 -\> 0.16
- kbd 2.4.0 -\> 2.5.1
- kea 2.0.2 -\> 2.2.0
- kexec-tools 2.0.23 -\> 2.0.25
- kmod 29 -\> 30
- kmscube git (9f63f359fab1...) -\> git (3bf6ee1a0233...)
- less 600 -\> 608
- libaio 0.3.112 -\> 0.3.113
- libbsd 0.11.5 -\> 0.11.6
- libcap-ng 0.8.2 -\> 0.8.3
- libcap-ng-python 0.8.2 -\> 0.8.3
- libcgroup 2.0.2 -\> 3.0.0
- libcomps 0.1.18 -\> 0.1.19
- libdnf 0.66.0 -\> 0.69.0
- libdrm 2.4.110 -\> 2.4.113
- libevdev 1.12.1 -\> 1.13.0
- libfontenc 1.1.4 -\> 1.1.6
- libgcc 11.3.0 -\> 12.2.0
- libgcc-initial 11.3.0 -\> 12.2.0
- libgcrypt 1.9.4 -\> 1.10.1
- libgfortran 11.3.0 -\> 12.2.0
- libgit2 1.4.3 -\> 1.5.0
- libgpg-error 1.44 -\> 1.45
- libhandy 1.5.0 -\> 1.6.3
- libidn2 2.3.2 -\> 2.3.3
- libjitterentropy 3.4.0 -\> 3.4.1
- libmnl 1.0.4 -\> 1.0.5
- libnl 3.5.0 -\> 3.7.0
- libnotify 0.7.9 -\> 0.8.1
- libpipeline 1.5.5 -\> 1.5.6
- libproxy 0.4.17 -\> 0.4.18
- librepo 1.14.3 -\> 1.14.5
- librsvg 2.52.7 -\> 2.54.5
- libsdl2 2.0.20 -\> 2.24.0
- libseccomp 2.5.3 -\> 2.5.4
- libsndfile1 1.0.31 -\> 1.1.0
- libstd-rs 1.59.0 -\> 1.63.0
- libtirpc 1.3.2 -\> 1.3.3
- libubootenv 0.3.2 -\> 0.3.3
- libva 2.14.0 -\> 2.15.0
- libva-utils 2.14.0 -\> 2.15.0
- libx11 1.7.3.1 -\> 1.8.1
- libxau 1.0.9 -\> 1.0.10
- libxcb 1.14 -\> 1.15
- libxcursor 1.2.0 -\> 1.2.1
- libxcvt 0.1.1 -\> 0.1.2
- libxfont2 2.0.5 -\> 2.0.6
- libxvmc 1.0.12 -\> 1.0.13
- linux-libc-headers 5.16 -\> 5.19
- linux-yocto 5.10.143+gitX, 5.15.68+gitX -\> 5.15.68+gitX, 5.19.9+gitX
- linux-yocto-dev 5.18++gitX -\> 5.19++gitX
- linux-yocto-rt 5.10.143+gitX, 5.15.68+gitX -\> 5.15.68+gitX, 5.19.9+gitX
- linux-yocto-tiny 5.10.143+gitX, 5.15.68+gitX -\> 5.15.68+gitX, 5.19.9+gitX
- llvm 13.0.1 -\> 14.0.6
- lsof 4.94.0 -\> 4.95.0
- ltp 20220121 -\> 20220527
- lttng-tools 2.13.4 -\> 2.13.8
- lttng-ust 2.13.3 -\> 2.13.4
- mc 4.8.27 -\> 4.8.28
- mesa 22.0.3 -\> 22.2.0
- mesa-demos 8.4.0 -\> 8.5.0
- mesa-gl 22.0.3 -\> 22.2.0
- meson 0.61.3 -\> 0.63.2
- mmc-utils 0.1+gitX (b7e4d5a6ae99...) -\> 0.1+gitX (d7b343fd2628...)
- mpg123 1.29.3 -\> 1.30.2
- msmtp 1.8.20 -\> 1.8.22
- mtools 4.0.38 -\> 4.0.40
- musl 1.2.3+gitX (7a43f6fea908...) -\> 1.2.3+gitX (37e18b7bf307...)
- musl-obstack 1.1 -\> 1.2
- ncurses 6.3+20220423 (a0bc708bc695...) -\> 6.3+20220423 (20db1fb41ec9...)
- neard 0.16 -\> 0.18
- nettle 3.7.3 -\> 3.8.1
- nfs-utils 2.6.1 -\> 2.6.2
- nghttp2 1.47.0 -\> 1.49.0
- ninja 1.10.2 -\> 1.11.1
- numactl 2.0.14 -\> 2.0.15
- ofono 1.34 -\> 2.0
- opensbi 1.0 -\> 1.1
- openssh 8.9p1 -\> 9.0p1
- opkg 0.5.0 -\> 0.6.0
- ovmf edk2-stable202202 -\> edk2-stable202205
- pango 1.50.4 -\> 1.50.9
- parted 3.4 -\> 3.5
- patchelf 0.14.5 -\> 0.15.0
- pciutils 3.7.0 -\> 3.8.0
- perl 5.34.1 -\> 5.36.0
- perlcross 1.3.7 -\> 1.4
- piglit 1.0+gitrX (2f80c7cc9c02...) -\> 1.0+gitrX (265896c86f90...)
- pkgconf 1.8.0 -\> 1.9.3
- psmisc 23.4 -\> 23.5
- pulseaudio 15.0 -\> 16.1
- puzzles 0.0+gitX (c43a34fbfe43...) -\> 0.0+gitX (8399cff6a3b9...)
- python3 3.10.4 -\> 3.10.6
- python3-atomicwrites 1.4.0 -\> 1.4.1
- python3-attrs 21.4.0 -\> 22.1.0
- python3-babel 2.9.1 -\> 2.10.3
- python3-bcrypt 3.2.0 -\> 3.2.2
- python3-certifi 2021.10.8 -\> 2022.9.14
- python3-cffi 1.15.0 -\> 1.15.1
- python3-chardet 4.0.0 -\> 5.0.0
- python3-cryptography 36.0.2 -\> 37.0.4
- python3-cryptography-vectors 36.0.2 -\> 37.0.4
- python3-cython 0.29.28 -\> 0.29.32
- python3-dbusmock 0.27.3 -\> 0.28.4
- python3-docutils 0.18.1 -\> 0.19
- python3-dtschema 2022.1 -\> 2022.8.3
- python3-hypothesis 6.39.5 -\> 6.54.5
- python3-idna 3.3 -\> 3.4
- python3-imagesize 1.3.0 -\> 1.4.1
- python3-importlib-metadata 4.11.3 -\> 4.12.0
- python3-jinja2 3.1.1 -\> 3.1.2
- python3-jsonpointer 2.2 -\> 2.3
- python3-jsonschema 4.4.0 -\> 4.9.1
- python3-magic 0.4.25 -\> 0.4.27
- python3-mako 1.1.6 -\> 1.2.2
- python3-markdown 3.3.6 -\> 3.4.1
- python3-more-itertools 8.12.0 -\> 8.14.0
- python3-numpy 1.22.3 -\> 1.23.3
- python3-pbr 5.8.1 -\> 5.10.0
- python3-pip 22.0.3 -\> 22.2.2
- python3-psutil 5.9.0 -\> 5.9.2
- python3-pycryptodome 3.14.1 -\> 3.15.0
- python3-pycryptodomex 3.14.1 -\> 3.15.0
- python3-pyelftools 0.28 -\> 0.29
- python3-pygments 2.11.2 -\> 2.13.0
- python3-pygobject 3.42.0 -\> 3.42.2
- python3-pyparsing 3.0.7 -\> 3.0.9
- python3-pytest 7.1.1 -\> 7.1.3
- python3-pytest-subtests 0.7.0 -\> 0.8.0
- python3-pytz 2022.1 -\> 2022.2.1
- python3-requests 2.27.1 -\> 2.28.1
- python3-scons 4.3.0 -\> 4.4.0
- python3-semantic-version 2.9.0 -\> 2.10.0
- python3-setuptools 59.5.0 -\> 65.0.2
- python3-setuptools-scm 6.4.2 -\> 7.0.5
- python3-sphinx 4.4.0 -\> 5.1.1
- python3-sphinx-rtd-theme 0.5.0 -\> 1.0.0
- python3-typing-extensions 3.10.0.0 -\> 4.3.0
- python3-urllib3 1.26.9 -\> 1.26.12
- python3-webcolors 1.11.1 -\> 1.12
- python3-zipp 3.7.0 -\> 3.8.1
- qemu 6.2.0 -\> 7.1.0
- repo 2.22 -\> 2.29.2
- rpm 4.17.0 -\> 4.18.0
- rsync 3.2.3 -\> 3.2.5
- rt-tests 2.3 -\> 2.4
- rust 1.59.0 -\> 1.63.0
- rust-llvm 1.59.0 -\> 1.63.0
- sbc 1.5 -\> 2.0
- seatd 0.6.4 -\> 0.7.0
- shaderc 2022.1 -\> 2022.2
- shadow 4.11.1 -\> 4.12.1
- shared-mime-info 2.1 -\> 2.2
- slang 2.3.2 -\> 2.3.3
- speex 1.2.0 -\> 1.2.1
- speexdsp 1.2.0 -\> 1.2.1
- spirv-headers 1.3.204.1 -\> 1.3.216.0
- spirv-tools 1.3.204.1 -\> 1.3.216.0
- sqlite3 3.38.5 -\> 3.39.3
- squashfs-tools 4.5 -\> 4.5.1
- strace 5.16 -\> 5.19
- stress-ng 0.13.12 -\> 0.14.03
- sudo 1.9.10 -\> 1.9.11p3
- sysklogd 2.3.0 -\> 2.4.4
- sysstat 12.4.5 -\> 12.6.0
- systemd 250.5 -\> 251.4
- systemd-boot 250.5 -\> 251.4
- systemtap 4.6 -\> 4.7
- systemtap-native 4.6 -\> 4.7
- systemtap-uprobes 4.6 -\> 4.7
- sysvinit 3.01 -\> 3.04
- tiff 4.3.0 -\> 4.4.0
- tzcode-native 2022c -\> 2022d
- tzdata 2022c -\> 2022d
- u-boot 2022.01 -\> 2022.07
- u-boot-tools 2022.01 -\> 2022.07
- util-linux 2.37.4 -\> 2.38.1
- util-linux-libuuid 2.37.4 -\> 2.38.1
- valgrind 3.18.1 -\> 3.19.0
- vim 9.0.0541 -\> 9.0.0598
- vim-tiny 9.0.0541 -\> 9.0.0598
- virglrenderer 0.9.1 -\> 0.10.3
- vte 0.66.2 -\> 0.68.0
- vulkan-headers 1.3.204.1 -\> 1.3.216.0
- vulkan-loader 1.3.204.1 -\> 1.3.216.0
- vulkan-samples git (28ca2dad83ce...) -\> git (74d45aace02d...)
- vulkan-tools 1.3.204.1 -\> 1.3.216.0
- wayland 1.20.0 -\> 1.21.0
- wayland-protocols 1.25 -\> 1.26
- webkitgtk 2.36.5 -\> 2.36.7
- x264 r3039+gitX (5db6aa6cab1b...) -\> r3039+gitX (baee400fa9ce...)
- xauth 1.1.1 -\> 1.1.2
- xcb-proto 1.14.1 -\> 1.15.2
- xf86-video-cirrus 1.5.3 -\> 1.6.0
- xkeyboard-config 2.35.1 -\> 2.36
- xmlto 0.0.28 -\> 0.0.28+0.0.29+gitX
- xorgproto 2021.5 -\> 2022.2
- zlib 1.2.11 -\> 1.2.12

# Contributors to 4.1

Thanks to the following people who contributed to this release:

- Aatir Manzur
- Ahmed Hossam
- Alejandro Hernandez Samaniego
- Alexander Kanavin
- Alexandre Belloni
- Alex Kiernan
- Alex Stewart
- Andrei Gherzan
- Andrej Valek
- Andrey Konovalov
- Aníbal Limón
- Anuj Mittal
- Arkadiusz Drabczyk
- Armin Kuster
- Aryaman Gupta
- Awais Belal
- Beniamin Sandu
- Bertrand Marquis
- Bob Henz
- Bruce Ashfield
- Carlos Rafael Giani
- Changhyeok Bae
- Changqing Li
- Chanho Park
- Chen Qi
- Christoph Lauer
- Claudius Heine
- Daiane Angolini
- Daniel Gomez
- Daniel McGregor
- David Bagonyi
- Davide Gardenal
- Denys Dmytriyenko
- Dmitry Baryshkov
- Drew Moseley
- Enrico Scholz
- Ernst Sjöstrand
- Etienne Cordonnier
- Fabio Estevam
- Federico Pellegrin
- Felix Moessbauer
- Ferry Toth
- Florin Diaconescu
- Gennaro Iorio
- Grygorii Tertychnyi
- Gunjan Gupta
- Henning Schild
- He Zhe
- Hitendra Prajapati
- Jack Mitchell
- Jacob Kroon
- Jan Kiszka
- Jan Luebbe
- Jan Vermaete
- Jasper Orschulko
- JeongBong Seo
- Jeremy Puhlman
- Jiaqing Zhao
- Joerg Vehlow
- Johan Korsnes
- Johannes Schneider
- John Edward Broadbent
- Jon Mason
- Jose Quaresma
- Joshua Watt
- Justin Bronder
- Kai Kang
- Kevin Hao
- Khem Raj
- Konrad Weihmann
- Kory Maincent
- Kristian Amlie
- Lee Chee Yang
- Lei Maohui
- Leon Anavi
- Luca Ceresoli
- Lucas Stach
- LUIS ENRIQUEZ
- Marcel Ziswiler
- Marius Kriegerowski
- Mark Hatle
- Markus Volk
- Marta Rybczynska
- Martin Beeger
- Martin Jansa
- Mateusz Marciniec
- Mattias Jernberg
- Matt Madison
- Maxime Roussin-Bélanger
- Michael Halstead
- Michael Opdenacker
- Mihai Lindner
- Mikko Rapeli
- Ming Liu
- Mingli Yu
- Muhammad Hamza
- Naveen Saini
- Neil Horman
- Nick Potenski
- Nicolas Dechesne
- Niko Mauno
- Ola x Nilsson
- Otavio Salvador
- Pascal Bach
- Paul Eggleton
- Paul Gortmaker
- Paulo Neves
- Pavel Zhukov
- Peter Bergin
- Peter Kjellerstedt
- Peter Marko
- Petr Vorel
- Pgowda
- Portia Stephens
- Quentin Schulz
- Rahul Kumar
- Raju Kumar Pothuraju
- Randy MacLeod
- Raphael Teller
- Rasmus Villemoes
- Ricardo Salveti
- Richard Purdie
- Robert Joslyn
- Robert Yang
- Roland Hieber
- Ross Burton
- Rouven Czerwinski
- Ruiqiang Hao
- Russ Dill
- Rusty Howell
- Sakib Sajal
- Samuli Piippo
- Schmidt, Adriaan
- Sean Anderson
- Shruthi Ravichandran
- Shubham Kulkarni
- Simone Weiss
- Sebastian Suesens
- Stefan Herbrechtsmeier
- Stefano Babic
- Stefan Wiehler
- Steve Sakoman
- Sundeep KOKKONDA
- Teoh Jay Shen
- Thomas Epperson
- Thomas Perrot
- Thomas Roos
- Tobias Schmidl
- Tomasz Dziendzielski
- Tom Hochstein
- Tom Rini
- Trevor Woerner
- Ulrich Ölmann
- Vyacheslav Yurkov
- Wang Mingyu
- William A. Kennington III
- Xiaobing Luo
- Xu Huan
- Yang Xu
- Yi Zhao
- Yogesh Tyagi
- Yongxin Liu
- Yue Tao
- Yulong (Kevin) Liu
- Zach Welch
- Zheng Ruoqin
- Zoltán Böszörményi

# Repositories / Downloads for 4.1

poky

- Repository Location: :yocto_[git:%60/poky](git:%60/poky)\`
- Branch: :yocto_[git:%60langdale](git:%60langdale) \</poky/log/?h=langdale\>\`
- Tag: :yocto_[git:%60yocto-4.1](git:%60yocto-4.1) \</poky/log/?h=yocto-4.1\>\`
- Git Revision: :yocto_[git:%605200799866b92259e855051112520006e1aaaac0](git:%605200799866b92259e855051112520006e1aaaac0) \</poky/commit/?id=5200799866b92259e855051112520006e1aaaac0\>\`

> - Git 版本：:yocto_[git:%605200799866b92259e855051112520006e1aaaac0](git:%605200799866b92259e855051112520006e1aaaac0) \</poky/commit/?id=5200799866b92259e855051112520006e1aaaac0\>\`

- Release Artefact: poky-5200799866b92259e855051112520006e1aaaac0
- sha: 9d9a2f7ecf2502f89f43bf45d63e6b61cdcb95ed1d75c8281372f550d809c823
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/poky-5200799866b92259e855051112520006e1aaaac0.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/poky-5200799866b92259e855051112520006e1aaaac0.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.1/poky-5200799866b92259e855051112520006e1aaaac0.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.1/poky-5200799866b92259e855051112520006e1aaaac0.tar.bz2)

> 下载位置：[http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/poky-5200799866b92259e855051112520006e1aaaac0.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/poky-5200799866b92259e855051112520006e1aaaac0.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.1/poky-5200799866b92259e855051112520006e1aaaac0.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.1/poky-5200799866b92259e855051112520006e1aaaac0.tar.bz2)

openembedded-core

- Repository Location: :oe_[git:%60/openembedded-core](git:%60/openembedded-core)\`
- Branch: :oe_[git:%60langdale](git:%60langdale) \</openembedded-core/log/?h=langdale\>\`
- Tag: :oe_[git:%60yocto-4.1](git:%60yocto-4.1) \</openembedded-core/log/?h=yocto-4.1\>\`
- Git Revision: :oe_[git:%60744a2277844ec9a384a9ca7dae2a634d5a0d3590](git:%60744a2277844ec9a384a9ca7dae2a634d5a0d3590) \</openembedded-core/commit/?id=744a2277844ec9a384a9ca7dae2a634d5a0d3590\>\`

> - Git 版本：<openembedded-core/commit/?id=744a2277844ec9a384a9ca7dae2a634d5a0d3590>(git：%60744a2277844ec9a384a9ca7dae2a634d5a0d3590)

- Release Artefact: oecore-744a2277844ec9a384a9ca7dae2a634d5a0d3590
- sha: 34f1fd5bb83514bf0ec8ad7f8cce088a8e28677e1338db94c188283da704c663
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/oecore-744a2277844ec9a384a9ca7dae2a634d5a0d3590.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/oecore-744a2277844ec9a384a9ca7dae2a634d5a0d3590.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.1/oecore-744a2277844ec9a384a9ca7dae2a634d5a0d3590.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.1/oecore-744a2277844ec9a384a9ca7dae2a634d5a0d3590.tar.bz2)

> 下载位置：[http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/oecore-744a2277844ec9a384a9ca7dae2a634d5a0d3590.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/oecore-744a2277844ec9a384a9ca7dae2a634d5a0d3590.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.1/oecore-744a2277844ec9a384a9ca7dae2a634d5a0d3590.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.1/oecore-744a2277844ec9a384a9ca7dae2a634d5a0d3590.tar.bz2) 下载地址：[http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/oecore-744a2277844ec9a384a9ca7dae2a634d5a0d3590.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/oecore-744a2277844ec9a384a9ca7dae2a634d5a0d3590.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.1/oecore-744a2277844ec9a384a9ca7dae2a634d5a0d3590.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.1/oecore-744a2277844ec9a384a9ca7dae2a634d5a0d3590.tar.bz2)

meta-mingw

- Repository Location: :yocto_[git:%60/meta-mingw](git:%60/meta-mingw)\`
- Branch: :yocto_[git:%60langdale](git:%60langdale) \</meta-mingw/log/?h=langdale\>\`
- Tag: :yocto_[git:%60yocto-4.1](git:%60yocto-4.1) \</meta-mingw/log/?h=yocto-4.1\>\`
- Git Revision: :yocto_[git:%60b0067202db8573df3d23d199f82987cebe1bee2c](git:%60b0067202db8573df3d23d199f82987cebe1bee2c) \</meta-mingw/commit/?id=b0067202db8573df3d23d199f82987cebe1bee2c\>\`

> Git 版本：yocto_[git:`b0067202db8573df3d23d199f82987cebe1bee2c`](git:%60b0067202db8573df3d23d199f82987cebe1bee2c%60) \</meta-mingw/commit/?id=b0067202db8573df3d23d199f82987cebe1bee2c\>\`

- Release Artefact: meta-mingw-b0067202db8573df3d23d199f82987cebe1bee2c
- sha: 704f2940322b81ce774e9cbd27c3cfa843111d497dc7b1eeaa39cd694d9a2366
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/meta-mingw-b0067202db8573df3d23d199f82987cebe1bee2c.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/meta-mingw-b0067202db8573df3d23d199f82987cebe1bee2c.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.1/meta-mingw-b0067202db8573df3d23d199f82987cebe1bee2c.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.1/meta-mingw-b0067202db8573df3d23d199f82987cebe1bee2c.tar.bz2)

> 下载位置：[http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/meta-mingw-b0067202db8573df3d23d199f82987cebe1bee2c.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/meta-mingw-b0067202db8573df3d23d199f82987cebe1bee2c.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.1/meta-mingw-b0067202db8573df3d23d199f82987cebe1bee2c.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.1/meta-mingw-b0067202db8573df3d23d199f82987cebe1bee2c.tar.bz2)

bitbake

- Repository Location: :oe_[git:%60/bitbake](git:%60/bitbake)\`
- Branch: :oe_[git:%602.2](git:%602.2) \</bitbake/log/?h=2.2\>\`
- Tag: :oe_[git:%60yocto-4.1](git:%60yocto-4.1) \</bitbake/log/?h=yocto-4.1\>\`
- Git Revision: :oe_[git:%60074da4c469d1f4177a1c5be72b9f3ccdfd379d67](git:%60074da4c469d1f4177a1c5be72b9f3ccdfd379d67) \</bitbake/commit/?id=074da4c469d1f4177a1c5be72b9f3ccdfd379d67\>\`

> Git 版本：<bitbake/commit/?id=074da4c469d1f4177a1c5be72b9f3ccdfd379d67>(git:%60074da4c469d1f4177a1c5be72b9f3ccdfd379d67)

- Release Artefact: bitbake-074da4c469d1f4177a1c5be72b9f3ccdfd379d67
- sha: e32c300e0c8522d8d49ef10aae473bd5f293202672eb9d38e90ed92594ed1fe8
- Download Locations: [http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/bitbake-074da4c469d1f4177a1c5be72b9f3ccdfd379d67.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/bitbake-074da4c469d1f4177a1c5be72b9f3ccdfd379d67.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.1/bitbake-074da4c469d1f4177a1c5be72b9f3ccdfd379d67.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.1/bitbake-074da4c469d1f4177a1c5be72b9f3ccdfd379d67.tar.bz2)

> 下载位置：[http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/bitbake-074da4c469d1f4177a1c5be72b9f3ccdfd379d67.tar.bz2](http://downloads.yoctoproject.org/releases/yocto/yocto-4.1/bitbake-074da4c469d1f4177a1c5be72b9f3ccdfd379d67.tar.bz2) [http://mirrors.kernel.org/yocto/yocto/yocto-4.1/bitbake-074da4c469d1f4177a1c5be72b9f3ccdfd379d67.tar.bz2](http://mirrors.kernel.org/yocto/yocto/yocto-4.1/bitbake-074da4c469d1f4177a1c5be72b9f3ccdfd379d67.tar.bz2)

yocto-docs

- Repository Location: :yocto_[git:%60/yocto-docs](git:%60/yocto-docs)\`
- Branch: :yocto_[git:%60langdale](git:%60langdale) \</yocto-docs/log/?h=langdale\>\`
- Tag: :yocto_[git:%60yocto-4.1](git:%60yocto-4.1) \</yocto-docs/log/?h=yocto-4.1\>\`
- Git Revision: :yocto_[git:%6042d3e26a0d04bc5951e640b471686f347dc9b74a](git:%6042d3e26a0d04bc5951e640b471686f347dc9b74a) \</yocto-docs/commit/?id=42d3e26a0d04bc5951e640b471686f347dc9b74a\>\`

> Git 版本：yocto_[git：`42d3e26a0d04bc5951e640b471686f347dc9b74a`](git%EF%BC%9A%6042d3e26a0d04bc5951e640b471686f347dc9b74a%60) \</yocto-docs/commit/?id=42d3e26a0d04bc5951e640b471686f347dc9b74a\>\`
