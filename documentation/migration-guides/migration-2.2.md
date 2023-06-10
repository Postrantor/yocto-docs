---
tip: translate by openai@2023-06-07 21:26:31
...
---
title: Release 2.2 (morty)
---

This section provides migration information for moving to the Yocto Project 2.2 Release (codename \"morty\") from the prior release.

> 这一节提供从先前版本迁移到 Yocto Project 2.2 Release（代号“morty”）的迁移信息。

# Minimum Kernel Version {#migration-2.2-minimum-kernel-version}


The minimum kernel version for the target system and for SDK is now 3.2.0, due to the upgrade to `glibc 2.24`. Specifically, for AArch64-based targets the version is 3.14. For Nios II-based targets, the minimum kernel version is 3.19.

> 目标系统和SDK的最低内核版本现在是3.2.0，由于升级到`glibc 2.24`。具体来说，对于基于AArch64的目标，版本是3.14。对于基于Nios II的目标，最低内核版本是3.19。

::: note
::: title
Note
:::


For x86 and x86_64, you can reset `OLDEST_KERNEL`{.interpreted-text role="term"} to anything down to 2.6.32 if desired.

> 对于x86和x86_64，您可以将`OLDEST_KERNEL`重置为任何低至2.6.32的版本，如果需要的话。
:::

# Staging Directories in Sysroot Has Been Simplified {#migration-2.2-staging-directories-in-sysroot-simplified}


The way directories are staged in sysroot has been simplified and introduces the new `SYSROOT_DIRS`{.interpreted-text role="term"}, `SYSROOT_DIRS_NATIVE`{.interpreted-text role="term"}, and `SYSROOT_DIRS_BLACKLIST` (replaced by `SYSROOT_DIRS_IGNORE`{.interpreted-text role="term"} in version 3.5). See the :oe_lists:[v2 patch series on the OE-Core Mailing List \</pipermail/openembedded-core/2016-May/121365.html\>]{.title-ref} for additional information.

> 系统根目录中的目录结构已经简化，引入了新的`SYSROOT_DIRS`、`SYSROOT_DIRS_NATIVE`和`SYSROOT_DIRS_BLACKLIST`（在3.5版本中被`SYSROOT_DIRS_IGNORE`取代）。有关更多信息，请参见OE-Core邮件列表中的v2补丁系列：\</pipermail/openembedded-core/2016-May/121365.html\>。

# Removal of Old Images and Other Files in `tmp/deploy` Now Enabled {#migration-2.2-removal-of-old-images-from-tmp-deploy-now-enabled}


Removal of old images and other files in `tmp/deploy/` is now enabled by default due to a new staging method used for those files. As a result of this change, the `RM_OLD_IMAGE` variable is now redundant.

> 现在默认情况下启用了`tmp/deploy/`中旧图像和其他文件的移除，这是因为新的用于这些文件的阶段方法。由于此更改，`RM_OLD_IMAGE`变量现在已经多余了。

# Python Changes {#migration-2.2-python-changes}


The following changes for Python occurred:

> 以下是Python的变化：

## BitBake Now Requires Python 3.4+ {#migration-2.2-bitbake-now-requires-python-3.4}


BitBake requires Python 3.4 or greater.

> BitBake需要Python 3.4或更高版本。

## UTF-8 Locale Required on Build Host {#migration-2.2-utf-8-locale-required-on-build-host}


A UTF-8 locale is required on the build host due to Python 3. Since C.UTF-8 is not a standard, the default is en_US.UTF-8.

> 需要在构建主机上使用UTF-8语言环境，因为Python 3。由于C.UTF-8不是标准，因此默认为en_US.UTF-8。

## Metadata Must Now Use Python 3 Syntax {#migration-2.2-metadata-now-must-use-python-3-syntax}


The metadata is now required to use Python 3 syntax. For help preparing metadata, see any of the many Python 3 porting guides available. Alternatively, you can reference the conversion commits for BitBake and you can use `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"} as a guide for changes. Following are particular areas of interest:

> 现在需要使用Python 3语法来使用元数据。要获取有关准备元数据的帮助，请参阅任何可用的Python 3移植指南。或者，您可以参考BitBake的转换提交，并使用OpenEmbedded-Core（OE-Core）作为更改的指南。以下是特别感兴趣的领域：

> - subprocess command-line pipes needing locale decoding
> - the syntax for octal values changed
> - the `iter*()` functions changed name
> - iterators now return views, not lists
> - changed names for Python modules

## Target Python Recipes Switched to Python 3 {#migration-2.2-target-python-recipes-switched-to-python-3}


Most target Python recipes have now been switched to Python 3. Unfortunately, systems using RPM as a package manager and providing online package-manager support through SMART still require Python 2.

> 大多数针对Python的配方现在已经转换到Python 3。不幸的是，使用RPM作为软件包管理器，并通过SMART提供在线软件包管理器支持的系统仍然需要Python 2。

::: note
::: title
Note
:::


Python 2 and recipes that use it can still be built for the target as with previous versions.

> Python 2及使用它的配方仍可以构建用于目标的版本，就像以前的版本一样。
:::

## `buildtools-tarball` Includes Python 3 {#migration-2.2-buildtools-tarball-includes-python-3}


The `buildtools`{.interpreted-text role="term"} tarball now includes Python 3.

> 现在`buildtools`{.interpreted-text role="term"}压缩包包含Python 3.

# uClibc Replaced by musl {#migration-2.2-uclibc-replaced-by-musl}


uClibc has been removed in favor of musl. Musl has matured, is better maintained, and is compatible with a wider range of applications as compared to uClibc.

> uClibc已被musl所取代。Musl已经成熟，维护得更好，与更多应用程序的兼容性比uClibc更强。

# `${B}` No Longer Default Working Directory for Tasks {#migration-2.2-B-no-longer-default-working-directory-for-tasks}


`${``B`{.interpreted-text role="term"}`}` is no longer the default working directory for tasks. Consequently, any custom tasks you define now need to either have the `[``dirs <bitbake-user-manual/bitbake-user-manual-metadata:variable flags>`{.interpreted-text role="ref"}`]` flag set, or the task needs to change into the appropriate working directory manually (e.g using `cd` for a shell task).

> "${B}不再是任务的默认工作目录。因此，您现在定义的任何自定义任务都需要设置[dirs <bitbake-user-manual/bitbake-user-manual-metadata:variable flags>]标志，或者任务需要手动更改到适当的工作目录（例如使用cd为shell任务）。

::: note
::: title
Note
:::


The preferred method is to use the \[dirs\] flag.

> 最佳方法是使用\[dirs\] 标志。
:::

# `runqemu` Ported to Python {#migration-2.2-runqemu-ported-to-python}


`runqemu` has been ported to Python and has changed behavior in some cases. Previous usage patterns continue to be supported.

> runqemu已经移植到Python，并在某些情况下改变了行为。 以前的使用模式仍然受到支持。


The new `runqemu` is a Python script. Machine knowledge is no longer hardcoded into `runqemu`. You can choose to use the `qemuboot` configuration file to define the BSP\'s own arguments and to make it bootable with `runqemu`. If you use a configuration file, use the following form:

> 新的`runqemu`是一个Python脚本。机器知识不再被硬编码进`runqemu`中。您可以选择使用`qemuboot`配置文件来定义BSP自己的参数，并使其可以通过`runqemu`启动。如果使用配置文件，请使用以下格式：

```
image-name-machine.qemuboot.conf
```


The configuration file enables fine-grained tuning of options passed to QEMU without the `runqemu` script hard-coding any knowledge about different machines. Using a configuration file is particularly convenient when trying to use QEMU with machines other than the `qemu*` machines in `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"}. The `qemuboot.conf` file is generated by the `qemuboot` class when the root filesystem is being built (i.e. build rootfs). QEMU boot arguments can be set in BSP\'s configuration file and the `qemuboot` class will save them to `qemuboot.conf`.

> 配置文件可以微调传递给QEMU的选项，而不需要“runqemu”脚本硬编码关于不同机器的任何知识。当尝试使用OpenEmbedded-Core（OE-Core）中的“qemu*”机器以外的机器时，使用配置文件尤其方便。当构建根文件系统（即构建根文件系统）时，qemuboot类会生成qemuboot.conf文件。可以在BSP的配置文件中设置QEMU启动参数，qemuboot类会将它们保存到qemuboot.conf中。


If you want to use `runqemu` without a configuration file, use the following command form:

> 如果你想不使用配置文件来使用runqemu，请使用以下命令：

```
$ runqemu machine rootfs kernel [options]
```


Supported machines are as follows:

> 支持的机器如下：

> - qemuarm
> - qemuarm64
> - qemux86
> - qemux86-64
> - qemuppc
> - qemumips
> - qemumips64
> - qemumipsel
> - qemumips64el


Consider the following example, which uses the `qemux86-64` machine, provides a root filesystem, provides an image, and uses the `nographic` option:

> 以下是一个例子，它使用`qemux86-64`机器，提供根文件系统，提供一个映像，并使用`nographic`选项：

```
$ runqemu qemux86-64 tmp/deploy/images/qemux86-64/core-image-minimal-qemux86-64.ext4 tmp/deploy/images/qemux86-64/bzImage nographic
```


Following is a list of variables that can be set in configuration files such as `bsp.conf` to enable the BSP to be booted by `runqemu`:

> 以下是可以在配置文件（如bsp.conf）中设置的变量，以使BSP能够通过runqemu启动：

```
QB_SYSTEM_NAME: QEMU name (e.g. "qemu-system-i386")
QB_OPT_APPEND: Options to append to QEMU (e.g. "-show-cursor")
QB_DEFAULT_KERNEL: Default kernel to boot (e.g. "bzImage")
QB_DEFAULT_FSTYPE: Default FSTYPE to boot (e.g. "ext4")
QB_MEM: Memory (e.g. "-m 512")
QB_MACHINE: QEMU machine (e.g. "-machine virt")
QB_CPU: QEMU cpu (e.g. "-cpu qemu32")
QB_CPU_KVM: Similar to QB_CPU except used for kvm support (e.g. "-cpu kvm64")
QB_KERNEL_CMDLINE_APPEND: Options to append to the kernel's -append
                          option (e.g. "console=ttyS0 console=tty")
QB_DTB: QEMU dtb name
QB_AUDIO_DRV: QEMU audio driver (e.g. "alsa", set it when support audio)
QB_AUDIO_OPT: QEMU audio option (e.g. "-soundhw ac97,es1370"), which is used
              when QB_AUDIO_DRV is set.
QB_KERNEL_ROOT: Kernel's root (e.g. /dev/vda)
QB_TAP_OPT: Network option for 'tap' mode (e.g.
            "-netdev tap,id=net0,ifname=@TAP@,script=no,downscript=no -device virtio-net-device,netdev=net0").
             runqemu will replace "@TAP@" with the one that is used, such as tap0, tap1 ...
QB_SLIRP_OPT: Network option for SLIRP mode (e.g. "-netdev user,id=net0 -device virtio-net-device,netdev=net0")
QB_ROOTFS_OPT: Used as rootfs (e.g.
               "-drive id=disk0,file=@ROOTFS@,if=none,format=raw -device virtio-blk-device,drive=disk0").
               runqemu will replace "@ROOTFS@" with the one which is used, such as
               core-image-minimal-qemuarm64.ext4.
QB_SERIAL_OPT: Serial port (e.g. "-serial mon:stdio")
QB_TCPSERIAL_OPT: tcp serial port option (e.g.
                  " -device virtio-serial-device -chardev socket,id=virtcon,port=@PORT@,host=127.0.0.1 -device      virtconsole,chardev=virtcon"
                  runqemu will replace "@PORT@" with the port number which is used.
```


To use `runqemu`, set `IMAGE_CLASSES`{.interpreted-text role="term"} as follows and run `runqemu`:

> 使用 `runqemu`，请将 `IMAGE_CLASSES` 设置如下，然后运行 `runqemu`：

::: note
::: title
Note
:::


\"QB\" means \"QEMU Boot\".

> "QB" 意思是"QEMU 启动"。
:::

::: note
::: title
Note
:::


For command-line syntax, use `runqemu help`.

> 使用 `runqemu help` 来查看命令行语法。
:::

```
IMAGE_CLASSES += "qemuboot"
```

# Default Linker Hash Style Changed {#migration-2.2-default-linker-hash-style-changed}


The default linker hash style for `gcc-cross` is now \"sysv\" in order to catch recipes that are building software without using the OpenEmbedded `LDFLAGS`{.interpreted-text role="term"}. This change could result in seeing some \"No GNU_HASH in the elf binary\" QA issues when building such recipes. You need to fix these recipes so that they use the expected `LDFLAGS`{.interpreted-text role="term"}. Depending on how the software is built, the build system used by the software (e.g. a Makefile) might need to be patched. However, sometimes making this fix is as simple as adding the following to the recipe:

> 默认的链接器哈希样式现在"sysv"，以捕获不使用OpenEmbedded `LDFLAGS`{.interpreted-text role="term"}构建软件的配方。当构建这样的配方时，此更改可能会导致看到一些“elf二进制文件中没有GNU_HASH”QA问题。您需要修复这些配方，以便它们使用预期的`LDFLAGS`{.interpreted-text role="term"}。根据软件的构建方式，软件使用的构建系统（例如Makefile）可能需要进行补丁。但是，有时只需要将以下内容添加到配方中即可：

```
TARGET_CC_ARCH += "${LDFLAGS}"
```

# `KERNEL_IMAGE_BASE_NAME` no Longer Uses `KERNEL_IMAGETYPE` {#migration-2.2-kernel-image-base-name-no-longer-uses-kernel-imagetype}


The `KERNEL_IMAGE_BASE_NAME` variable no longer uses the `KERNEL_IMAGETYPE`{.interpreted-text role="term"} variable to create the image\'s base name. Because the OpenEmbedded build system can now build multiple kernel image types, this part of the kernel image base name as been removed leaving only the following:

> 变量`KERNEL_IMAGE_BASE_NAME`不再使用变量`KERNEL_IMAGETYPE`来创建图像的基本名称。由于OpenEmbedded构建系统现在可以构建多种内核图像类型，因此已经移除了内核图像基本名称的这一部分，只留下以下部分：

```
KERNEL_IMAGE_BASE_NAME ?= "${PKGE}-${PKGV}-${PKGR}-${MACHINE}-${DATETIME}"
```


If you have recipes or classes that use `KERNEL_IMAGE_BASE_NAME` directly, you might need to update the references to ensure they continue to work.

> 如果您有使用`KERNEL_IMAGE_BASE_NAME`的配方或课程，您可能需要更新引用以确保它们继续有效。

# `IMGDEPLOYDIR` Replaces `DEPLOY_DIR_IMAGE` for Most Use Cases {#migration-2.2-imgdeploydir-replaces-deploy-dir-image-for-most-use-cases}


The `IMGDEPLOYDIR`{.interpreted-text role="term"} variable was introduced to allow sstate caching of image creation results. Image recipes defining custom `IMAGE_CMD`{.interpreted-text role="term"} or doing postprocessing on the generated images need to be adapted to use `IMGDEPLOYDIR`{.interpreted-text role="term"} instead of `DEPLOY_DIR_IMAGE`{.interpreted-text role="term"}. `IMAGE_MANIFEST`{.interpreted-text role="term"} creation and symlinking of the most recent image file will fail otherwise.

> 变量`IMGDEPLOYDIR`被引入以允许sstate缓存图像创建结果。定义自定义`IMAGE_CMD`或对生成的图像进行后处理的图像配方需要被改造以使用`IMGDEPLOYDIR`而不是`DEPLOY_DIR_IMAGE`。否则，`IMAGE_MANIFEST`的创建和最新图像文件的符号链接将失败。

# BitBake Changes {#migration-2.2-bitbake-changes}


The following changes took place for BitBake:

> 以下变化发生在BitBake上：


- The \"goggle\" UI and standalone image-writer tool have been removed as they both require GTK+ 2.0 and were not being maintained.

> "Goggle" 用户界面和独立的图像编写工具已被删除，因为它们都需要GTK+ 2.0，而且没有被维护。

- The Perforce fetcher now supports `SRCREV`{.interpreted-text role="term"} for specifying the source revision to use, be it `${``AUTOREV`{.interpreted-text role="term"}`}`, changelist number, p4date, or label, in preference to separate `SRC_URI`{.interpreted-text role="term"} parameters to specify these. This change is more in-line with how the other fetchers work for source control systems. Recipes that fetch from Perforce will need to be updated to use `SRCREV`{.interpreted-text role="term"} in place of specifying the source revision within `SRC_URI`{.interpreted-text role="term"}.

> - Perforce 获取器现在支持使用 `SRCREV`{.interpreted-text role="term"} 来指定要使用的源代码修订版本，无论是 `${``AUTOREV`{.interpreted-text role="term"}`}`、更改列表编号、p4date 或标签，都优先于使用单独的 `SRC_URI`{.interpreted-text role="term"} 参数来指定这些信息。此更改与其他获取器更加符合源代码控制系统的工作方式。从 Perforce 获取的配方需要更新为使用 `SRCREV`{.interpreted-text role="term"} 来代替在 `SRC_URI`{.interpreted-text role="term"} 中指定源代码修订版本。

- Some of BitBake\'s internal code structures for accessing the recipe cache needed to be changed to support the new multi-configuration functionality. These changes will affect external tools that use BitBake\'s tinfoil module. For information on these changes, see the changes made to the scripts supplied with OpenEmbedded-Core: :yocto\_[git:%601](git:%601) \</poky/commit/?id=189371f8393971d00bca0fceffd67cc07784f6ee\>[ and :yocto_git:\`2 \</poky/commit/?id=4a5aa7ea4d07c2c90a1654b174873abb018acc67\>]{.title-ref}.

> 一些BitBake的内部代码结构需要更改以支持新的多配置功能。这些更改将影响使用BitBake tinfoil模块的外部工具。有关这些更改的信息，请参见OpenEmbedded-Core提供的脚本所做的更改：:yocto_[git:%601](git:%601) \</poky/commit/?id=189371f8393971d00bca0fceffd67cc07784f6ee\> [和:yocto_git:\`2 \</poky/commit/?id=4a5aa7ea4d07c2c90a1654b174873abb018acc67\>]{.title-ref}。

- The task management code has been rewritten to avoid using ID indirection in order to improve performance. This change is unlikely to cause any problems for most users. However, the setscene verification function as pointed to by `BB_SETSCENE_VERIFY_FUNCTION` needed to change signature. Consequently, a new variable named `BB_SETSCENE_VERIFY_FUNCTION2` has been added allowing multiple versions of BitBake to work with suitably written metadata, which includes OpenEmbedded-Core and Poky. Anyone with custom BitBake task scheduler code might also need to update the code to handle the new structure.

> 任务管理代码已经重写，以避免使用ID间接来提高性能。这个变化不太可能给大多数用户带来任何问题。但是，由`BB_SETSCENE_VERIFY_FUNCTION`指向的setscene验证函数需要改变签名。因此，添加了一个新的变量`BB_SETSCENE_VERIFY_FUNCTION2`，使得兼容适当编写的元数据（包括OpenEmbedded-Core和Poky）的多个版本的BitBake可以正常工作。任何使用自定义BitBake任务调度器代码的人也可能需要更新代码以处理新的结构。

# Swabber has Been Removed {#migration-2.2-swabber-has-been-removed}


Swabber, a tool that was intended to detect host contamination in the build process, has been removed, as it has been unmaintained and unused for some time and was never particularly effective. The OpenEmbedded build system has since incorporated a number of mechanisms including enhanced QA checks that mean that there is less of a need for such a tool.

> 清洁工，一个旨在检测构建过程中主机污染的工具，已经被移除，因为它已经长期处于不维护和未使用状态，而且从来没有特别有效。OpenEmbedded构建系统自此采用了许多机制，包括增强的QA检查，这意味着不再需要这样的工具。

# Removed Recipes {#migration-2.2-removed-recipes}


The following recipes have been removed:

> 以下食谱已被移除：

- `augeas`: No longer needed and has been moved to `meta-oe`.
- `directfb`: Unmaintained and has been moved to `meta-oe`.
- `gcc`: Removed 4.9 version. Versions 5.4 and 6.2 are still present.
- `gnome-doc-utils`: No longer needed.
- `gtk-doc-stub`: Replaced by `gtk-doc`.
- `gtk-engines`: No longer needed and has been moved to `meta-gnome`.
- `gtk-sato-engine`: Became obsolete.
- `libglade`: No longer needed and has been moved to `meta-oe`.

- `libmad`: Unmaintained and functionally replaced by `libmpg123`. `libmad` has been moved to `meta-oe`.

> - `libmad`：未维护且功能已被`libmpg123`取代。`libmad`已移至`meta-oe`中。
- `libowl`: Became obsolete.
- `libxsettings-client`: No longer needed.
- `oh-puzzles`: Functionally replaced by `puzzles`.
- `oprofileui`: Became obsolete. OProfile has been largely supplanted by perf.
- `packagegroup-core-directfb.bb`: Removed.
- `core-image-directfb.bb`: Removed.
- `pointercal`: No longer needed and has been moved to `meta-oe`.
- `python-imaging`: No longer needed and moved to `meta-python`
- `python-pyrex`: No longer needed and moved to `meta-python`.
- `sato-icon-theme`: Became obsolete.

- `swabber-native`: Swabber has been removed. See the `entry on Swabber <migration-guides/migration-2.2:swabber has been removed>`{.interpreted-text role="ref"}.

> Swabber已被移除，请参阅<migration-guides/migration-2.2:swabber has been removed>的条目。
- `tslib`: No longer needed and has been moved to `meta-oe`.
- `uclibc`: Removed in favor of musl.
- `xtscal`: No longer needed and moved to `meta-oe`

# Removed Classes {#migration-2.2-removed-classes}


The following classes have been removed:

> 以下课程已被取消：

- `distutils-native-base`: No longer needed.
- `distutils3-native-base`: No longer needed.

- `sdl`: Only set `DEPENDS`{.interpreted-text role="term"} and `SECTION`{.interpreted-text role="term"}, which are better set within the recipe instead.

> 只设置`DEPENDS`{.interpreted-text role="term"}和`SECTION`{.interpreted-text role="term"}，这些更好地设置在食谱中。
- `sip`: Mostly unused.

- `swabber`: See the `entry on Swabber <migration-guides/migration-2.2:swabber has been removed>`{.interpreted-text role="ref"}.

> 请参阅“Swabber的条目<migration-guides/migration-2.2：Swabber已被移除>”。

# Minor Packaging Changes {#migration-2.2-minor-packaging-changes}


The following minor packaging changes have occurred:

> 以下的小型包装变更已经发生了：

- `grub`: Split `grub-editenv` into its own package.
- `systemd`: Split container and vm related units into a new package, systemd-container.
- `util-linux`: Moved `prlimit` to a separate `util-linux-prlimit` package.

# Miscellaneous Changes {#migration-2.2-miscellaneous-changes}


The following miscellaneous changes have occurred:

> 以下发生了各种各样的变化：


- `package_regex.inc`: Removed because the definitions `package_regex.inc` previously contained have been moved to their respective recipes.

> `- package_regex.inc`：已移除，因为之前包含的定义已移至各自的配方中。

- Both `devtool add` and `recipetool create` now use a fixed `SRCREV`{.interpreted-text role="term"} by default when fetching from a Git repository. You can override this in either case to use `${``AUTOREV`{.interpreted-text role="term"}`}` instead by using the `-a` or `--autorev` command-line option

> 两个命令`devtool add`和`recipetool create`默认情况下从Git存储库中获取时都使用固定的`SRCREV`。您可以通过使用`-a`或`--autorev`命令行选项来覆盖此设置，以使用`${``AUTOREV`}`代替。
- `distcc`: GTK+ UI is now disabled by default.
- `packagegroup-core-tools-testapps`: Removed Piglit.

- `ref-classes-image`{.interpreted-text role="ref"}: Renamed COMPRESS(ION) to CONVERSION. This change means that `COMPRESSIONTYPES`, `COMPRESS_DEPENDS` and `COMPRESS_CMD` are deprecated in favor of `CONVERSIONTYPES`, `CONVERSION_DEPENDS` and `CONVERSION_CMD`{.interpreted-text role="term"}. The `COMPRESS*` variable names will still work in the 2.2 release but metadata that does not need to be backwards-compatible should be changed to use the new names as the `COMPRESS*` ones will be removed in a future release.

> 重命名COMPRESS(ION)为CONVERSION。这个变化意味着`COMPRESSIONTYPES`、`COMPRESS_DEPENDS`和`COMPRESS_CMD`将在2.2版本中被弃用，改用`CONVERSIONTYPES`、`CONVERSION_DEPENDS`和`CONVERSION_CMD`。`COMPRESS*`变量名在2.2版本中仍然可以使用，但是不需要向后兼容的元数据应该改用新的名称，因为`COMPRESS*`将在未来的版本中被移除。

- `gtk-doc`: A full version of `gtk-doc` is now made available. However, some old software might not be capable of using the current version of `gtk-doc` to build documentation. You need to change recipes that build such software so that they explicitly disable building documentation with `gtk-doc`.

> `gtk-doc`：现在提供了完整版的`gtk-doc`。但是，一些旧的软件可能无法使用当前版本的`gtk-doc`来构建文档。您需要更改构建此类软件的配方，以便显式禁用使用`gtk-doc`构建文档。
