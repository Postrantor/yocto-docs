---
tip: translate by openai@2023-06-07 20:59:41
...
---
title: Release 1.6 (daisy)
--------------------------

This section provides migration information for moving to the Yocto Project 1.6 Release (codename \"daisy\") from the prior release.

> 此部分提供了从前一个版本迁移到 Yocto 项目 1.6 版本(代号“daisy”)的迁移信息。

# `archiver` Class

The `ref-classes-archiver`\" section in the Yocto Project Development Tasks Manual.

> `ref-classes-archiver`”部分。

# Packaging Changes

The following packaging changes have been made:

> 以下包装变更已做出：

- The `binutils` recipe no longer produces a `binutils-symlinks` package. `update-alternatives` is now used to handle the preferred `binutils` variant on the target instead.

> binutils recipes 不再生成 binutils-symlinks 包。现在使用 update-alternatives 来处理目标上的首选 binutils 变体。

- The tc (traffic control) utilities have been split out of the main `iproute2` package and put into the `iproute2-tc` package.

> tc(流量控制)实用程序已从主要的 iproute2 软件包中分离出来，并放入 iproute2-tc 软件包中。

- The `gtk-engines` schemas have been moved to a dedicated `gtk-engines-schemas` package.
- The `armv7a` with thumb package architecture suffix has changed. The suffix for these packages with the thumb optimization enabled is \"t2\" as it should be. Use of this suffix was not the case in the 1.5 release. Architecture names will change within package feeds as a result.

> armv7a 带有 thumb 包架构后缀已经改变。这些带有 thumb 优化的包的后缀是“t2”，应该如此。在 1.5 版本中，没有使用这个后缀。因此，包源中的架构名称将会发生变化。

# BitBake

The following changes have been made to `BitBake`.

> 以下对 BitBake 的更改已经做出。

## Matching Branch Requirement for Git Fetching

When fetching source from a Git repository using `SRC_URI` value against the branch. You can specify the branch using the following form:

> 当使用 `SRC_URI` 值进行验证。您可以使用以下形式指定分支：

```
SRC_URI = "git://server.name/repository;branch=branchname"
```

If you do not specify a branch, BitBake looks in the default \"master\" branch.

> 如果您没有指定分支，BitBake 会在默认的“master”分支中查找。

Alternatively, if you need to bypass this check (e.g. if you are fetching a revision corresponding to a tag that is not on any branch), you can add \";nobranch=1\" to the end of the URL within `SRC_URI`.

> 如果您需要绕过此检查(例如，如果您正在获取与没有分支上的标签对应的修订版本)，您可以在 SRC_URI 中的 URL 末尾添加“;nobranch=1”。

## Python Definition substitutions

BitBake had some previously deprecated Python definitions within its `bb` module removed. You should use their sub-module counterparts instead:

> BitBake 在其 `bb` 模块中移除了一些以前被弃用的 Python 定义。你应该使用它们的子模块替代：

- `bb.MalformedUrl`: Use `bb.fetch.MalformedUrl`.
- `bb.encodeurl`: Use `bb.fetch.encodeurl`.
- `bb.decodeurl`: Use `bb.fetch.decodeurl`
- `bb.mkdirhier`: Use `bb.utils.mkdirhier`.
- `bb.movefile`: Use `bb.utils.movefile`.
- `bb.copyfile`: Use `bb.utils.copyfile`.
- `bb.which`: Use `bb.utils.which`.
- `bb.vercmp_string`: Use `bb.utils.vercmp_string`.
- `bb.vercmp`: Use `bb.utils.vercmp`.

## SVK Fetcher

The SVK fetcher has been removed from BitBake.

> BitBake 已移除了 SVK 获取器。

## Console Output Error Redirection

The BitBake console UI will now output errors to `stderr` instead of `stdout`. Consequently, if you are piping or redirecting the output of `bitbake` to somewhere else, and you wish to retain the errors, you will need to add `2>&1` (or something similar) to the end of your `bitbake` command line.

> BitBake 控制台 UI 现在将错误输出到 `stderr` 而不是 `stdout`。因此，如果您正在将 `bitbake` 的输出管道或重定向到其他位置，并且您希望保留错误，则需要在 `bitbake` 命令行末尾添加 `2>&1`(或类似内容)。

## `task-` taskname Overrides

`task-` taskname overrides have been adjusted so that tasks whose names contain underscores have the underscores replaced by hyphens for the override so that they now function properly. For example, the task override for `ref-tasks-populate_sdk` is `task-populate-sdk`.

> 任务名称覆盖已调整，以便其名称包含下划线的任务的下划线被连字符号替换，以便它们现在可以正常工作。例如，`ref-tasks-populate_sdk` 的任务覆盖是 `task-populate-sdk`。

# Changes to Variables

The following variables have changed. For information on the OpenEmbedded build system variables, see the \"`/ref-manual/variables`\" Chapter.

> 以下变量已经更改。有关 OpenEmbedded 构建系统变量的信息，请参见“/ref-manual/variables”章节。

## `TMPDIR`

`TMPDIR`.

> TMPDIR 不能再挂载在 NFS 上。NFS 不支持完整的 POSIX 锁定和 inode 一致性，如果用来存储 TMPDIR 可能会导致意外问题。

The check for this occurs on startup. If `TMPDIR` is detected on an NFS mount, an error occurs.

> 如果在 NFS 挂载上检测到 `TMPDIR`，就会发生错误，这个检查在启动时进行。

## `PRINC`

The `PRINC` variable has been deprecated and triggers a warning if detected during a build. For `PR`\" section in the Yocto Project Development Tasks Manual.

> `PRINC` 变量已弃用，并且在构建过程中检测到时会发出警告。要对 `PR` 增量进行更改，请改用 PR 服务。您可以在 Yocto Project 开发任务手册中的“dev-manual/packages：使用 PR 服务”部分找到更多关于此服务的信息。

## `IMAGE_TYPES`

The \"sum.jffs2\" option for `IMAGE_TYPES` has been replaced by the \"jffs2.sum\" option, which fits the processing order.

> "IMAGE_TYPES" 中的 "sum.jffs2" 选项已被 "jffs2.sum" 选项取代，这符合处理顺序。

## `COPY_LIC_MANIFEST`

The `COPY_LIC_MANIFEST` variable must now be set to \"1\" rather than any value in order to enable it.

> 变量 `COPY_LIC_MANIFEST` 现在必须设置为“1”，而不是任何值，以启用它。

## `COPY_LIC_DIRS`

The `COPY_LIC_DIRS` variable must now be set to \"1\" rather than any value in order to enable it.

> `COPY_LIC_DIRS` 变量现在必须设置为“1”，而不是任何值，以启用它。

## `PACKAGE_GROUP`

The `PACKAGE_GROUP` variable has been renamed to `FEATURE_PACKAGES` to more accurately reflect its purpose. You can still use `PACKAGE_GROUP` but the OpenEmbedded build system produces a warning message when it encounters the variable.

> 变量 `PACKAGE_GROUP` 已更名为 `FEATURE_PACKAGES`，以更准确地反映其用途。您仍然可以使用 `PACKAGE_GROUP`，但 OpenEmbedded 构建系统在遇到该变量时会生成警告消息。

## Preprocess and Post Process Command Variable Behavior

The following variables now expect a semicolon separated list of functions to call and not arbitrary shell commands:

> 以下变量现在期望调用一系列以分号分隔的函数，而不是任意的 shell 命令。

> - `ROOTFS_PREPROCESS_COMMAND`
> - `ROOTFS_POSTPROCESS_COMMAND`
> - `SDK_POSTPROCESS_COMMAND`
> - `POPULATE_SDK_POST_TARGET_COMMAND`
> - `POPULATE_SDK_POST_HOST_COMMAND`
> - `IMAGE_POSTPROCESS_COMMAND`
> - `IMAGE_PREPROCESS_COMMAND`
> - `ROOTFS_POSTUNINSTALL_COMMAND`
> - `ROOTFS_POSTINSTALL_COMMAND`

For migration purposes, you can simply wrap shell commands in a shell function and then call the function. Here is an example:

> 为了迁移的目的，你可以简单地将 shell 命令包装在 shell 函数中，然后调用该函数。这里有一个例子：

```
my_postprocess_function() {
   echo "hello" > $/hello.txt
}
ROOTFS_POSTPROCESS_COMMAND += "my_postprocess_function; "
```

# Package Test (ptest)

Package Tests (ptest) are built but not installed by default. For information on using Package Tests, see the \"`dev-manual/packages:testing packages with ptest`\" section.

> 包测试(ptest)默认情况下可以构建但不会安装。有关使用包测试的信息，请参阅 Yocto 项目开发任务手册中的“dev-manual/packages：使用 ptest 测试包”部分。另请参阅“ref-classes-ptest”部分。

# Build Changes

Separate build and source directories have been enabled by default for selected recipes where it is known to work and for all recipes that inherit the `ref-classes-cmake` or `autotools_stage` classes.

> 默认情况下，为已知可以工作的特定配方以及所有继承 `ref-classes-cmake` 或 `autotools_stage` 类。

# `qemu-native`

`qemu-native` now builds without SDL-based graphical output support by default. The following additional lines are needed in your `local.conf` to enable it:

> 默认情况下，qemu-native 现在不再支持基于 SDL 的图形输出。要启用它，您需要在 local.conf 中添加以下附加行。

```
PACKAGECONFIG_pn-qemu-native = "sdl"
ASSUME_PROVIDED += "libsdl-native"
```

::: note
::: title
Note
:::

The default `local.conf` contains these statements. Consequently, if you are building a headless system and using a default `local.conf` file, you will need comment these two lines out.

> 默认的 `local.conf` 包含这些声明。因此，如果您正在构建一个无头系统并使用默认的 `local.conf` 文件，您将需要注释掉这两行。
> :::

# `core-image-basic`

`core-image-basic` has been renamed to `core-image-full-cmdline`.

> 核心映像基本已重命名为核心映像完整命令行。

In addition to `core-image-basic` being renamed, `packagegroup-core-basic` has been renamed to `packagegroup-core-full-cmdline` to match.

> 此外，将 `core-image-basic` 重命名为 `packagegroup-core-full-cmdline`，以匹配。

# Licensing

The top-level `LICENSE`. However, the licensing itself remains unchanged.

> 顶级 `LICENSE` 文件已被更改以更好地描述 `OpenEmbedded-Core(OE-Core)` 的各个组件的许可证。但是，许可本身保持不变。

Normally, this change would not cause any side-effects. However, some recipes point to this file within `LIC_FILES_CHKSUM`/LICENSE`.

> 通常，此更改不会导致任何副作用。但是，一些 recipes 指向 `LIC_FILES_CHKSUM`/LICENSE`。

# `CFLAGS` Options

The \"-fpermissive\" option has been removed from the default `CFLAGS` in the recipes.

> "CFLAGS"的默认值中已经移除了"-fpermissive"选项。您需要对使用这个选项构建失败的单个配方采取行动。您需要对报告的编译器问题进行修补，或者需要在配方中将"fpermissive"添加到"CFLAGS"中。

# Custom Image Output Types

Custom image output types, as selected using `IMAGE_FSTYPES` variable.

> 使用 `IMAGE_FSTYPES` 变量声明它们对其他镜像类型(如果有的话)的依赖性。

# Tasks

The `do_package_write` task has been removed. The task is no longer needed.

> `do_package_write` 任务已经被移除。该任务不再需要。

# `update-alternative` Provider

The default `update-alternatives` provider has been changed from `opkg` to `opkg-utils`. This change resolves some troublesome circular dependencies. The runtime package has also been renamed from `update-alternatives-cworth` to `update-alternatives-opkg`.

> 默认的 `update-alternatives` 提供者已从 `opkg` 更改为 `opkg-utils`。这一更改解决了一些麻烦的循环依赖关系。运行时包的名称也已从 `update-alternatives-cworth` 更改为 `update-alternatives-opkg`。

# `virtclass` Overrides

The `virtclass` overrides are now deprecated. Use the equivalent class overrides instead (e.g. `virtclass-native` becomes `class-native`.)

> `virtclass` 的覆盖现已弃用。请改用等效的类覆盖(例如，`virtclass-native` 变为 `class-native`)。

# Removed and Renamed Recipes

The following recipes have been removed:

> 以下 recipes 已被移除：

- `packagegroup-toolset-native` \-\-- this recipe is largely unused.
- `linux-yocto-3.8` \-\-- support for the Linux yocto 3.8 kernel has been dropped. Support for the 3.10 and 3.14 kernels have been added with the `linux-yocto-3.10` and `linux-yocto-3.14` recipes.

> 支持 Linux Yocto 3.8 内核已经被取消。支持 3.10 和 3.14 内核已经通过 `linux-yocto-3.10` 和 `linux-yocto-3.14` recipes 添加。

- `ocf-linux` \-\-- this recipe has been functionally replaced using `cryptodev-linux`.
- `genext2fs` \-\-- `genext2fs` is no longer used by the build system and is unmaintained upstream.

> - `genext2fs`--不再被构建系统使用，且上游未维护。

- `js` \-\-- this provided an ancient version of Mozilla\'s javascript engine that is no longer needed.

> 这提供了一个不再需要的 Mozilla 的古老版本的 JavaScript 引擎。

- `zaurusd` \-\-- the recipe has been moved to the `meta-handheld` layer.
- `eglibc 2.17` \-\-- replaced by the `eglibc 2.19` recipe.
- `gcc 4.7.2` \-\-- replaced by the now stable `gcc 4.8.2`.
- `external-sourcery-toolchain` \-\-- this recipe is now maintained in the `meta-sourcery` layer.
- `linux-libc-headers-yocto 3.4+git` \-\-- now using version 3.10 of the `linux-libc-headers` by default.

> Linux-libc-headers-yocto 3.4+git -- 现在默认使用 Linux-libc-headers 的 3.10 版本。

- `meta-toolchain-gmae` \-\-- this recipe is obsolete.
- `packagegroup-core-sdk-gmae` \-\-- this recipe is obsolete.
- `packagegroup-core-standalone-gmae-sdk-target` \-\-- this recipe is obsolete.

# Removed Classes

The following classes have become obsolete and have been removed:

> 以下课程已经过时，已被移除：

- `module_strip`
- `pkg_metainfo`
- `pkg_distribute`
- `image-empty`

# Reference Board Support Packages (BSPs)

The following reference BSPs changes occurred:

> 以下参考 BSPs 发生了变化：

- The BeagleBoard (`beagleboard`) ARM reference hardware has been replaced by the BeagleBone (`beaglebone`) hardware.

> BeagleBoard(`beagleboard`)这款 ARM 参考硬件已经被 BeagleBone(`beaglebone`)硬件所取代。

- The RouterStation Pro (`routerstationpro`) MIPS reference hardware has been replaced by the EdgeRouter Lite (`edgerouter`) hardware.

> 路由站 Pro(`routerstationpro`)MIPS 参考硬件已被 EdgeRouter Lite(`edgerouter`)硬件所取代。

The previous reference BSPs for the `beagleboard` and `routerstationpro` machines are still available in a new `meta-yocto-bsp-old` layer in the :yocto_[git:%60Source](git:%60Source) Repositories \<\>[ at :yocto_git:]/meta-yocto-bsp-old/\`.

> 之前的 `beagleboard` 和 `routerstationpro` 机器的参考 BSP 仍然可以在:yocto_[git:%60Source](git:%60Source) 存储库中的新 `meta-yocto-bsp-old` 层中找到，位置为：yocto_git:]/meta-yocto-bsp-old/\`。
