---
tip: translate by openai@2023-06-07 21:17:59
...
---
title: Release 2.1 (krogoth)
----------------------------

This section provides migration information for moving to the Yocto Project 2.1 Release (codename \"krogoth\") from the prior release.

> 此部分提供从先前发布版本迁移到 Yocto 项目 2.1 发布版(代号“krogoth”)的迁移信息。

# Variable Expansion in Python Functions

Variable expressions, such as `$` no longer expand automatically within Python functions. Suppressing expansion was done to allow Python functions to construct shell scripts or other code for situations in which you do not want such expressions expanded. For any existing code that relies on these expansions, you need to change the expansions to expand the value of individual variables through `d.getVar()`. To alternatively expand more complex expressions, use `d.expand()`.

> 变量表达式，如 `$` 不再在 Python 函数中自动展开。禁止展开是为了允许 Python 函数构建 shell 脚本或其他代码，以便在不希望这些表达式展开的情况下使用。对于任何依赖于这些展开的现有代码，您需要通过 `d.getVar()` 来更改展开以展开单个变量的值。要替代地展开更复杂的表达式，请使用 `d.expand()`。

# Overrides Must Now be Lower-Case

The convention for overrides has always been for them to be lower-case characters. This practice is now a requirement as BitBake\'s datastore now assumes lower-case characters in order to give a slight performance boost during parsing. In practical terms, this requirement means that anything that ends up in `OVERRIDES`, and also recipe names if `_pn-` recipename overrides are to be effective).

> 给覆盖的约定一直是使用小写字母。现在这个做法已经成为一项要求，因为 BitBake 的数据存储现在假定使用小写字母，以便在解析时获得轻微的性能提升。实际上，这一要求意味着任何最终进入 `OVERRIDES` 的值，以及如果要使 `_pn-` recipename 覆盖有效，则也要求使用小写字母的 recipes 名称)。

# Expand Parameter to `getVar()` and `getVarFlag()` is Now Mandatory

The expand parameter to `getVar()` and `getVarFlag()` previously defaulted to False if not specified. Now, however, no default exists so one must be specified. You must change any `getVar()` calls that do not specify the final expand parameter to calls that do specify the parameter. You can run the following `sed` command at the base of a layer to make this change:

> `getVar()` 和 `getVarFlag()` 中的 expand 参数以前如果没有指定，默认为 False。然而，现在不存在默认值，因此必须指定一个。您必须更改不指定最终 expand 参数的 `getVar()` 调用，以指定该参数。您可以在图层的基础上运行以下 `sed` 命令来进行此更改：

```
sed -e 's:\(\.getVar([^,()]*\)):\1, False):g' -i `grep -ril getVar *`
sed -e 's:\(\.getVarFlag([^,()]*,[^,()]*\)):\1, False):g' -i `grep -ril getVarFlag *`
```

::: note
::: title
Note
:::

The reason for this change is that it prepares the way for changing the default to True in a future Yocto Project release. This future change is a much more sensible default than False. However, the change needs to be made gradually as a sudden change of the default would potentially cause side-effects that would be difficult to detect.

> 这种变化的原因是，它为将来 Yocto 项目发行版中的默认值改为 True 做准备。这种未来的变化比 False 更加合理。但是，这种变化需要逐步进行，因为突然改变默认值可能会产生难以检测的副作用。
> :::

# Makefile Environment Changes

`EXTRA_OEMAKE`, which is typically only needed when a Makefile sets a default value for a variable that is inappropriate for cross-compilation using the \"=\" operator rather than the \"?=\" operator.

> EXTRA_OEMAKE 现在默认为“”而不是“-e MAKEFLAGS =”。将 EXTRA_OEMAKE 默认设置为“-e MAKEFLAGS =”是一个历史意外，这需要许多类(例如 ref-classes-autotools，module)和配方来覆盖这个默认值，以便与合理的构建系统一起工作。当升级到发布版本时，您必须编辑任何依赖于此旧默认值的配方，要么将 EXTRA_OEMAKE 重新设置为“-e MAKEFLAGS =”，要么使用 EXTRA_OEMAKE 明确设置任何需要的变量值覆盖，通常只有当 Makefile 使用“=”而不是“？=”运算符为跨编译设置默认值时，才需要这样做。

# `libexecdir` Reverted to `$

