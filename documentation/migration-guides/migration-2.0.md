---
tip: translate by openai@2023-06-07 21:13:33
...
---
title: Release 2.0 (jethro)
---------------------------

This section provides migration information for moving to the Yocto Project 2.0 Release (codename \"jethro\") from the prior release.

> 此节提供将从之前的版本迁移到 Yocto Project 2.0 版本(代号“jethro”)的迁移信息。

# GCC 5

The default compiler is now GCC 5.2. This change has required fixes for compilation errors in a number of other recipes.

> 默认编译器现在是 GCC 5.2。这一变更需要修复其他菜谱中的编译错误。

One important example is a fix for when the Linux kernel freezes at boot time on ARM when built with GCC 5. If you are using your own kernel recipe or source tree and building for ARM, you will likely need to apply this [patch](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit?id=a077224fd35b2f7fbc93f14cf67074fc792fbac2). The standard `linux-yocto` kernel source tree already has a workaround for the same issue.

> 一个重要的例子是当使用 GCC 5 在 ARM 上构建 Linux 内核时冻结引导时的修复。如果您使用自己的内核配方或源树并为 ARM 构建，您可能需要应用此[补丁](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit?id=a077224fd35b2f7fbc93f14cf67074fc792fbac2)。标准的 `linux-yocto` 内核源树已经有了同一问题的解决方案。

For further details, see [https://gcc.gnu.org/gcc-5/changes.html](https://gcc.gnu.org/gcc-5/changes.html) and the porting guide at [https://gcc.gnu.org/gcc-5/porting_to.html](https://gcc.gnu.org/gcc-5/porting_to.html).

> 更多详情，请参阅 [https://gcc.gnu.org/gcc-5/changes.html](https://gcc.gnu.org/gcc-5/changes.html) 和 [https://gcc.gnu.org/gcc-5/porting_to.html](https://gcc.gnu.org/gcc-5/porting_to.html) 上的移植指南。

Alternatively, you can switch back to GCC 4.9 or 4.8 by setting `GCCVERSION` in your configuration, as follows:

> 另外，你可以通过在你的配置中设置 `GCCVERSION` 来切换回 GCC 4.9 或 4.8：

```
GCCVERSION = "4.9%"
```

# Gstreamer 0.10 Removed

Gstreamer 0.10 has been removed in favor of Gstreamer 1.x. As part of the change, recipes for Gstreamer 0.10 and related software are now located in `meta-multimedia`. This change results in Qt4 having Phonon and Gstreamer support in QtWebkit disabled by default.

> Gstreamer 0.10 已被 Gstreamer 1.x 取代。作为更改的一部分，Gstreamer 0.10 及其相关软件的菜谱现在位于 `meta-multimedia` 中。这一变化导致 Qt4 默认情况下禁用了 Phonon 和 Gstreamer 支持的 QtWebkit。

# Removed Recipes

The following recipes have been moved or removed:

> 以下 recipes 已经被移动或删除：

- `bluez4`: The recipe is obsolete and has been moved due to `bluez5` becoming fully integrated. The `bluez4` recipe now resides in `meta-oe`.

> -`bluez4`：由于 `bluez5` 已经完全整合，该配方已经过时并被移动。`bluez4` 配方现在位于 `meta-oe` 中。

- `gamin`: The recipe is obsolete and has been removed.
- `gnome-icon-theme`: The recipe\'s functionally has been replaced by `adwaita-icon-theme`.
- Gstreamer 0.10 Recipes: Recipes for Gstreamer 0.10 have been removed in favor of the recipes for Gstreamer 1.x.

> Gstreamer 0.10 的 recipes 已经被取消，改为使用 Gstreamer 1.x 的 recipes。

- `insserv`: The recipe is obsolete and has been removed.
- `libunique`: The recipe is no longer used and has been moved to `meta-oe`.
- `midori`: The recipe\'s functionally has been replaced by `epiphany`.
- `python-gst`: The recipe is obsolete and has been removed since it only contains bindings for Gstreamer 0.10.

> - `python-gst`：由于该 recipes 仅包含 Gstreamer 0.10 的绑定，因此已被删除，已过时。

- `qt-mobility`: The recipe is obsolete and has been removed since it requires `Gstreamer 0.10`, which has been replaced.

> -`qt-mobility`：这个 recipes 已经过时，因为它需要 `Gstreamer 0.10`，它已经被取代了，所以已经被移除了。

- `subversion`: All 1.6.x versions of this recipe have been removed.
- `webkit-gtk`: The older 1.8.3 version of this recipe has been removed in favor of `webkitgtk`.

# BitBake datastore improvements

The method by which BitBake\'s datastore handles overrides has changed. Overrides are now applied dynamically and `bb.data.update_data()` is now a no-op. Thus, `bb.data.update_data()` is no longer required in order to apply the correct overrides. In practice, this change is unlikely to require any changes to Metadata. However, these minor changes in behavior exist:

> BitBake 的数据存储处理覆盖的方法已经改变。覆盖现在是动态应用的，`bb.data.update_data()` 现在是无操作的。因此，`bb.data.update_data()` 不再需要来应用正确的覆盖。实际上，这个变化不太可能需要任何元数据的改变。但是，这些行为上的微小变化存在：

- All potential overrides are now visible in the variable history as seen when you run the following:

> 所有潜在的覆盖现在可以在运行以下操作时在变量历史中看到：

```
$ bitbake -e
```

- `d.delVar('VARNAME')` and `d.setVar('VARNAME', None)` result in the variable and all of its overrides being cleared out. Before the change, only the non-overridden values were cleared.

> `d.delVar('VARNAME')` 和 `d.setVar('VARNAME', None)` 的结果是变量及其所有覆盖都被清除。在更改之前，只有未覆盖的值才会被清除。

# Shell Message Function Changes

The shell versions of the BitBake message functions (i.e. `bbdebug`, `bbnote`, `bbwarn`, `bbplain`, `bberror`, and `bbfatal`) are now connected through to their BitBake equivalents `bb.debug()`, `bb.note()`, `bb.warn()`, `bb.plain()`, `bb.error()`, and `bb.fatal()`, respectively. Thus, those message functions that you would expect to be printed by the BitBake UI are now actually printed. In practice, this change means two things:

> BitBake 消息函数的 shell 版本(即 `bbdebug`、`bbnote`、`bbwarn`、`bbplain`、`bberror` 和 `bbfatal`)现已连接到它们的 BitBake 等效函数 `bb.debug()`、`bb.note()`、`bb.warn()`、`bb.plain()`、`bb.error()` 和 `bb.fatal()`。因此，您期望由 BitBake UI 打印的消息函数现在实际上已经被打印出来了。实际上，这一变化意味着两件事：

- If you now see messages on the console that you did not previously see as a result of this change, you might need to clean up the calls to `bbwarn`, `bberror`, and so forth. Or, you might want to simply remove the calls.

> 如果因为这次变更而在控制台看到之前没有看到的消息，你可能需要清理对 `bbwarn`，`bberror` 等的调用。或者，你可能只想移除调用。

- The `bbfatal` message function now suppresses the full error log in the UI, which means any calls to `bbfatal` where you still wish to see the full error log should be replaced by `die` or `bbfatal_log`.

> `bbfatal` 消息功能现在在 UI 中抑制了完整的错误日志，这意味着任何仍希望看到完整错误日志的 `bbfatal` 调用都应该用 `die` 或 `bbfatal_log` 替换。

# Extra Development/Debug Package Cleanup

The following recipes have had extra `dev/dbg` packages removed:

> 以下 recipes 已经移除了额外的 `dev/dbg` 软件包：

- `acl`
- `apmd`
- `aspell`
- `attr`
- `augeas`
- `bzip2`
- `cogl`
- `curl`
- `elfutils`
- `gcc-target`
- `libgcc`
- `libtool`
- `libxmu`
- `opkg`
- `pciutils`
- `rpm`
- `sysfsutils`
- `tiff`
- `xz`

All of the above recipes now conform to the standard packaging scheme where a single `-dev`, `-dbg`, and `-staticdev` package exists per recipe.

> 所有上述 recipes 现在都符合标准包装方案，每个 recipes 都有单独的 `-dev`、`-dbg` 和 `-staticdev` 包。

# Recipe Maintenance Tracking Data Moved to OE-Core

Maintenance tracking data for recipes that was previously part of `meta-yocto` has been moved to `OpenEmbedded-Core (OE-Core)`. The change includes `package_regex.inc` and `distro_alias.inc`, which are typically enabled when using the `distrodata` class. Additionally, the contents of `upstream_tracking.inc` has now been split out to the relevant recipes.

> 维护跟踪 `meta-yocto` 中以前的配方数据已经被移动到 `OpenEmbedded-Core(OE-Core)`。该变更包括通常在使用 `distrodata` 类时启用的 `package_regex.inc` 和 `distro_alias.inc`。此外，`upstream_tracking.inc` 的内容现在已经被拆分到相关的配方中。

# Automatic Stale Sysroot File Cleanup

Stale files from recipes that no longer exist in the current configuration are now automatically removed from sysroot as well as removed from any other place managed by shared state. This automatic cleanup means that the build system now properly handles situations such as renaming the build system side of recipes, removal of layers from `bblayers.conf`, and `DISTRO_FEATURES` changes.

> 系统根目录以及其他由共享状态管理的任何地方都会自动删除已不存在于当前配置中的过时文件。此自动清理意味着构建系统现在可以正确处理诸如重命名构建系统侧的配方，从 `bblayers.conf` 中删除层以及 `DISTRO_FEATURES` 变更等情况。

Additionally, work directories for old versions of recipes are now pruned. If you wish to disable pruning old work directories, you can set the following variable in your configuration:

> 此外，旧版本 recipes 的工作目录现在已被修剪。如果您希望禁用修剪旧的工作目录，可以在配置中设置以下变量：

```
SSTATE_PRUNE_OBSOLETEWORKDIR = "0"
```

# `linux-yocto` Kernel Metadata Repository Now Split from Source

The `linux-yocto` tree has up to now been a combined set of kernel changes and configuration (meta) data carried in a single tree. While this format is effective at keeping kernel configuration and source modifications synchronized, it is not always obvious to developers how to manipulate the Metadata as compared to the source.

> linux-yocto 树至今一直是一个综合的内核更改和配置(元)数据的集合，存储在一棵树中。虽然这种格式有效地保持内核配置和源代码修改同步，但对于开发人员来说，如何操纵元数据与源代码并不总是很明显。

Metadata processing has now been removed from the `ref-classes-kernel-yocto` class and the external Metadata repository `yocto-kernel-cache`, which has always been used to seed the `linux-yocto` \"meta\" branch. This separate `linux-yocto` cache repository is now the primary location for this data. Due to this change, `linux-yocto` is no longer able to process combined trees. Thus, if you need to have your own combined kernel repository, you must do the split there as well and update your recipes accordingly. See the `meta/recipes-kernel/linux/linux-yocto_4.1.bb` recipe for an example.

> 处理元数据现已从 `ref-classes-kernel-yocto` 类和一直用于种子 `linux-yocto`“meta”分支的外部元数据存储库 `yocto-kernel-cache` 中移除。这个单独的 `linux-yocto` 缓存存储库现在是这些数据的主要位置。由于这个变化，`linux-yocto` 不再能够处理组合树。因此，如果您需要拥有自己的组合内核存储库，您必须在那里进行分割，并相应地更新您的配方。有关示例，请参阅 `meta/recipes-kernel/linux/linux-yocto_4.1.bb` 配方。

# Additional QA checks

The following QA checks have been added:

> 以下 QA 检查已添加：

- Added a \"host-user-contaminated\" check for ownership issues for packaged files outside of `/home`. The check looks for files that are incorrectly owned by the user that ran BitBake instead of owned by a valid user in the target system.

> 已经为位于 `/home` 以外的打包文件添加了一个“host-user-污染”检查，以检查文件是否被运行 BitBake 的用户错误地拥有，而不是被目标系统中的有效用户拥有。

- Added an \"invalid-chars\" check for invalid (non-UTF8) characters in recipe metadata variable values (i.e. `DESCRIPTION`). Some package managers do not support these characters.

> 新增了一個 "無效字元" 檢查，用於檢查食譜元資料變數值(例如 `DESCRIPTION`)中的無效(非 UTF8)字元。一些套件管理器不支援這些字元。

- Added an \"invalid-packageconfig\" check for any options specified in `PACKAGECONFIG` option defined for the recipe.

> 已为指定在 `PACKAGECONFIG` 中的任何选项添加了“invalid-packageconfig”检查，以检查与配方定义的 `PACKAGECONFIG` 选项是否匹配。

# Miscellaneous Changes

These additional changes exist:

> 这些额外的变化存在：

- `gtk-update-icon-cache` has been renamed to `gtk-icon-utils`.
- The `tools-profile` `IMAGE_FEATURES` item as well as its corresponding packagegroup and `packagegroup-core-tools-profile` no longer bring in `oprofile`. Bringing in `oprofile` was originally added to aid compilation on resource-constrained targets. However, this aid has not been widely used and is not likely to be used going forward due to the more powerful target platforms and the existence of better cross-compilation tools.

> -`tools-profile` 项目的 `IMAGE_FEATURES` 以及它的相关套件组和 `packagegroup-core-tools-profile` 不再引入 `oprofile`。最初，引入 `oprofile` 是为了帮助资源受限的目标进行编译。但是，由于更强大的目标平台以及更好的交叉编译工具的存在，这种帮助没有被广泛使用，也不太可能在未来被使用。

- The `IMAGE_FSTYPES` variable\'s default value now specifies `ext4` instead of `ext3`.

> 现在，`IMAGE_FSTYPES` 变量的默认值指定为 `ext4` 而不是 `ext3`。

- All support for the `PRINC` variable has been removed.
- The `packagegroup-core-full-cmdline` packagegroup no longer brings in `lighttpd` due to the fact that bringing in `lighttpd` is not really in line with the packagegroup\'s purpose, which is to add full versions of command-line tools that by default are provided by `busybox`.

> `packagegroup-core-full-cmdline` 包组不再包含 `lighttpd`，因为包含 `lighttpd` 与该包组的目的不符，该包组的目的是添加默认由 `busybox` 提供的完整命令行工具。
