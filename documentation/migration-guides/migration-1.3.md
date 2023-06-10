---
tip: translate by openai@2023-06-07 20:46:28
...
---
title: Release 1.3 (danny)
--------------------------

This section provides migration information for moving to the Yocto Project 1.3 Release (codename \"danny\") from the prior release.

> 此部分提供从先前版本迁移到 Yocto 项目 1.3 版本(代号“danny”)的迁移信息。

# Local Configuration

Differences include changes for `SSTATE_MIRRORS` and `bblayers.conf`.

> 差异包括对 `SSTATE_MIRRORS` 和 `bblayers.conf` 的更改。

## SSTATE_MIRRORS

The shared state cache (sstate-cache), as pointed to by `SSTATE_DIR`, you need to append \"PATH\" to the end of the mirror URL so that the path used by BitBake before the mirror substitution is appended to the path used to access the mirror. Here is an example:

> 状态共享缓存(sstate-cache)，指向 `SSTATE_DIR`，默认现在有两个字符的子目录，以防止同一个目录中有太多文件出现问题。此外，本机 sstate-cache 软件包，将构建以运行在主机系统上，将放入以使用发行版 ID 字符串命名的子目录中。如果您将新结构的 sstate-cache 复制到镜像位置(本地或远程)，然后在 `SSTATE_MIRRORS` 中指向它，则需要在镜像 URL 的末尾添加“PATH”，以便在镜像替换之前由 BitBake 使用的路径被附加到访问镜像的路径。这里是一个例子：

```
SSTATE_MIRRORS = "file://.* http://someserver.tld/share/sstate/PATH"
```

## bblayers.conf

The `meta-yocto` layer consists of two parts that correspond to the Poky reference distribution and the reference hardware Board Support Packages (BSPs), respectively: `meta-yocto` and `meta-yocto-bsp`. When running BitBake for the first time after upgrading, your `conf/bblayers.conf` file will be updated to handle this change and you will be asked to re-run or restart for the changes to take effect.

> meta-yocto 图层由两部分组成，分别对应 Poky 参考分发版和参考硬件板支持包(BSP)：meta-yocto 和 meta-yocto-bsp。在升级后第一次运行 BitBake 时，您的 conf/bblayers.conf 文件将被更新以处理此更改，并且会要求重新运行或重新启动以使更改生效。

# Recipes

Differences include changes for the following:

> 差异包括以下变更：

## Python Function Whitespace

All Python functions must now use four spaces for indentation. Previously, an inconsistent mix of spaces and tabs existed, which made extending these functions using `_append` or `_prepend` complicated given that Python treats whitespace as syntactically significant. If you are defining or extending any Python functions (e.g. `populate_packages`, `ref-tasks-unpack` and so forth) in custom recipes or classes, you need to ensure you are using consistent four-space indentation.

> 所有 Python 函数现在必须使用四个空格进行缩进。以前，空格和制表符混合使用，这使得使用 `_append` 或 `_prepend` 扩展这些函数变得复杂，因为 Python 将空白视为语法上重要的。如果您在自定义配方或类中定义或扩展任何 Python 函数(例如 `populate_packages`、`ref-tasks-unpack` 等)，您需要确保使用一致的四空格缩进。

## proto= in SRC_URI

Any use of `proto=` in `SRC_URI` needs to be changed to `protocol=`. In particular, this applies to the following URIs:

> 任何使用 `proto=` 在 `SRC_URI` 中的情况都需要改为 `protocol=`。特别是对以下 URI：

- `svn://`
- `bzr://`
- `hg://`
- `osc://`

Other URIs were already using `protocol=`. This change improves consistency.

> 其他 URI 已经在使用 `protocol=`。这个改变提高了一致性。

## nativesdk

The suffix `nativesdk` is now implemented as a prefix, which simplifies a lot of the packaging code for `ref-classes-nativesdk`, and any references need to be updated to use `nativesdk-*` instead of `*-nativesdk`.

> 后缀“nativesdk”现在被实现为前缀，这大大简化了“ref-classes-nativesdk”的可重定位包)以及所有引用都需要更新为使用“nativesdk-*”而不是“*-nativesdk”。

## Task Recipes

