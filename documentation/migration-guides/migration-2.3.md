---
tip: translate by openai@2023-06-07 21:32:45
...
---
title: Release 2.3 (pyro)
-------------------------

This section provides migration information for moving to the Yocto Project 2.3 Release (codename \"pyro\") from the prior release.

> 此部分提供了从以前的发行版本迁移到 Yocto 项目 2.3 发行版(代号“pyro”)的迁移信息。

# Recipe-specific Sysroots

The OpenEmbedded build system now uses one sysroot per recipe to resolve long-standing issues with configuration script auto-detection of undeclared dependencies. Consequently, you might find that some of your previously written custom recipes are missing declared dependencies, particularly those dependencies that are incidentally built earlier in a typical build process and thus are already likely to be present in the shared sysroot in previous releases.

> 现在，OpenEmbedded 构建系统为每个配方使用一个系统根来解决长期存在的配置脚本未声明依赖关系自动检测问题。因此，您可能发现您之前编写的自定义配方缺少声明的依赖关系，特别是那些在典型构建过程中早期已经可能存在于共享系统根中的依赖关系，这在以前的发行版中是如此。

Consider the following:

> 考虑以下：

- *Declare Build-Time Dependencies:* Because of this new feature, you must explicitly declare all build-time dependencies for your recipe. If you do not declare these dependencies, they are not populated into the sysroot for the recipe.

> - *声明构建时依赖关系：* 由于这个新功能，您必须明确声明配方的所有构建时依赖关系。如果您不声明这些依赖关系，它们不会被放入配方的 sysroot 中。

- *Specify Pre-Installation and Post-Installation Native Tool Dependencies:* You must specifically specify any special native tool dependencies of `pkg_preinst` and `pkg_postinst` scripts by using the `PACKAGE_WRITE_DEPS` task.

> 你必须使用 `PACKAGE_WRITE_DEPS` 变量特别指定 `pkg_preinst` 和 `pkg_postinst` 脚本的任何特殊本地工具依赖性。指定这些依赖性可确保在 `ref-tasks-rootfs` 任务期间在构建主机上运行这些脚本时，这些工具可用。

As an example, see the `dbus` recipe. You will see that this recipe has a `pkg_postinst` that calls `systemctl` if \"systemd\" is in `DISTRO_FEATURES`.

> 例如，参见 `dbus` 配方。您会看到此配方具有 `pkg_postinst`，如果\"systemd\"在 `DISTRO_FEATURES` 中。

- Examine Recipes that Use `SSTATEPOSTINSTFUNCS`: You need to examine any recipe that uses `SSTATEPOSTINSTFUNCS` and determine steps to take.

> 检查使用 `SSTATEPOSTINSTFUNCS` 的 recipes：您需要检查使用 `SSTATEPOSTINSTFUNCS` 的任何 recipes，并确定采取的步骤。

Functions added to `SSTATEPOSTINSTFUNCS` are still called as they were in previous Yocto Project releases. However, since a separate sysroot is now being populated for every recipe and if existing functions being called through `SSTATEPOSTINSTFUNCS` are doing relocation, then you will need to change these to use a post-installation script that is installed by a function added to `SYSROOT_PREPROCESS_FUNCS`.

> 函数添加到 `SSTATEPOSTINSTFUNCS` 仍然像以前的 Yocto 项目发布版本一样被称为。然而，由于为每个配方都填充了一个单独的 sysroot，如果现有的函数通过 `SSTATEPOSTINSTFUNCS` 被调用来进行重定位，那么您将需要更改这些函数以使用通过 `SYSROOT_PREPROCESS_FUNCS` 添加的函数安装的后期安装脚本。

For an example, see the `ref-classes-pixbufcache`.

> 例如，参见 `meta/classes/` 目录中的 `ref-classes-pixbufcache`。

::: note
::: title

Note

> 注意
> :::

The SSTATEPOSTINSTFUNCS variable itself is now deprecated in favor of the do_populate_sysroot\[postfuncs] task. Consequently, if you do still have any function or functions that need to be called after the sysroot component is created for a recipe, then you would be well advised to take steps to use a post installation script as described previously. Taking these steps prepares your code for when SSTATEPOSTINSTFUNCS is removed in a future Yocto Project release.

> SSTATEPOSTINSTFUNCS 变量本身已经被 do_populate_sysroot[postfuncs]任务取代。因此，如果您仍然有任何需要在为 recipes 创建 sysroot 组件后调用的函数，那么您最好采取措施使用先前描述的安装后脚本。采取这些措施可以为将来的 Yocto 项目发布中删除 SSTATEPOSTINSTFUNCS 做好准备。
> :::

- *Specify the Sysroot when Using Certain External Scripts:* Because the shared sysroot is now gone, the scripts `oe-find-native-sysroot` and `oe-run-native` have been changed such that you need to specify which recipe\'s `STAGING_DIR_NATIVE` is used.

> - 使用某些外部脚本时请指定 Sysroot：由于共享 sysroot 已经消失，`oe-find-native-sysroot` 和 `oe-run-native` 脚本已经更改，因此您需要指定使用哪个配方的 `STAGING_DIR_NATIVE`。

::: note
::: title
Note
:::

You can find more information on how recipe-specific sysroots work in the \"`ref-classes-staging`\" section.

> 你可以在“ref-classes-staging”部分找到更多有关特定 recipes 系统根如何工作的信息。
> :::

# `PATH` Variable

Within the environment used to run build tasks, the environment variable `PATH` is now sanitized such that the normal native binary paths (`/bin`, `/sbin`, `/usr/bin` and so forth) are removed and a directory containing symbolic links linking only to the binaries from the host mentioned in the `HOSTTOOLS` variables is added to `PATH`.

> 在用于运行构建任务的环境中，环境变量 `PATH` 现在已经进行了清理，以便移除正常的本机二进制路径(`/bin`、`/sbin`、`/usr/bin` 等)，并将一个包含仅链接到 `HOSTTOOLS` 变量中提到的主机二进制文件的符号链接的目录添加到 `PATH` 中。

Consequently, any native binaries provided by the host that you need to call needs to be in one of these two variables at the configuration level.

> 因此，您需要调用的主机提供的任何本机二进制文件都需要在配置级别的这两个变量中。

Alternatively, you can add a native recipe (i.e. `-native`) that provides the binary to the recipe\'s `DEPENDS` value.

> 另外，您可以添加一个本地配方(即 `-native`)，将二进制文件添加到配方的 `DEPENDS` 值中。

::: note
::: title
Note
:::

PATH is not sanitized in the same way within `devshell`. If it were, you would have difficulty running host tools for development and debugging within the shell.

> 在 devshell 中，PATH 的清理方式并不相同。如果是这样，您将很难在 shell 中运行用于开发和调试的主机工具。
> :::

# Changes to Scripts

The following changes to scripts took place:

> 以下腳本的更改已經發生了：

- `oe-find-native-sysroot`: The usage for the `oe-find-native-sysroot` script has changed to the following:

> :  `oe-find-native-sysroot` 脚本的用法已更改为以下内容：

```
$ . oe-find-native-sysroot recipe
```

You must now supply a recipe for recipe as part of the command. Prior to the Yocto Project 2.3 release, it was not necessary to provide the script with the command.

> 你现在必须提供一个配方作为命令的一部分。在 Yocto Project 2.3 发布之前，不需要为命令提供脚本。

- `oe-run-native`: The usage for the `oe-run-native` script has changed to the following:

  ```
  $ oe-run-native native_recipe tool
  ```

  You must supply the name of the native recipe and the tool you want to run as part of the command. Prior to the Yocto Project 2.3 release, it was not necessary to provide the native recipe with the command.

> 你必须提供本地配方的名称和你想要运行的工具作为命令的一部分。在 Yocto Project 2.3 发布之前，不需要在命令中提供本地配方。

- `cleanup-workdir`: The `cleanup-workdir` script has been removed because the script was found to be deleting files it should not have, which lead to broken build trees. Rather than trying to delete portions of `TMPDIR` and have it restored from shared state (sstate) on subsequent builds.

> `cleanup-workdir` 脚本已被移除，因为该脚本会误删除不该删除的文件，从而导致构建树破坏。为了避免试图删除 `TMPDIR`，并在后续构建中从共享状态(sstate)中恢复。

- `wipe-sysroot`: The `wipe-sysroot` script has been removed as it is no longer needed with recipe-specific sysroots.

> wipe-sysroot 脚本已经被移除，因为它不再需要与 recipes 特定的系统根。

# Changes to Functions

The previously deprecated `bb.data.getVar()`, `bb.data.setVar()`, and related functions have been removed in favor of `d.getVar()`, `d.setVar()`, and so forth.

> 以前被弃用的 `bb.data.getVar()`、`bb.data.setVar()` 及相关函数已被移除，取而代之的是 `d.getVar()`、`d.setVar()` 等等。

You need to fix any references to these old functions.

> 你需要修复对这些旧函数的任何引用。

# BitBake Changes

The following changes took place for BitBake:

> 以下变化发生在 BitBake 上：

- *BitBake\'s Graphical Dependency Explorer UI Replaced:* BitBake\'s graphical dependency explorer UI `depexp` was replaced by `taskexp` (\"Task Explorer\"), which provides a graphical way of exploring the `task-depends.dot` file. The data presented by Task Explorer is much more accurate than the data that was presented by `depexp`. Being able to visualize the data is an often requested feature as standard `*.dot` file viewers cannot usual cope with the size of the `task-depends.dot` file.

> BitBake 的图形依赖项浏览器界面已被替换：BitBake 的图形依赖项浏览器 UI `depexp` 已被 `taskexp`(“任务浏览器”)所取代，它提供了一种图形化的方式来探索 `task-depends.dot` 文件。任务浏览器提供的数据比 `depexp` 提供的数据更准确。能够将数据可视化是一个经常被要求的功能，因为标准的 `* .dot` 文件查看器通常无法处理 `task-depends.dot` 文件的大小。

- *BitBake \"-g\" Output Changes:* The `package-depends.dot` and `pn-depends.dot` files as previously generated using the `bitbake -g` command have been removed. A `recipe-depends.dot` file is now generated as a collapsed version of `task-depends.dot` instead.

> BitBake“-g”输出更改：以前使用“bitbake -g”命令生成的 `package-depends.dot` 和 `pn-depends.dot` 文件已被删除。现在生成一个 `recipe-depends.dot` 文件，它是 `task-depends.dot` 的压缩版本。

The reason for this change is because `package-depends.dot` and `pn-depends.dot` largely date back to a time before task-based execution and do not take into account task-level dependencies between recipes, which could be misleading.

> 这一变化的原因是因为 `package-depends.dot` 和 `pn-depends.dot` 大部分时间都是在任务执行之前，而且没有考虑到配方之间的任务级依赖关系，这可能会产生误导。

- *Mirror Variable Splitting Changes:* Mirror variables including `MIRRORS` can now separate values entirely with spaces. Consequently, you no longer need \"\\n\". BitBake looks for pairs of values, which simplifies usage. There should be no change required to existing mirror variable values themselves.

> - *镜像变量分割变更：* 镜像变量，包括 `MIRRORS`，现在可以完全使用空格分割值。因此，您不再需要"\\n"。BitBake 寻找值对，这简化了使用。现有的镜像变量值本身不需要做出任何更改。

- *The Subversion (SVN) Fetcher Uses an \"ssh\" Parameter and Not an \"rsh\" Parameter:* The SVN fetcher now takes an \"ssh\" parameter instead of an \"rsh\" parameter. This new optional parameter is used when the \"protocol\" parameter is set to \"svn+ssh\". You can only use the new parameter to specify the `ssh` program used by SVN. The SVN fetcher passes the new parameter through the `SVN_SSH` environment variable during the `ref-tasks-fetch` task.

> SVN 抓取器现在使用“ssh”参数而不是“rsh”参数。当“protocol”参数设置为“svn+ssh”时，将使用此新的可选参数。您只能使用新参数来指定 SVN 所使用的 `ssh` 程序。SVN 抓取器在 `ref-tasks-fetch` 任务期间将新参数通过 `SVN_SSH` 环境变量传递。

See the \"``bitbake-user-manual/bitbake-user-manual-fetching:subversion (svn) fetcher (\`\`svn://\`\`)``\" section in the BitBake User Manual for additional information.

> 请参阅 BitBake 用户手册中的“bitbake-user-manual / bitbake-user-manual-fetching：subversion(svn)获取器(svn：//)”部分，以获取更多信息。

- `BB_SETSCENE_VERIFY_FUNCTION` and `BB_SETSCENE_VERIFY_FUNCTION2` Removed: Because the mechanism they were part of is no longer necessary with recipe-specific sysroots, the `BB_SETSCENE_VERIFY_FUNCTION` and `BB_SETSCENE_VERIFY_FUNCTION2` variables have been removed.

> 已移除 `BB_SETSCENE_VERIFY_FUNCTION` 和 `BB_SETSCENE_VERIFY_FUNCTION2`：由于它们所属的机制在针对特定配方的系统根目录中不再必要，因此已移除 `BB_SETSCENE_VERIFY_FUNCTION` 和 `BB_SETSCENE_VERIFY_FUNCTION2` 变量。

# Absolute Symbolic Links

Absolute symbolic links (symlinks) within staged files are no longer permitted and now trigger an error. Any explicit creation of symlinks can use the `lnr` script, which is a replacement for `ln -r`.

> 现在不允许在阶段文件中使用绝对符号链接(符号链接)，现在会引发错误。任何显式创建符号链接的操作都可以使用 `lnr` 脚本，它是 `ln -r` 的替代品。

If the build scripts in the software that the recipe is building are creating a number of absolute symlinks that need to be corrected, you can inherit `relative_symlinks` within the recipe to turn those absolute symlinks into relative symlinks.

> 如果软件中的构建脚本创建了许多绝对符号链接需要纠正，您可以在配方中继承 `relative_symlinks`，将这些绝对符号链接转换为相对符号链接。

# GPLv2 Versions of GPLv3 Recipes Moved

Older GPLv2 versions of GPLv3 recipes have moved to a separate `meta-gplv2` layer.

> 旧版本的 GPLv2 的 GPLv3 recipes 已经移至单独的'meta-gplv2'层。

If you use `INCOMPATIBLE_LICENSE` to substitute a GPLv2 version of a GPLv3 recipe, then you must add the `meta-gplv2` layer to your configuration.

> 如果你使用不兼容许可证来排除 GPLv3 或者设置首选版本来替换 GPLv3 配方的 GPLv2 版本，那么你必须在你的配置中添加 meta-gplv2 层。

::: note
::: title
Note
:::

You can `find meta-gplv2` layer in the OpenEmbedded layer index at :oe_layer:[/meta-gplv2].

> 你可以在 OpenEmbedded 图层索引中找到 meta-gplv2 图层：oe_layer：[/meta-gplv2]。
> :::

These relocated GPLv2 recipes do not receive the same level of maintenance as other core recipes. The recipes do not get security fixes and upstream no longer maintains them. In fact, the upstream community is actively hostile towards people that use the old versions of the recipes. Moving these recipes into a separate layer both makes the different needs of the recipes clearer and clearly identifies the number of these recipes.

> 这些重新定位的 GPLv2 配方不会得到和其他核心配方相同的维护水平。这些配方不会得到安全修复，上游也不再维护它们。事实上，上游社区对使用旧版本配方的人有积极的敌意。将这些配方移入一个单独的层次，可以更清楚地显示配方的不同需求，并明确标识这些配方的数量。

::: note
::: title
Note
:::

The long-term solution might be to move to BSD-licensed replacements of the GPLv3 components for those that need to exclude GPLv3-licensed components from the target system. This solution will be investigated for future Yocto Project releases.

> 长期解决方案可能是为那些需要将 GPLv3 许可组件排除在目标系统之外的情况，移至基于 BSD 许可的 GPLv3 组件替代品。未来的 Yocto 项目版本将对此进行调查。
> :::

# Package Management Changes

The following package management changes took place:

> 以下的包管理变更已经发生:

- Smart package manager is replaced by DNF package manager. Smart has become unmaintained upstream, is not ported to Python 3.x. Consequently, Smart needed to be replaced. DNF is the only feasible candidate.

> 智能包管理器被 DNF 包管理器取代了。Smart 已经不再维护，也没有移植到 Python 3.x。因此，必须替换 Smart。DNF 是唯一可行的候选者。

The change in functionality is that the on-target runtime package management from remote package feeds is now done with a different tool that has a different set of command-line options. If you have scripts that call the tool directly, or use its API, they need to be fixed.

> 功能变化是，从远程软件包源获取的目标运行时软件包管理现在使用了一个不同的工具，它具有不同的命令行选项。如果您有直接调用该工具或使用其 API 的脚本，则需要进行修复。

For more information, see the [DNF Documentation](https://dnf.readthedocs.io/en/latest/).

> 欲了解更多信息，请参阅 [DNF 文档](https://dnf.readthedocs.io/en/latest/)。

- Rpm 5.x is replaced with Rpm 4.x. This is done for two major reasons:

  - DNF is API-incompatible with Rpm 5.x and porting it and maintaining the port is non-trivial.
  - Rpm 5.x itself has limited maintenance upstream, and the Yocto Project is one of the very few remaining users.

> RPM 5.x 本身只有有限的上游维护，而 Yocto 项目是极少数仍然在使用它的用户之一。

- Berkeley DB 6.x is removed and Berkeley DB 5.x becomes the default:

  - Version 6.x of Berkeley DB has largely been rejected by the open source community due to its AGPLv3 license. As a result, most mainstream open source projects that require DB are still developed and tested with DB 5.x.

> 版本 6.x 的 Berkeley DB 由于其 AGPLv3 许可证而受到开源社区的普遍拒绝。因此，大多数需要 DB 的主流开源项目仍然使用 DB 5.x 进行开发和测试。

- In OE-core, the only thing that was requiring DB 6.x was Rpm 5.x. Thus, no reason exists to continue carrying DB 6.x in OE-core.

> 在 OE-core 中，唯一需要 DB 6.x 的是 Rpm 5.x。因此，没有理由继续在 OE-core 中携带 DB 6.x。

- `createrepo` is replaced with `createrepo_c`.

  `createrepo_c` is the current incarnation of the tool that generates remote repository metadata. It is written in C as compared to `createrepo`, which is written in Python. `createrepo_c` is faster and is maintained.

> `createrepo_c` 是目前用于生成远程存储库元数据的工具。它是用 C 编写的，而 `createrepo` 则是用 Python 编写的。`createrepo_c` 更快，并且得到维护。

- Architecture-independent RPM packages are \"noarch\" instead of \"all\".

  This change was made because too many places in DNF/RPM4 stack already make that assumption. Only the filenames and the architecture tag has changed. Nothing else has changed in OE-core system, particularly in the `ref-classes-allarch` class.

> 这个更改是因为 DNF/RPM4 堆栈中的太多位置都做出了这个假设。只有文件名和架构标签发生了变化。OE-core 系统中其他任何东西都没有改变，尤其是在 `ref-classes-allarch` 类中。

- Signing of remote package feeds using `PACKAGE_FEED_SIGN` is not currently supported. This issue will be fully addressed in a future Yocto Project release. See :yocto_bugs:[defect 11209 \</show_bug.cgi?id=11209\>] for more information on a solution to package feed signing with RPM in the Yocto Project 2.3 release.

> 签署远程软件包提要(feed)使用 PACKAGE_FEED_SIGN 暂不支持。未来的 Yocto Project 发布版本将会全面解决此问题。请参考：yocto_bugs:[缺陷 11209 \</show_bug.cgi?id=11209\>] 了解在 Yocto Project 2.3 发布版本中用于 RPM 软件包提要(feed)签名的解决方案。

- OPKG now uses the libsolv backend for resolving package dependencies by default. This is vastly superior to OPKG\'s internal ad-hoc solver that was previously used. This change does have a small impact on disk (around 500 KB) and memory footprint.

> 现在，OPKG 默认使用 libsolv 后端来解析软件包依赖性。这比 OPKG 之前使用的内部特定解析器要好得多。这一变化会对磁盘(约 500KB)和内存使用率有一定影响。

::: note
::: title

Note

> 注意
> :::

For further details on this change, see the :yocto_[git:%60commit](git:%60commit) message \</poky/commit/?id=f4d4f99cfbc2396e49c1613a7d237b9e57f06f81\>\`.

> 对于此次变更的更多细节，请参阅:yocto_[git:`commit`](git:%60commit%60) 消息 \</poky/commit/?id=f4d4f99cfbc2396e49c1613a7d237b9e57f06f81\>\`。
> :::

# Removed Recipes

The following recipes have been removed:

> 以下 recipes 已被移除：

- `linux-yocto 4.8`: Version 4.8 has been removed. Versions 4.1 (LTSI), 4.4 (LTS), 4.9 (LTS/LTSI) and 4.10 are now present.

> Linux-Yocto 4.8：版本 4.8 已经被移除。现在有 4.1(LTSI)、4.4(LTS)、4.9(LTS / LTSI)和 4.10 版本。

- `python-smartpm`: Functionally replaced by `dnf`.
- `createrepo`: Replaced by the `createrepo-c` recipe.
- `rpmresolve`: No longer needed with the move to RPM 4 as RPM itself is used instead.
- `gstreamer`: Removed the GStreamer Git version recipes as they have been stale. `1.10.` x recipes are still present.

> GStreamer：移除了 GStreamer Git 版本的 recipes，因为它们已经过时了。1.10.x recipes 仍然存在。

- `alsa-conf-base`: Merged into `alsa-conf` since `libasound` depended on both. Essentially, no way existed to install only one of these.

> - `alsa-conf-base`: 因为 `libasound` 依赖于两者，因此已合并到 `alsa-conf` 中。实际上，没有办法只安装其中一个。

- `tremor`: Moved to `meta-multimedia`. Fixed-integer Vorbis decoding is not needed by current hardware. Thus, GStreamer\'s ivorbis plugin has been disabled by default eliminating the need for the `tremor` recipe in `OpenEmbedded-Core (OE-Core)`.

> 已移至 meta-multimedia。当前硬件无需固定整数 Vorbis 解码。因此，GStreamer 的 ivorbis 插件已默认禁用，从而消除了 OpenEmbedded-Core(OE-Core)中 tremor 配方的需要。

- `gummiboot`: Replaced by `systemd-boot`.

# Wic Changes

The following changes have been made to Wic:

> 以下对 Wic 的更改已经做出：

::: note
::: title
Note
:::

For more information on Wic, see the \"`dev-manual/wic:creating partitioned images using wic`\" section in the Yocto Project Development Tasks Manual.

> 要了解有关 Wic 的更多信息，请参阅 Yocto 项目开发任务手册中的“dev-manual / wic：使用 wic 创建分区映像”部分。
> :::

- *Default Output Directory Changed:* Wic\'s default output directory is now the current directory by default instead of the unusual `/var/tmp/wic`.

> *默认输出目录已更改：Wic 的默认输出目录现在默认为当前目录而不是不寻常的“/var/tmp/wic”。*

The `-o` and `--outdir` options remain unchanged and are used to specify your preferred output directory if you do not want to use the default directory.

> `-o` 和 `--outdir` 选项保持不变，用于指定您如果不想使用默认目录时所首选的输出目录。

- *fsimage Plug-in Removed:* The Wic fsimage plugin has been removed as it duplicates functionality of the rawcopy plugin.

> -*fsimage 插件已移除：*Wic fsimage 插件已移除，因为它重复了 rawcopy 插件的功能。

# QA Changes

The following QA checks have changed:

> 以下 QA 检查已经改变：

- `unsafe-references-in-binaries`: The `unsafe-references-in-binaries` QA check, which was disabled by default, has now been removed. This check was intended to detect binaries in `/bin` that link to libraries in `/usr/lib` and have the case where the user has `/usr` on a separate filesystem to `/`.

> `-unsafe-references-in-binaries`：默认情况下已禁用的 `unsafe-references-in-binaries` QA 检查现已删除。此检查旨在检测 `/ bin` 中链接到 `/ usr / lib` 中的二进制文件，并具有用户将 `/ usr` 放置在与 `/` 不同的文件系统中的情况。

The removed QA check was buggy. Additionally, `/usr` residing on a separate partition from `/` is now a rare configuration. Consequently, `unsafe-references-in-binaries` was removed.

> QA 检查被删除时存在缺陷。此外，`/usr` 从 `/` 分离到一个单独的分区的配置现在已经很少见。因此，`unsafe-references-in-binaries` 被移除了。

- `file-rdeps`: The `file-rdeps` QA check is now an error by default instead of a warning. Because it is an error instead of a warning, you need to address missing runtime dependencies.

> `file-rdeps` QA 检查现在默认为错误而不是警告。由于它是错误而不是警告，您需要解决缺少的运行时依赖项。

For additional information, see the `ref-classes-insane`\" section.

> 要了解更多信息，请参阅“ref-classes-insane”类和“ref-manual / qa-checks：错误和警告”部分。

# Miscellaneous Changes

The following miscellaneous changes have occurred:

> 以下发生了杂项变化：

- In this release, a number of recipes have been changed to ignore the `largefile` `DISTRO_FEATURES` item, enabling large file support unconditionally. This feature has always been enabled by default. Disabling the feature has not been widely tested.

> 在此版本中，许多配方已被更改为忽略 `largefile` `DISTRO_FEATURES` 项目，以无条件地启用大文件支持。此功能一直处于默认启用状态。尚未广泛测试禁用此功能。

::: note
::: title

Note

> 注意
> :::

Future releases of the Yocto Project will remove entirely the ability to disable the largefile feature, which would make it unconditionally enabled everywhere.

> 未来的 Yocto 项目发行版将完全移除禁用大文件功能的能力，这将使其在任何地方都被无条件地启用。
> :::

- If the `DISTRO_VERSION` value is inaccurate if the `base-files` recipe is restored from shared state (sstate) rather than rebuilt.

> 如果 `DISTRO_VERSION` 的值是不准确的。

If you need the build date recorded in `/etc/issue*` or anywhere else in your image, a better method is to define a post-processing function to do it and have the function called from `ROOTFS_POSTPROCESS_COMMAND`. Doing so ensures the value is always up-to-date with the created image.

> 如果您需要在 `/etc/issue*` 或其他任何地方记录构建日期，最好的方法是定义一个后处理函数来完成，并从 `ROOTFS_POSTPROCESS_COMMAND` 调用该函数。这样可以确保值总是与创建的映像保持最新。

- Dropbear\'s `init` script now disables DSA host keys by default. This change is in line with the systemd service file, which supports RSA keys only, and with recent versions of OpenSSH, which deprecates DSA host keys.

> Dropbear 的 `init` 脚本现在默认禁用 DSA 主机密钥。此更改与支持仅 RSA 密钥的 systemd 服务文件以及最近版本的 OpenSSH(它弃用 DSA 主机密钥)保持一致。

- The `ref-classes-buildhistory` class now correctly uses tabs as separators between all columns in `installed-package-sizes.txt` in order to aid import into other tools.

> - 现在，`ref-classes-buildhistory` 类正确使用制表符作为 `installed-package-sizes.txt` 中所有列之间的分隔符，以帮助导入其他工具。

- The `USE_LDCONFIG` variable has been replaced with the \"ldconfig\" `DISTRO_FEATURES` feature. Distributions that previously set:

> USE_LDCONFIG 變量已被"ldconfig" DISTRO_FEATURES 特性所取代。先前設置的分發版本：

```
USE_LDCONFIG = "0"
```

should now instead use the following:

> 应该使用以下内容：

```
DISTRO_FEATURES_BACKFILL_CONSIDERED_append = " ldconfig"
```

- The default value of `COPYLEFT_LICENSE_INCLUDE` now includes all versions of AGPL licenses in addition to GPL and LGPL.

> 默认值 `COPYLEFT_LICENSE_INCLUDE` 现在除了 GPL 和 LGPL 外，还包括所有版本的 AGPL 许可证。

::: note
::: title

Note

> 注意
> :::

The default list is not intended to be guaranteed as a complete safe list. You should seek legal advice based on what you are distributing if you are unsure.

> 默认列表不能保证是完整安全的列表。如果你不确定要发布什么内容，你应该寻求法律建议。
> :::

- Kernel module packages are now suffixed with the kernel version in order to allow module packages from multiple kernel versions to co-exist on a target system. If you wish to return to the previous naming scheme that does not include the version suffix, use the following:

> 内核模块包现在以内核版本作为后缀，以允许多个内核版本的模块包在目标系统上共存。如果您希望恢复不包含版本后缀的先前命名方案，请使用以下内容：

```
KERNEL_MODULE_PACKAGE_SUFFIX = ""
```

- Removal of `libtool` `*.la` files is now enabled by default. The `*.la` files are not actually needed on Linux and relocating them is an unnecessary burden.

> 移除 `libtool` `*.la` 文件现在默认启用。在 Linux 中，这些 `*.la` 文件实际上不需要，重新定位它们是没有必要的负担。

If you need to preserve these `.la` files (e.g. in a custom distribution), you must change `INHERIT_DISTRO`\" is not included in the value.

> 如果您需要保存这些 `.la` 文件(例如在自定义发行版中)，您必须更改“INHERIT_DISTRO”，以便不包含“ref-classes-remove-libtool”。

- Extensible SDKs built for GCC 5+ now refuse to install on a distribution where the host GCC version is 4.8 or 4.9. This change resulted from the fact that the installation is known to fail due to the way the `uninative` shared state (sstate) package is built. See the `ref-classes-uninative` class for additional information.

> SDK 可扩展性的构建已经不再安装在主机 GCC 版本为 4.8 或 4.9 的发行版上。这种变化是由于安装已知会由于 `uninative` 共享状态(sstate)包的构建而失败。有关额外信息，请参阅 `ref-classes-uninative` 类。

- All `ref-classes-native` value instead of sharing the value used by recipes for the target, in order to avoid unnecessary rebuilds.

> 所有 `ref-classes-native` 值，而不是与目标的 recipes 共享值，以避免不必要的重新构建。

The `DISTRO_FEATURES`.

> 特性 `DISTRO_FEATURES`。

For `ref-classes-nativesdk`.

> 对于 `ref-classes-nativesdk`。

- The `FILESDIR` variable, which was previously deprecated and rarely used, has now been removed. You should change any recipes that set `FILESDIR` to set `FILESPATH` instead.

> `FILESDIR` 变量已被弃用，很少使用，现已被移除。您应该更改任何设置 `FILESDIR` 的配方，而应该设置 `FILESPATH`。

- The `MULTIMACH_HOST_SYS` variable has been removed as it is no longer needed with recipe-specific sysroots.

> `MULTIMACH_HOST_SYS` 变量已被删除，因为它不再需要特定配方的系统根目录。
