---
tip: translate by openai@2023-06-07 21:48:20
...
---
title: Release 2.5 (sumo)
-------------------------

This section provides migration information for moving to the Yocto Project 2.5 Release (codename \"sumo\") from the prior release.

> 此部分提供从先前发行版本迁移到 Yocto 项目 2.5 发行版（代号“sumo”）的迁移信息。

# Packaging Changes {#migration-2.5-packaging-changes}

This section provides information about packaging changes that have occurred:

> 这一节提供有关已发生的包装更改的信息：

- `bind-libs`: The libraries packaged by the bind recipe are in a separate `bind-libs` package.
- `libfm-gtk`: The `libfm` GTK+ bindings are split into a separate `libfm-gtk` package.
- `flex-libfl`: The flex recipe splits out libfl into a separate `flex-libfl` package to avoid too many dependencies being pulled in where only the library is needed.

> flex-libfl：为了避免在只需要库的情况下引入过多的依赖，flex 食谱将 libfl 拆分成单独的 `flex-libfl` 包。

- `grub-efi`: The `grub-efi` configuration is split into a separate `grub-bootconf` recipe. However, the dependency relationship from `grub-efi` is through a virtual/grub-bootconf provider making it possible to have your own recipe provide the dependency. Alternatively, you can use a BitBake append file to bring the configuration back into the `grub-efi` recipe.

> GRUB-EFI：GRUB-EFI 的配置被分割为单独的 grub-bootconf 配方。但是，grub-efi 之间的依赖关系是通过一个虚拟/grub-bootconf 提供者实现的，这使得您可以使用自己的配方提供依赖关系。或者，您可以使用 BitBake 附加文件将配置恢复到 grub-efi 配方中。

- *armv7a Legacy Package Feed Support:* Legacy support is removed for transitioning from `armv7a` to `armv7a-vfp-neon` in package feeds, which was previously enabled by setting `PKGARCHCOMPAT_ARMV7A`. This transition occurred in 2011 and active package feeds should by now be updated to the new naming.

> 支持包发布源的 armv7a 遗留支持：为了从 armv7a 过渡到 armv7a-vfp-neon，遗留支持已经从包发布源中移除，以前可以通过设置 PKGARCHCOMPAT_ARMV7A 来启用。这个过渡发生在 2011 年，现在的活跃包发布源应该更新到新的命名。

# Removed Recipes {#migration-2.5-removed-recipes}

The following recipes have been removed:

> 以下食谱已被移除：

- `gcc`: The version 6.4 recipes are replaced by 7.x.
- `gst-player`: Renamed to `gst-examples` as per upstream.
- `hostap-utils`: This software package is obsolete.
- `latencytop`: This recipe is no longer maintained upstream. The last release was in 2009.
- `libpfm4`: The only file that requires this recipe is `oprofile`, which has been removed.
- `linux-yocto`: The version 4.4, 4.9, and 4.10 recipes have been removed. Versions 4.12, 4.14, and 4.15 remain.

> Linux-Yocto：已移除版本 4.4、4.9 和 4.10 的食谱。保留版本 4.12、4.14 和 4.15。

- `man`: This recipe has been replaced by modern `man-db`
- `mkelfimage`: This tool has been removed in the upstream coreboot project, and is no longer needed with the removal of the ELF image type.

> `mkelfimage`：在上游 coreboot 项目中已经移除，随着 ELF 图像类型的移除，不再需要这个工具了。

- `nativesdk-postinst-intercept`: This recipe is not maintained.
- `neon`: This software package is no longer maintained upstream and is no longer needed by anything in OpenEmbedded-Core.

> 这个软件包不再受到上游的维护，也不再被 OpenEmbedded-Core 中的任何内容所需要。

- `oprofile`: The functionality of this recipe is replaced by `perf` and keeping compatibility on an ongoing basis with `musl` is difficult.

> `oprofile`：由于与 `musl` 的兼容性难以维持，此配方的功能已被 `perf` 取代。

- `pax`: This software package is obsolete.
- `stat`: This software package is not maintained upstream. `coreutils` provides a modern stat binary.

> `stat`：此软件包没有得到上游的维护。 `coreutils` 提供了一个现代的 stat 二进制文件。

- `zisofs-tools-native`: This recipe is no longer needed because the compressed ISO image feature has been removed.

> 这个食谱不再需要了，因为压缩 ISO 映像功能已经被移除。

# Scripts and Tools Changes {#migration-2.5-scripts-and-tools-changes}

The following are changes to scripts and tools:

> 以下是对脚本和工具的更改：

- `yocto-bsp`, `yocto-kernel`, and `yocto-layer`: The `yocto-bsp`, `yocto-kernel`, and `yocto-layer` scripts previously shipped with poky but not in OpenEmbedded-Core have been removed. These scripts are not maintained and are outdated. In many cases, they are also limited in scope. The `bitbake-layers create-layer` command is a direct replacement for `yocto-layer`. See the documentation to create a BSP or kernel recipe in the \"`bsp-guide/bsp:bsp kernel recipe example`{.interpreted-text role="ref"}\" section.

> `yocto-bsp`、`yocto-kernel` 和 `yocto-layer` 脚本已从 poky 中移除，而不在 OpenEmbedded-Core 中。这些脚本没有维护，已经过时。在许多情况下，它们的范围也有限。 `bitbake-layers create-layer` 命令是 `yocto-layer` 的直接替代品。请参阅文档，在“bsp-guide/bsp：bsp 内核配方示例”部分中创建 BSP 或内核配方。

- `devtool finish`: `devtool finish` now exits with an error if there are uncommitted changes or a rebase/am in progress in the recipe\'s source repository. If this error occurs, there might be uncommitted changes that will not be included in updates to the patches applied by the recipe. A -f/\--force option is provided for situations that the uncommitted changes are inconsequential and you want to proceed regardless.

> devtool finish 现在会在菜谱源代码仓库中存在未提交的更改或正在进行 rebase/am 时退出并显示错误。如果发生此错误，可能存在未提交的更改，这些更改将不包括在对所应用补丁的更新中。提供了一个-f/--force 选项，用于未提交的更改不重要的情况下，您希望继续执行。

- `scripts/oe-setup-rpmrepo` script: The functionality of `scripts/oe-setup-rpmrepo` is replaced by `bitbake package-index`.

> `scripts/oe-setup-rpmrepo` 脚本：`scripts/oe-setup-rpmrepo` 的功能已由 `bitbake package-index` 替代。

- `scripts/test-dependencies.sh` script: The script is largely made obsolete by the recipe-specific sysroots functionality introduced in the previous release.

> 脚本在上一次发布中引入的针对食谱特定的系统根功能中大部分已经过时。

# BitBake Changes {#migration-2.5-bitbake-changes}

The following are BitBake changes:

> 以下是 BitBake 的更改：

- The `--runall` option has changed. There are two different behaviors people might want:

  - *Behavior A:* For a given target (or set of targets) look through the task graph and run task X only if it is present and will be built.

> - 行为 A：针对给定的目标（或一组目标），浏览任务图，只有当它存在并将被构建时，才运行任务 X。

- *Behavior B:* For a given target (or set of targets) look through the task graph and run task X if any recipe in the taskgraph has such a target, even if it is not in the original task graph.

> - 行为 B：对于给定的目标（或一组目标），浏览任务图，如果任务图中的任何配方都有这样的目标，即使它不在原始任务图中，也要运行任务 X。

The `--runall` option now performs \"Behavior B\". Previously `--runall` behaved like \"Behavior A\". A `--runonly` option has been added to retain the ability to perform \"Behavior A\".

> 现在，`--runall` 选项执行 "Behavior B"。之前，`--runall` 的行为类似 "Behavior A"。为了保留执行 "Behavior A" 的能力，已添加了 `--runonly` 选项。

- Several explicit \"run this task for all recipes in the dependency tree\" tasks have been removed (e.g. `fetchall`, `checkuriall`, and the `*all` tasks provided by the `distrodata` and `ref-classes-archiver`{.interpreted-text role="ref"} classes). There is a BitBake option to complete this for any arbitrary task. For example:

> 多个明确的“为依赖树中的所有配方运行此任务”任务已被移除（例如，`fetchall`，`checkuriall` 以及 `distrodata` 和 `ref-classes-archiver`{.interpreted-text role="ref"}类提供的 `*all` 任务）。BitBake 有一个选项可以完成任何任务。例如：

```
bitbake <target> -c fetchall
```

should now be replaced with:

> 应该现在被替换为：简体中文

```
bitbake <target> --runall=fetch
```

# Python and Python 3 Changes {#migration-2.5-python-and-python3-changes}

The following are auto-packaging changes to Python and Python 3:

> 以下是 Python 和 Python 3 的自动打包变更：

The script-managed `python-*-manifest.inc` files that were previously used to generate Python and Python 3 packages have been replaced with a JSON-based file that is easier to read and maintain. A new task is available for maintainers of the Python recipes to update the JSON file when upgrading to new Python versions. You can now edit the file directly instead of having to edit a script and run it to update the file.

> 以前用于生成 Python 和 Python 3 软件包的脚本管理的 `python-*-manifest.inc` 文件已经被更易于阅读和维护的基于 JSON 的文件所取代。当升级到新的 Python 版本时，Python 配方的维护者现在可以使用一个新的任务来更新 JSON 文件。您现在可以直接编辑文件，而不必编辑脚本并运行它来更新文件。

One particular change to note is that the Python recipes no longer have build-time provides for their packages. This assumes `python-foo` is one of the packages provided by the Python recipe. You can no longer run `bitbake python-foo` or have a `DEPENDS`{.interpreted-text role="term"} on `python-foo`, but doing either of the following causes the package to work as expected:

> 注意一个特殊的变化是 Python 食谱不再提供它们的软件包的构建时提供。假设 `python-foo` 是 Python 食谱提供的一个软件包。你不能再运行 `bitbake python-foo` 或者在 `DEPENDS` 上有一个 `python-foo`，但是以下任何一种方式都可以让软件包正常工作：

```
IMAGE_INSTALL_append = " python-foo"
```

or :

```
RDEPENDS_${PN} = "python-foo"
```

The earlier build-time provides behavior was a quirk of the way the Python manifest file was created. For more information on this change please see :yocto\_[git:%60this](git:%60this) commit \</poky/commit/?id=8d94b9db221d1def42f091b991903faa2d1651ce\>\`.

> 早期的构建时间提供行为是 Python 清单文件创建方式的一个特性。有关此更改的更多信息，请参阅：yocto_ [git:`这个`](git:%60%E8%BF%99%E4%B8%AA%60)提交 </poky/commit/?id=8d94b9db221d1def42f091b991903faa2d1651ce>。

# Miscellaneous Changes {#migration-2.5-miscellaneous-changes}

The following are additional changes:

> 以下是额外的更改：

- The `ref-classes-kernel`{.interpreted-text role="ref"} class supports building packages for multiple kernels. If your kernel recipe or `.bbappend` file mentions packaging at all, you should replace references to the kernel in package names with `${KERNEL_PACKAGE_NAME}`. For example, if you disable automatic installation of the kernel image using `RDEPENDS_kernel-base = ""` you can avoid warnings using `RDEPENDS_${KERNEL_PACKAGE_NAME}-base = ""` instead.

> ref-classes-kernel 类支持为多个内核构建软件包。如果您的内核配方或 `.bbappend` 文件提到了打包，则应将软件包名称中的内核引用替换为 `${KERNEL_PACKAGE_NAME}`。例如，如果使用 `RDEPENDS_kernel-base = ""` 禁用内核映像的自动安装，则可以使用 `RDEPENDS_${KERNEL_PACKAGE_NAME}-base = ""` 来避免警告。

- The `ref-classes-buildhistory`{.interpreted-text role="ref"} class commits changes to the repository by default so you no longer need to set `BUILDHISTORY_COMMIT = "1"`. If you want to disable commits you need to set `BUILDHISTORY_COMMIT = "0"` in your configuration.

> - 默认情况下，`ref-classes-buildhistory`{.interpreted-text role="ref"}类会将更改提交到存储库，因此您不再需要设置 `BUILDHISTORY_COMMIT = "1"`。 如果要禁用提交，则需要在配置中设置 `BUILDHISTORY_COMMIT = "0"`。

- The `beaglebone` reference machine has been renamed to `beaglebone-yocto`. The `beaglebone-yocto` BSP is a reference implementation using only mainline components available in OpenEmbedded-Core and `meta-yocto-bsp`, whereas Texas Instruments maintains a full-featured BSP in the `meta-ti` layer. This rename avoids the previous name clash that existed between the two BSPs.

> `Beaglebone` 参考机已更名为 `beaglebone-yocto`。 `Beaglebone-yocto` BSP 是一个参考实现，仅使用 OpenEmbedded-Core 和 `meta-yocto-bsp` 中可用的主线组件，而 Texas Instruments 在 `meta-ti` 层中维护一个功能齐全的 BSP。此重命名避免了两个 BSP 之间存在的先前名称冲突。

- The `ref-classes-update-alternatives`{.interpreted-text role="ref"} class no longer works with SysV `init` scripts because this usage has been problematic. Also, the `sysklogd` recipe no longer uses `update-alternatives` because it is incompatible with other implementations.

> - 由于这种用法存在问题，因此 `ref-classes-update-alternatives`{.interpreted-text role="ref"}类不再与 SysV `init` 脚本一起工作。此外，sysklogd 配方不再使用 update-alternatives，因为它与其他实现不兼容。

- By default, the `ref-classes-cmake`{.interpreted-text role="ref"} class uses `ninja` instead of `make` for building. This improves build performance. If a recipe is broken with `ninja`, then the recipe can set `OECMAKE_GENERATOR = "Unix Makefiles"` to change back to `make`.

> 默认情况下，`ref-classes-cmake`{.interpreted-text role="ref"} 类使用 `ninja` 而不是 `make` 进行构建，这会提高构建性能。如果使用 `ninja` 的菜谱出现问题，可以将 `OECMAKE_GENERATOR = "Unix Makefiles"` 设置为更改回 `make`。

- The previously deprecated `base_*` functions have been removed in favor of their replacements in `meta/lib/oe` and `bitbake/lib/bb`. These are typically used from recipes and classes. Any references to the old functions must be updated. The following table shows the removed functions and their replacements:

> 之前已弃用的 `base_*` 函数已被替换为 `meta/lib/oe` 和 `bitbake/lib/bb` 中的替代函数。这些通常用于配方和类。必须更新对旧函数的引用。以下表格显示了被移除的函数及其替代函数：

---

*Removed*                      *Replacement*

> *移除*                      *替换*

---

base_path_join()               oe.path.join()

> base_path_join()               oe.path.join()
> 结合路径：base_path_join()               oe.path.join()

base_path_relative()           oe.path.relative()

> base_path_relative()           oe.path.relative()
> 绝对路径相对()           oe.path.relative()

base_path_out()                oe.path.format_display()

> base_path_out()：基础路径输出()
> oe.path.format_display()：oe.path.格式化显示()

base_read_file()               oe.utils.read_file()

> base_read_file（）oe.utils.read_file（）

base_ifelse()                  oe.utils.ifelse()

> 基础 ifelse（）                  oe.utils.ifelse（）

base_conditional()             oe.utils.conditional()

> 基础条件函数()            oe.utils.conditional()

base_less_or_equal()           oe.utils.less_or_equal()

> base_less_or_equal() 或者 oe.utils.less_or_equal()

base_version_less_or_equal()   oe.utils.version_less_or_equal()

> base_version_less_or_equal() oe.utils.version_less_or_equal()：比较基础版本是否小于或等于 oe.utils.version_less_or_equal()

base_contains()                bb.utils.contains()

> bb.utils.contains()函数检查基础中是否包含某个元素。

base_both_contain()            oe.utils.both_contain()

> base_both_contain()和 oe.utils.both_contain()

base_prune_suffix()            oe.utils.prune_suffix()

> base_prune_suffix()函数：oe.utils.prune_suffix()

oe_filter()                    oe.utils.str_filter()

> oe_filter()                    oe.utils.str_filter()
> 筛选()                    oe.utils.str_filter()

oe_filter_out()                oe.utils.str_filter_out() (or use the \_remove operator)

> 使用 oe_filter_out()或 oe.utils.str_filter_out()（或使用_remove 操作符）过滤掉。

---

- Using `exit 1` to explicitly defer a postinstall script until first boot is now deprecated since it is not an obvious mechanism and can mask actual errors. If you want to explicitly defer a postinstall to first boot on the target rather than at `rootfs` creation time, use `pkg_postinst_ontarget()` or call `postinst_intercept delay_to_first_boot` from `pkg_postinst()`. Any failure of a `pkg_postinst()` script (including `exit 1`) will trigger a warning during `ref-tasks-rootfs`{.interpreted-text role="ref"}.

> 使用 `exit 1` 显式地推迟 `postinstall` 脚本直到第一次启动现在已经不被推荐，因为它不是一个明显的机制，可能会遮掩实际的错误。如果你想在目标上显式地推迟 `postinstall` 到第一次启动，而不是在 `rootfs` 创建时间，请使用 `pkg_postinst_ontarget()` 或从 `pkg_postinst()` 调用 `postinst_intercept delay_to_first_boot`。任何 `pkg_postinst()` 脚本的失败（包括 `exit 1`）都会在 `ref-tasks-rootfs`{.interpreted-text role="ref"}中触发警告。

For more information, see the \"`dev-manual/new-recipe:post-installation scripts`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 更多信息，请参阅 Yocto 项目开发任务手册中的“dev-manual/new-recipe：安装后脚本”部分。

- The `elf` image type has been removed. This image type was removed because the `mkelfimage` tool that was required to create it is no longer provided by coreboot upstream and required updating every time `binutils` updated.

> `elf` 图像类型已被删除。此图像类型被删除，是因为创建它所需的 `mkelfimage` 工具不再由 coreboot 上游提供，并且每次 `binutils` 更新时都需要更新。

- Support for .iso image compression (previously enabled through `COMPRESSISO = "1"`) has been removed. The userspace tools (`zisofs-tools`) are unmaintained and `squashfs` provides better performance and compression. In order to build a live image with squashfs+lz4 compression enabled you should now set `LIVE_ROOTFS_TYPE = "squashfs-lz4"` and ensure that `live` is in `IMAGE_FSTYPES`{.interpreted-text role="term"}.

> 支持.iso 图像压缩（以前通过“COMPRESSISO =”1“启用）已被删除。用户空间工具（“zisofs-tools”）未维护，而“squashfs”提供更好的性能和压缩。为了构建具有 squashfs + lz4 压缩的实时映像，现在应该设置“LIVE_ROOTFS_TYPE =”squashfs-lz4“，并确保”live“在”IMAGE_FSTYPES“中。

- Recipes with an unconditional dependency on `libpam` are only buildable with `pam` in `DISTRO_FEATURES`{.interpreted-text role="term"}. If the dependency is truly optional then it is recommended that the dependency be conditional upon `pam` being in `DISTRO_FEATURES`{.interpreted-text role="term"}.

> 如果对 `libpam` 的依赖是必须的，那么只有在 `DISTRO_FEATURES` 中包含 `pam` 时才能构建这些食谱。如果依赖是可选的，建议将依赖条件设置为 `DISTRO_FEATURES` 中包含 `pam`。

- For EFI-based machines, the bootloader (`grub-efi` by default) is installed into the image at /boot. Wic can be used to split the bootloader into separate boot and root filesystem partitions if necessary.

> 对于基于 EFI 的机器，默认的引导程序（grub-efi）安装在/boot 的镜像中。如果有必要，Wic 可以用来将引导程序分割成单独的引导和根文件系统分区。

- Patches whose context does not match exactly (i.e. where patch reports \"fuzz\" when applying) will generate a warning. For an example of this see :yocto\_[git:%60this](git:%60this) commit \</poky/commit/?id=cc97bc08125b63821ce3f616771830f77c456f57\>\`.

> 补丁的上下文不完全匹配（即在应用时报告“fuzz”）将生成警告。有关此示例，请参见：yocto_[git:`this`](git:%60this%60)提交 </poky/commit/?id=cc97bc08125b63821ce3f616771830f77c456f57>`.

- Layers are expected to set `LAYERSERIES_COMPAT_layername` to match the version(s) of OpenEmbedded-Core they are compatible with. This is specified as codenames using spaces to separate multiple values (e.g. \"rocko sumo\"). If a layer does not set `LAYERSERIES_COMPAT_layername`, a warning will is shown. If a layer sets a value that does not include the current version (\"sumo\" for the 2.5 release), then an error will be produced.

> 层期望设置“LAYERSERIES_COMPAT_layername”以与 OpenEmbedded-Core 的版本匹配。这是使用空格将多个值分隔开（例如“rocko sumo”）指定的代码名称。如果层未设置“LAYERSERIES_COMPAT_layername”，则会显示警告。如果层设置的值不包括当前版本（2.5 发行版的“sumo”），则会产生错误。

- The `TZ` environment variable is set to \"UTC\" within the build environment in order to fix reproducibility problems in some recipes.

> `环境变量` TZ `被设置为“UTC”，以便在构建环境中解决某些配方的可重现性问题。`
