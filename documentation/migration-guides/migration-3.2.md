---
tip: translate by openai@2023-06-07 22:09:04
...
---
title: Release 3.2 (gatesgarth)
-------------------------------

This section provides migration information for moving to the Yocto Project 3.2 Release (codename \"gatesgarth\") from the prior release.

# Minimum system requirements

`gcc` version 6.0 is now required at minimum on the build host. For older host distributions where this is not available, you can use the `buildtools-extended` tarball (easily installable using `scripts/install-buildtools`).

> GCC 版本 6.0 现在至少需要在构建主机上。对于不可用的旧主机分发，您可以使用 `buildtools-extended` tarball(使用 `scripts/install-buildtools` 轻松安装)。

# Removed recipes

The following recipes have been removed:

- `bjam-native`: replaced by `boost-build-native`
- `avahi-ui`: folded into the main `avahi` recipe \-\-- the GTK UI can be disabled using `PACKAGECONFIG` for `avahi`.
- `build-compare`: no longer needed with the removal of the `packagefeed-stability` class
- `dhcp`: obsolete, functionally replaced by `dhcpcd` and `kea`
- `libmodulemd-v1`: replaced by `libmodulemd`
- `packagegroup-core-device-devel`: obsolete

# Removed classes

The following classes (.bbclass files) have been removed:

- `spdx`: obsolete \-\-- the Yocto Project is a strong supporter of SPDX, but this class was old code using a dated approach and had the potential to be misleading. The `meta-sdpxscanner` layer is a much more modern and active approach to handling this and is recommended as a replacement.

> SPDX 已经过时--Yocto 项目支持 SPDX，但这个类是使用过时的方法的旧代码，可能会产生误导。`meta-sdpxscanner` 层是一种更现代化和活跃的处理方法，建议替换。

- `packagefeed-stability`: this class had become obsolete with the advent of hash equivalence and reproducible builds.

# pseudo path filtering and mismatch behaviour

pseudo now operates on a filtered subset of files. This is a significant change to the way pseudo operates within OpenEmbedded \-\-- by default, pseudo monitors and logs (adds to its database) any file created or modified whilst in a `fakeroot` environment. However, there are large numbers of files that we simply don\'t care about the permissions of whilst in that `fakeroot` context, for example \$, the central sstate control directories, and others.

> 伪现在在一个筛选过的文件子集上运行。这对 OpenEmbedded 中 pseudo 的运行方式是一个重大的变化--默认情况下，pseudo 会监视并记录(添加到其数据库)在 `fakeroot` 环境下创建或修改的任何文件。但是，我们在 `fakeroot` 上下文中根本不关心许多文件的权限，例如\$, 中央 sstate 控制目录和其他目录。

As of this release, new functionality in pseudo is enabled to ignore these directory trees (controlled using a new `PSEUDO_IGNORE_PATHS` variable) resulting in a cleaner database with less chance of \"stray\" mismatches if files are modified outside pseudo context. It also should reduce some overhead from pseudo as the interprocess round trip to the server is avoided.

> 从这个版本开始，pseudo 中的新功能可以忽略这些目录树(使用新的“PSEUDO_IGNORE_PATHS”变量控制)，从而使数据库更加干净，如果文件在 pseudo 上下文之外被修改，则“流浪”不匹配的机会也会减少。它还应该减少 pseudo 的一些开销，因为进程间的往返到服务器将被避免。

There is a possible complication where some existing recipe may break, for example, a recipe was found to be writing to `$ to cover additional paths that pseudo should not be monitoring.

> 有可能出现某些现有配方会出现问题的复杂情况，例如，在 `ref-tasks-install` 中发现一个配方在 `make install` 时向 `$` 被列为不跟踪，在这个位置尝试 `chown root` 时出现错误。另一个例子是 `tcl` 配方，源目录 `S` 被设置为源树的子目录，但文件被写入到该子目录之上的目录结构中。对于自己的配方中的这些类型的情况，请扩展 `PSEUDO_IGNORE_PATHS` 以覆盖 pseudo 不应监视的其他路径。

In addition, pseudo\'s behaviour on mismatches has now been changed \-\-- rather than doing what turns out to be a rather dangerous \"fixup\" if it sees a file with a different path but the same inode as another file it has previously seen, pseudo will throw an `abort()` and direct you to a :yocto_wiki:[wiki page \</Pseudo_Abort\>] that explains how to deal with this.

> 此外，Pseudo 在不匹配时的行为已经改变了--如果它看到一个具有不同路径但与之前看到的另一个文件具有相同 inode 的文件，它将抛出一个 `abort()`，并将您重定向到:yocto_wiki:[wiki 页面 \</Pseudo_Abort\>]，以解释如何处理此问题。

# `MLPREFIX` now required for multilib when runtime dependencies conditionally added

In order to solve some previously intractable problems with runtime dependencies and multilib, a change was made that now requires the `MLPREFIX` values) where the dependency is conditionally added.

> 为了解决先前难以解决的运行时依赖关系和多库问题，做出了一项更改，现在需要显式地将 `MLPREFIX` 值中)，其中条件性添加了依赖项。

If you have anonymous Python or in-line Python conditionally adding dependencies in your custom recipes, and you intend for those recipes to work with multilib, then you will need to ensure that `$` is prefixed on the package names in the dependencies, for example (from the `glibc` recipe):

> 如果您在自定义配方中使用匿名 Python 或行内 Python 有条件地添加依赖项，并且您希望这些配方能够与多库一起使用，那么您需要确保在依赖项的包名称中加上 `$`，例如(来自 `glibc` 配方)：

```
RRECOMMENDS_$"
```

This also applies when conditionally adding packages to `PACKAGES` where those packages have dependencies, for example (from the `alsa-plugins` recipe):

> 这也适用于有条件地将软件包添加到 `PACKAGES`，其中这些软件包具有依赖项，例如(来自 `alsa-plugins` 配方)：

```
PACKAGES += "$"
...
RDEPENDS_$-pulseaudio-conf += "\
        $libasound-module-conf-pulse \
        $libasound-module-ctl-pulse \
        $libasound-module-pcm-pulse \
"
```

# packagegroup-core-device-devel no longer included in images built for qemu\* machines

`packagegroup-core-device-devel` was previously added automatically to images built for `qemu*` machines, however the purpose of the group and what it should contain is no longer clear, and in general, adding userspace development items to images is best done at the image/class level; thus this packagegroup was removed.

> 之前会自动添加 `packagegroup-core-device-devel` 到 `qemu*` 机器构建的镜像中，但是现在它的目的和应该包含的内容不再清楚，总的来说，最好在镜像/类级别添加用户空间开发项目；因此，该软件包组已被删除。

This packagegroup previously pulled in the following:

- `distcc-config`
- `nfs-export-root`
- `bash`
- `binutils-symlinks`

If you still need any of these in your image built for a `qemu*` machine then you will add them explicitly to `IMAGE_INSTALL` or another appropriate place in the dependency chain for your image (if you have not already done so).

> 如果你在为 qemu*机器构建的镜像中仍然需要这些东西，那么你需要显式地将它们添加到 IMAGE_INSTALL 或其他适当的依赖链中(如果你还没有这样做的话)。

# DHCP server/client replaced

The `dhcp` software package has become unmaintained and thus has been functionally replaced by `dhcpcd` (client) and `kea` (server). You will need to replace references to the recipe/package names as appropriate \-\-- most commonly, at the package level `dhcp-client` should be replaced by `dhcpcd` and `dhcp-server` should be replaced by `kea`. If you have any custom configuration files for these they will need to be adapted \-\-- refer to the upstream documentation for `dhcpcd` and `kea` for further details.

> 软件包 `dhcp` 已经不再维护，因此已经被 `dhcpcd`(客户端)和 `kea`(服务器)功能性地取代。您需要根据适当的情况替换对配方/软件包名称的引用 - 通常，在软件包级别，`dhcp-client` 应替换为 `dhcpcd`，`dhcp-server` 应替换为 `kea`。如果您有任何这些软件的自定义配置文件，则需要进行调整 - 请参考 `dhcpcd` 和 `kea` 的上游文档以获取更多详细信息。

# Packaging changes

- `python3`: the `urllib` Python package has now moved into the core package, as it is used more commonly than just netclient (e.g. email, xml, mimetypes, pydoc). In addition, the `pathlib` module is now also part of the core package.

> python3：urllib 这个 Python 包现在已经移到核心包中，因为它比 netclient 更常用(例如电子邮件、xml、mimetypes 和 pydoc)。此外，pathlib 模块现在也是核心包的一部分。

- `iptables`: `iptables-apply` and `ip6tables-apply` have been split out to their own package to avoid a bash dependency in the main `iptables` package

> `iptables`：为了避免在主 `iptables` 包中产生 bash 依赖，`iptables-apply` 和 `ip6tables-apply` 已被拆分到自己的包中。

# Package QA check changes

Previously, the following package QA checks triggered warnings, however they can be indicators of genuine underlying problems and are therefore now treated as errors:

> 以前，以下软件包 QA 检查会引发警告，但它们可能是真正的潜在问题的指示符，因此现在被视为错误：

- `already-stripped <qa-check-already-stripped>`
- `compile-host-path <qa-check-compile-host-path>`
- `installed-vs-shipped <qa-check-installed-vs-shipped>`
- `ldflags <qa-check-ldflags>`
- `pn-overrides <qa-check-pn-overrides>`
- `rpaths <qa-check-rpaths>`
- `staticdev <qa-check-staticdev>`
- `unknown-configure-option <qa-check-unknown-configure-option>`
- `useless-rpaths <qa-check-useless-rpaths>`

In addition, the following new checks were added and default to triggering an error:

- `shebang-size <qa-check-shebang-size>`: Check for shebang (#!) lines longer than 128 characters, which can give an error at runtime depending on the operating system.

> 检查 shebang(#！)行是否超过 128 个字符，根据操作系统的不同可能会在运行时出错。

- `unhandled-features-check <qa-check-unhandled-features-check>` class is set while not inheriting the class itself.

> 检查在不继承 `ref-classes-features_check` 类的情况下，是否设置了该类支持的任何变量。

- `missing-update-alternatives <qa-check-missing-update-alternatives>` class.

> 检查 recipes 是否为其中任何一个软件包设置了 ALTERNATIVE 变量，并且没有继承 ref-classes-update-alternatives 类。

- A trailing slash or duplicated slashes in the value of `S` will now trigger a warning so that they can be removed and path comparisons can be more reliable \-\-- remove any instances of these in your recipes if the warning is displayed.

> - `S` 的值中的尾部斜杠或重复的斜杠现在将触发警告，以便可以删除它们并使路径比较更可靠--如果显示警告，请在您的配方中删除这些实例。

# Globbing no longer supported in `file://` entries in `SRC_URI`

Globbing (`*` and `?` wildcards) in `file://` URLs within `SRC_URI` task checksum, and consequently would not ensure that the changed files would be incorporated in subsequent builds.

> 在 `SRC_URI` 中的 `file://` URL 中使用通配符(`*` 和 `?`)不能正确支持文件校验和，因此源文件的更改不会总是改变 `ref-tasks-fetch` 任务的校验和，从而不能确保更改的文件会在随后的构建中被包含。

Unfortunately it is not practical to make globbing work generically here, so the decision was taken to remove support for globs in `file://` URLs. If you have any usage of these in your recipes, then you will now need to either add each of the files that you expect to match explicitly, or alternatively if you still need files to be pulled in dynamically, put the files into a subdirectory and reference that instead.

> 不幸的是，在这里无法通用地实现 globbing，因此决定移除对 `file://` URL 的 glob 支持。如果您的配方中有这些，现在您需要显式添加您期望匹配的每个文件，或者，如果仍然需要动态拉取文件，将文件放入子目录并引用该子目录。

# deploy class now cleans `DEPLOYDIR` before `do_deploy`

`ref-tasks-deploy` being accidentally contaminated by files from previous runs, possibly even with different config, in case of incremental builds.

> 任务部署，在部署类中实施，现在会在运行之前清理\$一样。这可以降低由于增量构建而导致 DEPLOYDIR 意外污染文件的风险。

Most recipes and classes that inherit the `ref-classes-deploy`` \-\-- these should be refactored to use ` do_deploy_prepend` instead.

> 大多数继承 `ref-classes-deploy`` \-\-- 这些应该重构为使用 ` do_deploy_prepend`。

# Custom SDK / SDK-style recipes need to include `nativesdk-sdk-provides-dummy`

All `ref-classes-nativesdk`.

> 所有 `ref-classes-nativesdk` 中。

# `ld.so.conf` now moved back to main `glibc` package

There are cases where one doesn\'t want `ldconfig` on target (e.g. for read-only root filesystems, it\'s rather pointless), yet one still needs `/etc/ld.so.conf` to be present at image build time:

> 有时候，我们不希望在目标机上运行 `ldconfig`(例如，对于只读根文件系统来说，这是毫无意义的)，但仍然需要在镜像构建时保留 `/etc/ld.so.conf` 文件：

When some recipe installs libraries to a non-standard location, and therefore installs in a file in `/etc/ld.so.conf.d/foo.conf`, we need `/etc/ld.so.conf` containing:

> 当某个配方安装库到一个非标准位置，因此安装在 `/etc/ld.so.conf.d/foo.conf` 文件中时，我们需要 `/etc/ld.so.conf` 包含：

```
include /etc/ld.so.conf.d/*.conf
```

in order to get those other locations picked up.

Thus `/etc/ld.so.conf` is now in the main `glibc` package so that there\'s always an `ld.so.conf` present when the build-time `ldconfig` runs towards the end of image construction.

> 因此，/etc/ld.so.conf 现在位于主要的 glibc 软件包中，这样当构建时的 ldconfig 在镜像构建结束时运行时，总会有一个 ld.so.conf 存在。

The `ld.so.conf` and `ld.so.conf.d/*.conf` files do not take up significant space (at least not compared to the \~700kB `ldconfig` binary), and they might be needed in case `ldconfig` is installable, so they are left in place after the image is built. Technically it would be possible to remove them if desired, though it would not be trivial if you still wanted the build-time ldconfig to function (`ROOTFS_POSTPROCESS_COMMAND` will not work as `ldconfig` is run after the functions referred to by that variable).

> `ld.so.conf` 和 `ld.so.conf.d/*.conf` 文件不会占用太多空间(至少与 700kB 的 `ldconfig` 二进制文件相比不会)，并且如果可以安装 `ldconfig`，可能需要它们，因此在构建映像后会将它们保留在原处。理论上，如果需要，可以移除它们，但是如果仍希望构建时运行 `ldconfig`，则不会很容易(`ROOTFS_POSTPROCESS_COMMAND` 将不起作用，因为 `ldconfig` 在该变量引用的函数之后运行)。

# Host DRI drivers now used for GL support within `runqemu`

`runqemu` now uses the mesa-native libraries everywhere virgl is used (i.e. when `gl`, `gl-es` or `egl-headless` options are specified), but instructs them to load DRI drivers from the host. Unfortunately this may not work well with proprietary graphics drivers such as those from Nvidia; if you are using such drivers then you may need to switch to an alternative (such as Nouveau in the case of Nvidia hardware) or avoid using the GL options.

> runqemu 现在在使用 virgl 时(即当指定 `gl`、`gl-es` 或 `egl-headless` 选项时)使用 mesa-native 库，但它会指示它们从主机加载 DRI 驱动程序。不幸的是，这可能与诸如 Nvidia 之类的专有图形驱动程序不能很好地工作；如果您正在使用此类驱动程序，则可能需要切换到替代品(例如 Nvidia 硬件的 Nouveau)或避免使用 GL 选项。

# Initramfs images now use a blank suffix

The reference `Initramfs`, which otherwise defaults to `".rootfs"`. These images aren\'t root filesystems and thus the rootfs label didn\'t make sense. If you are looking for the output files generated by these image recipes directly then you will need to adapt to the new naming without the `.rootfs` part.

> 参考 `Initramfs` 设置为空字符串，否则默认为 `".rootfs"`。这些镜像不是根文件系统，因此根文件系统标签没有意义。如果您直接查找这些镜像配方生成的输出文件，则需要适应没有 `.rootfs` 部分的新命名。

# Image artifact name variables now centralised in image-artifact-names class

The defaults for the following image artifact name variables have been moved from `bitbake.conf` to a new `image-artifact-names` class:

- `IMAGE_BASENAME`
- `IMAGE_LINK_NAME`
- `IMAGE_NAME`
- `IMAGE_NAME_SUFFIX`
- `IMAGE_VERSION_SUFFIX`

Image-related classes now inherit this class, and typically these variables are only referenced within image recipes so those will be unaffected by this change. However if you have references to these variables in either a recipe that is not an image or a class that is enabled globally, then those will now need to be changed to `inherit image-artifact-names`.

> 现在，与镜像相关的类继承了这个类，通常这些变量只在镜像配方中引用，因此这些变量不会受到这一更改的影响。但是，如果您在非镜像配方或全局启用的类中引用了这些变量，则这些变量现在需要更改为“继承镜像工件名称”。

# Miscellaneous changes

- Support for the long-deprecated `PACKAGE_GROUP` variable has now been removed \-\-- replace any remaining instances with `FEATURE_PACKAGES`.

> 支持长期被弃用的 `PACKAGE_GROUP` 变量已被移除---请用 `FEATURE_PACKAGES` 替换所有剩余实例。

- The `FILESPATHPKG` variable, having been previously deprecated, has now been removed. Replace any remaining references with appropriate use of `FILESEXTRAPATHS`.

> `FILESPATHPKG` 变量已经被弃用，现在已经被删除。用 `FILESEXTRAPATHS` 的适当使用替换任何剩余的引用。

- Erroneous use of `inherit +=` (instead of `INHERIT +=`) in a configuration file now triggers an error instead of silently being ignored.
- ptest support has been removed from the `kbd` recipe, as upstream has moved to autotest which is difficult to work with in a cross-compilation environment.

> `kbd` recipes 中已经移除了 ptest 支持，因为上游已经转向了 autotest，而跨编译环境中很难使用它。

- `oe.utils.is_machine_specific()` and `oe.utils.machine_paths()` have been removed as their utility was questionable. In the unlikely event that you have references to these in your own code, then the code will need to be reworked.

> `- oe.utils.is_machine_specific() 和 oe.utils.machine_paths() 已被删除，因为它们的实用性值得怀疑。如果您在自己的代码中引用了这些内容，那么代码将需要重新修改。`

- The `i2ctransfer` module is now disabled by default when building `busybox` in order to be consistent with disabling the other i2c tools there. If you do wish the i2ctransfer module to be built in BusyBox then add `CONFIG_I2CTRANSFER=y` to your custom BusyBox configuration.

> 构建 BusyBox 时，为了与禁用其他 i2c 工具保持一致，默认禁用了 `i2ctransfer` 模块。如果您确实希望在 BusyBox 中构建 i2ctransfer 模块，请将 `CONFIG_I2CTRANSFER=y` 添加到您的自定义 BusyBox 配置中。

- In the `Upstream-Status` header convention for patches, `Accepted` has been replaced with `Backport` as these almost always mean the same thing i.e. the patch is already upstream and may need to be removed in a future recipe upgrade. If you are adding these headers to your own patches then use `Backport` to indicate that the patch has been sent upstream.

> 在补丁的 `Upstream-Status` 头部约定中，`Accepted` 已被 `Backport` 所取代，因为这些几乎总是意味着相同的事情，即补丁已经上游，可能需要在将来的配方升级中删除。如果您向自己的补丁添加这些标头，请使用 `Backport` 来表示补丁已经发送到上游。

- The `tune-supersparc.inc` tune file has been removed as it does not appear to be widely used and no longer works.
