---
title: Release 2.4 (rocko)
---
This section provides migration information for moving to the Yocto Project 2.4 Release (codename \"rocko\") from the prior release.

# Memory Resident Mode {#migration-2.4-memory-resident-mode}

A persistent mode is now available in BitBake\'s default operation, replacing its previous \"memory resident mode\" (i.e. `oe-init-build-env-memres`). Now you only need to set `BB_SERVER_TIMEOUT`{.interpreted-text role="term"} to a timeout (in seconds) and BitBake\'s server stays resident for that amount of time between invocations. The `oe-init-build-env-memres` script has been removed since a separate environment setup script is no longer needed.

# Packaging Changes {#migration-2.4-packaging-changes}

This section provides information about packaging changes that have occurred:

- `python3` Changes:
  - The main \"python3\" package now brings in all of the standard Python 3 distribution rather than a subset. This behavior matches what is expected based on traditional Linux distributions. If you wish to install a subset of Python 3, specify `python-core` plus one or more of the individual packages that are still produced.
  - `python3`: The `bz2.py`, `lzma.py`, and `_compression.py` scripts have been moved from the `python3-misc` package to the `python3-compression` package.
- `binutils`: The `libbfd` library is now packaged in a separate \"libbfd\" package. This packaging saves space when certain tools (e.g. `perf`) are installed. In such cases, the tools only need `libbfd` rather than all the packages in `binutils`.
- `util-linux` Changes:
  - The `su` program is now packaged in a separate \"util-linux-su\" package, which is only built when \"pam\" is listed in the `DISTRO_FEATURES`{.interpreted-text role="term"} variable. `util-linux` should not be installed unless it is needed because `su` is normally provided through the shadow file format. The main `util-linux` package has runtime dependencies (i.e. `RDEPENDS`{.interpreted-text role="term"}) on the `util-linux-su` package when \"pam\" is in `DISTRO_FEATURES`{.interpreted-text role="term"}.
  - The `switch_root` program is now packaged in a separate \"util-linux-switch-root\" package for small `Initramfs`{.interpreted-text role="term"} images that do not need the whole `util-linux` package or the busybox binary, which are both much larger than `switch_root`. The main `util-linux` package has a recommended runtime dependency (i.e. `RRECOMMENDS`{.interpreted-text role="term"}) on the `util-linux-switch-root` package.
  - The `ionice` program is now packaged in a separate \"util-linux-ionice\" package. The main `util-linux` package has a recommended runtime dependency (i.e. `RRECOMMENDS`{.interpreted-text role="term"}) on the `util-linux-ionice` package.
- `initscripts`: The `sushell` program is now packaged in a separate \"initscripts-sushell\" package. This packaging change allows systems to pull `sushell` in when `selinux` is enabled. The change also eliminates needing to pull in the entire `initscripts` package. The main `initscripts` package has a runtime dependency (i.e. `RDEPENDS`{.interpreted-text role="term"}) on the `sushell` package when \"selinux\" is in `DISTRO_FEATURES`{.interpreted-text role="term"}.
- `glib-2.0`: The `glib-2.0` package now has a recommended runtime dependency (i.e. `RRECOMMENDS`{.interpreted-text role="term"}) on the `shared-mime-info` package, since large portions of GIO are not useful without the MIME database. You can remove the dependency by using the `BAD_RECOMMENDATIONS`{.interpreted-text role="term"} variable if `shared-mime-info` is too large and is not required.
- *Go Standard Runtime:* The Go standard runtime has been split out from the main `go` recipe into a separate `go-runtime` recipe.

# Removed Recipes {#migration-2.4-removed-recipes}

The following recipes have been removed:

- `acpitests`: This recipe is not maintained.
- `autogen-native`: No longer required by Grub, oe-core, or meta-oe.
- `bdwgc`: Nothing in OpenEmbedded-Core requires this recipe. It has moved to meta-oe.
- `byacc`: This recipe was only needed by rpm 5.x and has moved to meta-oe.
- `gcc (5.4)`: The 5.4 series dropped the recipe in favor of 6.3 / 7.2.
- `gnome-common`: Deprecated upstream and no longer needed.
- `go-bootstrap-native`: Go 1.9 does its own bootstrapping so this recipe has been removed.
- `guile`: This recipe was only needed by `autogen-native` and `remake`. The recipe is no longer needed by either of these programs.
- `libclass-isa-perl`: This recipe was previously needed for LSB 4, no longer needed.
- `libdumpvalue-perl`: This recipe was previously needed for LSB 4, no longer needed.
- `libenv-perl`: This recipe was previously needed for LSB 4, no longer needed.
- `libfile-checktree-perl`: This recipe was previously needed for LSB 4, no longer needed.
- `libi18n-collate-perl`: This recipe was previously needed for LSB 4, no longer needed.
- `libiconv`: This recipe was only needed for `uclibc`, which was removed in the previous release. `glibc` and `musl` have their own implementations. `meta-mingw` still needs `libiconv`, so it has been moved to `meta-mingw`.
- `libpng12`: This recipe was previously needed for LSB. The current `libpng` is 1.6.x.
- `libpod-plainer-perl`: This recipe was previously needed for LSB 4, no longer needed.
- `linux-yocto (4.1)`: This recipe was removed in favor of 4.4, 4.9, 4.10 and 4.12.
- `mailx`: This recipe was previously only needed for LSB compatibility, and upstream is defunct.
- `mesa (git version only)`: The git version recipe was stale with respect to the release version.
- `ofono (git version only)`: The git version recipe was stale with respect to the release version.
- `portmap`: This recipe is obsolete and is superseded by `rpcbind`.
- `python3-pygpgme`: This recipe is old and unmaintained. It was previously required by `dnf`, which has switched to official `gpgme` Python bindings.
- `python-async`: This recipe has been removed in favor of the Python 3 version.
- `python-gitdb`: This recipe has been removed in favor of the Python 3 version.
- `python-git`: This recipe was removed in favor of the Python 3 version.
- `python-mako`: This recipe was removed in favor of the Python 3 version.
- `python-pexpect`: This recipe was removed in favor of the Python 3 version.
- `python-ptyprocess`: This recipe was removed in favor of Python the 3 version.
- `python-pycurl`: Nothing is using this recipe in OpenEmbedded-Core (i.e. `meta-oe`).
- `python-six`: This recipe was removed in favor of the Python 3 version.
- `python-smmap`: This recipe was removed in favor of the Python 3 version.
- `remake`: Using `remake` as the provider of `virtual/make` is broken. Consequently, this recipe is not needed in OpenEmbedded-Core.

# Kernel Device Tree Move {#migration-2.4-kernel-device-tree-move}

Kernel Device Tree support is now easier to enable in a kernel recipe. The Device Tree code has moved to a `ref-classes-kernel-devicetree`{.interpreted-text role="ref"} class. Functionality is automatically enabled for any recipe that inherits the `kernel <ref-classes-kernel>`{.interpreted-text role="ref"} class and sets the `KERNEL_DEVICETREE`{.interpreted-text role="term"} variable. The previous mechanism for doing this, `meta/recipes-kernel/linux/linux-dtb.inc`, is still available to avoid breakage, but triggers a deprecation warning. Future releases of the Yocto Project will remove `meta/recipes-kernel/linux/linux-dtb.inc`. It is advisable to remove any `require` statements that request `meta/recipes-kernel/linux/linux-dtb.inc` from any custom kernel recipes you might have. This will avoid breakage in post 2.4 releases.

# Package QA Changes {#migration-2.4-package-qa-changes}

The following package QA changes took place:

- The \"unsafe-references-in-scripts\" QA check has been removed.
- If you refer to `${COREBASE}/LICENSE` within `LIC_FILES_CHKSUM`{.interpreted-text role="term"} you receive a warning because this file is a description of the license for OE-Core. Use `${COMMON_LICENSE_DIR}/MIT` if your recipe is MIT-licensed and you cannot use the preferred method of referring to a file within the source tree.

# `README` File Changes {#migration-2.4-readme-changes}

The following are changes to `README` files:

- The main Poky `README` file has been moved to the `meta-poky` layer and has been renamed `README.poky`. A symlink has been created so that references to the old location work.
- The `README.hardware` file has been moved to `meta-yocto-bsp`. A symlink has been created so that references to the old location work.
- A `README.qemu` file has been created with coverage of the `qemu*` machines.

# Miscellaneous Changes {#migration-2.4-miscellaneous-changes}

The following are additional changes:

- The `ROOTFS_PKGMANAGE_BOOTSTRAP` variable and any references to it have been removed. You should remove this variable from any custom recipes.
- The `meta-yocto` directory has been removed.

  ::: note
  ::: title
  Note
  :::

  In the Yocto Project 2.1 release meta-yocto was renamed to meta-poky and the meta-yocto subdirectory remained to avoid breaking existing configurations.
  :::
- The `maintainers.inc` file, which tracks maintainers by listing a primary person responsible for each recipe in OE-Core, has been moved from `meta-poky` to OE-Core (i.e. from `meta-poky/conf/distro/include` to `meta/conf/distro/include`).
- The `ref-classes-buildhistory`{.interpreted-text role="ref"} class now makes a single commit per build rather than one commit per subdirectory in the repository. This behavior assumes the commits are enabled with `BUILDHISTORY_COMMIT`{.interpreted-text role="term"} = \"1\", which is typical. Previously, the `ref-classes-buildhistory`{.interpreted-text role="ref"} class made one commit per subdirectory in the repository in order to make it easier to see the changes for a particular subdirectory. To view a particular change, specify that subdirectory as the last parameter on the `git show` or `git diff` commands.
- The `x86-base.inc` file, which is included by all x86-based machine configurations, now sets `IMAGE_FSTYPES`{.interpreted-text role="term"} using `?=` to \"live\" rather than appending with `+=`. This change makes the default easier to override.
- BitBake fires multiple \"BuildStarted\" events when multiconfig is enabled (one per configuration). For more information, see the \"`bitbake-user-manual/bitbake-user-manual-metadata:events`{.interpreted-text role="ref"}\" section in the BitBake User Manual.
- By default, the `security_flags.inc` file sets a `GCCPIE`{.interpreted-text role="term"} variable with an option to enable Position Independent Executables (PIE) within `gcc`. Enabling PIE in the GNU C Compiler (GCC), makes Return Oriented Programming (ROP) attacks much more difficult to execute.
- OE-Core now provides a `bitbake-layers` plugin that implements a \"create-layer\" subcommand. The implementation of this subcommand has resulted in the `yocto-layer` script being deprecated and will likely be removed in the next Yocto Project release.
- The `vmdk`, `vdi`, and `qcow2` image file types are now used in conjunction with the \"wic\" image type through `CONVERSION_CMD`{.interpreted-text role="term"}. Consequently, the equivalent image types are now `wic.vmdk`, `wic.vdi`, and `wic.qcow2`, respectively.
- `do_image_<type>[depends]` has replaced `IMAGE_DEPENDS_<type>`. If you have your own classes that implement custom image types, then you need to update them.
- OpenSSL 1.1 has been introduced. However, the default is still 1.0.x through the `PREFERRED_VERSION`{.interpreted-text role="term"} variable. This preference is set is due to the remaining compatibility issues with other software. The `PROVIDES`{.interpreted-text role="term"} variable in the openssl 1.0 recipe now includes \"openssl10\" as a marker that can be used in `DEPENDS`{.interpreted-text role="term"} within recipes that build software that still depend on OpenSSL 1.0.
- To ensure consistent behavior, BitBake\'s \"-r\" and \"-R\" options (i.e. prefile and postfile), which are used to read or post-read additional configuration files from the command line, now only affect the current BitBake command. Before these BitBake changes, these options would \"stick\" for future executions.