The use of `$/libexec` without breaking FHS.

> 使用 `$/libexec`。

# `ac_cv_sizeof_off_t` is No Longer Cached in Site Files

For recipes inheriting the `ref-classes-autotools`.

> 对于继承了 `ref-classes-autotools` 期间确定 `off_t` 的正确大小。

The best course of action is to patch the software as necessary to allow the default implementation from the `ref-classes-autotools` task such that the default implementation does get used.

> 最佳的行动方案是补丁软件，以便让 `ref-classes-autotools` 任务，以便使用默认实现。

# Image Generation is Now Split Out from Filesystem Generation

Previously, for image recipes the `ref-tasks-rootfs` tasks for clarity both in operation and in the code.

> 以前，对于镜像配方，`ref-tasks-rootfs` 任务，以清晰地操作和编码。

For most cases, this change does not present any problems. However, if you have made customizations that directly modify the `ref-tasks-rootfs` so that your added tasks run at the correct time.

> 大多数情况下，这种变化不会带来任何问题。但是，如果您进行了直接修改“ref-tasks-rootfs”任务或提到“ref-tasks-rootfs”的定制，则可能需要更新这些更改。特别是，如果您在“ref-tasks-rootfs”之后添加了任何任务，则应该进行编辑，使这些任务在“ref-tasks-image-complete”任务之后而不是“ref-tasks-rootfs”之后，以便您添加的任务在正确的时间运行。

A minor part of this restructuring is that the post-processing definitions and functions have been moved from the `ref-classes-image` class. Functionally, however, they remain unchanged.

> 部分重组的内容是将后处理定义和函数从 `ref-classes-image` 类。但是，功能上没有改变。

# Removed Recipes

The following recipes have been removed in the 2.1 release:

> 以下 recipes 已在 2.1 版本中被移除：

- `gcc` version 4.8: Versions 4.9 and 5.3 remain.
- `qt4`: All support for Qt 4.x has been moved out to a separate `meta-qt4` layer because Qt 4 is no longer supported upstream.

> - `qt4`：所有对 Qt 4.x 的支持都已移至单独的 `meta-qt4` 层，因为 Qt 4 不再受上游支持。

- `x11vnc`: Moved to the `meta-oe` layer.
- `linux-yocto-3.14`: No longer supported.
- `linux-yocto-3.19`: No longer supported.
- `libjpeg`: Replaced by the `libjpeg-turbo` recipe.
- `pth`: Became obsolete.
- `liboil`: Recipe is no longer needed and has been moved to the `meta-multimedia` layer.
- `gtk-theme-torturer`: Recipe is no longer needed and has been moved to the `meta-gnome` layer.
- `gnome-mime-data`: Recipe is no longer needed and has been moved to the `meta-gnome` layer.
- `udev`: Replaced by the `eudev` recipe for compatibility when using `sysvinit` with newer kernels.

> - `udev`：为了与新内核使用 `sysvinit` 兼容，由 `eudev` recipes 取代。

- `python-pygtk`: Recipe became obsolete.
- `adt-installer`: Recipe became obsolete. See the \"`migration-guides/migration-2.1:adt removed`\" section for more information.

> `- adt-installer：recipes已经过时。有关更多信息，请参见“migration-guides/migration-2.1：adt removed”部分。`

# Class Changes

The following classes have changed:

> 以下班级已经变更：

- `autotools_stage`: Removed because the `ref-classes-autotools` instead.

