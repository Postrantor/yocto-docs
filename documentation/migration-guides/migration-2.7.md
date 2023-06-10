---
tip: translate by openai@2023-06-10 11:07:25
...
---
title: Release 2.7 (warrior)
----------------------------

This section provides migration information for moving to the Yocto Project 2.7 Release (codename \"warrior\") from the prior release.

# BitBake Changes

The following changes have been made to BitBake:

- BitBake now checks anonymous Python functions and pure Python functions (e.g. `def funcname:`) in the metadata for tab indentation. If found, BitBake produces a warning.

> 现在，BitBake 检查元数据中的匿名 Python 函数和纯 Python 函数(例如 `def funcname:`)的制表符缩进。如果发现，BitBake 会产生警告。

- BitBake now checks `BBFILE_COLLECTIONS` for duplicate entries and triggers an error if any are found.

# Eclipse Support Removed

Support for the Eclipse IDE has been removed. Support continues for those releases prior to 2.7 that did include support. The 2.7 release does not include the Eclipse Yocto plugin.

> 支持 Eclipse IDE 的支持已经被取消。对于包含支持的 2.7 之前的版本，支持仍然继续。2.7 版本不包括 Eclipse Yocto 插件。

# `qemu-native` Splits the System and User-Mode Parts

The system and user-mode parts of `qemu-native` are now split. `qemu-native` provides the user-mode components and `qemu-system-native` provides the system components. If you have recipes that depend on QEMU\'s system emulation functionality at build time, they should now depend upon `qemu-system-native` instead of `qemu-native`.

> 系统模式和用户模式部分现在已经分离。`qemu-native` 提供用户模式组件，而 `qemu-system-native` 提供系统组件。如果您的配方在构建时依赖于 QEMU 的系统仿真功能，那么它们现在应该依赖于 `qemu-system-native` 而不是 `qemu-native`。

# The `upstream-tracking.inc` File Has Been Removed

The previously deprecated `upstream-tracking.inc` file is now removed. Any `UPSTREAM_TRACKING*` variables are now set in the corresponding recipes instead.

> 之前已弃用的 `upstream-tracking.inc` 文件现在已被移除。任何 `UPSTREAM_TRACKING*` 变量现在都设置在相应的配方中。

Remove any references you have to the `upstream-tracking.inc` file in your configuration.

# The `DISTRO_FEATURES_LIBC` Variable Has Been Removed

The `DISTRO_FEATURES_LIBC` variable is no longer used. The ability to configure glibc using kconfig has been removed for quite some time making the `libc-*` features set no longer effective.

> 变量 `DISTRO_FEATURES_LIBC` 不再被使用。已有一段时间以来，使用 kconfig 配置 glibc 的能力已被取消，使得 `libc-*` 功能集不再有效。

Remove any references you have to `DISTRO_FEATURES_LIBC` in your own layers.

# License Value Corrections

The following corrections have been made to the `LICENSE` values set by recipes:

- *socat*: Corrected `LICENSE` to be \"GPLv2\" rather than \"GPLv2+\".
- *libgfortran*: Set license to \"GPL-3.0-with-GCC-exception\".
- *elfutils*: Removed \"Elfutils-Exception\" and set to \"GPLv2\" for shared libraries

# Packaging Changes

This section provides information about packaging changes.

- `bind`: The `nsupdate` binary has been moved to the `bind-utils` package.
- Debug split: The default debug split has been changed to create separate source packages (i.e. `package_name-dbg` and `package_name-src`). If you are currently using `dbg-pkgs` in `IMAGE_FEATURES` that does not include `src-pkgs`.

> 调试分割：默认的调试分割已经更改为创建单独的源软件包(即 `package_name-dbg` 和 `package_name-src`)。如果您当前在 `IMAGE_FEATURES` 设置了不包括 `src-pkgs` 的自己的值，否则源软件包默认保留在 SDK 的目标部分中。

- Mount all using `util-linux`: `/etc/default/mountall` has moved into the -mount sub-package.
- Splitting binaries using `util-linux`: `util-linux` now splits each binary into its own package for fine-grained control. The main `util-linux` package pulls in the individual binary packages using the `RRECOMMENDS` is not set.

> 使用 util-linux 分割二进制文件：util-linux 现在将每个二进制文件分割成单独的软件包，以便进行细粒度控制。主要的 util-linux 软件包使用 RRECOMMENDS 和 RDEPENDS 变量拉取单独的二进制软件包。因此，假设未设置 NO_RECOMMENDATIONS，现有镜像不应该看到任何变化。

- `netbase/base-files`: `/etc/hosts` has moved from `netbase` to `base-files`.
- `tzdata`: The main package has been converted to an empty meta package that pulls in all `tzdata` packages by default.
- `lrzsz`: This package has been removed from `packagegroup-self-hosted` and `packagegroup-core-tools-testapps`. The X/Y/ZModem support is less likely to be needed on modern systems. If you are relying on these packagegroups to include the `lrzsz` package in your image, you now need to explicitly add the package.

> 这个软件包已从 `packagegroup-self-hosted` 和 `packagegroup-core-tools-testapps` 中移除。现代系统不太可能需要 X / Y / ZModem 支持。如果您依赖这些软件包组来将 `lrzsz` 软件包添加到您的镜像中，现在您需要明确添加该软件包。

# Removed Recipes

The following recipes have been removed:

- *gcc*: Drop version 7.3 recipes. Version 8.3 now remains.
- *linux-yocto*: Drop versions 4.14 and 4.18 recipes. Versions 4.19 and 5.0 remain.
- *go*: Drop version 1.9 recipes. Versions 1.11 and 1.12 remain.
- *xvideo-tests*: Became obsolete.
- *libart-lgpl*: Became obsolete.
- *gtk-icon-utils-native*: These tools are now provided by gtk+3-native
- *gcc-cross-initial*: No longer needed. gcc-cross/gcc-crosssdk is now used instead.
- *gcc-crosssdk-initial*: No longer needed. gcc-cross/gcc-crosssdk is now used instead.
- *glibc-initial*: Removed because the benefits of having it for site_config are currently outweighed by the cost of building the recipe.

# Removed Classes

The following classes have been removed:

- *distutils-tools*: This class was never used.
- *bugzilla.bbclass*: Became obsolete.
- *distrodata*: This functionally has been replaced by a more modern tinfoil-based implementation.

# Miscellaneous Changes

The following miscellaneous changes occurred:

- The `distro` subdirectory of the Poky repository has been removed from the top-level `scripts` directory.
- Perl now builds for the target using [perl-cross](https://arsv.github.io/perl-cross/) for better maintainability and improved build performance. This change should not present any problems unless you have heavily customized your Perl recipe.

> 現在，Perl 使用 [perl-cross](https://arsv.github.io/perl-cross/) 對目標進行構建，以提高可維護性和構建性能。除非您對 Perl 配方進行了大量定制，否則此變更不會出現任何問題。

- `arm-tunes`: Removed the \"-march\" option if mcpu is already added.
- `update-alternatives`: Convert file renames to `PACKAGE_PREPROCESS_FUNCS`
- `base/pixbufcache`: Obsolete `sstatecompletions` code has been removed.
- `ref-classes-native` handling has been enabled.
- `inetutils`: This recipe has rsh disabled.
