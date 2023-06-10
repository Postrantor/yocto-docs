---
tip: translate by openai@2023-06-07 21:10:04
...
---
title: Release 1.8 (fido)
-------------------------

This section provides migration information for moving to the Yocto Project 1.8 Release (codename \"fido\") from the prior release.

> 本节提供从前一个版本迁移到 Yocto 项目 1.8 发行版（代号“fido”）的迁移信息。

# Removed Recipes {#migration-1.8-removed-recipes}

The following recipes have been removed:

> 以下食谱已被移除：

- `owl-video`: Functionality replaced by `gst-player`.
- `gaku`: Functionality replaced by `gst-player`.
- `gnome-desktop`: This recipe is now available in `meta-gnome` and is no longer needed.
- `gsettings-desktop-schemas`: This recipe is now available in `meta-gnome` and is no longer needed.

> - `gsettings-desktop-schemas`：这个食谱现在可以在 `meta-gnome` 中找到，不再需要。

- `python-argparse`: The `argparse` module is already provided in the default Python distribution in a package named `python-argparse`. Consequently, the separate `python-argparse` recipe is no longer needed.

> `argparse` 模块已经在默认的 Python 发行版中以 `python-argparse` 名称提供了。因此，单独的 `python-argparse` 配方不再需要。

- `telepathy-python, libtelepathy, telepathy-glib, telepathy-idle, telepathy-mission-control`: All these recipes have moved to `meta-oe` and are consequently no longer needed by any recipes in OpenEmbedded-Core.

> - `telepathy-python、libtelepathy、telepathy-glib、telepathy-idle、telepathy-mission-control`：这些配方都已经移动到 `meta-oe`，因此在 OpenEmbedded-Core 中不再需要。

- `linux-yocto_3.10` and `linux-yocto_3.17`: Support for the linux-yocto 3.10 and 3.17 kernels has been dropped. Support for the 3.14 kernel remains, while support for 3.19 kernel has been added.

> 支持 linux-yocto 3.10 和 3.17 内核的支持已经被取消。仍然支持 3.14 内核，而支持 3.19 内核已经被添加。

- `poky-feed-config-opkg`: This recipe has become obsolete and is no longer needed. Use `distro-feed-config` from `meta-oe` instead.

> - `poky-feed-config-opkg`：这个配方已经过时，不再需要。请使用 `meta-oe` 中的 `distro-feed-config` 代替。

- `libav 0.8.x`: `libav 9.x` is now used.
- `sed-native`: No longer needed. A working version of `sed` is expected to be provided by the host distribution.

> 不再需要。预计主分发将提供一个可用的 `sed` 版本。

# BlueZ 4.x / 5.x Selection {#migration-1.8-bluez}

Proper built-in support for selecting BlueZ 5.x in preference to the default of 4.x now exists. To use BlueZ 5.x, simply add \"bluez5\" to your `DISTRO_FEATURES`{.interpreted-text role="term"} value. If you had previously added append files (`*.bbappend`) to make this selection, you can now remove them.

> 正确的内置支持可以优先选择 BlueZ 5.x 而不是默认的 4.x。要使用 BlueZ 5.x，只需将“bluez5”添加到您的“DISTRO_FEATURES”值中。如果您之前添加了附加文件（“*.bbappend”）来进行此选择，现在可以将其删除。

Additionally, a `bluetooth` class has been added to make selection of the appropriate bluetooth support within a recipe a little easier. If you wish to make use of this class in a recipe, add something such as the following:

> 此外，添加了一个 `蓝牙` 类，以使在食谱中更容易选择适当的蓝牙支持。如果您希望在食谱中使用此类，请添加以下内容之一：

```
inherit bluetooth
PACKAGECONFIG ??= "${@bb.utils.contains('DISTRO_FEATURES', 'bluetooth', '${BLUEZ}', '', d)}"
PACKAGECONFIG[bluez4] = "--enable-bluetooth,--disable-bluetooth,bluez4"
PACKAGECONFIG[bluez5] = "--enable-bluez5,--disable-bluez5,bluez5"
```

# Kernel Build Changes {#migration-1.8-kernel-build-changes}

The kernel build process was changed to place the source in a common shared work area and to place build artifacts separately in the source code tree. In theory, migration paths have been provided for most common usages in kernel recipes but this might not work in all cases. In particular, users need to ensure that `${S}` (source files) and `${B}` (build artifacts) are used correctly in functions such as `ref-tasks-configure`{.interpreted-text role="ref"} and `ref-tasks-install`{.interpreted-text role="ref"}. For kernel recipes that do not inherit from `ref-classes-kernel-yocto`{.interpreted-text role="ref"} or include `linux-yocto.inc`, you might wish to refer to the `linux.inc` file in the `meta-oe` layer for the kinds of changes you need to make. For reference, here is the :oe\_[git:%60commit](git:%60commit) \</meta-openembedded/commit/meta-oe/recipes-kernel/linux/linux.inc?id=fc7132ede27ac67669448d3d2845ce7d46c6a1ee\>[ where the ]{.title-ref}[linux.inc]{.title-ref}[ file in ]{.title-ref}[meta-oe]{.title-ref}\` was updated.

> 进行内核构建的过程已被改变，以将源码放置在一个共同的共享工作区中，并将构建的文件单独放置在源代码树中。理论上，内核食谱中已提供了大多数常见用法的迁移路径，但这在所有情况下都不一定有效。特别是，用户需要确保正确使用 `${S}`（源文件）和 `${B}`（构建文件）在函数中，如 `ref-tasks-configure`{.interpreted-text role="ref"}和 `ref-tasks-install`{.interpreted-text role="ref"}。对于不继承自 `ref-classes-kernel-yocto`{.interpreted-text role="ref"}或不包含 `linux-yocto.inc` 的内核食谱，您可能希望参考 `meta-oe` 层中的 `linux.inc` 文件，了解需要做出的更改类型。作为参考，以下是：oe\_[git:%60commit](git:%60commit) \</meta-openembedded/commit/meta-oe/recipes-kernel/linux/linux.inc?id=fc7132ede27ac67669448d3d2845ce7d46c6a1ee\>[ 其中的 ]{.title-ref}[linux.inc]{.title-ref}[ 文件在 ]{.title-ref}[meta-oe]{.title-ref}中被更新。

Recipes that rely on the kernel source code and do not inherit the `module <ref-classes-module>`{.interpreted-text role="ref"} classes might need to add explicit dependencies on the `ref-tasks-shared_workdir`{.interpreted-text role="ref"} kernel task, for example:

> 若是不继承 `module <ref-classes-module>`{.interpreted-text role="ref"}类，而是依赖内核源代码的食谱，可能需要显式地添加对 `ref-tasks-shared_workdir`{.interpreted-text role="ref"}内核任务的依赖，例如：

```
do_configure[depends] += "virtual/kernel:do_shared_workdir"
```

# SSL 3.0 is Now Disabled in OpenSSL {#migration-1.8-ssl}

SSL 3.0 is now disabled when building OpenSSL. Disabling SSL 3.0 avoids any lingering instances of the POODLE vulnerability. If you feel you must re-enable SSL 3.0, then you can add an append file (`*.bbappend`) for the `openssl` recipe to remove \"-no-ssl3\" from `EXTRA_OECONF`{.interpreted-text role="term"}.

> SSL 3.0 在构建 OpenSSL 时已禁用。禁用 SSL 3.0 可避免 POODLE 漏洞的遗留实例。如果您觉得必须重新启用 SSL 3.0，那么可以为 `openssl` 配方添加一个附加文件（`*.bbappend`），从 `EXTRA_OECONF` 中删除 “-no-ssl3”。

# Default Sysroot Poisoning {#migration-1.8-default-sysroot-poisoning}

`gcc's` default sysroot and include directories are now \"poisoned\". In other words, the sysroot and include directories are being redirected to a non-existent location in order to catch when host directories are being used due to the correct options not being passed. This poisoning applies both to the cross-compiler used within the build and to the cross-compiler produced in the SDK.

> GCC 的默认系统根目录和包含目录现在被“抹除”了。换句话说，由于没有传递正确的选项，系统根目录和包含目录被重定向到一个不存在的位置，以捕获主机目录的使用。这种抹除既适用于构建中使用的跨编译器，也适用于 SDK 中生成的跨编译器。

If this change causes something in the build to fail, it almost certainly means the various compiler flags and commands are not being passed correctly to the underlying piece of software. In such cases, you need to take corrective steps.

> 如果这个变化导致构建失败，几乎可以肯定的是各种编译器标志和命令没有正确传递给底层软件。在这种情况下，你需要采取纠正措施。

# Rebuild Improvements {#migration-1.8-rebuild-improvements}

Changes have been made to the `ref-classes-base`{.interpreted-text role="ref"}, `ref-classes-autotools`{.interpreted-text role="ref"}, and `ref-classes-cmake`{.interpreted-text role="ref"} classes to clean out generated files when the `ref-tasks-configure`{.interpreted-text role="ref"} task needs to be re-executed.

> 已经对 `ref-classes-base`{.interpreted-text role="ref"}、`ref-classes-autotools`{.interpreted-text role="ref"}和 `ref-classes-cmake`{.interpreted-text role="ref"}类进行了修改，以便在需要重新执行 `ref-tasks-configure`{.interpreted-text role="ref"}任务时清除生成的文件。

One of the improvements is to attempt to run \"make clean\" during the `ref-tasks-configure`{.interpreted-text role="ref"} task if a `Makefile` exists. Some software packages do not provide a working clean target within their make files. If you have such recipes, you need to set `CLEANBROKEN`{.interpreted-text role="term"} to \"1\" within the recipe, for example:

> 其中一个改进是在 `ref-tasks-configure`{.interpreted-text role="ref"}任务中尝试运行“make clean”，如果存在 `Makefile`。有些软件包的 Make 文件中没有提供可用的清理目标。如果您有这样的食谱，则需要在食谱中将 `CLEANBROKEN`{.interpreted-text role="term"}设置为“1”，例如：

```
CLEANBROKEN = "1"
```

# QA Check and Validation Changes {#migration-1.8-qa-check-and-validation-changes}

The following QA Check and Validation Changes have occurred:

> 以下 QA 检查和验证变更已发生：

- Usage of `PRINC` previously triggered a warning. It now triggers an error. You should remove any remaining usage of `PRINC` in any recipe or append file.

> 使用 `PRINC` 之前会引发警告。现在会引发错误。您应该删除任何剩余的 `PRINC` 用法，无论是食谱还是附加文件。

- An additional QA check has been added to detect usage of `${D}` in `FILES`{.interpreted-text role="term"} values where `D`{.interpreted-text role="term"} values should not be used at all. The same check ensures that `$D` is used in `pkg_preinst/pkg_postinst/pkg_prerm/pkg_postrm` functions instead of `${D}`.

> 针对 `FILES`{.interpreted-text role="term"}中不应使用 `D`{.interpreted-text role="term"}值的情况，新增了一项 QA 检查。同样的检查也确保在 `pkg_preinst/pkg_postinst/pkg_prerm/pkg_postrm` 函数中使用 `$D` 而不是 `${D}`。

- `S`{.interpreted-text role="term"} now needs to be set to a valid value within a recipe. If `S`{.interpreted-text role="term"} is not set in the recipe, the directory is not automatically created. If `S`{.interpreted-text role="term"} does not point to a directory that exists at the time the `ref-tasks-unpack`{.interpreted-text role="ref"} task finishes, a warning will be shown.

> 现在需要在配方中设置一个有效的值。如果在配方中没有设置 S，那么该目录不会自动创建。如果 S 指向的目录在 ref-tasks-unpack 任务完成时不存在，则会显示警告。

- `LICENSE`{.interpreted-text role="term"} is now validated for correct formatting of multiple licenses. If the format is invalid (e.g. multiple licenses are specified with no operators to specify how the multiple licenses interact), then a warning will be shown.

> `LICENSE` 已验证以正确格式化多个许可证。如果格式无效（例如，没有操作符指定多个许可证如何交互），则会显示警告。

# Miscellaneous Changes {#migration-1.8-miscellaneous-changes}

The following miscellaneous changes have occurred:

> 以下杂项变化已发生：

- The `send-error-report` script now expects a \"-s\" option to be specified before the server address. This assumes a server address is being specified.

> 脚本 `send-error-report` 现在需要在指定服务器地址之前指定一个\"-s\"选项。这假设指定了服务器地址。

- The `oe-pkgdata-util` script now expects a \"-p\" option to be specified before the `pkgdata` directory, which is now optional. If the `pkgdata` directory is not specified, the script will run BitBake to query `PKGDATA_DIR`{.interpreted-text role="term"} from the build environment.

> 脚本 `oe-pkgdata-util` 现在需要在可选的 `pkgdata` 目录之前指定一个“-p”选项。如果没有指定 `pkgdata` 目录，脚本将运行 BitBake 来查询来自构建环境的 `PKGDATA_DIR`。