> `- ` autotools_stage `: 因为` ref-classes-autotools ` 继承而不是。

- `boot-directdisk`: Merged into the `image-vm` class. The `boot-directdisk` class was rarely directly used. Consequently, this change should not cause any issues.

> - `boot-directdisk`：已合并到 `image-vm` 类中。`boot-directdisk` 类很少会被直接使用。因此，此更改不应引起任何问题。

- `bootimg`: Merged into the `ref-classes-image-live` class. The `bootimg` class was rarely directly used. Consequently, this change should not cause any issues.

> `bootimg`：已合并到 `ref-classes-image-live` 类中。`bootimg` 类很少直接使用。因此，此更改不应引起任何问题。

- `packageinfo`: Removed due to its limited use by the Hob UI, which has itself been removed.

# Build System User Interface Changes

The following changes have been made to the build system user interface:

> 以下对构建系统用户界面做出了更改：

- *Hob GTK+-based UI*: Removed because it is unmaintained and based on the outdated GTK+ 2 library. The Toaster web-based UI is much more capable and is actively maintained. See the \"`toaster-manual/setup-and-use:using the toaster web interface`\" section in the Toaster User Manual for more information on this interface.

> - *基于 GTK+ 的 Hob UI*：因为它已经不再维护，而且基于过时的 GTK+ 2 库，所以已经被移除。Toaster 基于网页的 UI 功能更强大，而且正在积极维护。有关此接口的更多信息，请参阅 Toaster 用户手册中的“toaster-manual/setup-and-use：使用 Toaster Web 界面”部分。

- *\"puccho\" BitBake UI*: Removed because is unmaintained and no longer useful.

# ADT Removed

The Application Development Toolkit (ADT) has been removed because its functionality almost completely overlapped with the `standard SDK <sdk-manual/using:using the standard sdk>` manual.

> 应用程序开发工具包(ADT)已经被移除，因为它的功能几乎完全与 `标准SDK <sdk-manual/using:using the standard sdk>` 手册。

::: note
::: title
Note
:::

The Yocto Project Eclipse IDE Plug-in is still supported and is not affected by this change.

> 该 Yocto 项目 Eclipse IDE 插件仍然受到支持，不受此更改影响。
> :::

# Poky Reference Distribution Changes

The following changes have been made for the Poky distribution:

> 以下的改变已经为 Poky 发行版做出了更改：

- The `meta-yocto` layer has been renamed to `meta-poky` to better match its purpose, which is to provide the Poky reference distribution. The `meta-yocto-bsp` layer retains its original name since it provides reference machines for the Yocto Project and it is otherwise unrelated to Poky. References to `meta-yocto` in your `conf/bblayers.conf` should automatically be updated, so you should not need to change anything unless you are relying on this naming elsewhere.

> meta-yocto 层已被重命名为 meta-poky，以更好地匹配其目的，即提供 Poky 参考分发。meta-yocto-bsp 层保留其原始名称，因为它为 Yocto 项目提供参考机器，并且与 Poky 无关。conf / bblayers.conf 中对 meta-yocto 的引用应自动更新，因此除非您在其他位置依赖此命名，否则不需要更改任何内容。

- The `ref-classes-uninative` class is now enabled by default in Poky. This class attempts to isolate the build system from the host distribution\'s C library and makes re-use of native shared state artifacts across different host distributions practical. With this class enabled, a tarball containing a pre-built C library is downloaded at the start of the build.

> 现在在 Poky 中默认启用了 `ref-classes-uninative` 类。该类试图将构建系统与主机分发的 C 库隔离开来，使得在不同主机分发中重用本机共享状态的实体变得实际可行。启用此类后，将在构建开始时下载一个包含预构建 C 库的压缩包。

The `ref-classes-uninative` class is enabled through the `meta/conf/distro/include/yocto-uninative.inc` file, which for those not using the Poky distribution, can include to easily enable the same functionality.

> `ref-classes-uninative` 类可以通过 `meta/conf/distro/include/yocto-uninative.inc` 文件启用，对于那些不使用 Poky 分发版的人，可以包含它以轻松启用相同的功能。

Alternatively, if you wish to build your own `uninative` tarball, you can do so by building the `uninative-tarball` recipe, making it available to your build machines (e.g. over HTTP/HTTPS) and setting a similar configuration as the one set by `yocto-uninative.inc`.

> 如果您想构建自己的 `uninative` tarball，可以通过构建 `uninative-tarball` 配方来实现，将其可用于构建机器(例如通过 HTTP/HTTPS)，并设置与 `yocto-uninative.inc` 设置的类似的配置。

- Static library generation, for most cases, is now disabled by default in the Poky distribution. Disabling this generation saves some build time as well as the size used for build output artifacts.

> 在 Poky 发行版中，默认情况下，静态库的生成已被禁用。禁用此生成可以节省构建时间以及用于构建输出的大小。

Disabling this library generation is accomplished through a `meta/conf/distro/include/no-static-libs.inc`, which for those not using the Poky distribution can easily include to enable the same functionality.

> 禁用此库生成可通过“meta/conf/distro/include/no-static-libs.inc”实现，对于不使用 Poky 分发的人来说，可以轻松包含以启用相同的功能。

Any recipe that needs to opt-out of having the `--disable-static` option specified on the configure command line either because it is not a supported option for the configure script or because static libraries are needed should set the following variable:

> 任何配方需要退出指定在 configure 命令行上的 `--disable-static` 选项，要么是因为它不是 configure 脚本支持的选项，要么是因为需要静态库，应该设置以下变量：

```
DISABLE_STATIC = ""
```

- The separate `poky-tiny` distribution now uses the musl C library instead of a heavily pared down `glibc`. Using musl results in a smaller distribution and facilitates much greater maintainability because musl is designed to have a small footprint.

> 独立的 `poky-tiny` 发行版现在使用 musl C 库，而不是大量削减的 `glibc`。使用 musl 可以得到更小的发行版，并且可以更容易地维护，因为 musl 设计具有较小的足迹。

If you have used `poky-tiny` and have customized the `glibc` configuration you will need to redo those customizations with musl when upgrading to the new release.

> 如果您使用了 `poky-tiny` 并且自定义了 `glibc` 配置，在升级到新版本时，您需要使用 musl 重新完成这些定制。

# Packaging Changes

The following changes have been made to packaging:

> 以下对包装做出了更改：

- The `runuser` and `mountpoint` binaries, which were previously in the main `util-linux` package, have been split out into the `util-linux-runuser` and `util-linux-mountpoint` packages, respectively.

> `runuser` 和 `mountpoint` 二进制文件，原本在主 `util-linux` 包中，现在分别被拆分到 `util-linux-runuser` 和 `util-linux-mountpoint` 包中。

- The `python-elementtree` package has been merged into the `python-xml` package.

# Tuning File Changes

The following changes have been made to the tuning files:

> 以下对调音文件做出了以下更改：

- The \"no-thumb-interwork\" tuning feature has been dropped from the ARM tune include files. Because interworking is required for ARM EABI, attempting to disable it through a tuning feature no longer makes sense.

> ARM 调整包括文件中已经放弃了“无拇指互通”调整特性，因为 ARM EABI 需要互通，试图通过调整特性禁用它不再有意义。

::: note
::: title

Note

> 注意
> :::

Support for ARM OABI was deprecated in gcc 4.7.

> 在 GCC 4.7 中已经不再支持 ARM OABI 了。
> :::

- The `tune-cortexm*.inc` and `tune-cortexr4.inc` files have been removed because they are poorly tested. Until the OpenEmbedded build system officially gains support for CPUs without an MMU, these tuning files would probably be better maintained in a separate layer if needed.

> 这些调谐文件 tune-cortexm* .inc 和 tune-cortexr4.inc 已被移除，因为它们没有经过充分测试。在 OpenEmbedded 构建系统正式支持没有 MMU 的处理器之前，如果有需要，这些调谐文件最好在单独的层中维护。

# Supporting GObject Introspection

This release supports generation of GLib Introspective Repository (GIR) files through GObject introspection, which is the standard mechanism for accessing GObject-based software from runtime environments. You can enable, disable, and test the generation of this data. See the \"`dev-manual/gobject-introspection:enabling gobject introspection support`\" section in the Yocto Project Development Tasks Manual for more information.

> 此发行版支持通过 GObject 内省生成 GLib 内省存储库(GIR)文件，这是从运行时环境访问基于 GObject 的软件的标准机制。您可以启用、禁用和测试此数据的生成。有关更多信息，请参阅 Yocto 项目开发任务手册中的“dev-manual / gobject-introspection：启用 gobject introspection 支持”部分。

# Miscellaneous Changes

These additional changes exist:

> 这些额外的变化存在：

- The minimum Git version has been increased to 1.8.3.1. If your host distribution does not provide a sufficiently recent version, you can install the `buildtools` tarball.

> 最低 Git 版本已经更新到 1.8.3.1。如果您的主机发行版不提供足够新的版本，您可以安装 `buildtools` 部分。

- The buggy and incomplete support for the RPM version 4 package manager has been removed. The well-tested and maintained support for RPM version 5 remains.

> 已移除对 RPM 版本 4 包管理器的错误且不完整的支持，仍保留对 RPM 版本 5 的经过测试和维护的支持。

- Previously, the following list of packages were removed if package-management was not in `IMAGE_FEATURES`, regardless of any dependencies:

> 之前，如果 package-management 不在 IMAGE_FEATURES 中，则会删除以下软件包列表，无论有无依赖关系：

```
update-rc.d
base-passwd
shadow
update-alternatives
run-postinsts
```

With the Yocto Project 2.1 release, these packages are only removed if \"read-only-rootfs\" is in `IMAGE_FEATURES`, since they might still be needed for a read-write image even in the absence of a package manager (e.g. if users need to be added, modified, or removed at runtime).

> 随着 Yocto Project 2.1 发布，如果“read-only-rootfs”在 IMAGE_FEATURES 中，这些软件包将只会被移除，因为即使在没有软件包管理器的情况下，它们仍可能被用于读写映像(例如，如果需要在运行时添加、修改或删除用户)。

- The ``devtool modify <sdk-manual/extensible:use \`\`devtool modify\`\` to modify the source of an existing component>`` command now defaults to extracting the source since that is most commonly expected. The `-x` or `--extract` options are now no-ops. If you wish to provide your own existing source tree, you will now need to specify either the `-n` or `--no-extract` options when running `devtool modify`.

> `devtool modify <sdk-manual/extensible:use \`\`devtool modify\`\` 来修改现有组件的源码 >`` 命令现在默认提取源码，因为这是最常见的预期。`-x` 或 `--extract` 选项现在是无效的。如果您希望提供自己的现有源树，则现在需要在运行 `devtool modify` 时指定 `-n` 或 `--no-extract` 选项。

- If the formfactor for a machine is either not supplied or does not specify whether a keyboard is attached, then the default is to assume a keyboard is attached rather than assume no keyboard. This change primarily affects the Sato UI.

> 如果机器的形式因子未提供或没有指定是否附加键盘，则默认假定附加键盘而不是假定没有键盘。此更改主要影响 Sato UI。

- The `.debug` directory packaging is now automatic. If your recipe builds software that installs binaries into directories other than the standard ones, you no longer need to take care of setting `FILES_$-dbg` to pick up the resulting `.debug` directories as these directories are automatically found and added.

> 现在 `.debug` 目录打包是自动的。如果您的配方构建的软件将二进制文件安装到标准目录以外的目录，您不再需要费心设置 `FILES_$-dbg` 以捕获结果的 `.debug` 目录，因为这些目录会自动发现并添加。

- Inaccurate disk and CPU percentage data has been dropped from `ref-classes-buildstats` data.

> 磁盘和 CPU 百分比数据从 `ref-classes-buildstats` 数据的自定义代码。

- The `meta/conf/distro/include/package_regex.inc` is now deprecated. The contents of this file have been moved to individual recipes.

> `meta/conf/distro/include/package_regex.inc` 已经不再使用了，其内容已经移动到各个配方中。

::: note
::: title

Note

> 注意
> :::

Because this file will likely be removed in a future Yocto Project release, it is suggested that you remove any references to the file that might be in your configuration.

> 由于这个文件很可能会在未来的 Yocto Project 发布版本中被移除，建议您在配置中移除对该文件的任何引用。
> :::

- The `v86d/uvesafb` has been removed from the `genericx86` and `genericx86-64` reference machines, which are provided by the `meta-yocto-bsp` layer. Most modern x86 boards do not rely on this file and it only adds kernel error messages during startup. If you do still need to support `uvesafb`, you can simply add `v86d` to your image.

> v86d/uvesafb 已从 meta-yocto-bsp 层提供的 genericx86 和 genericx86-64 参考机器中移除。大多数现代 x86 板不依赖这个文件，它只会在启动时添加内核错误消息。如果仍然需要支持 uvesafb，可以简单地将 v86d 添加到镜像中。

- Build sysroot paths are now removed from debug symbol files. Removing these paths means that remote GDB using an unstripped build system sysroot will no longer work (although this was never documented to work). The supported method to accomplish something similar is to set `IMAGE_GEN_DEBUGFS` to \"1\", which will generate a companion debug image containing unstripped binaries and associated debug sources alongside the image.

> 现在从调试符号文件中删除了构建系统根路径。删除这些路径意味着使用未剥离的构建系统根的远程 GDB 将不再工作(尽管这从未被文档化过)。实现类似功能的支持方法是将 `IMAGE_GEN_DEBUGFS` 设置为“1”，这将生成一个伴随调试映像，其中包含未剥离的二进制文件和相关的调试源。
