---
tip: translate by openai@2023-06-07 20:53:23
...
---
title: Release 1.5 (dora)
-------------------------

This section provides migration information for moving to the Yocto Project 1.5 Release (codename \"dora\") from the prior release.

> 这一节提供从前一个版本迁移到 Yocto Project 1.5 Release(代号“dora”)的迁移信息。

# Host Dependency Changes

The OpenEmbedded build system now has some additional requirements on the host system:

> 现在，OpenEmbedded 构建系统对主机系统有一些额外的要求：

- Python 2.7.3+
- Tar 1.24+
- Git 1.7.8+
- Patched version of Make if you are using 3.82. Most distributions that provide Make 3.82 use the patched version.

> 如果您正在使用 3.82，可以使用修补版本的 Make。大多数提供 Make 3.82 的发行版都使用了修补版本。

If the Linux distribution you are using on your build host does not provide packages for these, you can install and use the Buildtools tarball, which provides an SDK-like environment containing them.

> 如果您在构建主机上使用的 Linux 发行版不提供这些包，您可以安装并使用 Buildtools tarball，它提供了一个类似 SDK 的环境，包含它们。

For more information on this requirement, see the \"`system-requirements-buildtools`\" section.

> 对于这个要求的更多信息，请参见“system-requirements-buildtools”部分。

# `atom-pc` Board Support Package (BSP)

The `atom-pc` hardware reference BSP has been replaced by a `genericx86` BSP. This BSP is not necessarily guaranteed to work on all x86 hardware, but it will run on a wider range of systems than the `atom-pc` did.

> `atom-pc` 硬件参考 BSP 已被 `genericx86` BSP 所取代。这个 BSP 不一定能保证在所有 x86 硬件上运行，但它可以在比 `atom-pc` 更宽广的系统上运行。

::: note
::: title
Note
:::

Additionally, a `genericx86-64` BSP has been added for 64-bit Atom systems.

> 此外，为 64 位 Atom 系统增加了一个 `genericx86-64` BSP。
> :::

# BitBake

The following changes have been made that relate to BitBake:

> 以下更改与 BitBake 有关：

- BitBake now supports a `_remove` operator. The addition of this operator means you will have to rename any items in recipe space (functions, variables) whose names currently contain `_remove_` or end with `_remove` to avoid unexpected behavior.

> BitBake 现在支持一个 `_remove` 操作符。添加这个操作符意味着您将不得不重命名配方空间(函数、变量)中当前含有 `_remove_` 或以 `_remove` 结尾的项目，以避免意外行为。

- BitBake\'s global method pool has been removed. This method is not particularly useful and led to clashes between recipes containing functions that had the same name.

> BitBake 的全局方法池已被移除。这种方法并不特别有用，导致包含具有相同名称的函数的配方之间发生冲突。

- The \"none\" server backend has been removed. The \"process\" server backend has been serving well as the default for a long time now.

> "无"服务器后端已经被移除。"进程"服务器后端已经很长一段时间以来都很好地担任默认服务了。

- The `bitbake-runtask` script has been removed.
- `$ items were seldom used. Attempting to use them could result in two versions being built simultaneously rather than just one version due to the way BitBake resolves dependencies.

> - `P` 和 `PF` 不再默认添加到 bitbake.conf 中的 PROVIDES 中。这些特定版本的 PROVIDES 项很少被使用。尝试使用它们可能会导致由于 BitBake 解析依赖关系的方式，同时构建两个版本而不是只有一个版本。

# QA Warnings

The following changes have been made to the package QA checks:

> 以下对包裹质量检查做出了如下更改：

- If you have customized `ERROR_QA`\" section.

> 如果您在配置中自定义了 ERROR_QA 或 WARN_QA 值，请检查它们是否包含您希望报告的所有问题。以前的 Yocto Project 版本存在一个错误，这意味着任何未在 ERROR_QA 或 WARN_QA 中提及的项目都将被视为警告。因此，几个重要的项目尚未出现在 WARN_QA 的默认值中。所有可能的 QA 检查现已在“ref-classes-insane”部分中文档化。

- An additional QA check has been added to check if `/usr/share/info/dir` is being installed. Your recipe should delete this file within `ref-tasks-install` if \"make install\" is installing it.

> 额外的 QA 检查已添加，以检查是否正在安装 `/usr/share/info/dir`。如果使用“make install”安装，您的配方应该在 ref-tasks-install 中删除此文件。

- If you are using the `ref-classes-buildhistory`\" section.

> 如果您正在使用 `ref-classes-buildhistory`”部分中的文档 QA 检查。

# Directory Layout Changes

The following directory changes exist:

> 以下目录变更存在:

- Output SDK installer files are now named to include the image name and tuning architecture through the `SDK_NAME` variable.

> 现在，SDK 安装程序文件的名称已经通过 `SDK_NAME` 变量包含了映像名称和调整架构。

- Images and related files are now installed into a directory that is specific to the machine, instead of a parent directory containing output files for multiple machines. The `DEPLOY_DIR_IMAGE` variable in the external environment.

> 镜像及相关文件现在被安装到一个特定于机器的目录中，而不是包含多台机器输出文件的父目录中。`DEPLOY_DIR_IMAGE` 变量。

- When buildhistory is enabled, its output is now written under the `Build Directory`.

> 当启用 buildhistory 时，它的输出现在被写入到 `构建目录` 而不是 `TMPDIR`。这样做可以更容易地删除 `TMPDIR` 并保留构建历史记录。此外，生成的 SDK 的数据现在按 `IMAGE_NAME` 分割。

- When `ref-classes-buildhistory`.

> 当启用 `ref-classes-buildhistory` 分割。

- The `pkgdata` directory produced as part of the packaging process has been collapsed into a single machine-specific directory. This directory is located under `sysroots` and uses a machine-specific name (i.e. `tmp/sysroots/machine/pkgdata`).

> `pkgdata` 目录是打包过程中产生的，已经被压缩到一个特定机器的目录中。这个目录位于 `sysroots` 下，使用特定机器的名称(例如 `tmp/sysroots/machine/pkgdata`)。

# Shortened Git `SRCREV` Values

BitBake will now shorten revisions from Git repositories from the normal 40 characters down to 10 characters within `SRCPV` for improved usability in path and filenames. This change should be safe within contexts where these revisions are used because the chances of spatially close collisions is very low. Distant collisions are not a major issue in the way the values are used.

> BitBake 现在会将 Git 仓库中的修订从正常的 40 个字符缩减到 SRCPV 中的 10 个字符，以提高路径和文件名的可用性。在使用这些修订的上下文中，此更改应该是安全的，因为空间上接近的碰撞的可能性很低。在使用这些值的方式中，遥远的碰撞不是一个主要问题。

# `IMAGE_FEATURES`

The following changes have been made that relate to `IMAGE_FEATURES`:

> 以下更改与“IMAGE_FEATURES”有关：

- The value of `IMAGE_FEATURES`. The \"validitems\" varflag change allows additional features to be added if they are not provided using the previous two mechanisms.

> `IMAGE_FEATURES` 上的新“validitems”varflag。“validitems”varflag 更改允许添加其他功能，如果它们不是使用前两种机制提供的。

- The previously deprecated \"apps-console-core\" `IMAGE_FEATURES` if you wish to have the splash screen enabled, since this is all that apps-console-core was doing.

> 之前被弃用的“apps-console-core”  `IMAGE_FEATURES` 中，因为这是 apps-console-core 所做的所有事情。

# `/run`

The `/run` directory from the Filesystem Hierarchy Standard 3.0 has been introduced. You can find some of the implications for this change :oe_[git:%60here](git:%60here) \</openembedded-core/commit/?id=0e326280a15b0f2c4ef2ef4ec441f63f55b75873\>[. The change also means that recipes that install files to ]__.

> 文件系统层次结构标准 3.0 引入了“/run”目录。您可以在此处找到一些关于此更改的影响：oe_[git:`here`](git:%60here%60) </openembedded-core/commit/?id=0e326280a15b0f2c4ef2ef4ec441f63f55b75873> 。这一变化还意味着将文件安装到[/var/run][的配方必须更改。您可以在此处找到有关如何进行这些更改的指南：[https://www.mail-archive.com/openembedded-devel@lists.openembedded.org/msg31649.html](https://www.mail-archive.com/openembedded-devel@lists.openembedded.org/msg31649.html) 。

# Removal of Package Manager Database Within Image Recipes

The image `core-image-minimal` no longer adds `remove_packaging_data_files` to `ROOTFS_POSTPROCESS_COMMAND`. If you have custom image recipes that make this addition, you should remove the lines, as they are not needed and might interfere with correct operation of postinstall scripts.

> 镜像 `core-image-minimal` 不再将 `remove_packaging_data_files` 添加到 `ROOTFS_POSTPROCESS_COMMAND` 中时，这个添加现在会自动处理。如果您有自定义的镜像配方会进行这样的添加，您应该删除这些行，因为它们不再需要，并可能干扰 postinstall 脚本的正确操作。

# Images Now Rebuild Only on Changes Instead of Every Time

The `ref-tasks-rootfs` and other related image construction tasks are no longer marked as \"nostamp\". Consequently, they will only be re-executed when their inputs have changed. Previous versions of the OpenEmbedded build system always rebuilt the image when requested rather when necessary.

> ref-tasks-rootfs 和其他相关的镜像构建任务不再标记为“nostamp”。因此，它们只有在输入发生变化时才会被重新执行。以前版本的 OpenEmbedded 构建系统总是在请求时重建镜像，而不是必要时。

# Task Recipes

The previously deprecated `task.bbclass` has now been dropped. For recipes that previously inherited from this class, you should rename them from `task-*` to `packagegroup-*` and inherit `ref-classes-packagegroup` instead.

> 以前弃用的 `task.bbclass` 现在已经被删除。对于以前从此类继承的 recipes，您应该将它们从 `task-*` 重命名为 `packagegroup-*`，并继承 `ref-classes-packagegroup` 。

For more information, see the \"`ref-classes-packagegroup`\" section.

> 更多信息，请参见“ref-classes-packagegroup”部分。

# BusyBox

By default, we now split BusyBox into two binaries: one that is suid root for those components that need it, and another for the rest of the components. Splitting BusyBox allows for optimization that eliminates the `tinylogin` recipe as recommended by upstream. You can disable this split by setting `BUSYBOX_SPLIT_SUID` to \"0\".

> 默认情况下，我们现在将 BusyBox 拆分成两个二进制文件：一个是需要它的组件的 suid root，另一个是其余组件。拆分 BusyBox 允许优化，按照上游的建议消除了“tinylogin”配方。您可以通过将“BUSYBOX_SPLIT_SUID”设置为“0”来禁用此拆分。

# Automated Image Testing

A new automated image testing framework has been added through the `ref-classes-testimage` classes. This framework replaces the older `imagetest-qemu` framework.

> 一个新的自动化镜像测试框架已经通过 `ref-classes-testimage` 类添加。这个框架替换了较旧的 `imagetest-qemu` 框架。

You can learn more about performing automated image tests in the \"`dev-manual/runtime-testing:performing automated runtime testing`\" section in the Yocto Project Development Tasks Manual.

> 你可以在 Yocto 项目开发任务手册中的“dev-manual/runtime-testing：执行自动运行时测试”部分中了解更多有关执行自动镜像测试的信息。

# Build History

Following are changes to Build History:

> 以下是构建历史的变更：

- Installed package sizes: `installed-package-sizes.txt` for an image now records the size of the files installed by each package instead of the size of each compressed package archive file.

> 已安装的软件包大小：`installed-package-sizes.txt` 现在记录每个软件包安装的文件大小，而不是每个压缩软件包存档文件的大小。

- The dependency graphs (`depends*.dot`) now use the actual package names instead of replacing dashes, dots and plus signs with underscores.

> 依赖图(`depends*.dot`)现在使用实际的包名，而不是用下划线替换破折号、点和加号。

- The `buildhistory-diff` and `buildhistory-collect-srcrevs` utilities have improved command-line handling. Use the `--help` option for each utility for more information on the new syntax.

> `buildhistory-diff` 和 `buildhistory-collect-srcrevs` 实用程序的命令行处理已经得到改进。每个实用程序使用 `--help` 选项可以获得有关新语法的更多信息。

For more information on Build History, see the \"`dev-manual/build-quality:maintaining build output quality`\" section in the Yocto Project Development Tasks Manual.

> 欲了解更多有关构建历史的信息，请参阅 Yocto 项目开发任务手册中的“dev-manual / build-quality：维护构建输出质量”部分。

# `udev`

Following are changes to `udev`:

> 以下是对 `udev` 的更改：

- `udev` no longer brings in `udev-extraconf` automatically through `RRECOMMENDS`, since this was originally intended to be optional. If you need the extra rules, then add `udev-extraconf` to your image.

> udev 不再自动通过 RRECOMMENDS 加载 udev-extraconf，因为这本来是可选的。如果您需要额外的规则，请将 udev-extraconf 添加到您的映像中。

- `udev` no longer brings in `pciutils-ids` or `usbutils-ids` through `RRECOMMENDS`. These are not needed by `udev` itself and removing them saves around 350KB.

> udev 不再通过 RRECOMMENDS 把 pciutils-ids 或 usbutils-ids 带入。这些对 udev 本身没有必要，移除它们可以节省大约 350KB。

# Removed and Renamed Recipes

- The `linux-yocto` 3.2 kernel has been removed.
- `libtool-nativesdk` has been renamed to `nativesdk-libtool`.
- `tinylogin` has been removed. It has been replaced by a suid portion of Busybox. See the \"`migration-1.5-busybox`\" section for more information.

> `tinylogin` 已被移除。它已被 Busybox 的 suid 部分所取代。有关更多信息，请参阅“migration-1.5-busybox”部分。

- `external-python-tarball` has been renamed to `buildtools-tarball`.
- `web-webkit` has been removed. It has been functionally replaced by `midori`.
- `imake` has been removed. It is no longer needed by any other recipe.
- `transfig-native` has been removed. It is no longer needed by any other recipe.
- `anjuta-remote-run` has been removed. Anjuta IDE integration has not been officially supported for several releases.

> `anjuta-remote-run` 已被移除。几个发行版本以来，Anjuta IDE 集成就没有得到官方支持。

# Other Changes

Following is a list of short entries describing other changes:

> 以下是描述其他变化的简短条目列表：

- `run-postinsts`: Make this generic.
- `base-files`: Remove the unnecessary `media/` xxx directories.
- `alsa-state`: Provide an empty `asound.conf` by default.
- `classes/image`: Ensure `BAD_RECOMMENDATIONS` supports pre-renamed package names.

> 确保 `BAD_RECOMMENDATIONS` 支持预先命名的包名。

- `classes/rootfs_rpm`: Implement `BAD_RECOMMENDATIONS` for RPM.
- `systemd`: Remove `systemd_unitdir` if `systemd` is not in `DISTRO_FEATURES`.

> 如果 `DISTRO_FEATURES` 中没有 `systemd`，则删除 `systemd_unitdir`。

- `systemd`: Remove `init.d` dir if `systemd` unit file is present and `sysvinit` is not a distro feature.

> 如果存在 systemd 单元文件而 sysvinit 不是发行版的特性，则删除 init.d 目录。

- `libpam`: Deny all services for the `OTHER` entries.
- `ref-classes-image` in Bugzilla for more information.

> - `ref-classes-image`。

- `linux-dtb`: Use kernel build system to generate the `dtb` files.
- `kern-tools`: Switch from guilt to new `kgit-s2q` tool.