\"Task\" recipes are now known as \"Package groups\" and have been renamed from `task-*.bb` to `packagegroup-*.bb`. Existing references to the previous `task-*` names should work in most cases as there is an automatic upgrade path for most packages. However, you should update references in your own recipes and configurations as they could be removed in future releases. You should also rename any custom `task-*` recipes to `packagegroup-*`, and change them to inherit `ref-classes-packagegroup` section for further details.

> "任务"配方现在被称为"软件包组"，并已从 `task-*.bb` 更名为 `packagegroup-*.bb`。大多数情况下，对以前的 `task-*` 名称的现有引用应该可以正常工作，因为大多数软件包都有自动升级路径。但是，您应该更新自己配方和配置中的引用，因为它们可能在未来的发布版本中被删除。您还应该将任何自定义的 `task-*` 配方重命名为 `packagegroup-*`，并将其更改为继承 `ref-classes-packagegroup` 部分。

## IMAGE_FEATURES

Image recipes that previously included `apps-console-core` in `IMAGE_FEATURES` features have been removed.

> 以前在 `IMAGE_FEATURES` 特性已被移除。

## Removed Recipes

The following recipes have been removed. For most of them, it is unlikely that you would have any references to them in your own `Metadata`. However, you should check your metadata against this list to be sure:

> 以下 recipes 已经被移除。对于大多数 recipes，您在自己的元数据中不太可能有任何参考。但是，您应该检查自己的元数据与此列表是否一致：

- `libx11-trim`: Replaced by `libx11`, which has a negligible size difference with modern Xorg.
- `xserver-xorg-lite`: Use `xserver-xorg`, which has a negligible size difference when DRI and GLX modules are not installed.

> 使用 `xserver-xorg`，当 DRI 和 GLX 模块未安装时，大小几乎没有差别。

- `xserver-kdrive`: Effectively unmaintained for many years.
- `mesa-xlib`: No longer serves any purpose.
- `galago`: Replaced by telepathy.
- `gail`: Functionality was integrated into GTK+ 2.13.
- `eggdbus`: No longer needed.
- `gcc-*-intermediate`: The build has been restructured to avoid the need for this step.
- `libgsmd`: Unmaintained for many years. Functionality now provided by `ofono` instead.
- *contacts, dates, tasks, eds-tools*: Largely unmaintained PIM application suite. It has been moved to `meta-gnome` in `meta-openembedded`.

> -*联系人、日期、任务和 EDS-Tools*：大部分未维护的 PIM 应用程序套件。它已经移动到 Meta-GNOME 中的 Meta-OpenEmbedded 中。

In addition to the previously listed changes, the `meta-demoapps` directory has also been removed because the recipes in it were not being maintained and many had become obsolete or broken. Additionally, these recipes were not parsed in the default configuration. Many of these recipes are already provided in an updated and maintained form within the OpenEmbedded community layers such as `meta-oe` and `meta-gnome`. For the remainder, you can now find them in the `meta-extras` repository, which is in the :yocto_[git:%60Source](git:%60Source) Repositories \<\>[ at :yocto_git:]/meta-extras/\`.

> 此外，由于 `meta-demoapps` 目录中的菜谱未经维护，且许多已经过时或已损坏，因此已经删除了该目录。此外，这些菜谱在默认配置中也未被解析。许多这些菜谱现在已经在 OpenEmbedded 社区层(如 `meta-oe` 和 `meta-gnome`)中以更新和维护的形式提供。其余的，您现在可以在 `meta-extras` 存储库中找到它们，该存储库位于：Yocto [git:`Source`](git:%60Source%60)存储库\<\>[在 Yocto git:]/meta-extras/\`中。

# Linux Kernel Naming

The naming scheme for kernel output binaries has been changed to now include `PE` as part of the filename:

> 内核输出二进制文件的命名方案已经更改，现在文件名中包括 `PE` 作为部分。

```
KERNEL_IMAGE_BASE_NAME ?= "$"
```

Because the `PE` variable is not set by default, these binary files could result with names that include two dash characters. Here is an example:

> 因为 PE 变量默认没有设置，这些二进制文件的名字可能会包含两个破折号。下面是一个例子：

```
bzImage--3.10.9+git0+cd502a8814_7144bcc4b8-r0-qemux86-64-20130830085431.bin
```
