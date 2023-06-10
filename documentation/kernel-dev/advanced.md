---
tip: translate by openai@2023-06-10 09:59:35
...
---
title: Working with Advanced Metadata (`yocto-kernel-cache`)
----------------------------------------

# Overview

In addition to supporting configuration fragments and patches, the Yocto Project kernel tools also support rich `Metadata` that you can use to define complex policies and Board Support Package (BSP) support. The purpose of the Metadata and the tools that manage it is to help you manage the complexity of the configuration and sources used to support multiple BSPs and Linux kernel types.

> 除了支持配置片段和补丁之外，Yocto Project 内核工具还支持丰富的元数据，您可以使用它来定义复杂的策略和板支持包(BSP)支持。元数据及其管理工具的目的是帮助您管理支持多个 BSP 和 Linux 内核类型所使用的配置和源的复杂性。

Kernel Metadata exists in many places. One area in the `overview-manual/development-environment:yocto project source repositories` is the `yocto-kernel-cache` Git repository. You can find this repository grouped under the \"Yocto Linux Kernel\" heading in the :yocto_[git:%60Yocto](git:%60Yocto) Project Source Repositories \<\>\`.

> 内核元数据存在于许多地方。在“overview-manual/development-environment:yocto project source repositories”中的一个区域是“yocto-kernel-cache”Git 存储库。您可以在 Yocto Linux Kernel 标题下找到该存储库，在 Yocto [git:`Yocto`]项目源存储库 <> 中。

