---
tip: translate by openai@2023-06-07 22:00:13
...
---
title: Release 3.0 (zeus)
---

This section provides migration information for moving to the Yocto Project 3.0 Release (codename \"zeus\") from the prior release.

> 此部分提供从前一个版本迁移到Yocto Project 3.0发行版（代号“zeus”）的迁移信息。

# Init System Selection {#migration-3.0-init-system-selection}


Changing the init system manager previously required setting a number of different variables. You can now change the manager by setting the `INIT_MANAGER` variable and the corresponding include files (i.e. `conf/distro/include/init-manager-*.conf`). Include files are provided for four values: \"none\", \"sysvinit\", \"systemd\", and \"mdev-busybox\". The default value, \"none\", for `INIT_MANAGER` should allow your current settings to continue working. However, it is advisable to explicitly set `INIT_MANAGER`.

> 以前更改初始化系统管理器需要设置多个不同的变量。现在，您可以通过设置“INIT_MANAGER”变量和相应的包含文件（即“conf/distro/include/init-manager-* .conf”）来更改管理器。提供了四个值的包含文件：“none”、“sysvinit”、“systemd”和“mdev-busybox”。“INIT_MANAGER”的默认值“none”应该允许您继续使用当前设置。但是，建议明确设置“INIT_MANAGER”。

# LSB Support Removed {#migration-3.0-lsb-support-removed}


Linux Standard Base (LSB) as a standard is not current, and is not well suited for embedded applications. Support can be continued in a separate layer if needed. However, presently LSB support has been removed from the core.

> Linux标准基础(LSB)作为一个标准已经不再更新，不适合嵌入式应用程序。如果需要，可以在单独的层中继续支持。但是，目前LSB支持已经从核心中移除了。


As a result of this change, the `poky-lsb` derivative distribution configuration that was also used for testing alternative configurations has been replaced with a `poky-altcfg` distribution that has LSB parts removed.

> 由于这一变更，曾用于测试替代配置的`poky-lsb`衍生发行版配置已被一个移除了LSB部分的`poky-altcfg`发行版所取代。

# Removed Recipes {#migration-3.0-removed-recipes}


The following recipes have been removed.

> 以下食谱已被移除。

- `core-image-lsb-dev`: Part of removed LSB support.
- `core-image-lsb`: Part of removed LSB support.
- `core-image-lsb-sdk`: Part of removed LSB support.

- `cve-check-tool`: Functionally replaced by the `cve-update-db` recipe and `ref-classes-cve-check`{.interpreted-text role="ref"} class.

> `cve-check-tool`：已被`cve-update-db`配方和`ref-classes-cve-check`{.interpreted-text role="ref"}类所取代。

- `eglinfo`: No longer maintained. `eglinfo` from `mesa-demos` is an adequate and maintained alternative.

> `eglinfo`不再维护。`mesa-demos`中的`eglinfo`是一个足够好且维护的替代品。
- `gcc-8.3`: Version 8.3 removed. Replaced by 9.2.
- `gnome-themes-standard`: Only needed by gtk+ 2.x, which has been removed.
- `gtk+`: GTK+ 2 is obsolete and has been replaced by gtk+3.

- `irda-utils`: Has become obsolete. IrDA support has been removed from the Linux kernel in version 4.17 and later.

> - `irda-utils`：已经过时了。从Linux内核4.17版本开始，IrDA支持已经被移除。
- `libnewt-python`: `libnewt` Python support merged into main `libnewt` recipe.
- `libsdl`: Replaced by newer `libsdl2`.
- `libx11-diet`: Became obsolete.
- `libxx86dga`: Removed obsolete client library.
- `libxx86misc`: Removed. Library is redundant.
- `linux-yocto`: Version 5.0 removed, which is now redundant (5.2 / 4.19 present).
- `lsbinitscripts`: Part of removed LSB support.
- `lsb`: Part of removed LSB support.
- `lsbtest`: Part of removed LSB support.
- `openssl10`: Replaced by newer `openssl` version 1.1.
- `packagegroup-core-lsb`: Part of removed LSB support.
- `python-nose`: Removed the Python 2.x version of the recipe.
- `python-numpy`: Removed the Python 2.x version of the recipe.
- `python-scons`: Removed the Python 2.x version of the recipe.
- `source-highlight`: No longer needed.
- `stress`: Replaced by `stress-ng`.
- `vulkan`: Split into `vulkan-loader`, `vulkan-headers`, and `vulkan-tools`.
- `weston-conf`: Functionality moved to `weston-init`.

# Packaging Changes {#migration-3.0-packaging-changes}


The following packaging changes have occurred.

> 以下包装发生了变化。


- The `Epiphany <GNOME_Web>`{.interpreted-text role="wikipedia"} browser has been dropped from `packagegroup-self-hosted` as it has not been needed inside `build-appliance-image` for quite some time and was causing resource problems.

> Epiphany浏览器已从packagegroup-self-hosted中删除，因为很长一段时间内，它在build-appliance-image中没有被需要，并且引起了资源问题。

- `libcap-ng` Python support has been moved to a separate `libcap-ng-python` recipe to streamline the build process when the Python bindings are not needed.

> `libcap-ng` 的 Python 支持已经移至单独的 `libcap-ng-python` 配方中，以简化当不需要 Python 绑定时的构建过程。
- `libdrm` now packages the file `amdgpu.ids` into a separate `libdrm-amdgpu` package.

- `python3`: The `runpy` module is now in the `python3-core` package as it is required to support the common \"python3 -m\" command usage.

> `Python3`：为了支持常见的“python3 -m”命令使用，`runpy`模块现在在`python3-core`包中。

- `distcc` now provides separate `distcc-client` and `distcc-server` packages as typically one or the other are needed, rather than both.

> distcc现在提供单独的distcc-client和distcc-server软件包，因为通常只需要其中一个，而不是两个。

- `python*-setuptools` recipes now separately package the `pkg_resources` module in a `python-pkg-resources` / `python3-pkg-resources` package as the module is useful independent of the rest of the setuptools package. The main `python-setuptools` / `python3-setuptools` package depends on this new package so you should only need to update dependencies unless you want to take advantage of the increased granularity.

> 现在，`python-setuptools` / `python3-setuptools` 将`pkg_resources`模块分别打包到`python-pkg-resources` / `python3-pkg-resources` 包中，因为该模块独立于其余的setuptools包而有用。主`python-setuptools` / `python3-setuptools` 包依赖于这个新包，所以您只需要更新依赖项，除非您想利用增加的粒度。

# CVE Checking {#migration-3.0-cve-checking}


`cve-check-tool` has been functionally replaced by a new `cve-update-db` recipe and functionality built into the `ref-classes-cve-check`{.interpreted-text role="ref"} class. The result uses NVD JSON data feeds rather than the deprecated XML feeds that `cve-check-tool` was using, supports CVSSv3 scoring, and makes other improvements.

> `cve-check-tool`已被一个新的`cve-update-db`配方和`ref-classes-cve-check`类中的功能所取代。结果使用NVD JSON数据源而不是`cve-check-tool`正在使用的已弃用的XML数据源，支持CVSSv3评分，并做出其他改进。


Additionally, the `CVE_CHECK_CVE_WHITELIST` variable has been replaced by `CVE_CHECK_WHITELIST` (replaced by `CVE_CHECK_IGNORE`{.interpreted-text role="term"} in version 3.5).

> 此外，`CVE_CHECK_CVE_WHITELIST`变量已由`CVE_CHECK_WHITELIST`取代（在3.5版本中由`CVE_CHECK_IGNORE`取代）。

# BitBake Changes {#migration-3.0-bitbake-changes}


The following BitBake changes have occurred.

> 以下BitBake变化已发生。


- `addtask` statements now properly validate dependent tasks. Previously, an invalid task was silently ignored. With this change, the invalid task generates a warning.

> `- `addtask`语句现在可以正确验证依赖任务。以前，无效任务被静默忽略。本次更改，无效任务会生成警告。

- Other invalid `addtask` and `deltask` usages now trigger these warnings: \"multiple target tasks arguments with addtask / deltask\", and \"multiple before/after clauses\".

> 其他无效的`addtask`和`deltask`用法现在会触发以下警告：“使用addtask/deltask的多个目标任务参数”以及“多个before/after子句”。

- The \"multiconfig\" prefix is now shortened to \"mc\". \"multiconfig\" will continue to work, however it may be removed in a future release.

> "multiconfig"前缀现在缩写为"mc"。"multiconfig"仍然可以使用，但可能在将来的版本中被移除。

- The `bitbake -g` command no longer generates a `recipe-depends.dot` file as the contents (i.e. a reprocessed version of `task-depends.dot`) were confusing.

> 命令`bitbake -g`不再生成`recipe-depends.dot`文件，因为其内容（即`task-depends.dot`的重新处理版本）令人困惑。

- The `bb.build.FuncFailed` exception, previously raised by `bb.build.exec_func()` when certain other exceptions have occurred, has been removed. The real underlying exceptions will be raised instead. If you have calls to `bb.build.exec_func()` in custom classes or `tinfoil-using` scripts, any references to `bb.build.FuncFailed` should be cleaned up.

> bb.build.exec_func()函数在某些异常发生时会抛出bb.build.FuncFailed异常，这已经被移除。相应的，将会抛出真正的底层异常。如果你在自定义类或使用tinfoil脚本中调用了bb.build.exec_func()，那么应该清理掉对bb.build.FuncFailed的任何引用。

- Additionally, the `bb.build.exec_func()` no longer accepts the \"pythonexception\" parameter. The function now always raises exceptions. Remove this argument in any calls to `bb.build.exec_func()` in custom classes or scripts.

> 除此之外，`bb.build.exec_func()` 不再接受“pythonexception”参数。该函数现在始终会引发异常。在自定义类或脚本中对`bb.build.exec_func()`的调用中，请删除此参数。

- The `BB_SETSCENE_VERIFY_FUNCTION2` variable is no longer used. In the unlikely event that you have any references to it, they should be removed.

> - `BB_SETSCENE_VERIFY_FUNCTION2` 变量不再使用。 如果您不太可能有任何对它的引用，应该将其删除。

- The `RunQueueExecuteScenequeue` and `RunQueueExecuteTasks` events have been removed since setscene tasks are now executed as part of the normal runqueue. Any event handling code in custom classes or scripts that handles these two events need to be updated.

> 已经移除了`RunQueueExecuteScenequeue`和`RunQueueExecuteTasks`事件，因为setscene任务现在作为正常runqueue的一部分执行。需要更新自定义类或脚本中处理这两个事件的事件处理代码。

- The arguments passed to functions used with `BB_HASHCHECK_FUNCTION`{.interpreted-text role="term"} have changed. If you are using your own custom hash check function, see :yocto\_[git:%60/poky/commit/?id=40a5e193c4ba45c928fccd899415ea56b5417725](git:%60/poky/commit/?id=40a5e193c4ba45c928fccd899415ea56b5417725)\` for details.

> 参数传递给与`BB_HASHCHECK_FUNCTION`一起使用的函数已经改变了。如果您正在使用自己的自定义哈希检查函数，请参阅：yocto_[git:`/poky/commit/?id=40a5e193c4ba45c928fccd899415ea56b5417725`](git:`/poky/commit/?id=40a5e193c4ba45c928fccd899415ea56b5417725`)以获取详细信息。

- Task specifications in `BB_TASKDEPDATA` and class implementations used in signature generator classes now use \"\<fn\>:\<task\>\" everywhere rather than the \".\" delimiter that was being used in some places. This change makes it consistent with all areas in the code. Custom signature generator classes and code that reads `BB_TASKDEPDATA` need to be updated to use \':\' as a separator rather than \'.\'.

> 在`BB_TASKDEPDATA`中的任务规范和签名生成类中使用的类实现现在都使用“<fn>:<task>”，而不是在某些地方使用的“.”分隔符。这个更改使它与代码中的所有区域保持一致。自定义签名生成类和读取`BB_TASKDEPDATA`的代码需要更新以使用':'作为分隔符，而不是'.'。

# Sanity Checks {#migration-3.0-sanity-checks}


The following sanity check changes occurred.

> 以下健全性检查更改发生了。

- `SRC_URI`{.interpreted-text role="term"} is now checked for usage of two problematic items:


  - \"\${PN}\" prefix/suffix use \-\-- warnings always appear if \${PN} is used. You must fix the issue regardless of whether multiconfig or anything else that would cause prefixing/suffixing to happen.

> "\${PN}" 前缀/后缀使用 --- 总是会出现警告，如果使用\${PN}。无论是多种配置还是其他导致前缀/后缀发生的情况，都必须解决问题。

  - Github archive tarballs \-\-- these are not guaranteed to be stable. Consequently, it is likely that the tarballs will be refreshed and thus the `SRC_URI`{.interpreted-text role="term"} checksums will fail to apply. It is recommended that you fetch either an official release tarball or a specific revision from the actual Git repository instead.

> GitHub 归档 tarball 不能保证稳定。因此，tarball 可能会被刷新，从而导致 SRC_URI 校验和无法应用。建议您从实际的 Git 仓库获取官方发布的 tarball 或特定版本。


  Either one of these items now trigger a warning by default. If you wish to disable this check, remove `src-uri-bad` from `WARN_QA`{.interpreted-text role="term"}.

> 如果您希望禁用此检查，请从`WARN_QA`中删除`src-uri-bad`，这些项目中的任何一个现在都会默认触发警告。

- The `file-rdeps` runtime dependency check no longer expands `RDEPENDS`{.interpreted-text role="term"} recursively as there is no mechanism to ensure they can be fully computed, and thus races sometimes result in errors either showing up or not. Thus, you might now see errors for missing runtime dependencies that were previously satisfied recursively. Here is an example: package A contains a shell script starting with `#!/bin/bash` but has no dependency on bash. However, package A depends on package B, which does depend on bash. You need to add the missing dependency or dependencies to resolve the warning.

> 文件-rdeps运行时依赖性检查不再将RDEPENDS递归展开，因为没有机制可以确保它们可以被完全计算，因此竞争有时会导致错误出现或不出现。因此，您现在可能会看到未满足的运行时依赖项的错误，而这些错误以前是通过递归解决的。这里是一个例子：软件包A包含一个以#！/ bin / bash开头的shell脚本，但没有对bash的依赖性。但是，软件包A依赖于软件包B，该软件包确实依赖于bash。您需要添加缺少的依赖项才能解决警告。

- Setting `DEPENDS_${PN}` anywhere (i.e. typically in a recipe) now triggers an error. The error is triggered because `DEPENDS`{.interpreted-text role="term"} is not a package-specific variable unlike RDEPENDS. You should set `DEPENDS`{.interpreted-text role="term"} instead.

> 设置`DEPENDS_${PN}`任何地方（例如通常在食谱中）现在会触发错误。触发错误是因为`DEPENDS`不像RDEPENDS那样是特定于软件包的变量。你应该设置`DEPENDS`而不是它。

- systemd currently does not work well with the musl C library because only upstream officially supports linking the library with glibc. Thus, a warning is shown when building systemd in conjunction with musl.

> systemd 目前无法很好地与 musl C 库配合使用，因为只有上游官方支持将该库与 glibc 连接起来。因此，在将 systemd 与 musl 构建时会出现警告。

# Miscellaneous Changes {#migration-3.0-miscellaneous-changes}


The following miscellaneous changes have occurred.

> 以下发生了各种各样的变化。


- The `gnome` class has been removed because it now does very little. You should update recipes that previously inherited this class to do the following:

> 因为`gnome`类现在几乎没有什么作用，所以已经被移除。您应该更新先前继承此类的食谱，以执行以下操作：

  ```
  inherit gnomebase gtk-icon-cache gconf mime
  ```

- The `meta/recipes-kernel/linux/linux-dtb.inc` file has been removed. This file was previously deprecated in favor of setting `KERNEL_DEVICETREE`{.interpreted-text role="term"} in any kernel recipe and only produced a warning. Remove any `include` or `require` statements pointing to this file.

> 文件`meta/recipes-kernel/linux/linux-dtb.inc`已被移除。此文件以前已被弃用，改为在任何内核配方中设置`KERNEL_DEVICETREE`。任何指向此文件的`include`或`require`语句都应该移除。

- `TARGET_CFLAGS`{.interpreted-text role="term"}, `TARGET_CPPFLAGS`{.interpreted-text role="term"}, `TARGET_CXXFLAGS`{.interpreted-text role="term"}, and `TARGET_LDFLAGS`{.interpreted-text role="term"} are no longer exported to the external environment. This change did not require any changes to core recipes, which is a good indicator that no changes will be required. However, if for some reason the software being built by one of your recipes is expecting these variables to be set, then building the recipe will fail. In such cases, you must either export the variable or variables in the recipe or change the scripts so that exporting is not necessary.

> `TARGET_CFLAGS`{.interpreted-text role="term"}, `TARGET_CPPFLAGS`{.interpreted-text role="term"}, `TARGET_CXXFLAGS`{.interpreted-text role="term"}, 和 `TARGET_LDFLAGS`{.interpreted-text role="term"} 不再被导出到外部环境。这个变化并不需要对核心配方做任何改变，这是一个很好的指标，表明不需要做任何改变。但是，如果由于某些原因，您的配方构建的软件期望这些变量被设置，那么构建配方将会失败。在这种情况下，您必须在配方中导出变量或变量，或者更改脚本以便不需要导出。

- You must change the host distro identifier used in `NATIVELSBSTRING`{.interpreted-text role="term"} to use all lowercase characters even if it does not contain a version number. This change is necessary only if you are not using `ref-classes-uninative`{.interpreted-text role="ref"} and `SANITY_TESTED_DISTROS`{.interpreted-text role="term"}.

> 你必须把 `NATIVELSBSTRING`{.interpreted-text role="term"} 中使用的主机发行版标识符改成全部小写字母，即使它不包含版本号也要如此。只有当你不使用 `ref-classes-uninative`{.interpreted-text role="ref"} 和 `SANITY_TESTED_DISTROS`{.interpreted-text role="term"} 时，才需要这样做。

- In the `base-files` recipe, writing the hostname into `/etc/hosts` and `/etc/hostname` is now done within the main `ref-tasks-install`{.interpreted-text role="ref"} function rather than in the `do_install_basefilesissue` function. The reason for the change is because `do_install_basefilesissue` is more easily overridden without having to duplicate the hostname functionality. If you have done the latter (e.g. in a `base-files` bbappend), then you should remove it from your customized `do_install_basefilesissue` function.

> 在`base-files`配方中，将主机名写入`/etc/hosts`和`/etc/hostname`现在是在主`ref-tasks-install`函数中完成的，而不是在`do_install_basefilesissue`函数中。更改的原因是因为`do_install_basefilesissue`更容易覆盖，而无需重复主机名功能。如果您已经这样做（例如在`base-files` bbappend中），那么您应该从自定义的`do_install_basefilesissue`函数中删除它。
- The `wic --expand` command now uses commas to separate \"key:value\" pairs rather than hyphens.

  ::: note
  ::: title

  Note

> 注意
  :::


  The wic command-line help is not updated.

> 命令行帮助文档没有更新。
  :::


  You must update any scripts or commands where you use `wic --expand` with multiple \"key:value\" pairs.

> 你必须更新任何使用多个“键：值”对的`wic --expand`的脚本或命令。

- UEFI image variable settings have been moved from various places to a central `conf/image-uefi.conf`. This change should not influence any existing configuration as the `meta/conf/image-uefi.conf` in the core metadata sets defaults that can be overridden in the same manner as before.

> UEFI图像变量设置已从多个位置移动到中央的“conf/image-uefi.conf”。此更改不应影响任何现有配置，因为核心元数据中的“meta/conf/image-uefi.conf”设置了默认值，可以以与以前相同的方式进行覆盖。

- `conf/distro/include/world-broken.inc` has been removed. For cases where certain recipes need to be disabled when using the musl C library, these recipes now have `COMPATIBLE_HOST_libc-musl` set with a comment that explains why.

> `- conf/distro/include/world-broken.inc 已被删除。 对于使用musl C库时需要禁用某些配方的情况，这些配方现在设置了COMPATIBLE_HOST_libc-musl，并注释解释了原因。`
