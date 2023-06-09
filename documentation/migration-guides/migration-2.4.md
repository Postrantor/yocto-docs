---
tip: translate by openai@2023-06-07 21:43:43
...
---
title: Release 2.4 (rocko)
--------------------------

This section provides migration information for moving to the Yocto Project 2.4 Release (codename \"rocko\") from the prior release.

> 这一节提供了从之前的发行版本迁移到 Yocto 项目 2.4 发行版(代号“rocko”)的迁移信息。

# Memory Resident Mode

A persistent mode is now available in BitBake\'s default operation, replacing its previous \"memory resident mode\" (i.e. `oe-init-build-env-memres`). Now you only need to set `BB_SERVER_TIMEOUT` to a timeout (in seconds) and BitBake\'s server stays resident for that amount of time between invocations. The `oe-init-build-env-memres` script has been removed since a separate environment setup script is no longer needed.

> 现在 BitBake 的默认操作中可以使用一种持久模式，取代先前的“内存驻留模式”(即 `oe-init-build-env-memres`)。现在，您只需将 `BB_SERVER_TIMEOUT` 设置为超时(以秒为单位)，BitBake 的服务器将在调用之间保持驻留该时间量。由于不再需要单独的环境设置脚本，因此已经删除 `oe-init-build-env-memres` 脚本。

# Packaging Changes

This section provides information about packaging changes that have occurred:

> 这一节提供了有关打包变更的信息：

- `python3` Changes:

  - The main \"python3\" package now brings in all of the standard Python 3 distribution rather than a subset. This behavior matches what is expected based on traditional Linux distributions. If you wish to install a subset of Python 3, specify `python-core` plus one or more of the individual packages that are still produced.

> 主要的"python3"包现在拥有完整的 Python 3 分发版本，而不是一个子集。这种行为与传统的 Linux 发行版本所期望的相匹配。如果您想安装一个 Python 3 的子集，请指定 `python-core` 加上一个或多个仍然生成的单独的包。

- `python3`: The `bz2.py`, `lzma.py`, and `_compression.py` scripts have been moved from the `python3-misc` package to the `python3-compression` package.

> `python3`：`bz2.py`、`lzma.py` 和 `_compression.py` 脚本已从 `python3-misc` 软件包移动到 `python3-compression` 软件包中。

- `binutils`: The `libbfd` library is now packaged in a separate \"libbfd\" package. This packaging saves space when certain tools (e.g. `perf`) are installed. In such cases, the tools only need `libbfd` rather than all the packages in `binutils`.

> `binutils`：现在将 `libbfd` 库打包成一个单独的“libbfd”软件包。当安装某些工具(例如 `perf`)时，此打包方式可以节省空间。在这种情况下，这些工具只需要 `libbfd` 而不需要 `binutils` 中的所有软件包。

- `util-linux` Changes:

  - The `su` program is now packaged in a separate \"util-linux-su\" package, which is only built when \"pam\" is listed in the `DISTRO_FEATURES`.

> 现在将 `su` 程序打包在一个单独的“util-linux-su”软件包中，只有在“pam”被列入 `DISTRO_FEATURES`)`util-linux-su` 软件包。

- The `switch_root` program is now packaged in a separate \"util-linux-switch-root\" package for small `Initramfs`) on the `util-linux-switch-root` package.

> `switch_root` 程序现在被打包到一个单独的“util-linux-switch-root”软件包中，用于不需要整个 `util-linux` 软件包或更大的 `busybox` 二进制文件的小 `Initramfs` 映像。主 `util-linux` 软件包有一个运行时依赖(即 `RRECOMMENDS`)`util-linux-switch-root` 软件包。

- The `ionice` program is now packaged in a separate \"util-linux-ionice\" package. The main `util-linux` package has a recommended runtime dependency (i.e. `RRECOMMENDS`) on the `util-linux-ionice` package.

> 现在，`ionice` 程序已经打包在一个单独的“util-linux-ionice”软件包中。主 `util-linux` 软件包推荐在 `util-linux-ionice` 软件包上运行时依赖(即 `RRECOMMENDS`)。

