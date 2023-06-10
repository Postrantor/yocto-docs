---
tip: translate by openai@2023-06-07 20:49:04
...
---
title: Release 1.4 (dylan)
--------------------------

This section provides migration information for moving to the Yocto Project 1.4 Release (codename \"dylan\") from the prior release.

> 本节提供从前一个版本迁移到 Yocto Project 1.4 发行版（代号“dylan”）的迁移信息。

# BitBake {#migration-1.4-bitbake}

Differences include the following:

> 以下是不同之处：

- *Comment Continuation:* If a comment ends with a line continuation (\\) character, then the next line must also be a comment. Any instance where this is not the case, now triggers a warning. You must either remove the continuation character, or be sure the next line is a comment.

> - *注释续行：*如果注释以行续行符（\）结尾，则下一行也必须是注释。任何情况下，如果不是这种情况，现在都会发出警告。您必须删除续行符，或确保下一行是注释。

- *Package Name Overrides:* The runtime package specific variables `RDEPENDS`{.interpreted-text role="term"}, `RRECOMMENDS`{.interpreted-text role="term"}, `RSUGGESTS`{.interpreted-text role="term"}, `RPROVIDES`{.interpreted-text role="term"}, `RCONFLICTS`{.interpreted-text role="term"}, `RREPLACES`{.interpreted-text role="term"}, `FILES`{.interpreted-text role="term"}, `ALLOW_EMPTY`{.interpreted-text role="term"}, and the pre, post, install, and uninstall script functions `pkg_preinst`, `pkg_postinst`, `pkg_prerm`, and `pkg_postrm` should always have a package name override. For example, use `RDEPENDS_${PN}` for the main package instead of `RDEPENDS`{.interpreted-text role="term"}. BitBake uses more strict checks when it parses recipes.

> *包名覆盖：运行时包特定变量 `RDEPENDS`{.interpreted-text role="term"}、`RRECOMMENDS`{.interpreted-text role="term"}、`RSUGGESTS`{.interpreted-text role="term"}、`RPROVIDES`{.interpreted-text role="term"}、`RCONFLICTS`{.interpreted-text role="term"}、`RREPLACES`{.interpreted-text role="term"}、`FILES`{.interpreted-text role="term"}、`ALLOW_EMPTY`{.interpreted-text role="term"}以及预、后安装和卸载脚本函数 `pkg_preinst`、`pkg_postinst`、`pkg_prerm` 和 `pkg_postrm` 应始终具有包名覆盖。例如，使用 `RDEPENDS_${PN}` 代替主包的 `RDEPENDS`{.interpreted-text role="term"}。BitBake 在解析配方时会使用更严格的检查。

# Build Behavior {#migration-1.4-build-behavior}

Differences include the following:

> 以下是不同之处：

- *Shared State Code:* The shared state code has been optimized to avoid running unnecessary tasks. For example, the following no longer populates the target sysroot since that is not necessary:

> 共享状态代码已经优化，以避免运行不必要的任务。例如，以下内容不再填充目标 sysroot，因为这是不必要的：

```
$ bitbake -c rootfs some-image
```

Instead, the system just needs to extract the output package contents, re-create the packages, and construct the root filesystem. This change is unlikely to cause any problems unless you have missing declared dependencies.

> 系统只需要提取输出包的内容，重新创建包并组建根文件系统。除非您有缺少的声明依赖关系，否则此更改不太可能造成任何问题。

- *Scanning Directory Names:* When scanning for files in `SRC_URI`{.interpreted-text role="term"}, the build system now uses `FILESOVERRIDES`{.interpreted-text role="term"} instead of `OVERRIDES`{.interpreted-text role="term"} for the directory names. In general, the values previously in `OVERRIDES`{.interpreted-text role="term"} are now in `FILESOVERRIDES`{.interpreted-text role="term"} as well. However, if you relied upon an additional value you previously added to `OVERRIDES`{.interpreted-text role="term"}, you might now need to add it to `FILESOVERRIDES`{.interpreted-text role="term"} unless you are already adding it through the `MACHINEOVERRIDES`{.interpreted-text role="term"} or `DISTROOVERRIDES`{.interpreted-text role="term"} variables, as appropriate. For more related changes, see the \"`migration-guides/migration-1.4:variables`{.interpreted-text role="ref"}\" section.

> 扫描目录名称：在 SRC_URI 中搜索文件时，构建系统现在使用 FILESOVERRIDES 而不是 OVERRIDES 来指定目录名称。通常，原先在 OVERRIDES 中的值也会出现在 FILESOVERRIDES 中。但如果您依赖于原先在 OVERRIDES 中添加的其他值，除非您已经通过 MACHINEOVERRIDES 或 DISTROOVERRIDES 变量添加，否则您可能现在需要将其添加到 FILESOVERRIDES 中。有关更多相关更改，请参见“migration-guides/migration-1.4:variables”部分。

# Proxies and Fetching Source {#migration-1.4-proxies-and-fetching-source}

A new `oe-git-proxy` script has been added to replace previous methods of handling proxies and fetching source from Git. See the `meta-yocto/conf/site.conf.sample` file for information on how to use this script.

> 一个新的 `oe-git-proxy` 脚本已经添加以取代以前处理代理和从 Git 获取源的方法。有关如何使用此脚本的信息，请参阅 `meta-yocto/conf/site.conf.sample` 文件。

# Custom Interfaces File (netbase change) {#migration-1.4-custom-interfaces-file-netbase-change}

If you have created your own custom `etc/network/interfaces` file by creating an append file for the `netbase` recipe, you now need to create an append file for the `init-ifupdown` recipe instead, which you can find in the `Source Directory`{.interpreted-text role="term"} at `meta/recipes-core/init-ifupdown`. For information on how to use append files, see the \"`dev-manual/layers:appending other layers metadata with your layer`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 如果您已经为 `netbase` 食谱创建了自己的自定义 `etc/network/interfaces` 文件，您现在需要为 `init-ifupdown` 食谱创建一个附加文件，您可以在 `源目录` 中找到 `meta/recipes-core/init-ifupdown`。有关如何使用附加文件的信息，请参阅 Yocto Project 开发任务手册中的“使用您的层附加其他层元数据 `dev-manual/layers:appending other layers metadata with your layer`”部分。

# Remote Debugging {#migration-1.4-remote-debugging}

Support for remote debugging with the Eclipse IDE is now separated into an image feature (`eclipse-debug`) that corresponds to the `packagegroup-core-eclipse-debug` package group. Previously, the debugging feature was included through the `tools-debug` image feature, which corresponds to the `packagegroup-core-tools-debug` package group.

> 支持使用 Eclipse IDE 进行远程调试现在已经分离到一个图像特性（`eclipse-debug`）中，对应于 `packagegroup-core-eclipse-debug` 软件包组。以前，调试特性是通过 `tools-debug` 图像特性包含的，对应于 `packagegroup-core-tools-debug` 软件包组。

# Variables {#migration-1.4-variables}

The following variables have changed:

> 以下变量已经改变了：

- `SANITY_TESTED_DISTROS`{.interpreted-text role="term"}: This variable now uses a distribution ID, which is composed of the host distributor ID followed by the release. Previously, `SANITY_TESTED_DISTROS`{.interpreted-text role="term"} was composed of the description field. For example, \"Ubuntu 12.10\" becomes \"Ubuntu-12.10\". You do not need to worry about this change if you are not specifically setting this variable, or if you are specifically setting it to \"\".

> `- SANITY_TESTED_DISTROS`：这个变量现在使用发行版 ID，由主机发行商 ID 加上发行版构成。以前，`SANITY_TESTED_DISTROS`{.interpreted-text role="term"} 由描述字段组成。例如，“Ubuntu 12.10”变成“Ubuntu-12.10”。如果您没有特别设置这个变量，或者您特别将其设置为“”，则您不需要担心这个更改。

- `SRC_URI`{.interpreted-text role="term"}: The `${``PN`{.interpreted-text role="term"}`}`, `${``PF`{.interpreted-text role="term"}`}`, `${``P`{.interpreted-text role="term"}`}`, and `FILE_DIRNAME` directories have been dropped from the default value of the `FILESPATH`{.interpreted-text role="term"} variable, which is used as the search path for finding files referred to in `SRC_URI`{.interpreted-text role="term"}. If you have a recipe that relied upon these directories, which would be unusual, then you will need to add the appropriate paths within the recipe or, alternatively, rearrange the files. The most common locations are still covered by `${``BP`{.interpreted-text role="term"}`}`, `${``BPN`{.interpreted-text role="term"}`}`, and \"files\", which all remain in the default value of `FILESPATH`{.interpreted-text role="term"}.

> SRC_URI：默认的 FILESPATH 变量值中已经删除了${PN}、${PF}、${P}和FILE_DIRNAME目录，该变量用于搜索SRC_URI中提到的文件。如果您的配方依赖这些目录（这是不常见的），那么您将需要在配方中添加适当的路径，或者改变文件的位置。最常见的位置仍然由${BP}、${BPN}和“files”涵盖，它们仍然保留在 FILESPATH 的默认值中。

# Target Package Management with RPM {#migration-target-package-management-with-rpm}

If runtime package management is enabled and the RPM backend is selected, Smart is now installed for package download, dependency resolution, and upgrades instead of Zypper. For more information on how to use Smart, run the following command on the target:

> 如果启用了运行时包管理，并且选择了 RPM 后端，Smart 现在取代了 Zypper 用于包下载、依赖关系解析和升级。要了解有关如何使用 Smart 的更多信息，请在目标上运行以下命令：

```
smart --help
```

# Recipes Moved {#migration-1.4-recipes-moved}

The following recipes were moved from their previous locations because they are no longer used by anything in the OpenEmbedded-Core:

> 以下食谱已从其先前位置移走，因为 OpenEmbedded-Core 中不再使用它们：

- `clutter-box2d`: Now resides in the `meta-oe` layer.
- `evolution-data-server`: Now resides in the `meta-gnome` layer.
- `gthumb`: Now resides in the `meta-gnome` layer.
- `gtkhtml2`: Now resides in the `meta-oe` layer.
- `gupnp`: Now resides in the `meta-multimedia` layer.
- `gypsy`: Now resides in the `meta-oe` layer.
- `libcanberra`: Now resides in the `meta-gnome` layer.
- `libgdata`: Now resides in the `meta-gnome` layer.
- `libmusicbrainz`: Now resides in the `meta-multimedia` layer.
- `metacity`: Now resides in the `meta-gnome` layer.
- `polkit`: Now resides in the `meta-oe` layer.
- `zeroconf`: Now resides in the `meta-networking` layer.

# Removals and Renames {#migration-1.4-removals-and-renames}

The following list shows what has been removed or renamed:

> 以下列表显示已经被移除或重命名的内容：

- `evieext`: Removed because it has been removed from `xserver` since 2008.
- *Gtk+ DirectFB:* Removed support because upstream Gtk+ no longer supports it as of version 2.18.
- `libxfontcache / xfontcacheproto`: Removed because they were removed from the Xorg server in 2008.

> - libxfontcache / xfontcacheproto：2008 年被 Xorg 服务器移除，因此被移除。

- `libxp / libxprintapputil / libxprintutil / printproto`: Removed because the XPrint server was removed from Xorg in 2008.

> - libxp / libxprintapputil / libxprintutil / printproto：由于 Xorg 在 2008 年删除了 XPrint 服务器，因此已经删除。

- `libxtrap / xtrapproto`: Removed because their functionality was broken upstream.
- *linux-yocto 3.0 kernel:* Removed with linux-yocto 3.8 kernel being added. The linux-yocto 3.2 and linux-yocto 3.4 kernels remain as part of the release.

> 在添加 linux-yocto 3.8 内核的同时，已经移除了 linux-yocto 3.0 内核。linux-yocto 3.2 和 linux-yocto 3.4 内核仍然是发行版的一部分。

- `lsbsetup`: Removed with functionality now provided by `lsbtest`.
- `matchbox-stroke`: Removed because it was never more than a proof-of-concept.
- `matchbox-wm-2 / matchbox-theme-sato-2`: Removed because they are not maintained. However, `matchbox-wm` and `matchbox-theme-sato` are still provided.

> - matchbox-wm-2 / matchbox-theme-sato-2：由于不再维护，已经移除。但是仍然提供 `matchbox-wm` 和 `matchbox-theme-sato`。

- `mesa-dri`: Renamed to `mesa`.
- `mesa-xlib`: Removed because it was no longer useful.
- `mutter`: Removed because nothing ever uses it and the recipe is very old.
- `orinoco-conf`: Removed because it has become obsolete.
- `update-modules`: Removed because it is no longer used. The kernel module `postinstall` and `postrm` scripts can now do the same task without the use of this script.

> - `update-modules`：因不再使用而被移除。内核模块的 `postinstall` 和 `postrm` 脚本现在可以在不使用此脚本的情况下完成相同的任务。

- `web`: Removed because it is not maintained. Superseded by `web-webkit`.
- `xf86bigfontproto`: Removed because upstream it has been disabled by default since 2007. Nothing uses `xf86bigfontproto`.

> - `xf86bigfontproto`: 自 2007 年以来，上游默认情况下已经禁用，因此已经被移除。没有任何东西使用 `xf86bigfontproto`。

- `xf86rushproto`: Removed because its dependency in `xserver` was spurious and it was removed in 2005.

> - `xf86rushproto`：由于它在 `xserver` 中的依赖是虚假的，已于 2005 年被移除，因此已被移除。

- `zypper / libzypp / sat-solver`: Removed and been functionally replaced with Smart (`python-smartpm`) when RPM packaging is used and package management is enabled on the target.

> - zypper/libzypp/sat-solver：当使用 RPM 打包并在目标上启用包管理时，已经被 Smart（python-smartpm）替换并功能性删除。
