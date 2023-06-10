---
tip: translate by openai@2023-06-10 11:07:03
...
---
title: Release 2.6 (thud)
-------------------------

This section provides migration information for moving to the Yocto Project 2.6 Release (codename \"thud\") from the prior release.

# GCC 8.2 is Now Used by Default {#migration-2.6-gcc-changes}

The GNU Compiler Collection version 8.2 is now used by default for compilation. For more information on what has changed in the GCC 8.x release, see [https://gcc.gnu.org/gcc-8/changes.html](https://gcc.gnu.org/gcc-8/changes.html).

> 现在默认使用 GNU 编译器集合版本 8.2 进行编译。有关 GCC 8.x 发行版中发生的变化的更多信息，请参见 [https://gcc.gnu.org/gcc-8/changes.html](https://gcc.gnu.org/gcc-8/changes.html)。

If you still need to compile with version 7.x, GCC 7.3 is also provided. You can select this version by setting the and can be selected by setting the `GCCVERSION`{.interpreted-text role="term"} variable to \"7.%\" in your configuration.

> 如果你仍然需要用 7.x 版本编译，GCC 7.3 也提供了。你可以通过在配置中设置 `GCCVERSION` 变量为"7.%"来选择这个版本。

# Removed Recipes {#migration-2.6-removed-recipes}

The following recipes have been removed:

- *beecrypt*: No longer needed since moving to RPM 4.
- *bigreqsproto*: Replaced by `xorgproto`.
- *calibrateproto*: Removed in favor of `xinput`.
- *compositeproto*: Replaced by `xorgproto`.
- *damageproto*: Replaced by `xorgproto`.
- *dmxproto*: Replaced by `xorgproto`.
- *dri2proto*: Replaced by `xorgproto`.
- *dri3proto*: Replaced by `xorgproto`.
- *eee-acpi-scripts*: Became obsolete.
- *fixesproto*: Replaced by `xorgproto`.
- *fontsproto*: Replaced by `xorgproto`.
- *fstests*: Became obsolete.
- *gccmakedep*: No longer used.
- *glproto*: Replaced by `xorgproto`.
- *gnome-desktop3*: No longer needed. This recipe has moved to `meta-oe`.
- *icon-naming-utils*: No longer used since the Sato theme was removed in 2016.
- *inputproto*: Replaced by `xorgproto`.
- *kbproto*: Replaced by `xorgproto`.
- *libusb-compat*: Became obsolete.
- *libuser*: Became obsolete.
- *libnfsidmap*: No longer an external requirement since `nfs-utils` 2.2.1. `libnfsidmap` is now integrated.
- *libxcalibrate*: No longer needed with `xinput`
- *mktemp*: Became obsolete. The `mktemp` command is provided by both `busybox` and `coreutils`.
- *ossp-uuid*: Is not being maintained and has mostly been replaced by `uuid.h` in `util-linux`.
- *pax-utils*: No longer needed. Previous QA tests that did use this recipe are now done at build time.
- *pcmciautils*: Became obsolete.
- *pixz*: No longer needed. `xz` now supports multi-threaded compression.
- *presentproto*: Replaced by `xorgproto`.
- *randrproto*: Replaced by `xorgproto`.
- *recordproto*: Replaced by `xorgproto`.
- *renderproto*: Replaced by `xorgproto`.
- *resourceproto*: Replaced by `xorgproto`.
- *scrnsaverproto*: Replaced by `xorgproto`.
- *trace-cmd*: Became obsolete. `perf` replaced this recipe\'s functionally.
- *videoproto*: Replaced by `xorgproto`.
- *wireless-tools*: Became obsolete. Superseded by `iw`.
- *xcmiscproto*: Replaced by `xorgproto`.
- *xextproto*: Replaced by `xorgproto`.
- *xf86dgaproto*: Replaced by `xorgproto`.
- *xf86driproto*: Replaced by `xorgproto`.
- *xf86miscproto*: Replaced by `xorgproto`.
- *xf86-video-omapfb*: Became obsolete. Use kernel modesetting driver instead.
- *xf86-video-omap*: Became obsolete. Use kernel modesetting driver instead.
- *xf86vidmodeproto*: Replaced by `xorgproto`.
- *xineramaproto*: Replaced by `xorgproto`.
- *xproto*: Replaced by `xorgproto`.
- *yasm*: No longer needed since previous usages are now satisfied by `nasm`.

# Packaging Changes {#migration-2.6-packaging-changes}

The following packaging changes have been made:

- *cmake*: `cmake.m4` and `toolchain` files have been moved to the main package.
- *iptables*: The `iptables` modules have been split into separate packages.
- *alsa-lib*: `libasound` is now in the main `alsa-lib` package instead of `libasound`.
- *glibc*: `libnss-db` is now in its own package along with a `/var/db/makedbs.sh` script to update databases.
- *python and python3*: The main package has been removed from the recipe. You must install specific packages or `python-modules` / `python3-modules` for everything.

> 包已从配方中移除。您必须安装特定的包或 `python-modules`/`python3-modules` 才能完成所有操作。

- *systemtap*: Moved `systemtap-exporter` into its own package.

# XOrg Protocol dependencies {#migration-2.6-xorg-protocol-dependencies}

The `*proto` upstream repositories have been combined into one \"xorgproto\" repository. Thus, the corresponding recipes have also been combined into a single `xorgproto` recipe. Any recipes that depend upon the older `*proto` recipes need to be changed to depend on the newer `xorgproto` recipe instead.

> 所有的 `proto` 上游仓库已经合并成一个"xorgproto"仓库。因此，相应的配方也合并成一个单一的 `xorgproto` 配方。任何依赖旧的 `*proto` 配方的配方都需要改为依赖新的 `xorgproto` 配方。

For names of recipes removed because of this repository change, see the `migration-guides/migration-2.6:removed recipes`{.interpreted-text role="ref"} section.

> 对于因此存储库更改而被移除的食谱名称，请参阅“迁移指南/迁移 2.6：移除食谱”部分。

# `distutils` and `distutils3` Now Prevent Fetching Dependencies During the `do_configure` Task {#migration-2.6-distutils-distutils3-fetching-dependencies}

Previously, it was possible for Python recipes that inherited the `distutils` and `distutils3` classes to fetch code during the `ref-tasks-configure`{.interpreted-text role="ref"} task to satisfy dependencies mentioned in `setup.py` if those dependencies were not provided in the sysroot (i.e. recipes providing the dependencies were missing from `DEPENDS`{.interpreted-text role="term"}).

> 以前，继承了 `distutils` 和 `distutils3` 类的 Python 食谱可以在 `ref-tasks-configure` 任务期间获取代码，以满足 `setup.py` 中提到的依赖项，如果这些依赖项在 sysroot 中没有提供（即 `DEPENDS` 中缺少提供这些依赖项的食谱）。

::: note
::: title
Note
:::

This change affects classes beyond just the two mentioned (i.e. `distutils` and `distutils3`). Any recipe that inherits `distutils*` classes are affected. For example, the `setuptools` and `ref-classes-setuptools3`{.interpreted-text role="ref"} recipes are affected since they inherit the `distutils*` classes.

> 这个改变不仅影响到前面提到的两个类（即 `distutils` 和 `distutils3`）。任何继承 `distutils*` 类的配方都受到影响。例如，`setuptools` 和 `ref-classes-setuptools3`{.interpreted-text role="ref"}配方受到影响，因为它们继承了 `distutils*` 类。
> :::

Fetching these types of dependencies that are not provided in the sysroot negatively affects the ability to reproduce builds. This type of fetching is now explicitly disabled. Consequently, any missing dependencies in Python recipes that use these classes now result in an error during the `ref-tasks-configure`{.interpreted-text role="ref"} task.

> 从系统根目录中获取这些未提供的依赖项会影响重现构建的能力。现在明确禁止这种获取方式。因此，使用这些类的 Python 配方中的任何缺失依赖项现在会在 `ref-tasks-configure`{.interpreted-text role="ref"}任务期间导致错误。

# `linux-yocto` Configuration Audit Issues Now Correctly Reported {#migration-2.6-linux-yocto-configuration-audit-issues-now-correctly-reported}

Due to a bug, the kernel configuration audit functionality was not writing out any resulting warnings during the build. This issue is now corrected. You might notice these warnings now if you have a custom kernel configuration with a `linux-yocto` style kernel recipe.

> 由于存在一个 bug，内核配置审计功能在构建期间没有输出任何警告。该问题现在已经得到修正。如果您有一个使用 `linux-yocto` 样式内核配方的自定义内核配置，您可能会注意到这些警告。

# Image/Kernel Artifact Naming Changes {#migration-2.6-image-kernel-artifact-naming-changes}

The following changes have been made:

- Name variables (e.g. `IMAGE_NAME`{.interpreted-text role="term"}) use a new `IMAGE_VERSION_SUFFIX`{.interpreted-text role="term"} variable instead of `DATETIME`{.interpreted-text role="term"}. Using `IMAGE_VERSION_SUFFIX`{.interpreted-text role="term"} allows easier and more direct changes.

> 更改变量名（例如 `IMAGE_NAME`），使用新的 `IMAGE_VERSION_SUFFIX` 变量代替 `DATETIME`。使用 `IMAGE_VERSION_SUFFIX` 可以更容易、更直接地进行更改。

The `IMAGE_VERSION_SUFFIX`{.interpreted-text role="term"} variable is set in the `bitbake.conf` configuration file as follows:

```
IMAGE_VERSION_SUFFIX = "-${DATETIME}"
```

- Several variables have changed names for consistency:

  ```
  Old Variable Name             New Variable Name
  ========================================================
  KERNEL_IMAGE_BASE_NAME        KERNEL_IMAGE_NAME
  KERNEL_IMAGE_SYMLINK_NAME     KERNEL_IMAGE_LINK_NAME
  MODULE_TARBALL_BASE_NAME      MODULE_TARBALL_NAME
  MODULE_TARBALL_SYMLINK_NAME   MODULE_TARBALL_LINK_NAME
  INITRAMFS_BASE_NAME           INITRAMFS_NAME
  ```
- The `MODULE_IMAGE_BASE_NAME` variable has been removed. The module tarball name is now controlled directly with the `MODULE_TARBALL_NAME`{.interpreted-text role="term"} variable.

> `MODULE_IMAGE_BASE_NAME` 变量已被移除。模块归档文件的名称现在直接由 `MODULE_TARBALL_NAME` 变量控制。

- The `KERNEL_DTB_NAME`{.interpreted-text role="term"} and `KERNEL_DTB_LINK_NAME`{.interpreted-text role="term"} variables have been introduced to control kernel Device Tree Binary (DTB) artifact names instead of mangling `KERNEL_IMAGE_*` variables.

> 引入了 `KERNEL_DTB_NAME`{.interpreted-text role="term"}和 `KERNEL_DTB_LINK_NAME`{.interpreted-text role="term"}变量，以控制内核设备树二进制（DTB）工件名称，而不是混淆 `KERNEL_IMAGE_*` 变量。

- The `KERNEL_FIT_NAME`{.interpreted-text role="term"} and `KERNEL_FIT_LINK_NAME`{.interpreted-text role="term"} variables have been introduced to specify the name of flattened image tree (FIT) kernel images similar to other deployed artifacts.

> 引入了 `KERNEL_FIT_NAME`{.interpreted-text role="term"}和 `KERNEL_FIT_LINK_NAME`{.interpreted-text role="term"}这两个变量，以指定压缩映像树（FIT）内核映像的名称，与其他部署的工件类似。

- The `MODULE_TARBALL_NAME`{.interpreted-text role="term"} and `MODULE_TARBALL_LINK_NAME`{.interpreted-text role="term"} variable values no longer include the \"module-\" prefix or \".tgz\" suffix. These parts are now hardcoded so that the values are consistent with other artifact naming variables.

> `MODULE_TARBALL_NAME`{.interpreted-text role="term"}和 `MODULE_TARBALL_LINK_NAME`{.interpreted-text role="term"}变量的值不再包括“module-”前缀或“.tgz”后缀。这些部分现在是硬编码的，以便值与其他工件命名变量一致。

- Added the `INITRAMFS_LINK_NAME`{.interpreted-text role="term"} variable so that the symlink can be controlled similarly to other artifact types.
- `INITRAMFS_NAME`{.interpreted-text role="term"} now uses \"\${PKGE}-\${PKGV}-\${PKGR}-\${MACHINE}\${IMAGE_VERSION_SUFFIX}\" instead of \"\${PV}-\${PR}-\${MACHINE}-\${DATETIME}\", which makes it consistent with other variables.

> INITRAMFS_NAME 现在使用"\${PKGE}-\${PKGV}-\${PKGR}-\${MACHINE}\${IMAGE_VERSION_SUFFIX}"代替"\${PV}-\${PR}-\${MACHINE}-\${DATETIME}"，这使它与其他变量一致。

# `SERIAL_CONSOLE` Deprecated {#migration-2.6-serial-console-deprecated}

The `SERIAL_CONSOLE` variable has been functionally replaced by the `SERIAL_CONSOLES`{.interpreted-text role="term"} variable for some time. With the Yocto Project 2.6 release, `SERIAL_CONSOLE` has been officially deprecated.

> `SERIAL_CONSOLE` 变量已经有一段时间被 `SERIAL_CONSOLES` 变量功能性地取代了。随着 Yocto Project 2.6 的发布，`SERIAL_CONSOLE` 已经正式弃用。

`SERIAL_CONSOLE` will continue to work as before for the 2.6 release. However, for the sake of future compatibility, it is recommended that you replace all instances of `SERIAL_CONSOLE` with `SERIAL_CONSOLES`{.interpreted-text role="term"}.

> `SERIAL_CONSOLE` 在 2.6 发行版中将继续如常工作。但为了未来的兼容性，建议您将所有 `SERIAL_CONSOLE` 实例替换为 `SERIAL_CONSOLES`。

::: note
::: title
Note
:::

The only difference in usage is that `SERIAL_CONSOLES`{.interpreted-text role="term"} expects entries to be separated using semicolons as compared to `SERIAL_CONSOLE`, which expects spaces.

> 唯一的用法不同之处在于 `SERIAL_CONSOLES` 需要使用分号来分隔条目，而 `SERIAL_CONSOLE` 则需要使用空格来分隔。
> :::

# Configure Script Reports Unknown Options as Errors {#migration-2.6-poky-sets-unknown-configure-option-to-qa-error}

If the configure script reports an unknown option, this now triggers a QA error instead of a warning. Any recipes that previously got away with specifying such unknown options now need to be fixed.

> 如果配置脚本报告有未知选项，现在会触发 QA 错误而不是警告。以前可以指定这种未知选项的食谱现在需要修复。

# Override Changes {#migration-2.6-override-changes}

The following changes have occurred:

- The `virtclass-native` and `virtclass-nativesdk` Overrides Have Been Removed: The `virtclass-native` and `virtclass-nativesdk` overrides have been deprecated since 2012 in favor of `class-native` and `class-nativesdk`, respectively. Both `virtclass-native` and `virtclass-nativesdk` are now dropped.

> 已经取消了 `virtclass-native` 和 `virtclass-nativesdk` 覆盖：自 2012 年以来，`virtclass-native` 和 `virtclass-nativesdk` 已经被弃用，分别改用 `class-native` 和 `class-nativesdk`。现在已经取消了 `virtclass-native` 和 `virtclass-nativesdk`。

::: note
::: title
Note
:::

The `virtclass-multilib-` overrides for multilib are still valid.
:::

- The `forcevariable` Override Now Has a Higher Priority Than `libc` Overrides: The `forcevariable` override is documented to be the highest priority override. However, due to a long-standing quirk of how `OVERRIDES`{.interpreted-text role="term"} is set, the `libc` overrides (e.g. `libc-glibc`, `libc-musl`, and so forth) erroneously had a higher priority. This issue is now corrected.

> - `forcevariable` 覆盖现在比 `libc` 覆盖优先级更高：`forcevariable` 覆盖已被记录为最高优先级覆盖。 然而，由于 `OVERRIDES`{.interpreted-text role="term"}设置的长期怪癖，`libc` 覆盖（例如 `libc-glibc`，`libc-musl` 等）错误地具有更高的优先级。 现在已经纠正了这个问题。

It is likely this change will not cause any problems. However, it is possible with some unusual configurations that you might see a change in behavior if you were relying on the previous behavior. Be sure to check how you use `forcevariable` and `libc-*` overrides in your custom layers and configuration files to ensure they make sense.

> 这种变化很可能不会引起任何问题。但是，如果您依赖之前的行为，在一些不寻常的配置下可能会看到行为的变化。请务必检查您在自定义图层和配置文件中如何使用 `forcevariable` 和 `libc-*` 覆盖，以确保它们是有意义的。

- The `build-${BUILD_OS}` Override Has Been Removed: The `build-${BUILD_OS}`, which is typically `build-linux`, override has been removed because building on a host operating system other than a recent version of Linux is neither supported nor recommended. Dropping the override avoids giving the impression that other host operating systems might be supported.

> 已移除 `build-${BUILD_OS}` 覆盖：通常是 `build-linux` 的 `build-${BUILD_OS}` 覆盖已被移除，因为在非最新版本的 Linux 主机操作系统上构建是不受支持也不建议的。去掉覆盖可以避免给其他主机操作系统带来可能被支持的错觉。

- The \"\_remove\" operator now preserves whitespace. Consequently, when specifying list items to remove, be aware that leading and trailing whitespace resulting from the removal is retained.

> "_remove"操作符现在保留了空格。因此，当指定要删除的列表项时，请注意由删除而产生的前导和尾随空格将被保留。

See the \"`bitbake-user-manual/bitbake-user-manual-metadata:removal (override style syntax)`{.interpreted-text role="ref"}\" section in the BitBake User Manual for a detailed example.

> 见 BitBake 用户手册中的“bitbake-user-manual/bitbake-user-manual-metadata：removal（覆盖样式语法）”部分，有详细的示例。

# `systemd` Configuration is Now Split Into `systemd-conf` {#migration-2.6-systemd-configuration-now-split-out-to-system-conf}

The configuration for the `systemd` recipe has been moved into a `system-conf` recipe. Moving this configuration to a separate recipe avoids the `systemd` recipe from becoming machine-specific for cases where machine-specific configurations need to be applied (e.g. for `qemu*` machines).

> 配置文件已经从 `systemd` 食谱移到 `system-conf` 食谱中。将此配置移到单独的食谱中可以避免 `systemd` 食谱变得特定于机器，以便在需要应用特定于机器的配置（例如 `qemu*` 机器）的情况下使用。

Currently, the new recipe packages the following files:

```
${sysconfdir}/machine-id
${sysconfdir}/systemd/coredump.conf
${sysconfdir}/systemd/journald.conf
${sysconfdir}/systemd/logind.conf
${sysconfdir}/systemd/system.conf
${sysconfdir}/systemd/user.conf
```

If you previously used bbappend files to append the `systemd` recipe to change any of the listed files, you must do so for the `systemd-conf` recipe instead.

> 如果您以前使用 bbappend 文件来附加 `systemd` 配方以更改列出的任何文件，您必须为 `systemd-conf` 配方执行相同操作。

# Automatic Testing Changes {#migration-2.6-automatic-testing-changes}

This section provides information about automatic testing changes:

- `TEST_IMAGE` Variable Removed: Prior to this release, you set the `TEST_IMAGE` variable to \"1\" to enable automatic testing for successfully built images. The `TEST_IMAGE` variable no longer exists and has been replaced by the `TESTIMAGE_AUTO`{.interpreted-text role="term"} variable.

> 在本次发布之前，您可以将 `TEST_IMAGE` 变量设置为“1”以启用成功构建的图像的自动测试。 `TEST_IMAGE` 变量不再存在，已由 `TESTIMAGE_AUTO` 变量替换。

- Inheriting the `ref-classes-testimage`{.interpreted-text role="ref"} and `ref-classes-testsdk`{.interpreted-text role="ref"} classes: best practices now dictate that you use the `IMAGE_CLASSES`{.interpreted-text role="term"} variable rather than the `INHERIT`{.interpreted-text role="term"} variable when you inherit the `ref-classes-testimage`{.interpreted-text role="ref"} and `ref-classes-testsdk`{.interpreted-text role="ref"} classes used for automatic testing.

> 继承 `ref-classes-testimage`{.interpreted-text role="ref"}和 `ref-classes-testsdk`{.interpreted-text role="ref"}类时，最佳实践现在规定，您应该使用 `IMAGE_CLASSES`{.interpreted-text role="term"}变量而不是 `INHERIT`{.interpreted-text role="term"}变量，以便继承用于自动测试的 `ref-classes-testimage`{.interpreted-text role="ref"}和 `ref-classes-testsdk`{.interpreted-text role="ref"}类。

# OpenSSL Changes {#migration-2.6-openssl-changes}

[OpenSSL](https://www.openssl.org/) has been upgraded from 1.0 to 1.1. By default, this upgrade could cause problems for recipes that have both versions in their dependency chains. The problem is that both versions cannot be installed together at build time.

> OpenSSL 从 1.0 升级到 1.1。由于默认情况下，这种升级可能会对依赖链中同时具有两个版本的配方造成问题。问题是，在构建时不能同时安装这两个版本。

::: note
::: title
Note
:::

It is possible to have both versions of the library at runtime.
:::

# BitBake Changes {#migration-2.6-bitbake-changes}

The server logfile `bitbake-cookerdaemon.log` is now always placed in the `Build Directory`{.interpreted-text role="term"} instead of the current directory.

> 现在服务器日志文件 `bitbake-cookerdaemon.log` 总是放置在 `构建目录` 中，而不是当前目录。

# Security Changes {#migration-2.6-security-changes}

The Poky distribution now uses security compiler flags by default. Inclusion of these flags could cause new failures due to stricter checking for various potential security issues in code.

> 现在，Poky 分发默认使用安全编译器标志。包括这些标志可能会由于代码中各种潜在安全问题的严格检查而导致新的失败。

# Post Installation Changes {#migration-2.6-post-installation-changes}

You must explicitly mark post installs to defer to the target. If you want to explicitly defer a postinstall to first boot on the target rather than at root filesystem creation time, use `pkg_postinst_ontarget()` or call `postinst_intercept delay_to_first_boot` from `pkg_postinst()`. Any failure of a `pkg_postinst()` script (including exit 1) triggers an error during the `ref-tasks-rootfs`{.interpreted-text role="ref"} task.

> 你必须明确标记 post 安装以延迟到目标。如果你想明确将 postinstall 延迟到目标上的第一次启动而不是在根文件系统创建时间，请使用 `pkg_postinst_ontarget()` 或从 `pkg_postinst()` 调用 `postinst_intercept delay_to_first_boot`。任何 `pkg_postinst()` 脚本的失败（包括退出 1）都会在 `ref-tasks-rootfs`{.interpreted-text role="ref"}任务中触发错误。

For more information on post-installation behavior, see the \"`dev-manual/new-recipe:post-installation scripts`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 要了解有关安装后行为的更多信息，请参阅 Yocto 项目开发任务手册中的“dev-manual/new-recipe：安装后脚本”部分。

# Python 3 Profile-Guided Optimization {#migration-2.6-python-3-profile-guided-optimizations}

The `python3` recipe now enables profile-guided optimization. Using this optimization requires a little extra build time in exchange for improved performance on the target at runtime. Additionally, the optimization is only enabled if the current `MACHINE`{.interpreted-text role="term"} has support for user-mode emulation in QEMU (i.e. \"qemu-usermode\" is in `MACHINE_FEATURES`{.interpreted-text role="term"}, which it is by default).

> 现在，`python3` 食谱支持面向概要的优化。使用此优化需要额外的构建时间，以换取目标在运行时的改进性能。此外，仅当当前的 `MACHINE`{.interpreted-text role="term"}支持 QEMU 中的用户模式仿真（即 `MACHINE_FEATURES`{.interpreted-text role="term"}中有“qemu-usermode”，默认情况下是这样）时，才会启用优化。

If you wish to disable Python profile-guided optimization regardless of the value of `MACHINE_FEATURES`{.interpreted-text role="term"}, then ensure that `PACKAGECONFIG`{.interpreted-text role="term"} for the `python3` recipe does not contain \"pgo\". You could accomplish the latter using the following at the configuration level:

> 如果您希望无论 `MACHINE_FEATURES`{.interpreted-text role="term"}的值如何，都禁用 Python 配置文件引导优化，请确保 `PACKAGECONFIG`{.interpreted-text role="term"}中的 `python3` 配方不包含“pgo”。您可以使用以下配置级别来实现：

```
PACKAGECONFIG_remove_pn-python3 = "pgo"
```

Alternatively, you can set `PACKAGECONFIG`{.interpreted-text role="term"} using an append file for the `python3` recipe.

# Miscellaneous Changes {#migration-2.6-miscellaneous-changes}

The following miscellaneous changes occurred:

- Default to using the Thumb-2 instruction set for armv7a and above. If you have any custom recipes that build software that needs to be built with the ARM instruction set, change the recipe to set the instruction set as follows:

> 默认为 armv7a 及以上使用 Thumb-2 指令集。如果您有任何需要使用 ARM 指令集构建的自定义配方，请更改该配方以如下方式设置指令集：

```
ARM_INSTRUCTION_SET = "arm"
```

- `run-postinsts` no longer uses `/etc/*-postinsts` for `dpkg/opkg` in favor of built-in postinst support. RPM behavior remains unchanged.
- The `NOISO` and `NOHDD` variables are no longer used. You now control building `*.iso` and `*.hddimg` image types directly by using the `IMAGE_FSTYPES`{.interpreted-text role="term"} variable.

> `NOISO` 和 `NOHDD` 变量不再使用。您现在可以直接使用 `IMAGE_FSTYPES` 变量来控制生成 `*.iso` 和 `*.hddimg` 图像类型。

- The `scripts/contrib/mkefidisk.sh` has been removed in favor of Wic.
- `kernel-modules` has been removed from `RRECOMMENDS`{.interpreted-text role="term"} for `qemumips` and `qemumips64` machines. Removal also impacts the `x86-base.inc` file.

> 为 qemumips 和 qemumips64 机器从 RRECOMMENDS 中移除了 kernel-modules，这也影响了 x86-base.inc 文件。

::: note
::: title
Note
:::

`genericx86` and `genericx86-64` retain `kernel-modules` as part of the `RRECOMMENDS`{.interpreted-text role="term"} variable setting.
:::

- The `LGPLv2_WHITELIST_GPL-3.0` variable has been removed. If you are setting this variable in your configuration, set or append it to the `WHITELIST_GPL-3.0` variable instead.

> `LGPLv2_WHITELIST_GPL-3.0` 变量已经被移除。如果您在配置中设置此变量，请将其设置或附加到 `WHITELIST_GPL-3.0` 变量中。

- `${ASNEEDED}` is now included in the `TARGET_LDFLAGS`{.interpreted-text role="term"} variable directly. The remaining definitions from `meta/conf/distro/include/as-needed.inc` have been moved to corresponding recipes.

> - 目前已经将 `${ASNEEDED}` 直接包含在 `TARGET_LDFLAGS` 变量中。来自 `meta/conf/distro/include/as-needed.inc` 的其余定义已经移动到相应的配方中。

- Support for DSA host keys has been dropped from the OpenSSH recipes. If you are still using DSA keys, you must switch over to a more secure algorithm as recommended by OpenSSH upstream.

> 支持 DSA 主机密钥的 OpenSSH 配方已被取消。如果您仍在使用 DSA 密钥，则必须按照 OpenSSH 上游的建议切换到更安全的算法。

- The `dhcp` recipe now uses the `dhcpd6.conf` configuration file in `dhcpd6.service` for IPv6 DHCP rather than re-using `dhcpd.conf`, which is now reserved for IPv4.

> 现在，`dhcp` 食谱使用 `dhcpd6.service` 中的 `dhcpd6.conf` 配置文件来进行 IPv6 DHCP，而不是重复使用 `dhcpd.conf`，后者现在仅供 IPv4 使用。
