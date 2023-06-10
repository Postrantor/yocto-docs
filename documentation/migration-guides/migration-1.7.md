---
tip: translate by openai@2023-06-07 21:05:57
...
---
title: Release 1.7 (dizzy)
--------------------------

This section provides migration information for moving to the Yocto Project 1.7 Release (codename \"dizzy\") from the prior release.

> 这一节提供了从前一个发行版本迁移到 Yocto 项目 1.7 发行版（代号“dizzy”）的迁移信息。

# Changes to Setting QEMU `PACKAGECONFIG` Options in `local.conf` {#migration-1.7-changes-to-setting-qemu-packageconfig-options}

The QEMU recipe now uses a number of `PACKAGECONFIG`{.interpreted-text role="term"} options to enable various optional features. The method used to set defaults for these options means that existing `local.conf` files will need to be modified to append to `PACKAGECONFIG`{.interpreted-text role="term"} for `qemu-native` and `nativesdk-qemu` instead of setting it. In other words, to enable graphical output for QEMU, you should now have these lines in `local.conf`:

> QEMU 食谱现在使用多个 PACKAGECONFIG 选项来启用各种可选功能。用于设置这些选项的默认值的方法意味着现有的 local.conf 文件将需要修改以附加到 qemu-native 和 nativesdk-qemu 的 PACKAGECONFIG 而不是设置它。换句话说，为了启用 QEMU 的图形输出，您现在应该在 local.conf 中有这些行：

```
PACKAGECONFIG_append_pn-qemu-native = " sdl"
PACKAGECONFIG_append_pn-nativesdk-qemu = " sdl"
```

# Minimum Git version {#migration-1.7-minimum-git-version}

The minimum `overview-manual/development-environment:git`{.interpreted-text role="ref"} version required on the build host is now 1.7.8 because the `--list` option is now required by BitBake\'s Git fetcher. As always, if your host distribution does not provide a version of Git that meets this requirement, you can use the `buildtools`{.interpreted-text role="term"} tarball that does. See the \"`ref-manual/system-requirements:required git, tar, python, make and gcc versions`{.interpreted-text role="ref"}\" section for more information.

> 所需的构建主机上的 Git 最低版本现在是 1.7.8，因为 BitBake 的 Git 获取器现在需要使用 `--list` 选项。如往常一样，如果您的主机分发不提供满足此要求的 Git 版本，您可以使用提供的 `buildtools` tarball。有关更多信息，请参阅“`ref-manual/system-requirements:required git, tar, python, make and gcc versions`”部分。

# Autotools Class Changes {#migration-1.7-autotools-class-changes}

The following `ref-classes-autotools`{.interpreted-text role="ref"} class changes occurred:

> 以下 ref-classes-autotools 类的变化发生了：

- *A separate :term:\`Build Directory\` is now used by default:* The `ref-classes-autotools`{.interpreted-text role="ref"} class has been changed to use a directory for building (`B`{.interpreted-text role="term"}), which is separate from the source directory (`S`{.interpreted-text role="term"}). This is commonly referred to as `B != S`, or an out-of-tree build.

> 默认情况下现在使用单独的“构建目录”：`ref-classes-autotools`{.interpreted-text role="ref"} 类已经改变使用一个用于构建（`B`{.interpreted-text role="term"}）的目录，这个目录与源目录（`S`{.interpreted-text role="term"}）是分离的。这通常被称为 `B != S`，或者一个树外构建。

If the software being built is already capable of building in a directory separate from the source, you do not need to do anything. However, if the software is not capable of being built in this manner, you will need to either patch the software so that it can build separately, or you will need to change the recipe to inherit the `autotools-brokensep <ref-classes-autotools>`{.interpreted-text role="ref"} class instead of the `ref-classes-autotools`{.interpreted-text role="ref"} or `autotools_stage` classes.

> 如果正在构建的软件已经能够在与源代码不同的目录中构建，则不需要做任何事情。但是，如果软件不能以这种方式构建，则需要补丁软件以使其可以单独构建，或者需要更改配方以继承 `autotools-brokensep <ref-classes-autotools>`{.interpreted-text role="ref"} 类而不是 `ref-classes-autotools`{.interpreted-text role="ref"} 或 `autotools_stage` 类。

- The `--foreign` option is no longer passed to `automake` when running `autoconf`: This option tells `automake` that a particular software package does not follow the GNU standards and therefore should not be expected to distribute certain files such as `ChangeLog`, `AUTHORS`, and so forth. Because the majority of upstream software packages already tell `automake` to enable foreign mode themselves, the option is mostly superfluous. However, some recipes will need patches for this change. You can easily make the change by patching `configure.ac` so that it passes \"foreign\" to `AM_INIT_AUTOMAKE()`. See :oe\_[git:%60this](git:%60this) commit \</openembedded-core/commit/?id=01943188f85ce6411717fb5bf702d609f55813f2\>\` for an example showing how to make the patch.

> - 在运行 autoconf 时不再传递 `--foreign` 选项给 automake：此选项告诉 automake，特定的软件包不遵循 GNU 标准，因此不应期望分发诸如 ChangeLog、AUTHORS 等文件。由于大多数上游软件包已经告诉 automake 自行启用 foreign 模式，因此此选项基本上是多余的。但是，一些食谱需要修补此更改。您可以通过修补 configure.ac，使其将“foreign”传递给 AM_INIT_AUTOMAKE()来轻松完成此更改。有关如何制作补丁的示例，请参见：oe_[git:`this`](git:%60this%60)提交 <openembedded-core/commit/?id=01943188f85ce6411717fb5bf702d609f55813f2>。

# Binary Configuration Scripts Disabled {#migration-1.7-binary-configuration-scripts-disabled}

Some of the core recipes that package binary configuration scripts now disable the scripts due to the scripts previously requiring error-prone path substitution. Software that links against these libraries using these scripts should use the much more robust `pkg-config` instead. The list of recipes changed in this version (and their configuration scripts) is as follows:

> 一些核心的配方现在因为这些脚本以前需要错误率高的路径替换而禁用了二进制配置脚本。使用这些脚本链接这些库的软件应该使用更加强大的 `pkg-config` 来代替。这个版本更改的配方(及其配置脚本)列表如下：

```
directfb (directfb-config)
freetype (freetype-config)
gpgme (gpgme-config)
libassuan (libassuan-config)
libcroco (croco-6.0-config)
libgcrypt (libgcrypt-config)
libgpg-error (gpg-error-config)
libksba (ksba-config)
libpcap (pcap-config)
libpcre (pcre-config)
libpng (libpng-config, libpng16-config)
libsdl (sdl-config)
libusb-compat (libusb-config)
libxml2 (xml2-config)
libxslt (xslt-config)
ncurses (ncurses-config)
neon (neon-config)
npth (npth-config)
pth (pth-config)
taglib (taglib-config)
```

Additionally, support for `pkg-config` has been added to some recipes in the previous list in the rare cases where the upstream software package does not already provide it.

> 此外，在极少数情况下，上游软件包本身不提供 pkg-config 支持时，在上面列表中的一些配方中也已经添加了对 pkg-config 的支持。

# `eglibc 2.19` Replaced with `glibc 2.20` {#migration-1.7-glibc-replaces-eglibc}

Because `eglibc` and `glibc` were already fairly close, this replacement should not require any significant changes to other software that links to `eglibc`. However, there were a number of minor changes in `glibc 2.20` upstream that could require patching some software (e.g. the removal of the `_BSD_SOURCE` feature test macro).

> 由于 `eglibc` 和 `glibc` 已经相当接近，因此替换不需要对链接到 `eglibc` 的其他软件做出任何重大更改。但是，`glibc 2.20` 上游的一些小变化可能需要修补一些软件（例如删除 `_BSD_SOURCE` 功能测试宏）。

`glibc 2.20` requires version 2.6.32 or greater of the Linux kernel. Thus, older kernels will no longer be usable in conjunction with it.

> glibc 2.20 需要 Linux 内核的 2.6.32 或更高版本。因此，旧的内核将不再能与之一起使用。

For full details on the changes in `glibc 2.20`, see the upstream release notes [here](https://sourceware.org/ml/libc-alpha/2014-09/msg00088.html).

> 对于 `glibc 2.20` 中的所有变更的详细信息，请参见上游发布说明[此处](https://sourceware.org/ml/libc-alpha/2014-09/msg00088.html)。

# Kernel Module Autoloading {#migration-1.7-kernel-module-autoloading}

The `module_autoload_* <module_autoload>`{.interpreted-text role="term"} variable is now deprecated and a new `KERNEL_MODULE_AUTOLOAD`{.interpreted-text role="term"} variable should be used instead. Also, `module_conf_* <module_conf>`{.interpreted-text role="term"} must now be used in conjunction with a new `KERNEL_MODULE_PROBECONF`{.interpreted-text role="term"} variable. The new variables no longer require you to specify the module name as part of the variable name. This change not only simplifies usage but also allows the values of these variables to be appropriately incorporated into task signatures and thus trigger the appropriate tasks to re-execute when changed. You should replace any references to `module_autoload_*` with `KERNEL_MODULE_AUTOLOAD`{.interpreted-text role="term"}, and add any modules for which `module_conf_*` is specified to `KERNEL_MODULE_PROBECONF`{.interpreted-text role="term"}.

> 变量 `module_autoload_*<module_autoload>`{.interpreted-text role="term"}已被弃用，应使用新的 `KERNEL_MODULE_AUTOLOAD`{.interpreted-text role="term"}变量代替。此外，必须使用新的 `KERNEL_MODULE_PROBECONF`{.interpreted-text role="term"}变量与 `module_conf_*<module_conf>`{.interpreted-text role="term"}一起使用。新变量不再需要您将模块名称作为变量名的一部分指定。此更改不仅简化了使用，而且允许将这些变量的值适当地纳入任务签名中，从而在更改时触发适当的任务重新执行。您应该将任何对 `module_autoload_*` 的引用替换为 `KERNEL_MODULE_AUTOLOAD`{.interpreted-text role="term"}，并将指定 `module_conf_*` 的任何模块添加到 `KERNEL_MODULE_PROBECONF`{.interpreted-text role="term"}中。

# QA Check Changes {#migration-1.7-qa-check-changes}

The following changes have occurred to the QA check process:

> 以下变化已经发生在 QA 检查流程上：

- Additional QA checks `file-rdeps` and `build-deps` have been added in order to verify that file dependencies are satisfied (e.g. package contains a script requiring `/bin/bash`) and build-time dependencies are declared, respectively. For more information, please see the \"`/ref-manual/qa-checks`{.interpreted-text role="doc"}\" chapter.

> 除此之外，为了验证文件依赖性是否满足（例如，包含一个需要“/bin/bash”的脚本），并且声明构建时间依赖关系，已添加了 `文件-rdeps` 和 `构建-deps` 的额外 QA 检查。有关更多信息，请参见“`/ref-manual/qa-checks`{.interpreted-text role="doc"}”章节。

- Package QA checks are now performed during a new `ref-tasks-package_qa`{.interpreted-text role="ref"} task rather than being part of the `ref-tasks-package`{.interpreted-text role="ref"} task. This allows more parallel execution. This change is unlikely to be an issue except for highly customized recipes that disable packaging tasks themselves by marking them as `noexec`. For those packages, you will need to disable the `ref-tasks-package_qa`{.interpreted-text role="ref"} task as well.

> 现在在新的 `ref-tasks-package_qa`{.interpreted-text role="ref"}任务中执行包 QA 检查，而不是作为 `ref-tasks-package`{.interpreted-text role="ref"}任务的一部分。这样可以实现更多的并行执行。除了通过将任务标记为 `noexec` 而禁用打包任务本身的高度定制配方外，此更改不太可能引起问题。对于这些软件包，您还需要禁用 `ref-tasks-package_qa`{.interpreted-text role="ref"}任务。

- Files being overwritten during the `ref-tasks-populate_sysroot`{.interpreted-text role="ref"} task now trigger an error instead of a warning. Recipes should not be overwriting files written to the sysroot by other recipes. If you have these types of recipes, you need to alter them so that they do not overwrite these files.

> 在 `ref-tasks-populate_sysroot` 任务期间被覆盖的文件现在会引发错误而不是警告。食谱不应该覆盖由其他食谱写入的 sysroot 文件。如果您有这些类型的食谱，您需要改变它们，以便它们不会覆盖这些文件。

You might now receive this error after changes in configuration or metadata resulting in orphaned files being left in the sysroot. If you do receive this error, the way to resolve the issue is to delete your `TMPDIR`{.interpreted-text role="term"} or to move it out of the way and then re-start the build. Anything that has been fully built up to that point and does not need rebuilding will be restored from the shared state cache and the rest of the build will be able to proceed as normal.

> 你可能会在配置或元数据发生变化后收到这个错误，导致系统根目录中留下孤立的文件。如果你收到了这个错误，解决方法是删除你的 TMPDIR 或者将其移开，然后重新启动构建。任何已经完全构建完成但不需要重新构建的内容都会从共享状态缓存中恢复，构建的其余部分也会按正常方式继续进行。

# Removed Recipes {#migration-1.7-removed-recipes}

The following recipes have been removed:

> 以下食谱已被移除：

- `x-load`: This recipe has been superseded by U-Boot SPL for all Cortex-based TI SoCs. For legacy boards, the `meta-ti` layer, which contains a maintained recipe, should be used instead.

> - `X-Load`：对于所有基于 Cortex 的 TI SoC，此配方已被 U-Boot SPL 取代。对于传统的板子，应该使用包含维护配方的 `meta-ti` 层替代。

- `ubootchart`: This recipe is obsolete. A `bootchart2` recipe has been added to functionally replace it.

> - `ubootchart`：此食谱已过时。已添加 `bootchart2` 食谱以功能上取代它。

- `linux-yocto 3.4`: Support for the linux-yocto 3.4 kernel has been dropped. Support for the 3.10 and 3.14 kernels remains, while support for version 3.17 has been added.

> 支持 linux-yocto 3.4 内核已经取消。3.10 和 3.14 内核仍然受支持，而 3.17 版本已经添加了支持。

- `eglibc` has been removed in favor of `glibc`. See the \"`migration-1.7-glibc-replaces-eglibc`{.interpreted-text role="ref"}\" section for more information.

> `- ` Eglibc `已被` glibc ` 替代。有关更多信息，请参阅“` migration-1.7-glibc-replaces-eglibc`{.interpreted-text role="ref"}”部分。

# Miscellaneous Changes {#migration-1.7-miscellaneous-changes}

The following miscellaneous change occurred:

> 以下发生了杂项变更。

- The build history feature now writes `build-id.txt` instead of `build-id`. Additionally, `build-id.txt` now contains the full build header as printed by BitBake upon starting the build. You should manually remove old \"build-id\" files from your existing build history repositories to avoid confusion. For information on the build history feature, see the \"`dev-manual/build-quality:maintaining build output quality`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 现在，构建历史功能会写入 `build-id.txt` 而不是 `build-id`。此外，`build-id.txt` 现在包含 BitBake 启动构建时打印的完整构建头。您应该从现有的构建历史存储库中手动删除旧的“build-id”文件，以避免混淆。有关构建历史功能的信息，请参阅 Yocto 项目开发任务手册中的“维护构建输出质量”部分。