- `initscripts`: The `sushell` program is now packaged in a separate \"initscripts-sushell\" package. This packaging change allows systems to pull `sushell` in when `selinux` is enabled. The change also eliminates needing to pull in the entire `initscripts` package. The main `initscripts` package has a runtime dependency (i.e. `RDEPENDS`.

> `initscripts`：当启用 `selinux` 时，现在将 `sushell` 程序打包到一个单独的“initscripts-sushell”软件包中。这种打包更改允许系统在启用 `selinux` 时拉取 `sushell`。该更改还可以消除需要拉取整个 `initscripts` 软件包的需求。当“selinux”在 `DISTRO_FEATURES`)在 `sushell` 软件包上。

- `glib-2.0`: The `glib-2.0` package now has a recommended runtime dependency (i.e. `RRECOMMENDS` variable if `shared-mime-info` is too large and is not required.

> `glib-2.0` 包现在有一个推荐的运行时依赖(即 `RRECOMMENDS` 变量来移除这个依赖。

- *Go Standard Runtime:* The Go standard runtime has been split out from the main `go` recipe into a separate `go-runtime` recipe.

> Go 标准运行时：Go 标准运行时已从主 `go` 配方中分离出单独的 `go-runtime` 配方。

# Removed Recipes

The following recipes have been removed:

> 以下 recipes 已被移除：

- `acpitests`: This recipe is not maintained.
- `autogen-native`: No longer required by Grub, oe-core, or meta-oe.
- `bdwgc`: Nothing in OpenEmbedded-Core requires this recipe. It has moved to meta-oe.
- `byacc`: This recipe was only needed by rpm 5.x and has moved to meta-oe.
- `gcc (5.4)`: The 5.4 series dropped the recipe in favor of 6.3 / 7.2.
- `gnome-common`: Deprecated upstream and no longer needed.
- `go-bootstrap-native`: Go 1.9 does its own bootstrapping so this recipe has been removed.
- `guile`: This recipe was only needed by `autogen-native` and `remake`. The recipe is no longer needed by either of these programs.

> 这个 recipes 只被 `autogen-native` 和 `remake` 所需要，但现在这两个程序都不再需要这个 recipes 了。

- `libclass-isa-perl`: This recipe was previously needed for LSB 4, no longer needed.
- `libdumpvalue-perl`: This recipe was previously needed for LSB 4, no longer needed.
- `libenv-perl`: This recipe was previously needed for LSB 4, no longer needed.
- `libfile-checktree-perl`: This recipe was previously needed for LSB 4, no longer needed.
- `libi18n-collate-perl`: This recipe was previously needed for LSB 4, no longer needed.
- `libiconv`: This recipe was only needed for `uclibc`, which was removed in the previous release. `glibc` and `musl` have their own implementations. `meta-mingw` still needs `libiconv`, so it has been moved to `meta-mingw`.

> `libiconv`：在上一个版本中，这个配方仅需要 `uclibc`，但已经被移除了。`glibc` 和 `musl` 有自己的实现。`meta-mingw` 仍然需要 `libiconv`，因此它已经被移动到 `meta-mingw` 中。

- `libpng12`: This recipe was previously needed for LSB. The current `libpng` is 1.6.x.
- `libpod-plainer-perl`: This recipe was previously needed for LSB 4, no longer needed.
- `linux-yocto (4.1)`: This recipe was removed in favor of 4.4, 4.9, 4.10 and 4.12.
- `mailx`: This recipe was previously only needed for LSB compatibility, and upstream is defunct.
- `mesa (git version only)`: The git version recipe was stale with respect to the release version.
- `ofono (git version only)`: The git version recipe was stale with respect to the release version.

> 只有 Git 版本的 ofono：与发布版本相比，Git 版本的 recipes 已经过时了。

- `portmap`: This recipe is obsolete and is superseded by `rpcbind`.
- `python3-pygpgme`: This recipe is old and unmaintained. It was previously required by `dnf`, which has switched to official `gpgme` Python bindings.

> - `python3-pygpgme`：这个 recipes 已经过时且未维护。它以前被 `dnf` 所需，但 `dnf` 已经转换到官方的 `gpgme` Python 绑定。

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

> 使用 `remake` 作为 `virtual/make` 的提供者已经损坏。因此，OpenEmbedded-Core 中不需要这个配方了。

# Kernel Device Tree Move

Kernel Device Tree support is now easier to enable in a kernel recipe. The Device Tree code has moved to a `ref-classes-kernel-devicetree` variable. The previous mechanism for doing this, `meta/recipes-kernel/linux/linux-dtb.inc`, is still available to avoid breakage, but triggers a deprecation warning. Future releases of the Yocto Project will remove `meta/recipes-kernel/linux/linux-dtb.inc`. It is advisable to remove any `require` statements that request `meta/recipes-kernel/linux/linux-dtb.inc` from any custom kernel recipes you might have. This will avoid breakage in post 2.4 releases.

> 支持内核设备树现在更容易在内核配方中启用。设备树代码已经移动到 `ref-classes-kernel-devicetree` 变量的任何配方，都会自动启用功能。之前用于执行此操作的机制 `meta/recipes-kernel/linux/linux-dtb.inc` 仍然可用，以避免破坏，但会触发弃用警告。Yocto 项目的未来版本将删除 `meta/recipes-kernel/linux/linux-dtb.inc`。建议从您可能拥有的任何自定义内核配方中删除请求 `meta/recipes-kernel/linux/linux-dtb.inc` 的任何 `require` 语句。这将避免 2.4 版本后的破坏。

# Package QA Changes

The following package QA changes took place:

> 以下的软件包 QA 变更已经发生：

- The \"unsafe-references-in-scripts\" QA check has been removed.
- If you refer to `$/MIT` if your recipe is MIT-licensed and you cannot use the preferred method of referring to a file within the source tree.

> 如果你在 `LIC_FILES_CHKSUM` 中引用 `$/MIT`。

# `README` File Changes

The following are changes to `README` files:

> 以下是对 `README` 文件的更改：

- The main Poky `README` file has been moved to the `meta-poky` layer and has been renamed `README.poky`. A symlink has been created so that references to the old location work.

> 主要的 Poky `README` 文件已经移动到 `meta-poky` 层并被重命名为 `README.poky`。已经创建了一个符号链接，以便对旧位置的引用可以正常工作。

- The `README.hardware` file has been moved to `meta-yocto-bsp`. A symlink has been created so that references to the old location work.

> `README.hardware` 文件已移动到 `meta-yocto-bsp` 中。已创建一个符号链接，以便对旧位置的引用有效。

- A `README.qemu` file has been created with coverage of the `qemu*` machines.

# Miscellaneous Changes

The following are additional changes:

> 以下是额外的更改：

- The `ROOTFS_PKGMANAGE_BOOTSTRAP` variable and any references to it have been removed. You should remove this variable from any custom recipes.

> `ROOTFS_PKGMANAGE_BOOTSTRAP` 变量及其所有引用已被删除。您应该从任何自定义 recipes 中删除此变量。

- The `meta-yocto` directory has been removed.

  ::: note
  ::: title

  Note

> 注意
> :::

In the Yocto Project 2.1 release meta-yocto was renamed to meta-poky and the meta-yocto subdirectory remained to avoid breaking existing configurations.

> 在 Yocto Project 2.1 发行版中，meta-yocto 被重命名为 meta-poky，并且保留了 meta-yocto 子目录以避免破坏现有配置。
> :::

- The `maintainers.inc` file, which tracks maintainers by listing a primary person responsible for each recipe in OE-Core, has been moved from `meta-poky` to OE-Core (i.e. from `meta-poky/conf/distro/include` to `meta/conf/distro/include`).

> maintainers.inc 文件，它跟踪 OE-Core 中每个 recipes 的主要负责人，已经从 meta-poky 移动到 OE-Core(即从 meta-poky/conf/distro/include 移动到 meta/conf/distro/include)。

- The `ref-classes-buildhistory` class made one commit per subdirectory in the repository in order to make it easier to see the changes for a particular subdirectory. To view a particular change, specify that subdirectory as the last parameter on the `git show` or `git diff` commands.

> `ref-classes-buildhistory` 类在存储库中的每个子目录中做一次提交，以便更容易看到特定子目录的变化。要查看特定的变化，请将该子目录指定为 `git show` 或 `git diff` 命令的最后一个参数。

- The `x86-base.inc` file, which is included by all x86-based machine configurations, now sets `IMAGE_FSTYPES` using `?=` to \"live\" rather than appending with `+=`. This change makes the default easier to override.

> 文件 x86-base.inc，被所有基于 x86 的机器配置所包含，现在使用“?=”设置 IMAGE_FSTYPES 为“live”，而不是使用“+=”来追加。这一更改使默认值更容易被覆盖。

- BitBake fires multiple \"BuildStarted\" events when multiconfig is enabled (one per configuration). For more information, see the \"`bitbake-user-manual/bitbake-user-manual-metadata:events`\" section in the BitBake User Manual.

> BitBake 在启用了多配置功能时会发出多个“BuildStarted”事件(每个配置一个)。有关更多信息，请参阅 BitBake 用户手册中的“bitbake-user-manual/bitbake-user-manual-metadata：events”部分。

- By default, the `security_flags.inc` file sets a `GCCPIE` variable with an option to enable Position Independent Executables (PIE) within `gcc`. Enabling PIE in the GNU C Compiler (GCC), makes Return Oriented Programming (ROP) attacks much more difficult to execute.

> 默认情况下，`security_flags.inc` 文件使用 `GCCPIE` 变量设置选项以在 `gcc` 中启用位置独立可执行文件(PIE)。在 GNU C 编译器(GCC)中启用 PIE，可以使返回导向编程(ROP)攻击更加难以执行。

- OE-Core now provides a `bitbake-layers` plugin that implements a \"create-layer\" subcommand. The implementation of this subcommand has resulted in the `yocto-layer` script being deprecated and will likely be removed in the next Yocto Project release.

> OE-Core 现在提供了一个 `bitbake-layers` 插件，它实现了一个“create-layer”子命令。这个子命令的实现导致 `yocto-layer` 脚本被弃用，并可能在下一个 Yocto Project 发布中删除。

- The `vmdk`, `vdi`, and `qcow2` image file types are now used in conjunction with the \"wic\" image type through `CONVERSION_CMD`. Consequently, the equivalent image types are now `wic.vmdk`, `wic.vdi`, and `wic.qcow2`, respectively.

> 这三种镜像文件类型(vmdk、vdi 和 qcow2)现在可以通过 CONVERSION_CMD 与“wic”镜像类型配合使用。因此，等效的镜像类型现在分别是 wic.vmdk、wic.vdi 和 wic.qcow2。

- `do_image_<type>[depends]` has replaced `IMAGE_DEPENDS_<type>`. If you have your own classes that implement custom image types, then you need to update them.

> `- ` do_image_<type>[depends]`已取代了` IMAGE_DEPENDS_<type>`。如果您有自己的实现自定义镜像类型的类，则需要对其进行更新。

- OpenSSL 1.1 has been introduced. However, the default is still 1.0.x through the `PREFERRED_VERSION` within recipes that build software that still depend on OpenSSL 1.0.

> OpenSSL 1.1 已经引入。但是，默认仍然是通过 `PREFERRED_VERSION` 变量使用 1.0.x 版本。这种偏好是由于与其他软件的兼容性问题而设置的。openssl 1.0 配方中的 `PROVIDES` 变量现在包括“openssl10”作为一个标记，可以在构建仍然依赖 OpenSSL 1.0 的配方的 `DEPENDS` 中使用。

- To ensure consistent behavior, BitBake\'s \"-r\" and \"-R\" options (i.e. prefile and postfile), which are used to read or post-read additional configuration files from the command line, now only affect the current BitBake command. Before these BitBake changes, these options would \"stick\" for future executions.

> 为了确保一致的行为，BitBake 的“-r”和“-R”选项(即 prefile 和 postfile)，用于从命令行读取或后读取附加配置文件，现在只影响当前的 BitBake 命令。在这些 BitBake 更改之前，这些选项会“坚持”未来的执行。