Kernel development tools (\"kern-tools\") are also available in the Yocto Project Source Repositories under the \"Yocto Linux Kernel\" heading in the `yocto-kernel-tools` Git repository. The recipe that builds these tools is `meta/recipes-kernel/kern-tools/kern-tools-native_git.bb` in the `Source Directory` (e.g. `poky`).

> 开发内核工具(“kern-tools”)也可以在 Yocto Project 源代码仓库中的“Yocto Linux Kernel”下载，在 yocto-kernel-tools Git 仓库中。构建这些工具的配方是源目录(例如“poky”)中的 `meta/recipes-kernel/kern-tools/kern-tools-native_git.bb`。

# Using Kernel Metadata in a Recipe

As mentioned in the introduction, the Yocto Project contains kernel Metadata, which is located in the `yocto-kernel-cache` Git repository. This Metadata defines Board Support Packages (BSPs) that correspond to definitions in linux-yocto recipes for corresponding BSPs. A BSP consists of an aggregation of kernel policy and enabled hardware-specific features. The BSP can be influenced from within the linux-yocto recipe.

> 在介绍中提到，Yocto 项目包含内核元数据，位于 `yocto-kernel-cache` Git 存储库中。此元数据定义了与 linux-yocto 配方中相应的板支持包(BSP)对应的板支持包(BSP)。BSP 由内核策略和启用的硬件特定功能的聚合组成。BSP 可以从 linux-yocto 配方内部受到影响。

::: note
::: title
Note
:::

A Linux kernel recipe that contains kernel Metadata (e.g. inherits from the `linux-yocto.inc` file) is said to be a \"linux-yocto style\" recipe.
:::

Every linux-yocto style recipe must define the `KMACHINE` variable. This variable is typically set to the same value as the `MACHINE` variable, which is used by `BitBake`. However, in some cases, the variable might instead refer to the underlying platform of the `MACHINE`.

> 每个 linux-yocto 风格的 recipes 都必须定义 `KMACHINE` 变量。该变量通常设置为与 `BitBake` 使用的 `MACHINE` 变量相同的值。但是，在某些情况下，该变量可能指的是 `MACHINE` 的基础平台。

Multiple BSPs can reuse the same `KMACHINE` name if they are built using the same BSP description. Multiple Corei7-based BSPs could share the same \"intel-corei7-64\" value for `KMACHINE`. It is important to realize that `KMACHINE` is just for kernel mapping, while `MACHINE` is the machine type within a BSP Layer. Even with this distinction, however, these two variables can hold the same value. See the \"`kernel-dev/advanced:bsp descriptions`\" section for more information.

> 多个 BSP 可以在使用相同的 BSP 描述时重用相同的 KMACHINE 名称。多个基于 Corei7 的 BSP 可以共享相同的"intel-corei7-64"值作为 KMACHINE。重要的是要意识到，KMACHINE 仅用于内核映射，而 MACHINE 是 BSP 层中的机器类型。即使有这种区别，但这两个变量也可以具有相同的值。有关更多信息，请参阅“kernel-dev / advanced：bsp descriptions”部分。

Every linux-yocto style recipe must also indicate the Linux kernel source repository branch used to build the Linux kernel. The `KBRANCH` variable must be set to indicate the branch.

> 每个 Linux-Yocto 风格的配方都必须指明用于构建 Linux 内核的 Linux 内核源代码库分支。必须设置 `KBRANCH` 变量来指示分支。

::: note
::: title
Note
:::

You can use the `KBRANCH` value to define an alternate branch typically with a machine override as shown here from the `meta-yocto-bsp` layer:

```
KBRANCH:edgerouter = "standard/edgerouter"
```

:::

The linux-yocto style recipes can optionally define the following variables:

> - `KERNEL_FEATURES`
> - `LINUX_KERNEL_TYPE`

`LINUX_KERNEL_TYPE` defines the kernel type to be used in assembling the configuration. If you do not specify a `LINUX_KERNEL_TYPE`, it defaults to \"standard\". Together with `KMACHINE`, `LINUX_KERNEL_TYPE` defines the search arguments used by the kernel tools to find the appropriate description within the kernel Metadata with which to build out the sources and configuration. The linux-yocto recipes define \"standard\", \"tiny\", and \"preempt-rt\" kernel types. See the \"`kernel-dev/advanced:kernel types`\" section for more information on kernel types.

> LINUX_KERNEL_TYPE 定义用于组装配置的内核类型。如果没有指定 LINUX_KERNEL_TYPE，它默认为“标准”。与 KMACHINE 一起，LINUX_KERNEL_TYPE 定义内核工具用于查找适当描述的搜索参数，以便构建源和配置。linux-yocto 配方定义了“标准”，“小”和“抢先-rt”内核类型。有关内核类型的更多信息，请参见“kernel-dev/advanced：kernel types”部分。

During the build, the kern-tools search for the BSP description file that most closely matches the `KMACHINE` and `LINUX_KERNEL_TYPE` variables passed in from the recipe. The tools use the first BSP description they find that matches both variables. If the tools cannot find a match, they issue a warning.

> 在构建过程中，kern-tools 会搜索与从配方传入的 `KMACHINE` 和 `LINUX_KERNEL_TYPE` 变量最接近的 BSP 描述文件。工具会使用第一个与两个变量都匹配的 BSP 描述。如果工具找不到匹配的，它们会发出警告。

The tools first search for the `KMACHINE` and then for the `LINUX_KERNEL_TYPE`. If the tools cannot find a partial match, they will use the sources from the `KBRANCH` and any configuration specified in the `SRC_URI`.

> 首先，工具会搜索 `KMACHINE`，然后搜索 `LINUX_KERNEL_TYPE`。如果工具找不到部分匹配，它们将使用 `KBRANCH` 中的源以及 `SRC_URI` 中指定的任何配置。

You can use the `KERNEL_FEATURES` variable to include features (configuration fragments, patches, or both) that are not already included by the `KMACHINE` and `LINUX_KERNEL_TYPE` variable combination. For example, to include a feature specified as \"features/netfilter/netfilter.scc\", specify:

> 你可以使用 `KERNEL_FEATURES` 变量来包含尚未由 `KMACHINE` 和 `LINUX_KERNEL_TYPE` 变量组合包含的特性(配置片段，补丁或两者)。例如，要包含一个指定为“features/netfilter/netfilter.scc”的特性，请指定：

```
KERNEL_FEATURES += "features/netfilter/netfilter.scc"
```

To include a feature called \"cfg/sound.scc\" just for the `qemux86` machine, specify:

```
KERNEL_FEATURES:append:qemux86 = " cfg/sound.scc"
```

The value of the entries in `KERNEL_FEATURES` are dependent on their location within the kernel Metadata itself. The examples here are taken from the `yocto-kernel-cache` repository. Each branch of this repository contains \"features\" and \"cfg\" subdirectories at the top-level. For more information, see the \"`kernel-dev/advanced:kernel metadata syntax`\" section.

> 值 `KERNEL_FEATURES` 中的条目取决于它们在内核元数据中的位置。这里的示例是从 `yocto-kernel-cache` 存储库中获取的。此存储库的每个分支在顶级位置都包含“功能”和“cfg”子目录。有关更多信息，请参阅“`kernel-dev/advanced：kernel metadata syntax`”部分。

# Kernel Metadata Syntax

The kernel Metadata consists of three primary types of files: `scc` [^1] description files, configuration fragments, and patches. The `scc` files define variables and include or otherwise reference any of the three file types. The description files are used to aggregate all types of kernel Metadata into what ultimately describes the sources and the configuration required to build a Linux kernel tailored to a specific machine.

> 内核元数据由三种主要类型的文件组成：`scc`[^1]描述文件、配置片段和补丁。`scc` 文件定义变量，并包含或引用任何三种文件类型。描述文件用于将所有类型的内核元数据聚合到最终描述构建针对特定机器的 Linux 内核所需的源和配置的内容中。

The `scc` description files are used to define two fundamental types of kernel Metadata:

- Features
- Board Support Packages (BSPs)

Features aggregate sources in the form of patches and configuration fragments into a modular reusable unit. You can use features to implement conceptually separate kernel Metadata descriptions such as pure configuration fragments, simple patches, complex features, and kernel types. `kernel-dev/advanced:kernel types` define general kernel features and policy to be reused in the BSPs.

> 特性将源以补丁和配置片段的形式聚合成模块化可重用的单元。您可以使用特性来实现概念上分离的内核元数据描述，例如纯配置片段、简单补丁、复杂功能和内核类型。`kernel-dev/advanced：kernel types` 定义用于在 BSP 中重用的常见内核特性和策略。

BSPs define hardware-specific features and aggregate them with kernel types to form the final description of what will be assembled and built.

While the kernel Metadata syntax does not enforce any logical separation of configuration fragments, patches, features or kernel types, best practices dictate a logical separation of these types of Metadata. The following Metadata file hierarchy is recommended:

> 尽管内核元数据语法不强制配置片段、补丁、特性或内核类型的逻辑分离，但最佳实践建议这些类型的元数据进行逻辑分离。推荐以下元数据文件层次结构：

```
base/
   bsp/
   cfg/
   features/
   ktypes/
   patches/
```

The `bsp` directory contains the `kernel-dev/advanced:bsp descriptions`. The remaining directories all contain \"features\". Separating `bsp` from the rest of the structure aids conceptualizing intended usage.

> bsp 目录包含 `kernel-dev/advanced:bsp descriptions`。其余的目录都包含“功能”。将 bsp 与其余的结构分开，有助于概念化预期的用途。

Use these guidelines to help place your `scc` description files within the structure:

- If your file contains only configuration fragments, place the file in the `cfg` directory.
- If your file contains only source-code fixes, place the file in the `patches` directory.
- If your file encapsulates a major feature, often combining sources and configurations, place the file in `features` directory.
- If your file aggregates non-hardware configuration and patches in order to define a base kernel policy or major kernel type to be reused across multiple BSPs, place the file in `ktypes` directory.

> 如果您的文件汇集了非硬件配置和补丁，以便定义可在多个 BSP 之间重用的基础内核策略或主要内核类型，请将文件放置在 `ktypes` 目录中。

These distinctions can easily become blurred \-\-- especially as out-of-tree features slowly merge upstream over time. Also, remember that how the description files are placed is a purely logical organization and has no impact on the functionality of the kernel Metadata. There is no impact because all of `cfg`, `features`, `patches`, and `ktypes`, contain \"features\" as far as the kernel tools are concerned.

> 这些区分很容易模糊掉——特别是随着隔离树上的特性慢慢地合并到上游。还要记住，描述文件的放置是完全逻辑的组织，对内核元数据功能没有影响。没有影响是因为所有的 `cfg`、`features`、`patches` 和 `ktypes` 都包含“特性”，就内核工具而言。

Paths used in kernel Metadata files are relative to base, which is either `FILESEXTRAPATHS` if you are creating Metadata in `recipe-space <kernel-dev/advanced:recipe-space metadata>`.

> 路径在内核元数据文件中使用的是相对于基础的，如果您在 recipe-space<kernel-dev/advanced:recipe-space metadata> 中创建元数据，则基础是 FILESEXTRAPATHS，如果您在 recipe-space 之外创建元数据，则基础是 yocto-kernel-cache 的顶级目录。

## Configuration

The simplest unit of kernel Metadata is the configuration-only feature. This feature consists of one or more Linux kernel configuration parameters in a configuration fragment file (`.cfg`) and a `.scc` file that describes the fragment.

> 最简单的内核元数据单元是仅配置特性。该特性由一个或多个 Linux 内核配置参数组成，这些参数位于配置片段文件(`.cfg`)中，以及描述片段的 `.scc` 文件中。

As an example, consider the Symmetric Multi-Processing (SMP) fragment used with the `linux-yocto-4.12` kernel as defined outside of the recipe space (i.e. `yocto-kernel-cache`). This Metadata consists of two files: `smp.scc` and `smp.cfg`. You can find these files in the `cfg` directory of the `yocto-4.12` branch in the `yocto-kernel-cache` Git repository:

> 例如，使用“linux-yocto-4.12”内核定义的 Symmetric Multi-Processing(SMP)片段，在配方空间之外定义。此元数据由两个文件组成：“smp.scc”和“smp.cfg”。您可以在“yocto-kernel-cache”Git 存储库的“yocto-4.12”分支的“cfg”目录中找到这些文件：

```
cfg/smp.scc:
   define KFEATURE_DESCRIPTION "Enable SMP for 32 bit builds"
   define KFEATURE_COMPATIBILITY all

   kconf hardware smp.cfg

cfg/smp.cfg:
   CONFIG_SMP=y
   CONFIG_SCHED_SMT=y
   # Increase default NR_CPUS from 8 to 64 so that platform with
   # more than 8 processors can be all activated at boot time
   CONFIG_NR_CPUS=64
   # The following is needed when setting NR_CPUS to something
   # greater than 8 on x86 architectures, it should be automatically
   # disregarded by Kconfig when using a different arch
   CONFIG_X86_BIGSMP=y
```

You can find general information on configuration fragment files in the \"`kernel-dev/common:creating configuration fragments`\" section.

> 你可以在“kernel-dev/common：创建配置片段”部分找到有关配置片段文件的一般信息。

Within the `smp.scc` file, the `KFEATURE_DESCRIPTION` statement provides a short description of the fragment. Higher level kernel tools use this description.

> 在 `smp.scc` 文件中，`KFEATURE_DESCRIPTION` 语句提供了片段的简短描述。更高级别的内核工具使用此描述。

Also within the `smp.scc` file, the `kconf` command includes the actual configuration fragment in an `.scc` file, and the \"hardware\" keyword identifies the fragment as being hardware enabling, as opposed to general policy, which would use the \"non-hardware\" keyword. The distinction is made for the benefit of the configuration validation tools, which warn you if a hardware fragment overrides a policy set by a non-hardware fragment.

> 在 `smp.scc` 文件中，`kconf` 命令将实际配置片段包含在 `.scc` 文件中，而“硬件”关键字标识该片段是硬件启用，而不是一般策略，这将使用“非硬件”关键字。为了便于配置验证工具，我们做出了这种区分，如果硬件片段覆盖了非硬件片段设置的策略，它将警告您。

::: note
::: title
Note
:::

The description file can include multiple `kconf` statements, one per fragment.
:::

As described in the \"`kernel-dev/common:validating configuration`\" section, you can use the following BitBake command to audit your configuration:

> 根据“kernel-dev / common：验证配置”部分的描述，您可以使用以下 BitBake 命令来审核您的配置：

```
$ bitbake linux-yocto -c kernel_configcheck -f
```

## Patches

Patch descriptions are very similar to configuration fragment descriptions, which are described in the previous section. However, instead of a `.cfg` file, these descriptions work with source patches (i.e. `.patch` files).

> 补丁描述与前一节中描述的配置片段描述非常相似。但是，这些描述不是使用 `.cfg` 文件，而是使用源补丁(即 `.patch` 文件)。

A typical patch includes a description file and the patch itself. As an example, consider the build patches used with the `linux-yocto-4.12` kernel as defined outside of the recipe space (i.e. `yocto-kernel-cache`). This Metadata consists of several files: `build.scc` and a set of `*.patch` files. You can find these files in the `patches/build` directory of the `yocto-4.12` branch in the `yocto-kernel-cache` Git repository.

> 一个典型的补丁通常包含一个描述文件和补丁本身。例如，考虑使用 `linux-yocto-4.12` 内核在配方空间之外定义的构建补丁。这个元数据包括几个文件：`build.scc` 和一组 `*.patch` 文件。您可以在 `yocto-kernel-cache` Git 存储库中的 `yocto-4.12` 分支的 `patches/build` 目录中找到这些文件。

The following listings show the `build.scc` file and part of the `modpost-mask-trivial-warnings.patch` file:

```
patches/build/build.scc:
   patch arm-serialize-build-targets.patch
   patch powerpc-serialize-image-targets.patch
   patch kbuild-exclude-meta-directory-from-distclean-processi.patch

   # applied by kgit
   # patch kbuild-add-meta-files-to-the-ignore-li.patch

   patch modpost-mask-trivial-warnings.patch
   patch menuconfig-check-lxdiaglog.sh-Allow-specification-of.patch

patches/build/modpost-mask-trivial-warnings.patch:
   From bd48931bc142bdd104668f3a062a1f22600aae61 Mon Sep 17 00:00:00 2001
   From: Paul Gortmaker <paul.gortmaker@windriver.com>
   Date: Sun, 25 Jan 2009 17:58:09 -0500
   Subject: [PATCH] modpost: mask trivial warnings

   Newer HOSTCC will complain about various stdio fcns because
                     .
                     .
                     .
         char *dump_write = NULL, *files_source = NULL;
         int opt;
   --
   2.10.1

   generated by cgit v0.10.2 at 2017-09-28 15:23:23 (GMT)
```

The description file can include multiple patch statements where each statement handles a single patch. In the example `build.scc` file, there are five patch statements for the five patches in the directory.

> 描述文件可以包括多个补丁语句，每个语句处理一个补丁。在 `build.scc` 文件中，为目录中的五个补丁编写了五个补丁语句。

You can create a typical `.patch` file using `diff -Nurp` or `git format-patch` commands. For information on how to create patches, see the \"``kernel-dev/common:using \`\`devtool\`\` to patch the kernel``\" sections.

> 你可以使用 `diff -Nurp` 或 `git format-patch` 命令创建一个典型的 `.patch` 文件。要了解如何创建补丁，请参阅“kernel-dev/common：使用 `devtool` 来修补内核”和“kernel-dev/common：使用传统内核开发来修补内核”部分。

## Features

Features are complex kernel Metadata types that consist of configuration fragments, patches, and possibly other feature description files. As an example, consider the following generic listing:

> 特征是由配置片段、补丁以及可能其他特征描述文件组成的复杂内核元数据类型。例如，考虑以下通用列表：

```
features/myfeature.scc
   define KFEATURE_DESCRIPTION "Enable myfeature"

   patch 0001-myfeature-core.patch
   patch 0002-myfeature-interface.patch

   include cfg/myfeature_dependency.scc
   kconf non-hardware myfeature.cfg
```

This example shows how the `patch` and `kconf` commands are used as well as how an additional feature description file is included with the `include` command.

> 这个例子展示了如何使用 `patch` 和 `kconf` 命令，以及如何使用 `include` 命令包含一个附加的功能描述文件。

Typically, features are less granular than configuration fragments and are more likely than configuration fragments and patches to be the types of things you want to specify in the `KERNEL_FEATURES` variable of the Linux kernel recipe. See the \"`kernel-dev/advanced:using kernel metadata in a recipe`\" section earlier in the manual.

> 一般来说，特性比配置片段更不细致，比配置片段和补丁更可能是您想要在 Linux 内核配方的“KERNEL_FEATURES”变量中指定的类型。请参阅本手册前面的“kernel-dev / advanced：在配方中使用内核元数据”部分。

## Kernel Types

A kernel type defines a high-level kernel policy by aggregating non-hardware configuration fragments with patches you want to use when building a Linux kernel of a specific type (e.g. a real-time kernel). Syntactically, kernel types are no different than features as described in the \"`kernel-dev/advanced:features` directive includes the `poky/meta/recipes-kernel/linux/linux-yocto.inc` file, which has the following statement that defines the default kernel type:

> 一个内核类型通过聚合非硬件配置片段和要在构建特定类型的 Linux 内核时使用的补丁，来定义高级内核策略(例如实时内核)。语法上，内核类型与“kernel-dev/advanced：features”部分中描述的功能没有区别。内核配方中的 `LINUX_KERNEL_TYPE` 变量选择内核类型。例如，在 `poky/meta/recipes-kernel/linux` 中的 `linux-yocto_4.12.bb` 内核配方中，一个 `require <bitbake-user-manual/bitbake-user-manual-metadata:\`\`require\`\` directive>`指令包括` poky/meta/recipes-kernel/linux/linux-yocto.inc` 文件，该文件具有定义默认内核类型的以下语句：

```
LINUX_KERNEL_TYPE ??= "standard"
```

Another example would be the real-time kernel (i.e. `linux-yocto-rt_4.12.bb`). This kernel recipe directly sets the kernel type as follows:

```
LINUX_KERNEL_TYPE = "preempt-rt"
```

::: note
::: title
Note
:::

You can find kernel recipes in the `meta/recipes-kernel/linux` directory of the `overview-manual/development-environment:yocto project source repositories`\" section for more information.

> 你可以在 `overview-manual/development-environment:yocto project source repositories`”部分。
> :::

Three kernel types (\"standard\", \"tiny\", and \"preempt-rt\") are supported for Linux Yocto kernels:

- \"standard\": Includes the generic Linux kernel policy of the Yocto Project linux-yocto kernel recipes. This policy includes, among other things, which file systems, networking options, core kernel features, and debugging and tracing options are supported.

> "标准"：包括 Yocto 项目 linux-yocto 内核 recipes 的通用 Linux 内核策略。该策略包括，但不限于，支持的文件系统，网络选项，核心内核功能以及调试和跟踪选项。

- \"preempt-rt\": Applies the `PREEMPT_RT` patches and the configuration options required to build a real-time Linux kernel. This kernel type inherits from the \"standard\" kernel type.

> - \"PREEMPT-RT\": 应用 `PREEMPT_RT` 补丁和构建实时 Linux 内核所需的配置选项。此内核类型继承自“标准”内核类型。

- \"tiny\": Defines a bare minimum configuration meant to serve as a base for very small Linux kernels. The \"tiny\" kernel type is independent from the \"standard\" configuration. Although the \"tiny\" kernel type does not currently include any source changes, it might in the future.

> “小型”：定义了一个最低配置，旨在为非常小的 Linux 内核提供基础。“小型”内核类型与“标准”配置独立。尽管“小型”内核类型目前不包括任何源代码更改，但未来可能会有。

For any given kernel type, the Metadata is defined by the `.scc` (e.g. `standard.scc`). Here is a partial listing for the `standard.scc` file, which is found in the `ktypes/standard` directory of the `yocto-kernel-cache` Git repository:

> 对于任何给定的内核类型，元数据由 `.scc`(例如 `standard.scc`)定义。以下是位于 `yocto-kernel-cache` Git 存储库的 `ktypes / standard` 目录中的 `standard.scc` 文件的部分列表：

```
# Include this kernel type fragment to get the standard features and
# configuration values.

# Note: if only the features are desired, but not the configuration
#       then this should be included as:
#             include ktypes/standard/standard.scc nocfg
#       if no chained configuration is desired, include it as:
#             include ktypes/standard/standard.scc nocfg inherit



include ktypes/base/base.scc
branch standard

kconf non-hardware standard.cfg

include features/kgdb/kgdb.scc
           .
           .
           .

include cfg/net/ip6_nf.scc
include cfg/net/bridge.scc

include cfg/systemd.scc

include features/rfkill/rfkill.scc
```

As with any `.scc` file, a kernel type definition can aggregate other `.scc` files with `include` commands. These definitions can also directly pull in configuration fragments and patches with the `kconf` and `patch` commands, respectively.

> 随着任何 `.scc` 文件一样，内核类型定义可以使用 `include` 命令将其他 `.scc` 文件聚合在一起。这些定义也可以直接使用 `kconf` 和 `patch` 命令拉取配置片段和补丁。

::: note
::: title
Note
:::

It is not strictly necessary to create a kernel type `.scc` file. The Board Support Package (BSP) file can implicitly define the kernel type using a `define` `KTYPE` `myktype` line. See the \"`kernel-dev/advanced:bsp descriptions`\" section for more information.

> 不严格必要创建一个内核类型的 `.scc` 文件。板支持包(BSP)文件可以使用 `define` `KTYPE` `myktype` 行隐式定义内核类型。有关更多信息，请参见“kernel-dev / advanced：bsp 描述”部分。
> :::

## BSP Descriptions

BSP descriptions (i.e. `*.scc` files) combine kernel types with hardware-specific features. The hardware-specific Metadata is typically defined independently in the BSP layer, and then aggregated with each supported kernel type.

> BSP 描述(即“*.scc”文件)将内核类型与硬件特定功能结合起来。硬件特定的元数据通常在 BSP 层中单独定义，然后与每个支持的内核类型聚合在一起。

::: note
::: title
Note
:::

For BSPs supported by the Yocto Project, the BSP description files are located in the `bsp` directory of the `yocto-kernel-cache` repository organized under the \"Yocto Linux Kernel\" heading in the :yocto_[git:%60Yocto](git:%60Yocto) Project Source Repositories \<\>\`.

> 对于由 Yocto 项目支持的 BSP，BSP 描述文件位于 Yocto 项目源存储库的“Yocto Linux 内核”下的 yocto-kernel-cache 存储库的 `bsp` 目录中。
> :::

This section overviews the BSP description structure, the aggregation concepts, and presents a detailed example using a BSP supported by the Yocto Project (i.e. BeagleBone Board). For complete information on BSP layer file hierarchy, see the `/bsp-guide/index`.

> 这一部分概述了 BSP 描述结构、聚合概念，并举出了一个使用 Yocto 项目支持的 BSP(例如 BeagleBone 板)的详细示例。有关 BSP 层次文件层次结构的完整信息，请参阅/bsp-guide/index。

### Description Overview

For simplicity, consider the following root BSP layer description files for the BeagleBone board. These files employ both a structure and naming convention for consistency. The naming convention for the file is as follows:

> 为了简化，考虑以下 BeagleBone 板的根 BSP 层描述文件。这些文件采用结构和命名约定以保持一致性。文件的命名约定如下：

```
bsp_root_name-kernel_type.scc
```

Here are some example root layer BSP filenames for the BeagleBone Board BSP, which is supported by the Yocto Project:

```
beaglebone-standard.scc
beaglebone-preempt-rt.scc
```

Each file uses the root name (i.e \"beaglebone\") BSP name followed by the kernel type.

Examine the `beaglebone-standard.scc` file:

```
define KMACHINE beaglebone
define KTYPE standard
define KARCH arm

include ktypes/standard/standard.scc
branch beaglebone

include beaglebone.scc

# default policy for standard kernels
include features/latencytop/latencytop.scc
include features/profiling/profiling.scc
```

Every top-level BSP description file should define the `KMACHINE`, `KTYPE`, and `KARCH` variables. These variables allow the OpenEmbedded build system to identify the description as meeting the criteria set by the recipe being built. This example supports the \"beaglebone\" machine for the \"standard\" kernel and the \"arm\" architecture.

> 每个顶层 BSP 描述文件都应该定义 `KMACHINE`、`KTYPE` 和 `KARCH` 变量。这些变量允许 OpenEmbedded 构建系统识别描述符符合正在构建的配方设定的标准。此示例支持“beaglebone”机器的“标准”内核和“arm”架构。

Be aware that there is no hard link between the `KTYPE` variable and a kernel type description file. Thus, if you do not have the kernel type defined in your kernel Metadata as it is here, you only need to ensure that the `LINUX_KERNEL_TYPE` variable in the kernel recipe and the `KTYPE` variable in the BSP description file match.

> 请注意，`KTYPE` 变量与内核类型描述文件没有硬链接。因此，如果您在内核元数据中没有定义内核类型，如此处所示，您只需确保内核配方中的 `LINUX_KERNEL_TYPE` 变量与 BSP 描述文件中的 `KTYPE` 变量匹配即可。

To separate your kernel policy from your hardware configuration, you include a kernel type (`ktype`), such as \"standard\". In the previous example, this is done using the following:

> 将内核策略与硬件配置分开，您可以包括内核类型(`ktype`)，例如“标准”。在上面的示例中，可以使用以下方法实现：

```
include ktypes/standard/standard.scc
```

This file aggregates all the configuration fragments, patches, and features that make up your standard kernel policy. See the \"`kernel-dev/advanced:kernel types`\" section for more information.

> 这个文件汇总了所有构成您标准内核策略的配置片段、补丁和功能。有关更多信息，请参见“kernel-dev/advanced：kernel types”部分。

To aggregate common configurations and features specific to the kernel for [mybsp], use the following:

```
include mybsp.scc
```

You can see that in the BeagleBone example with the following:

```
include beaglebone.scc
```

For information on how to break a complete `.config` file into the various configuration fragments, see the \"`kernel-dev/common:creating configuration fragments`\" section.

> 要了解如何将完整的 `.config` 文件分解为各种配置片段的信息，请参阅“kernel-dev/common：创建配置片段”部分。

Finally, if you have any configurations specific to the hardware that are not in a `*.scc` file, you can include them as follows:

```
kconf hardware mybsp-extra.cfg
```

The BeagleBone example does not include these types of configurations. However, the Malta 32-bit board does (\"mti-malta32\"). Here is the `mti-malta32-le-standard.scc` file:

> 示例中的 BeagleBone 不包括这些类型的配置。但是，Malta 32 位板有(“mti-malta32”)。这里是 mti-malta32-le-standard.scc 文件：

```
define KMACHINE mti-malta32-le
define KMACHINE qemumipsel
define KTYPE standard
define KARCH mips

include ktypes/standard/standard.scc
branch mti-malta32

include mti-malta32.scc
kconf hardware mti-malta32-le.cfg
```

### Example

Many real-world examples are more complex. Like any other `.scc` file, BSP descriptions can aggregate features. Consider the Minnow BSP definition given the `linux-yocto-4.4` branch of the `yocto-kernel-cache` (i.e. `yocto-kernel-cache/bsp/minnow/minnow.scc`):

> 许多现实世界的例子更加复杂。就像其他任何 `.scc` 文件一样，BSP 描述可以聚合功能。考虑 `yocto-kernel-cache`(即 `yocto-kernel-cache/bsp/minnow/minnow.scc`)的 `linux-yocto-4.4` 分支中给出的 Minnow BSP 定义：

```
include cfg/x86.scc
include features/eg20t/eg20t.scc
include cfg/dmaengine.scc
include features/power/intel.scc
include cfg/efi.scc
include features/usb/ehci-hcd.scc
include features/usb/ohci-hcd.scc
include features/usb/usb-gadgets.scc
include features/usb/touchscreen-composite.scc
include cfg/timer/hpet.scc
include features/leds/leds.scc
include features/spi/spidev.scc
include features/i2c/i2cdev.scc
include features/mei/mei-txe.scc

# Earlyprintk and port debug requires 8250
kconf hardware cfg/8250.cfg

kconf hardware minnow.cfg
kconf hardware minnow-dev.cfg
```

::: note
::: title
Note
:::

Although the Minnow Board BSP is unused, the Metadata remains and is being used here just as an example.
:::

The `minnow.scc` description file includes a hardware configuration fragment (`minnow.cfg`) specific to the Minnow BSP as well as several more general configuration fragments and features enabling hardware found on the machine. This `minnow.scc` description file is then included in each of the three \"minnow\" description files for the supported kernel types (i.e. \"standard\", \"preempt-rt\", and \"tiny\"). Consider the \"minnow\" description for the \"standard\" kernel type (i.e. `minnow-standard.scc`):

> Minnow.scc 描述文件包括一个特定于 Minnow BSP 的硬件配置片段(minnow.cfg)以及几个更加一般的配置片段和功能，可以在机器上找到。然后，这个 minnow.scc 描述文件被包含在支持的内核类型的三个“minnow”描述文件中(即“标准”，“preempt-rt”和“tiny”)。考虑“标准”内核类型的“minnow”描述(即 minnow-standard.scc)：

```
define KMACHINE minnow
define KTYPE standard
define KARCH i386

include ktypes/standard

include minnow.scc

# Extra minnow configs above the minimal defined in minnow.scc
include cfg/efi-ext.scc
include features/media/media-all.scc
include features/sound/snd_hda_intel.scc

# The following should really be in standard.scc
# USB live-image support
include cfg/usb-mass-storage.scc
include cfg/boot-live.scc

# Basic profiling
include features/latencytop/latencytop.scc
include features/profiling/profiling.scc

# Requested drivers that don't have an existing scc
kconf hardware minnow-drivers-extra.cfg
```

The `include` command midway through the file includes the `minnow.scc` description that defines all enabled hardware for the BSP that is common to all kernel types. Using this command significantly reduces duplication.

> `include` 命令位于文件中部，包括了 `minnow.scc` 描述，它定义了所有可用的硬件，对于所有内核类型来说都是共同的 BSP。使用这个命令可以显著减少重复。

Now consider the \"minnow\" description for the \"tiny\" kernel type (i.e. `minnow-tiny.scc`):

```
define KMACHINE minnow
define KTYPE tiny
define KARCH i386

include ktypes/tiny

include minnow.scc
```

As you might expect, the \"tiny\" description includes quite a bit less. In fact, it includes only the minimal policy defined by the \"tiny\" kernel type and the hardware-specific configuration required for booting the machine along with the most basic functionality of the system as defined in the base \"minnow\" description file.

> 你可能已经预料到，“tiny”描述包含的内容要少得多。实际上，它仅包含由“tiny”内核类型定义的最小策略，以及用于引导计算机的硬件特定配置，以及基于“minnow”描述文件定义的系统的最基本功能。

Notice again the three critical variables: `KMACHINE`, `KTYPE`, and `KARCH`. Of these variables, only `KTYPE` has changed to specify the \"tiny\" kernel type.

> 注意再次看到三个关键变量：`KMACHINE`、`KTYPE` 和 `KARCH`。在这些变量中，只有 `KTYPE` 已经改变以指定“tiny”内核类型。

# Kernel Metadata Location

Kernel Metadata always exists outside of the kernel tree either defined in a kernel recipe (recipe-space) or outside of the recipe. Where you choose to define the Metadata depends on what you want to do and how you intend to work. Regardless of where you define the kernel Metadata, the syntax used applies equally.

> 内核元数据总是存在于内核树之外，无论是在内核配方(recipe-space)中定义还是在配方之外定义。您选择定义内核元数据的位置取决于您想要做什么以及您打算如何工作。无论您在何处定义内核元数据，所使用的语法都是相同的。

If you are unfamiliar with the Linux kernel and only wish to apply a configuration and possibly a couple of patches provided to you by others, the recipe-space method is recommended. This method is also a good approach if you are working with Linux kernel sources you do not control or if you just do not want to maintain a Linux kernel Git repository on your own. For partial information on how you can define kernel Metadata in the recipe-space, see the \"`kernel-dev/common:modifying an existing recipe`\" section.

> 如果你不熟悉 Linux 内核，只想应用由他人提供的配置和可能的几个补丁，建议使用配方空间方法。如果你正在使用你不控制的 Linux 内核源码，或者你不想自己维护 Linux 内核 Git 存储库，这种方法也是一个不错的选择。有关如何在配方空间中定义内核元数据的部分信息，请参见“kernel-dev / common：修改现有配方”部分。

Conversely, if you are actively developing a kernel and are already maintaining a Linux kernel Git repository of your own, you might find it more convenient to work with kernel Metadata kept outside the recipe-space. Working with Metadata in this area can make iterative development of the Linux kernel more efficient outside of the BitBake environment.

> 反之，如果您正在积极开发内核，并且已经维护了自己的 Linux 内核 Git 存储库，那么您可能会发现在 recipes 空间之外保存内核元数据更方便。在这个领域使用元数据可以使 Linux 内核的迭代开发在 BitBake 环境之外更有效率。

## Recipe-Space Metadata

When stored in recipe-space, the kernel Metadata files reside in a directory hierarchy below `FILESEXTRAPATHS`. For a linux-yocto recipe or for a Linux kernel recipe derived by copying :oe_[git:%60meta-skeleton/recipes-kernel/linux/linux-yocto-custom.bb](git:%60meta-skeleton/recipes-kernel/linux/linux-yocto-custom.bb) \</openembedded-core/tree/meta-skeleton/recipes-kernel/linux/linux-yocto-custom.bb\>[ into your layer and modifying it, :term:\`FILESEXTRAPATHS]\" section for more information.

> 当存储在配方空间中时，内核元数据文件位于 `FILESEXTRAPATHS` 下的目录层次结构中。对于 linux-yocto 配方或通过复制:oe_[git:%60meta-skeleton/recipes-kernel/linux/linux-yocto-custom.bb](git:%60meta-skeleton/recipes-kernel/linux/linux-yocto-custom.bb) \</openembedded-core/tree/meta-skeleton/recipes-kernel/linux/linux-yocto-custom.bb\>[到您的层并进行修改而来的 Linux 内核配方，:term:\`FILESEXTRAPATHS]”部分。

Here is an example that shows a trivial tree of kernel Metadata stored in recipe-space within a BSP layer:

```
meta-my_bsp_layer/
`-- recipes-kernel
    `-- linux
        `-- linux-yocto
            |-- bsp-standard.scc
            |-- bsp.cfg
            `-- standard.cfg
```

When the Metadata is stored in recipe-space, you must take steps to ensure BitBake has the necessary information to decide what files to fetch and when they need to be fetched again. It is only necessary to specify the `.scc` files on the `SRC_URI`. BitBake parses them and fetches any files referenced in the `.scc` files by the `include`, `patch`, or `kconf` commands. Because of this, it is necessary to bump the recipe `PR` value when changing the content of files not explicitly listed in the `SRC_URI`.

> 当元数据存储在配方空间中时，您必须采取措施确保 BitBake 具备必要的信息，以决定应该获取哪些文件以及何时需要再次获取它们。只需在 `SRC_URI` 上指定 `.scc` 文件即可。BitBake 解析它们，并通过 `include`、`patch` 或 `kconf` 命令获取 `.scc` 文件中引用的任何文件。因此，在更改 `SRC_URI` 中未明确列出的文件内容时，需要更改配方 `PR` 值。

If the BSP description is in recipe space, you cannot simply list the `*.scc` in the `SRC_URI` statement. You need to use the following form from your kernel append file:

> 如果 BSP 描述在配方空间中，您不能简单地在 `SRC_URI` 语句中列出 `*.scc`。您需要从内核附加文件中使用以下格式：

```
SRC_URI:append:myplatform = " \
    file://myplatform;type=kmeta;destsuffix=myplatform \
    "
```

## Metadata Outside the Recipe-Space

When stored outside of the recipe-space, the kernel Metadata files reside in a separate repository. The OpenEmbedded build system adds the Metadata to the build as a \"type=kmeta\" repository through the `SRC_URI` variable. As an example, consider the following `SRC_URI` statement from the `linux-yocto_5.15.bb` kernel recipe:

> 当存储在配方空间之外时，内核元数据文件位于单独的存储库中。OpenEmbedded 构建系统通过 `SRC_URI` 变量将元数据添加到构建中，作为“ type = kmeta”存储库。作为示例，请考虑以下来自 `linux-yocto_5.15.bb` 内核配方的 `SRC_URI` 语句：

```
SRC_URI = "git://git.yoctoproject.org/linux-yocto.git;name=machine;branch=$;protocol=https \
           git://git.yoctoproject.org/yocto-kernel-cache;type=kmeta;name=meta;branch=yocto-5.15;destsuffix=$;protocol=https"
```

`$`, in this context, is simply used to name the directory into which the Git fetcher places the Metadata. This behavior is no different than any multi-repository `SRC_URI` statement used in a recipe (e.g. see the previous section).

> "$" 在这种情况下，只是用来命名 Git 获取器将元数据放置的目录。这种行为与配方中使用的任何多仓库 `SRC_URI` 语句没有任何不同(例如，参见上一节)。

You can keep kernel Metadata in a \"kernel-cache\", which is a directory containing configuration fragments. As with any Metadata kept outside the recipe-space, you simply need to use the `SRC_URI` statement with the \"type=kmeta\" attribute. Doing so makes the kernel Metadata available during the configuration phase.

> 你可以在一个“内核缓存”中保存内核元数据，这是一个包含配置片段的目录。与保存在配方空间之外的任何元数据一样，你只需要使用带有“type=kmeta”属性的 `SRC_URI` 语句即可。这样做可以在配置阶段访问内核元数据。

If you modify the Metadata, you must not forget to update the `SRCREV` statements in the kernel\'s recipe. In particular, you need to update the `SRCREV_meta` variable to match the commit in the `KMETA` branch you wish to use. Changing the data in these branches and not updating the `SRCREV` statements to match will cause the build to fetch an older commit.

> 如果你修改元数据，你不能忘记更新内核 recipes 中的 `SRCREV` 语句。特别是，你需要更新 `SRCREV_meta` 变量以匹配你希望使用的 `KMETA` 分支中的提交。在这些分支中更改数据而不更新 `SRCREV` 语句将导致构建获取较旧的提交。

# Organizing Your Source

Many recipes based on the `linux-yocto-custom.bb` recipe use Linux kernel sources that have only a single branch. This type of repository structure is fine for linear development supporting a single machine and architecture. However, if you work with multiple boards and architectures, a kernel source repository with multiple branches is more efficient. For example, suppose you need a series of patches for one board to boot. Sometimes, these patches are works-in-progress or fundamentally wrong, yet they are still necessary for specific boards. In these situations, you most likely do not want to include these patches in every kernel you build (i.e. have the patches as part of the default branch). It is situations like these that give rise to multiple branches used within a Linux kernel sources Git repository.

> 许多基于“linux-yocto-custom.bb”配方的 recipes 使用只有单个分支的 Linux 内核源码。这种类型的存储库结构对于支持单个机器和架构的线性开发是很好的。但是，如果您使用多个板和架构，具有多个分支的内核源码存储库更有效。例如，假设您需要一系列补丁来启动板。有时，这些补丁是正在进行中或根本错误的，但它们对特定板来说仍然是必要的。在这种情况下，您最有可能不希望将这些补丁包括在每个内核中(即将补丁作为默认分支的一部分)。正是这种情况使 Linux 内核源码 Git 存储库中使用了多个分支。

Here are repository organization strategies maximizing source reuse, removing redundancy, and logically ordering your changes. This section presents strategies for the following cases:

> 这里有一些仓库组织策略，可以最大化源代码的重用，消除冗余，并合理排列你的更改。本节将为以下情况提供策略：

- Encapsulating patches in a feature description and only including the patches in the BSP descriptions of the applicable boards.
- Creating a machine branch in your kernel source repository and applying the patches on that branch only.
- Creating a feature branch in your kernel source repository and merging that branch into your BSP when needed.

The approach you take is entirely up to you and depends on what works best for your development model.

## Encapsulating Patches

If you are reusing patches from an external tree and are not working on the patches, you might find the encapsulated feature to be appropriate. Given this scenario, you do not need to create any branches in the source repository. Rather, you just take the static patches you need and encapsulate them within a feature description. Once you have the feature description, you simply include that into the BSP description as described in the \"`kernel-dev/advanced:bsp descriptions`\" section.

> 如果您正在重用外部树上的补丁，而不是在补丁上工作，您可能会认为封装功能是合适的。在这种情况下，您不需要在源存储库中创建任何分支。而是，您只需要获取所需的静态补丁，并将它们封装在功能描述中。一旦您有了功能描述，您只需按照“kernel-dev / advanced：bsp descriptions”部分中的说明将其包含在 BSP 描述中即可。

You can find information on how to create patches and BSP descriptions in the \"`kernel-dev/advanced:patches`\" sections.

> 你可以在“kernel-dev / advanced：patches”和“kernel-dev / advanced：bsp 描述”部分找到有关如何创建补丁和 BSP 描述的信息。

## Machine Branches

When you have multiple machines and architectures to support, or you are actively working on board support, it is more efficient to create branches in the repository based on individual machines. Having machine branches allows common source to remain in the development branch with any features specific to a machine stored in the appropriate machine branch. This organization method frees you from continually reintegrating your patches into a feature.

> 当您有多台机器和架构需要支持时，或者您正在积极开发板支持，最有效的方法是在存储库中基于单独的机器创建分支。拥有机器分支可以使公共源代码保持在开发分支中，而特定于机器的任何功能都存储在适当的机器分支中。此组织方法可让您免于不断地将补丁重新集成到功能中。

Once you have a new branch, you can set up your kernel Metadata to use the branch a couple different ways. In the recipe, you can specify the new branch as the `KBRANCH` to use for the board as follows:

> 一旦你有了一个新分支，你可以设置你的内核元数据以使用该分支的几种不同方式。在配方中，你可以指定新分支作为“KBRANCH”来使用板，如下所示：

```
KBRANCH = "mynewbranch"
```

Another method is to use the `branch` command in the BSP description:

```
mybsp.scc:
   define KMACHINE mybsp
   define KTYPE standard
   define KARCH i386
   include standard.scc

   branch mynewbranch

   include mybsp-hw.scc
```

If you find yourself with numerous branches, you might consider using a hierarchical branching system similar to what the Yocto Linux Kernel Git repositories use:

> 如果你发现自己有很多分支，你可以考虑使用类似于 Yocto Linux 内核 Git 存储库使用的分层分支系统。

```
common/kernel_type/machine
```

If you had two kernel types, \"standard\" and \"small\" for instance, three machines, and common as `mydir`, the branches in your Git repository might look like this:

> 如果你有两种内核类型，比如“标准”和“小型”，三台机器，以及共同的“mydir”，你的 Git 存储库中的分支可能看起来像这样：

```
mydir/base
mydir/standard/base
mydir/standard/machine_a
mydir/standard/machine_b
mydir/standard/machine_c
mydir/small/base
mydir/small/machine_a
```

This organization can help clarify the branch relationships. In this case, `mydir/standard/machine_a` includes everything in `mydir/base` and `mydir/standard/base`. The \"standard\" and \"small\" branches add sources specific to those kernel types that for whatever reason are not appropriate for the other branches.

> 这个组织可以帮助澄清分支关系。在这种情况下，`mydir/standard/machine_a` 包括 `mydir/base` 和 `mydir/standard/base` 中的所有内容。"标准"和"小"分支添加了那些出于某种原因不适合其他分支的特定内核类型的源代码。

::: note
::: title
Note
:::

The \"base\" branches are an artifact of the way Git manages its data internally on the filesystem: Git will not allow you to use `mydir/standard` and `mydir/standard/machine_a` because it would have to create a file and a directory named \"standard\".

> Git 在文件系统中内部管理其数据的方式导致了“基础”分支的产生：Git 不允许您使用 `mydir/standard` 和 `mydir/standard/machine_a`，因为它必须创建一个名为“standard”的文件和目录。
> :::

## Feature Branches

When you are actively developing new features, it can be more efficient to work with that feature as a branch, rather than as a set of patches that have to be regularly updated. The Yocto Project Linux kernel tools provide for this with the `git merge` command.

> 当你积极开发新功能时，与其将其作为一系列需要定期更新的补丁，不如将其作为分支来处理可能更有效率。Yocto Project Linux 内核工具提供了 `git merge` 命令来实现这一点。

To merge a feature branch into a BSP, insert the `git merge` command after any `branch` commands:

```
mybsp.scc:
   define KMACHINE mybsp
   define KTYPE standard
   define KARCH i386
   include standard.scc

   branch mynewbranch
   git merge myfeature

   include mybsp-hw.scc
```

# SCC Description File Reference

This section provides a brief reference for the commands you can use within an SCC description file (`.scc`):

- `branch [ref]`: Creates a new branch relative to the current branch (typically `$`) using the currently checked-out branch, or \"ref\" if specified.

> `-` 分支[ref]：使用当前检出的分支(通常是 `$`)或指定的“ref”创建一个相对于当前分支的新分支。

- `define`: Defines variables, such as `KMACHINE`, `KTYPE`, `KARCH`, and `KFEATURE_DESCRIPTION`.
- `include SCC_FILE`: Includes an SCC file in the current file. The file is parsed as if you had inserted it inline.
- `kconf [hardware|non-hardware] CFG_FILE`: Queues a configuration fragment for merging into the final Linux `.config` file.
- `git merge GIT_BRANCH`: Merges the feature branch into the current branch.
- `patch PATCH_FILE`: Applies the patch to the current Git branch.

[^1]: `scc` stands for Series Configuration Control, but the naming has less significance in the current implementation of the tooling than it had in the past. Consider `scc` files to be description files.


> [^1]: `scc` 代表系列配置控制，但在当前工具实施中，命名的重要性不及过去。将 `scc` 文件视为描述文件。
>
