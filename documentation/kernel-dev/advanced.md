---
tip: translate by baidu@2023-06-07 10:55:28
...
---
title: Working with Advanced Metadata (`yocto-kernel-cache`)
----------------------------------------

# Overview

In addition to supporting configuration fragments and patches, the Yocto Project kernel tools also support rich `Metadata`{.interpreted-text role="term"} that you can use to define complex policies and Board Support Package (BSP) support. The purpose of the Metadata and the tools that manage it is to help you manage the complexity of the configuration and sources used to support multiple BSPs and Linux kernel types.

> 除了支持配置片段和修补程序外，Yocto Project 内核工具还支持丰富的“元数据”｛.explored text role=“term”｝，您可以使用它来定义复杂的策略和 Board support Package（BSP）支持。元数据及其管理工具的目的是帮助您管理用于支持多个 BSP 和 Linux 内核类型的配置和源的复杂性。

Kernel Metadata exists in many places. One area in the `overview-manual/development-environment:yocto project source repositories`{.interpreted-text role="ref"} is the `yocto-kernel-cache` Git repository. You can find this repository grouped under the \"Yocto Linux Kernel\" heading in the :yocto\_[git:%60Yocto](git:%60Yocto) Project Source Repositories \<\>\`.

> 内核元数据存在于许多地方。“概述手册/开发环境：yocto 项目源存储库”中的一个区域是“yocto 内核缓存”Git 存储库。您可以在：Yocto\_[git:%60Yocto](git:%60Yocto) 项目源存储库\<\>\`中的“Yocto Linux Kernel”标题下找到此存储库。

Kernel development tools (\"kern-tools\") are also available in the Yocto Project Source Repositories under the \"Yocto Linux Kernel\" heading in the `yocto-kernel-tools` Git repository. The recipe that builds these tools is `meta/recipes-kernel/kern-tools/kern-tools-native_git.bb` in the `Source Directory`{.interpreted-text role="term"} (e.g. `poky`).

> 内核开发工具（“kern tools”）也可在 Yocto Project Source repository 中的“Yocto Linux Kernel”标题下的“Yocto Kernel tools”Git 存储库中找到。构建这些工具的配方是“源目录”中的“meta/precipes kernel/kern tools/kern-tools-native_git.bb”｛.depreted text role=“term”｝（例如“poky”）。

# Using Kernel Metadata in a Recipe

As mentioned in the introduction, the Yocto Project contains kernel Metadata, which is located in the `yocto-kernel-cache` Git repository. This Metadata defines Board Support Packages (BSPs) that correspond to definitions in linux-yocto recipes for corresponding BSPs. A BSP consists of an aggregation of kernel policy and enabled hardware-specific features. The BSP can be influenced from within the linux-yocto recipe.

> 如引言中所述，Yocto 项目包含内核元数据，该元数据位于“Yocto 内核缓存”Git 存储库中。此元数据定义了板支持包（BSP），这些包对应于 linux yocto 配方中对应 BSP 的定义。BSP 由内核策略和启用的硬件特定功能的集合组成。BSP 可能会受到 linux yocto 配方中的影响。

::: note
::: title
Note
:::

A Linux kernel recipe that contains kernel Metadata (e.g. inherits from the `linux-yocto.inc` file) is said to be a \"linux-yocto style\" recipe.

> 包含内核元数据的 Linux 内核配方（例如继承自 `Linux-yocto.inc` 文件）称为“Linux-youcto 样式”配方。
> :::

Every linux-yocto style recipe must define the `KMACHINE`{.interpreted-text role="term"} variable. This variable is typically set to the same value as the `MACHINE`{.interpreted-text role="term"} variable, which is used by `BitBake`{.interpreted-text role="term"}. However, in some cases, the variable might instead refer to the underlying platform of the `MACHINE`{.interpreted-text role="term"}.

> 每个 linux yocto 风格的配方都必须定义 `KMACHINE`｛.explored text role=“term”｝变量。此变量通常设置为与 `BitBake`｛.depredicted text role=“term”｝使用的 `MACHINE`｛.repredicted text role=”term“｝变量相同的值。然而，在某些情况下，变量可能会改为引用 `MACHINE` 的底层平台{.depreted text role=“term”}。

Multiple BSPs can reuse the same `KMACHINE`{.interpreted-text role="term"} name if they are built using the same BSP description. Multiple Corei7-based BSPs could share the same \"intel-corei7-64\" value for `KMACHINE`{.interpreted-text role="term"}. It is important to realize that `KMACHINE`{.interpreted-text role="term"} is just for kernel mapping, while `MACHINE`{.interpreted-text role="term"} is the machine type within a BSP Layer. Even with this distinction, however, these two variables can hold the same value. See the \"`kernel-dev/advanced:bsp descriptions`{.interpreted-text role="ref"}\" section for more information.

> 如果多个 BSP 是使用相同的 BSP 描述构建的，则它们可以重用相同的 `KMACHINE`{.depreced text role=“term”}名称。多个基于 Corei7 的 BSP 可以共享 `KMACHINE` 的相同\“interl-Corei7-64\”值｛.explored text role=“term”｝。重要的是要认识到，`KMACHINE`｛.depreced text role=“term”｝仅用于内核映射，而 `MACHINE`｛.epreced textrole=”term“｝是 BSP 层内的机器类型。然而，即使有这种区别，这两个变量也可以保持相同的值。有关详细信息，请参阅\“`kernel dev/advanced:bsp descriptions`｛.depreted text role=“ref”｝\”部分。

Every linux-yocto style recipe must also indicate the Linux kernel source repository branch used to build the Linux kernel. The `KBRANCH`{.interpreted-text role="term"} variable must be set to indicate the branch.

> 每个 linux yocto 风格的配方还必须指示用于构建 linux 内核的 linux 内核源存储库分支。必须设置 `KBRANCH`｛.explored text role=“term”｝变量来指示分支。

::: note
::: title
Note
:::

You can use the `KBRANCH`{.interpreted-text role="term"} value to define an alternate branch typically with a machine override as shown here from the `meta-yocto-bsp` layer:

> 您可以使用 `KBRANCH`｛.explored text role=“term”｝值来定义一个备用分支，通常带有机器覆盖，如“meta yocto bsp”层中所示：

```
KBRANCH:edgerouter = "standard/edgerouter"
```

:::

The linux-yocto style recipes can optionally define the following variables:

> linux yocto 风格的配方可以选择性地定义以下变量：

> - `KERNEL_FEATURES`{.interpreted-text role="term"}
> - `LINUX_KERNEL_TYPE`{.interpreted-text role="term"}

`LINUX_KERNEL_TYPE`{.interpreted-text role="term"} defines the kernel type to be used in assembling the configuration. If you do not specify a `LINUX_KERNEL_TYPE`{.interpreted-text role="term"}, it defaults to \"standard\". Together with `KMACHINE`{.interpreted-text role="term"}, `LINUX_KERNEL_TYPE`{.interpreted-text role="term"} defines the search arguments used by the kernel tools to find the appropriate description within the kernel Metadata with which to build out the sources and configuration. The linux-yocto recipes define \"standard\", \"tiny\", and \"preempt-rt\" kernel types. See the \"`kernel-dev/advanced:kernel types`{.interpreted-text role="ref"}\" section for more information on kernel types.

> `LINUX_KERNEL_TYPE`｛.explored text role=“term”｝定义了组装配置时要使用的内核类型。如果未指定 `LINUX_KERNEL_TYPE`｛.depreted text role=“term”｝，则默认为\“standard\”。`LINUX_KERNEL_TYPE` 与 `KMACHINE`｛.depreted text role=“term”｝一起定义了内核工具用于在内核元数据中查找适当描述的搜索参数，以构建源和配置。linux yocto 配方定义了“标准”、“微小”和“preempt-rt”内核类型。有关内核类型的更多信息，请参阅“`kernel dev/advanced:kernel types`｛.depreced text role=“ref”｝\”一节。

During the build, the kern-tools search for the BSP description file that most closely matches the `KMACHINE`{.interpreted-text role="term"} and `LINUX_KERNEL_TYPE`{.interpreted-text role="term"} variables passed in from the recipe. The tools use the first BSP description they find that matches both variables. If the tools cannot find a match, they issue a warning.

> 在构建过程中，kern 工具会搜索与配方中传入的 `KMACHINE`｛.explored text role=“term”｝和 `LINUX_KERNEL_TYPE｝｛.expered text rol=”term“｝变量最匹配的 BSP 描述文件。这些工具使用他们发现的与两个变量匹配的第一个 BSP 描述。如果工具找不到匹配项，则会发出警告。

The tools first search for the `KMACHINE`{.interpreted-text role="term"} and then for the `LINUX_KERNEL_TYPE`{.interpreted-text role="term"}. If the tools cannot find a partial match, they will use the sources from the `KBRANCH`{.interpreted-text role="term"} and any configuration specified in the `SRC_URI`{.interpreted-text role="term"}.

> 这些工具首先搜索 `KMACHINE`｛.depreted text role=“term”｝，然后搜索 `LINUX_KERNEL_TYPE`｛.epreted text role=“term“｝。如果工具找不到部分匹配，它们将使用来自 `KBRANCH`｛.depreced text role=“term”｝的源和 `SRC_URI`｛.epreced text role=”term“｝中指定的任何配置。

You can use the `KERNEL_FEATURES`{.interpreted-text role="term"} variable to include features (configuration fragments, patches, or both) that are not already included by the `KMACHINE`{.interpreted-text role="term"} and `LINUX_KERNEL_TYPE`{.interpreted-text role="term"} variable combination. For example, to include a feature specified as \"features/netfilter/netfilter.scc\", specify:

> 您可以使用 `KERNEL_FEATURE`｛.depredicted text role=“term”｝变量来包括 `KMACHINE`｛.epredicted textrole=”term“｝和 `LINUX_KERNEL_TYPE｝｛.expected textrol=”term”}变量组合尚未包括的功能（配置片段、修补程序或两者）。例如，要包含指定为“features/netfilter/netfilter.scc\”的功能，请指定：

```
KERNEL_FEATURES += "features/netfilter/netfilter.scc"
```

To include a feature called \"cfg/sound.scc\" just for the `qemux86` machine, specify:

> 要仅为“qemux86”计算机包含名为“cfg/sound.scc\”的功能，请指定：

```
KERNEL_FEATURES:append:qemux86 = " cfg/sound.scc"
```

The value of the entries in `KERNEL_FEATURES`{.interpreted-text role="term"} are dependent on their location within the kernel Metadata itself. The examples here are taken from the `yocto-kernel-cache` repository. Each branch of this repository contains \"features\" and \"cfg\" subdirectories at the top-level. For more information, see the \"`kernel-dev/advanced:kernel metadata syntax`{.interpreted-text role="ref"}\" section.

> `KERNEL_FEATURE`｛.explored text role=“term”｝中的条目的值取决于它们在内核元数据本身中的位置。这里的示例取自“yocto 内核缓存”存储库。该存储库的每个分支都包含位于顶层的“features”和“cfg”子目录。有关更多信息，请参阅“`kernel dev/advanced:kernel元数据语法`｛.depreted text role=“ref”｝\”一节。

# Kernel Metadata Syntax

The kernel Metadata consists of three primary types of files: `scc` [^1] description files, configuration fragments, and patches. The `scc` files define variables and include or otherwise reference any of the three file types. The description files are used to aggregate all types of kernel Metadata into what ultimately describes the sources and the configuration required to build a Linux kernel tailored to a specific machine.

> 内核元数据由三种主要类型的文件组成：“scc”[^1]描述文件、配置片段和修补程序。“scc”文件定义变量，并包括或以其他方式引用三种文件类型中的任何一种。描述文件用于将所有类型的内核元数据聚合为最终描述构建针对特定机器定制的 Linux 内核所需的源和配置的内容。

The `scc` description files are used to define two fundamental types of kernel Metadata:

> “scc”描述文件用于定义两种基本类型的内核元数据：

- Features
- Board Support Packages (BSPs)

Features aggregate sources in the form of patches and configuration fragments into a modular reusable unit. You can use features to implement conceptually separate kernel Metadata descriptions such as pure configuration fragments, simple patches, complex features, and kernel types. `kernel-dev/advanced:kernel types`{.interpreted-text role="ref"} define general kernel features and policy to be reused in the BSPs.

> 特性以补丁和配置片段的形式将源聚合到一个模块化的可重用单元中。您可以使用特性来实现概念上独立的内核元数据描述，例如纯配置片段、简单补丁、复杂特性和内核类型 `kernel dev/advanced:kernel类型`{.depredicted text role=“ref”}定义了要在 BSP 中重用的一般内核功能和策略。

BSPs define hardware-specific features and aggregate them with kernel types to form the final description of what will be assembled and built.

> BSP 定义特定于硬件的特性，并将它们与内核类型聚合，以形成将要组装和构建的内容的最终描述。

While the kernel Metadata syntax does not enforce any logical separation of configuration fragments, patches, features or kernel types, best practices dictate a logical separation of these types of Metadata. The following Metadata file hierarchy is recommended:

> 虽然内核元数据语法不强制对配置片段、修补程序、功能或内核类型进行任何逻辑分离，但最佳实践要求对这些类型的元数据进行逻辑分离。建议使用以下元数据文件层次结构：

```
base/
   bsp/
   cfg/
   features/
   ktypes/
   patches/
```

The `bsp` directory contains the `kernel-dev/advanced:bsp descriptions`{.interpreted-text role="ref"}. The remaining directories all contain \"features\". Separating `bsp` from the rest of the structure aids conceptualizing intended usage.

> “nbsp”目录包含“内核开发/高级：nbsp 说明”｛.解释文本角色=“ref”｝。剩下的目录都包含“features”。将“nbsp”与结构的其余部分分离有助于概念化预期用法。

Use these guidelines to help place your `scc` description files within the structure:

> 使用以下准则可以帮助将“scc”描述文件放置在结构中：

- If your file contains only configuration fragments, place the file in the `cfg` directory.
- If your file contains only source-code fixes, place the file in the `patches` directory.
- If your file encapsulates a major feature, often combining sources and configurations, place the file in `features` directory.

> -如果您的文件封装了一个主要功能，通常将源和配置组合在一起，请将该文件放在“features”目录中。

- If your file aggregates non-hardware configuration and patches in order to define a base kernel policy or major kernel type to be reused across multiple BSPs, place the file in `ktypes` directory.

> -如果您的文件聚合了非硬件配置和修补程序，以便定义要在多个 BSP 中重用的基本内核策略或主要内核类型，请将该文件放在“ktypes”目录中。

These distinctions can easily become blurred \-\-- especially as out-of-tree features slowly merge upstream over time. Also, remember that how the description files are placed is a purely logical organization and has no impact on the functionality of the kernel Metadata. There is no impact because all of `cfg`, `features`, `patches`, and `ktypes`, contain \"features\" as far as the kernel tools are concerned.

> 这些区别很容易变得模糊，尤其是当树外特征随着时间的推移向上游缓慢融合时。此外，请记住，描述文件的放置方式完全是一个逻辑组织，对内核元数据的功能没有影响。没有影响，因为就内核工具而言，所有的“cfg”、“features”、“patches”和“ktypes”都包含“features\”。

Paths used in kernel Metadata files are relative to base, which is either `FILESEXTRAPATHS`{.interpreted-text role="term"} if you are creating Metadata in `recipe-space <kernel-dev/advanced:recipe-space metadata>`{.interpreted-text role="ref"}, or the top level of :yocto\_[git:%60yocto-kernel-cache](git:%60yocto-kernel-cache) \</yocto-kernel-cache/tree/\>[ if you are creating :ref:\`kernel-dev/advanced:metadata outside the recipe-space]{.title-ref}.

> 内核元数据文件中使用的路径是相对于基本路径的，如果您在 `recipe space<kernel dev/advanced:recipe space Metadata>`{.depredicted text role=“ref”}中创建元数据，则基本路径为 `FILESEXTRAPATHS`｛.repredicted text-role=“term”｝，或者：yocto\_＜git:%60yocto kernel cache>\</yocto kernel cache/tree/\>[如果您正在配方空间外创建：ref:\`kernel dev/advanced:metadata]｛.title-ref｝的顶级。

## Configuration

The simplest unit of kernel Metadata is the configuration-only feature. This feature consists of one or more Linux kernel configuration parameters in a configuration fragment file (`.cfg`) and a `.scc` file that describes the fragment.

> 内核元数据中最简单的单元是仅配置功能。此功能由配置片段文件（“.cfg”）和描述该片段的“.scc”文件中的一个或多个 Linux 内核配置参数组成。

As an example, consider the Symmetric Multi-Processing (SMP) fragment used with the `linux-yocto-4.12` kernel as defined outside of the recipe space (i.e. `yocto-kernel-cache`). This Metadata consists of two files: `smp.scc` and `smp.cfg`. You can find these files in the `cfg` directory of the `yocto-4.12` branch in the `yocto-kernel-cache` Git repository:

> 例如，考虑在配方空间（即“yocto 内核缓存”）之外定义的与“linux-octo-4.12”内核一起使用的对称多处理（SMP）片段。此元数据由两个文件组成：“smp.scc”和“smp.cfg”。您可以在“yocto kernel cache”Git 存储库的“yocto-4.12”分支的“cfg”目录中找到这些文件：

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

You can find general information on configuration fragment files in the \"`kernel-dev/common:creating configuration fragments`{.interpreted-text role="ref"}\" section.

> 您可以在\“`kernel dev/common:createing configuration fragments`｛.depreted text role=”ref“｝\”部分找到有关配置片段文件的一般信息。

Within the `smp.scc` file, the `KFEATURE_DESCRIPTION`{.interpreted-text role="term"} statement provides a short description of the fragment. Higher level kernel tools use this description.

> 在 `smp.scc` 文件中，`KFEATURE_DESCRIPTION`｛.depredicted text role=“term”｝语句提供了片段的简短描述。更高级别的内核工具使用此描述。

Also within the `smp.scc` file, the `kconf` command includes the actual configuration fragment in an `.scc` file, and the \"hardware\" keyword identifies the fragment as being hardware enabling, as opposed to general policy, which would use the \"non-hardware\" keyword. The distinction is made for the benefit of the configuration validation tools, which warn you if a hardware fragment overrides a policy set by a non-hardware fragment.

> 同样在“smp.scc”文件中，“kconf”命令包括“.scc”文件中的实际配置片段，并且\“hardware\”关键字将该片段标识为启用硬件，而不是使用\“non-hardware”关键字的常规策略。这种区别是为了配置验证工具的好处，如果硬件片段覆盖了非硬件片段设置的策略，配置验证工具会向您发出警告。

::: note
::: title
Note
:::

The description file can include multiple `kconf` statements, one per fragment.

> 描述文件可以包括多个“kconf”语句，每个片段一个。
> :::

As described in the \"`kernel-dev/common:validating configuration`{.interpreted-text role="ref"}\" section, you can use the following BitBake command to audit your configuration:

> 如“`kernel dev/common:validing configuration`｛.respered text role=“ref”｝”部分所述，您可以使用以下 BitBake 命令来审核您的配置：

```
$ bitbake linux-yocto -c kernel_configcheck -f
```

## Patches

Patch descriptions are very similar to configuration fragment descriptions, which are described in the previous section. However, instead of a `.cfg` file, these descriptions work with source patches (i.e. `.patch` files).

> 修补程序描述与配置片段描述非常相似，后者在上一节中进行了描述。但是，这些描述不使用“.cfg”文件，而是使用源修补程序（即“.patch”文件）。

A typical patch includes a description file and the patch itself. As an example, consider the build patches used with the `linux-yocto-4.12` kernel as defined outside of the recipe space (i.e. `yocto-kernel-cache`). This Metadata consists of several files: `build.scc` and a set of `*.patch` files. You can find these files in the `patches/build` directory of the `yocto-4.12` branch in the `yocto-kernel-cache` Git repository.

> 典型的修补程序包括一个描述文件和修补程序本身。例如，考虑在配方空间（即“yocto-kernel 缓存”）之外定义的与“linux-octo-4.12”内核一起使用的构建补丁。此元数据由几个文件组成：“build.scc”和一组“*.patch”文件。您可以在“yocto kernel cache”Git 存储库中“yocto-4.12”分支的“patches/build”目录中找到这些文件。

The following listings show the `build.scc` file and part of the `modpost-mask-trivial-warnings.patch` file:

> 以下列表显示了“build.scc”文件和“modpost-mask 琐碎警告.patch”文件的一部分：

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

> 描述文件可以包括多个修补程序语句，其中每个语句处理一个修补程序。在示例“build.scc”文件中，目录中的五个修补程序有五条修补程序语句。

You can create a typical `.patch` file using `diff -Nurp` or `git format-patch` commands. For information on how to create patches, see the \"``kernel-dev/common:using \`\`devtool\`\` to patch the kernel``{.interpreted-text role="ref"}\" and \"`kernel-dev/common:using traditional kernel development to patch the kernel`{.interpreted-text role="ref"}\" sections.

> 您可以使用“diff-Nurp”或“git-format-patch”命令创建一个典型的“.patch”文件。有关如何创建修补程序的信息，请参阅“``kernel dev/common:使用`` devtool\``修补内核``｛.depreted text role=“ref”｝”和“` kernel/dev/common:使用传统内核开发修补内核 `{.depreted textrole=”ref“｝”部分。

## Features

Features are complex kernel Metadata types that consist of configuration fragments, patches, and possibly other feature description files. As an example, consider the following generic listing:

> 功能是复杂的内核元数据类型，由配置片段、修补程序以及可能的其他功能描述文件组成。例如，考虑以下通用列表：

```
features/myfeature.scc
   define KFEATURE_DESCRIPTION "Enable myfeature"

   patch 0001-myfeature-core.patch
   patch 0002-myfeature-interface.patch

   include cfg/myfeature_dependency.scc
   kconf non-hardware myfeature.cfg
```

This example shows how the `patch` and `kconf` commands are used as well as how an additional feature description file is included with the `include` command.

> 此示例显示了如何使用“patch”和“kconf”命令，以及如何将附加功能描述文件包含在“include”命令中。

Typically, features are less granular than configuration fragments and are more likely than configuration fragments and patches to be the types of things you want to specify in the `KERNEL_FEATURES`{.interpreted-text role="term"} variable of the Linux kernel recipe. See the \"`kernel-dev/advanced:using kernel metadata in a recipe`{.interpreted-text role="ref"}\" section earlier in the manual.

> 通常，功能比配置片段粒度更小，并且比配置片段和补丁更可能是您希望在 Linux 内核配方的 `KERNEL_FEATURE`{.depredicted text role=“term”}变量中指定的类型。请参阅本手册前面的“`kernel dev/advanced:using kernel metadata in a recipe`｛.depredicted text role=“ref”｝”一节。

## Kernel Types

A kernel type defines a high-level kernel policy by aggregating non-hardware configuration fragments with patches you want to use when building a Linux kernel of a specific type (e.g. a real-time kernel). Syntactically, kernel types are no different than features as described in the \"`kernel-dev/advanced:features`{.interpreted-text role="ref"}\" section. The `LINUX_KERNEL_TYPE`{.interpreted-text role="term"} variable in the kernel recipe selects the kernel type. For example, in the `linux-yocto_4.12.bb` kernel recipe found in `poky/meta/recipes-kernel/linux`, a ``require <bitbake-user-manual/bitbake-user-manual-metadata:\`\`require\`\` directive>``{.interpreted-text role="ref"} directive includes the `poky/meta/recipes-kernel/linux/linux-yocto.inc` file, which has the following statement that defines the default kernel type:

> 内核类型通过将非硬件配置片段与构建特定类型的 Linux 内核（例如实时内核）时要使用的补丁聚合来定义高级内核策略。从语法上讲，内核类型与“`kernel dev/advanced:features`｛.depredicted text role=“ref”｝”一节中描述的功能没有什么不同。内核配方中的 `LINUX_KERNEL_TYPE`｛.explored text role=“term”｝变量选择内核类型。例如，在 `poky/meta/recipes-kernel/linux` 中的 `linux-yocto_4.12.bb` 内核配方中，``require<bitbake-user-manual/bitbake-user-manual-metadata:\`\` require\`\`directive>``{.dinterpreted-text role=“ref”}指令包含 `poky/meta/ecipes-kernel/linus/linux-yoctor.inc` 文件，该文件包含以下语句，用于定义默认内核类型：

```
LINUX_KERNEL_TYPE ??= "standard"
```

Another example would be the real-time kernel (i.e. `linux-yocto-rt_4.12.bb`). This kernel recipe directly sets the kernel type as follows:

> 另一个例子是实时内核（即“linux-yoct-rt_4.12.bb”）。这个内核配方直接设置内核类型如下：

```
LINUX_KERNEL_TYPE = "preempt-rt"
```

::: note
::: title
Note
:::

You can find kernel recipes in the `meta/recipes-kernel/linux` directory of the `overview-manual/development-environment:yocto project source repositories`{.interpreted-text role="ref"} (e.g. `poky/meta/recipes-kernel/linux/linux-yocto_4.12.bb`). See the \"`kernel-dev/advanced:using kernel metadata in a recipe`{.interpreted-text role="ref"}\" section for more information.

> 您可以在 `overview manual/development environment:yocto project source repositories`｛.depredicted text role=“ref”｝（例如 `poky/meta/precipes kernel/linux/linux-yoct_4.12.bb`）的 `meta/precipes-kernel/liinux` 目录中找到内核配方。有关更多信息，请参阅“`kernel dev/advanced:using kernel metadata in a recipe`｛.epredicted text-role=“ref”}\”部分。
> :::

Three kernel types (\"standard\", \"tiny\", and \"preempt-rt\") are supported for Linux Yocto kernels:

> Linux Yocto 内核支持三种内核类型（“standard”、“tiny”和“preempt-rt”）：

- \"standard\": Includes the generic Linux kernel policy of the Yocto Project linux-yocto kernel recipes. This policy includes, among other things, which file systems, networking options, core kernel features, and debugging and tracing options are supported.

> -\“standard\”：包括 Yocto Project Linux Yocto 内核配方的通用 Linux 内核策略。此策略包括支持哪些文件系统、网络选项、核心内核功能以及调试和跟踪选项。

- \"preempt-rt\": Applies the `PREEMPT_RT` patches and the configuration options required to build a real-time Linux kernel. This kernel type inherits from the \"standard\" kernel type.

> -\“preempt-rt”：应用“preempt_rt”修补程序和构建实时 Linux 内核所需的配置选项。此内核类型继承自“标准”内核类型。

- \"tiny\": Defines a bare minimum configuration meant to serve as a base for very small Linux kernels. The \"tiny\" kernel type is independent from the \"standard\" configuration. Although the \"tiny\" kernel type does not currently include any source changes, it might in the future.

> -\“tiny\”：定义一个最低限度的配置，作为非常小的 Linux 内核的基础。“微小”内核类型独立于“标准”配置。尽管“微小”内核类型目前不包括任何源代码更改，但将来可能会更改。

For any given kernel type, the Metadata is defined by the `.scc` (e.g. `standard.scc`). Here is a partial listing for the `standard.scc` file, which is found in the `ktypes/standard` directory of the `yocto-kernel-cache` Git repository:

> 对于任何给定的内核类型，元数据都由“.scc”（例如“standard.scc”）定义。以下是“standard.ssc”文件的部分列表，该文件位于“yocto kernel cache”Git 存储库的“ktypes/standard”目录中：

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

> 与任何“.scc'文件一样，内核类型定义可以使用“include”命令聚合其他“.scc”文件。这些定义还可以分别使用“kconf”和“patch”命令直接引入配置片段和补丁。

::: note
::: title
Note
:::

It is not strictly necessary to create a kernel type `.scc` file. The Board Support Package (BSP) file can implicitly define the kernel type using a `define` `KTYPE`{.interpreted-text role="term"} `myktype` line. See the \"`kernel-dev/advanced:bsp descriptions`{.interpreted-text role="ref"}\" section for more information.

> 创建内核类型“.scc'”文件并不是绝对必要的。Board Support Package（BSP）文件可以使用 `define` KTYPE `{.depreted text role=“term”}` myktype `行隐式定义内核类型。有关详细信息，请参阅\“` kernel dev/advanced:bsp descriptions`｛.depreted text role=“ref”｝\”部分。
> :::

## BSP Descriptions

BSP descriptions (i.e. `*.scc` files) combine kernel types with hardware-specific features. The hardware-specific Metadata is typically defined independently in the BSP layer, and then aggregated with each supported kernel type.

> BSP 描述（即“*.scc”文件）将内核类型与硬件特定功能相结合。硬件特定元数据通常在 BSP 层中独立定义，然后与每个支持的内核类型聚合。

::: note
::: title
Note
:::

For BSPs supported by the Yocto Project, the BSP description files are located in the `bsp` directory of the `yocto-kernel-cache` repository organized under the \"Yocto Linux Kernel\" heading in the :yocto\_[git:%60Yocto](git:%60Yocto) Project Source Repositories \<\>\`.

> 对于 Yocto 项目支持的 BSP，BSP 描述文件位于“Yocto kernel cache”存储库的“BSP”目录中，该存储库组织在：Yocto\_[git:%60Yocto](git:%60Yocto) 项目源存储库\<\>\`的“Yocto Linux kernel”标题下。
> :::

This section overviews the BSP description structure, the aggregation concepts, and presents a detailed example using a BSP supported by the Yocto Project (i.e. BeagleBone Board). For complete information on BSP layer file hierarchy, see the `/bsp-guide/index`{.interpreted-text role="doc"}.

> 本节概述了 BSP 描述结构、聚合概念，并提供了一个使用 Yocto 项目支持的 BSP（即 BeagleBone Board）的详细示例。有关 BSP 层文件层次结构的完整信息，请参阅 `nbsp guide/index`｛.depreted text role=“doc”｝。

### Description Overview

For simplicity, consider the following root BSP layer description files for the BeagleBone board. These files employ both a structure and naming convention for consistency. The naming convention for the file is as follows:

> 为了简单起见，请考虑以下 BeagleBone 板的根 BSP 层描述文件。为了保持一致性，这些文件同时采用了结构和命名约定。文件的命名约定如下：

```
bsp_root_name-kernel_type.scc
```

Here are some example root layer BSP filenames for the BeagleBone Board BSP, which is supported by the Yocto Project:

> 以下是 Yocto 项目支持的 BeagleBone Board BSP 的一些根层 BSP 文件名示例：

```
beaglebone-standard.scc
beaglebone-preempt-rt.scc
```

Each file uses the root name (i.e \"beaglebone\") BSP name followed by the kernel type.

> 每个文件都使用根名称（即“beaglebone”）BSP 名称，后跟内核类型。

Examine the `beaglebone-standard.scc` file:

> 检查“beaglebone standard.scc”文件：

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

Every top-level BSP description file should define the `KMACHINE`{.interpreted-text role="term"}, `KTYPE`{.interpreted-text role="term"}, and `KARCH`{.interpreted-text role="term"} variables. These variables allow the OpenEmbedded build system to identify the description as meeting the criteria set by the recipe being built. This example supports the \"beaglebone\" machine for the \"standard\" kernel and the \"arm\" architecture.

> 每个顶级 BSP 描述文件都应定义 `KMACHINE`｛.depreced text role=“term”｝、`KTYPE`｛.epreced text-role=“term“｝和 `KARCH`｛.repreced ext-role=”term“}变量。这些变量允许 OpenEmbedded 构建系统将描述标识为符合正在构建的配方设置的标准。此示例支持“标准”内核和“arm”体系结构的“beaglebone”机器。

Be aware that there is no hard link between the `KTYPE`{.interpreted-text role="term"} variable and a kernel type description file. Thus, if you do not have the kernel type defined in your kernel Metadata as it is here, you only need to ensure that the `LINUX_KERNEL_TYPE`{.interpreted-text role="term"} variable in the kernel recipe and the `KTYPE`{.interpreted-text role="term"} variable in the BSP description file match.

> 请注意，`KTYPE`｛.explored text role=“term”｝变量和内核类型描述文件之间没有硬链接。因此，如果您的内核元数据中没有像这里那样定义内核类型，您只需要确保内核配方中的 `LINUX_kernel_type`｛.depreted text role=“term”｝变量和 BSP 描述文件中的 `KTYPE`{.depreted textrole=”term“｝变量匹配即可。

To separate your kernel policy from your hardware configuration, you include a kernel type (`ktype`), such as \"standard\". In the previous example, this is done using the following:

> 要将内核策略与硬件配置分开，您需要包含一个内核类型（“ktype”），例如\“standard\”。在前面的示例中，这是使用以下方法完成的：

```
include ktypes/standard/standard.scc
```

This file aggregates all the configuration fragments, patches, and features that make up your standard kernel policy. See the \"`kernel-dev/advanced:kernel types`{.interpreted-text role="ref"}\" section for more information.

> 该文件聚合了构成标准内核策略的所有配置片段、修补程序和功能。有关详细信息，请参阅\“`kernel dev/advanced:kernel types`｛.depreced text role=“ref”｝\”部分。

To aggregate common configurations and features specific to the kernel for [mybsp]{.title-ref}, use the following:

> 要聚合特定于〔mybsp〕｛.title-ref｝内核的常见配置和功能，请使用以下内容：

```
include mybsp.scc
```

You can see that in the BeagleBone example with the following:

> 您可以在 BeagleBone 示例中看到以下内容：

```
include beaglebone.scc
```

For information on how to break a complete `.config` file into the various configuration fragments, see the \"`kernel-dev/common:creating configuration fragments`{.interpreted-text role="ref"}\" section.

> 有关如何将完整的 `.config` 文件分解为各种配置片段的信息，请参阅\“`kernel dev/common:createing configuration fragments`｛.depreted text role=“ref”｝\”一节。

Finally, if you have any configurations specific to the hardware that are not in a `*.scc` file, you can include them as follows:

> 最后，如果您有任何特定于硬件的配置，但这些配置不在“*.scc”文件中，则可以按如下方式包括这些配置：

```
kconf hardware mybsp-extra.cfg
```

The BeagleBone example does not include these types of configurations. However, the Malta 32-bit board does (\"mti-malta32\"). Here is the `mti-malta32-le-standard.scc` file:

> BeagleBone 示例不包括这些类型的配置。但是，Malta 32 位板确实如此（\“mti-malta32\”）。以下是“mti-malta32-le-standard.scc”文件：

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

> 许多现实世界中的例子更为复杂。与任何其他“.scc”文件一样，BSP 描述可以聚合功能。考虑给定“yocto kernel cache”的“linux-octo-4.4”分支的 Minnow BSP 定义（即“yocto-kernel cache/BSP/minow/Minnow.scc”）：

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

> 尽管 Minnow Board BSP 未使用，但元数据仍保留下来，并在此处作为示例使用。
> :::

The `minnow.scc` description file includes a hardware configuration fragment (`minnow.cfg`) specific to the Minnow BSP as well as several more general configuration fragments and features enabling hardware found on the machine. This `minnow.scc` description file is then included in each of the three \"minnow\" description files for the supported kernel types (i.e. \"standard\", \"preempt-rt\", and \"tiny\"). Consider the \"minnow\" description for the \"standard\" kernel type (i.e. `minnow-standard.scc`):

> “minnow.scc”描述文件包括特定于 minnow BSP 的硬件配置片段（“minnow.cfg”），以及启用机器上硬件的几个更通用的配置片段和功能。然后，这个“minnow.scc”描述文件包含在支持的内核类型的三个“minnow”描述文件中（即“标准”、“preempt-rt”和“微小”）。请考虑“标准”内核类型（即“minnow-standard.scc'）的“minnow”描述：

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

> 文件中间的“include”命令包括“minnow.scc”描述，该描述定义了所有内核类型通用的 BSP 的所有启用硬件。使用此命令可以显著减少重复。

Now consider the \"minnow\" description for the \"tiny\" kernel type (i.e. `minnow-tiny.scc`):

> 现在考虑“minnow”内核类型（即“minnow-minin.scc”）的“minnow\”描述：

```
define KMACHINE minnow
define KTYPE tiny
define KARCH i386

include ktypes/tiny

include minnow.scc
```

As you might expect, the \"tiny\" description includes quite a bit less. In fact, it includes only the minimal policy defined by the \"tiny\" kernel type and the hardware-specific configuration required for booting the machine along with the most basic functionality of the system as defined in the base \"minnow\" description file.

> 正如你所料，“微小”的描述包含的内容要少得多。事实上，它只包括“微小”内核类型定义的最小策略和启动机器所需的硬件特定配置，以及基本“minnow”描述文件中定义的系统的最基本功能。

Notice again the three critical variables: `KMACHINE`{.interpreted-text role="term"}, `KTYPE`{.interpreted-text role="term"}, and `KARCH`{.interpreted-text role="term"}. Of these variables, only `KTYPE`{.interpreted-text role="term"} has changed to specify the \"tiny\" kernel type.

> 再次注意三个关键变量：`KMACHINE`｛.depreted text role=“term”｝、`KTYPE`｛.repreted text role=“term“｝和 `KARCH`｛.epreted text 角色=“term”｝。在这些变量中，只有 `KTYPE`｛.explored text role=“term”｝更改为指定“微小”内核类型。

# Kernel Metadata Location

Kernel Metadata always exists outside of the kernel tree either defined in a kernel recipe (recipe-space) or outside of the recipe. Where you choose to define the Metadata depends on what you want to do and how you intend to work. Regardless of where you define the kernel Metadata, the syntax used applies equally.

> 内核元数据总是存在于内核树之外，要么在内核配方（配方空间）中定义，要么在配方之外。选择在哪里定义元数据取决于您想要做什么以及打算如何工作。无论在哪里定义内核元数据，所使用的语法都同样适用。

If you are unfamiliar with the Linux kernel and only wish to apply a configuration and possibly a couple of patches provided to you by others, the recipe-space method is recommended. This method is also a good approach if you are working with Linux kernel sources you do not control or if you just do not want to maintain a Linux kernel Git repository on your own. For partial information on how you can define kernel Metadata in the recipe-space, see the \"`kernel-dev/common:modifying an existing recipe`{.interpreted-text role="ref"}\" section.

> 如果您不熟悉 Linux 内核，只想应用一个配置，可能还有其他人提供的几个补丁，建议使用配方空间方法。如果您使用的是不受控制的 Linux 内核源代码，或者您只是不想自己维护 Linux 内核 Git 存储库，那么这种方法也是一种很好的方法。有关如何在配方空间中定义内核元数据的部分信息，请参阅\“`kernel dev/common:修改现有配方`{.depreted text role=“ref”}\”一节。

Conversely, if you are actively developing a kernel and are already maintaining a Linux kernel Git repository of your own, you might find it more convenient to work with kernel Metadata kept outside the recipe-space. Working with Metadata in this area can make iterative development of the Linux kernel more efficient outside of the BitBake environment.

> 相反，如果您正在积极开发内核，并且已经在维护自己的 Linux 内核 Git 存储库，那么您可能会发现使用保存在配方空间之外的内核元数据更方便。在这个领域中使用元数据可以使 Linux 内核的迭代开发在 BitBake 环境之外更加高效。

## Recipe-Space Metadata

When stored in recipe-space, the kernel Metadata files reside in a directory hierarchy below `FILESEXTRAPATHS`{.interpreted-text role="term"}. For a linux-yocto recipe or for a Linux kernel recipe derived by copying :oe\_[git:%60meta-skeleton/recipes-kernel/linux/linux-yocto-custom.bb](git:%60meta-skeleton/recipes-kernel/linux/linux-yocto-custom.bb) \</openembedded-core/tree/meta-skeleton/recipes-kernel/linux/linux-yocto-custom.bb\>[ into your layer and modifying it, :term:\`FILESEXTRAPATHS]{.title-ref} is typically set to `${``THISDIR`{.interpreted-text role="term"}`}/${``PN`{.interpreted-text role="term"}`}`. See the \"`kernel-dev/common:modifying an existing recipe`{.interpreted-text role="ref"}\" section for more information.

> 当存储在配方空间中时，内核元数据文件位于 `FILESEXTRAPATHS`｛.depreted text role=“term”｝下方的目录层次结构中。对于 linux yocto 配方或通过将以下内容复制到您的层中并对其进行修改而派生的 linux 内核配方：oe\_<git:%60meta sklete/recipes kernel/linux/linux yocto-custom.bb>\</openembeded core/tree/meta-stelet/recipes cornel/linux-yocto custom.bb\>[：term:\`FILESEXTRAPATHS]｛.title-ref｝通常设置为 `${` THISDIR `｛.解释文本角色=“term”｝｝/${` PN `｛解释文本角色=“术语”}`}`。有关更多信息，请参阅“` kernel dev/common:修改现有配方 `{.depreted text role=“ref”}\”一节。

Here is an example that shows a trivial tree of kernel Metadata stored in recipe-space within a BSP layer:

> 以下是一个示例，显示了存储在 BSP 层内的配方空间中的内核元数据的琐碎树：

```
meta-my_bsp_layer/
`-- recipes-kernel
    `-- linux
        `-- linux-yocto
            |-- bsp-standard.scc
            |-- bsp.cfg
            `-- standard.cfg
```

When the Metadata is stored in recipe-space, you must take steps to ensure BitBake has the necessary information to decide what files to fetch and when they need to be fetched again. It is only necessary to specify the `.scc` files on the `SRC_URI`{.interpreted-text role="term"}. BitBake parses them and fetches any files referenced in the `.scc` files by the `include`, `patch`, or `kconf` commands. Because of this, it is necessary to bump the recipe `PR`{.interpreted-text role="term"} value when changing the content of files not explicitly listed in the `SRC_URI`{.interpreted-text role="term"}.

> 当元数据存储在配方空间中时，您必须采取措施确保 BitBake 拥有必要的信息来决定要提取哪些文件以及何时需要再次提取这些文件。只需在 `SRC_URI`｛.depredicted text role=“term”｝上指定 `.scc'文件。BitBake解析它们，并通过“include”、“patch”或“kconf”命令获取“.scc”文件中引用的任何文件。因此，在更改` SRC_URI `中未明确列出的文件的内容时，有必要更改配方` PR`{.depredicted text role=“term”}值。

If the BSP description is in recipe space, you cannot simply list the `*.scc` in the `SRC_URI`{.interpreted-text role="term"} statement. You need to use the following form from your kernel append file:

> 如果 BSP 描述在配方空间中，则不能简单地在 `SRC_URI`｛.depreted text role=“term”｝语句中列出 `*.scc'。您需要使用内核附加文件中的以下表单：

```
SRC_URI:append:myplatform = " \
    file://myplatform;type=kmeta;destsuffix=myplatform \
    "
```

## Metadata Outside the Recipe-Space

When stored outside of the recipe-space, the kernel Metadata files reside in a separate repository. The OpenEmbedded build system adds the Metadata to the build as a \"type=kmeta\" repository through the `SRC_URI`{.interpreted-text role="term"} variable. As an example, consider the following `SRC_URI`{.interpreted-text role="term"} statement from the `linux-yocto_5.15.bb` kernel recipe:

> 当存储在配方空间之外时，内核元数据文件位于一个单独的存储库中。OpenEmbedded 构建系统通过 `SRC_URI`｛.explored text role=“term”｝变量将元数据作为“type=kmeta”存储库添加到构建中。例如，考虑 `linux-octo_5.15.bb` 内核配方中的以下 `SRC_URI`{.depreced text role=“term”}语句：

```
SRC_URI = "git://git.yoctoproject.org/linux-yocto.git;name=machine;branch=${KBRANCH};protocol=https \
           git://git.yoctoproject.org/yocto-kernel-cache;type=kmeta;name=meta;branch=yocto-5.15;destsuffix=${KMETA};protocol=https"
```

`${KMETA}`, in this context, is simply used to name the directory into which the Git fetcher places the Metadata. This behavior is no different than any multi-repository `SRC_URI`{.interpreted-text role="term"} statement used in a recipe (e.g. see the previous section).

> `在这种情况下，${KMETA}` 仅用于命名 Git 提取器放置元数据的目录。这种行为与配方中使用的任何多存储库 `SRC_URI`｛.explored text role=“term”｝语句没有什么不同（例如，请参阅上一节）。

You can keep kernel Metadata in a \"kernel-cache\", which is a directory containing configuration fragments. As with any Metadata kept outside the recipe-space, you simply need to use the `SRC_URI`{.interpreted-text role="term"} statement with the \"type=kmeta\" attribute. Doing so makes the kernel Metadata available during the configuration phase.

> 您可以将内核元数据保存在“内核缓存”中，这是一个包含配置片段的目录。与保存在配方空间之外的任何元数据一样，您只需使用带有\“type=kmeta\”属性的 `SRC_URI`｛.respered text role=“term”｝语句。这样做可以使内核元数据在配置阶段可用。

If you modify the Metadata, you must not forget to update the `SRCREV`{.interpreted-text role="term"} statements in the kernel\'s recipe. In particular, you need to update the `SRCREV_meta` variable to match the commit in the `KMETA` branch you wish to use. Changing the data in these branches and not updating the `SRCREV`{.interpreted-text role="term"} statements to match will cause the build to fetch an older commit.

> 如果您修改元数据，您一定不要忘记更新内核配方中的 `SRCREV`｛.explored text role=“term”｝语句。特别是，您需要更新“SRCREV_meta”变量，以匹配您希望使用的“KMETA”分支中的提交。更改这些分支中的数据，而不更新 `SRCREV`｛.explored text role=“term”｝语句以匹配，将导致生成获取旧的提交。

# Organizing Your Source

Many recipes based on the `linux-yocto-custom.bb` recipe use Linux kernel sources that have only a single branch. This type of repository structure is fine for linear development supporting a single machine and architecture. However, if you work with multiple boards and architectures, a kernel source repository with multiple branches is more efficient. For example, suppose you need a series of patches for one board to boot. Sometimes, these patches are works-in-progress or fundamentally wrong, yet they are still necessary for specific boards. In these situations, you most likely do not want to include these patches in every kernel you build (i.e. have the patches as part of the default branch). It is situations like these that give rise to multiple branches used within a Linux kernel sources Git repository.

> 许多基于“linux-yocto-custom.bb”配方的配方使用只有一个分支的 linux 内核源。这种类型的存储库结构适用于支持单机和体系结构的线性开发。但是，如果您使用多个板和体系结构，那么具有多个分支的内核源代码存储库会更高效。例如，假设您需要一系列补丁来启动一块板。有时，这些补丁正在进行中或根本错误，但对于特定的董事会来说，它们仍然是必要的。在这些情况下，您很可能不希望在构建的每个内核中都包含这些补丁（即，将补丁作为默认分支的一部分）。正是这种情况导致了在 Linux 内核源 Git 存储库中使用多个分支。

Here are repository organization strategies maximizing source reuse, removing redundancy, and logically ordering your changes. This section presents strategies for the following cases:

> 以下是存储库组织策略，最大限度地提高源代码重用，消除冗余，并按逻辑顺序排列更改。本节介绍了以下情况下的策略：

- Encapsulating patches in a feature description and only including the patches in the BSP descriptions of the applicable boards.

> -将补丁封装在功能描述中，并且仅将补丁包括在适用板的 BSP 描述中。

- Creating a machine branch in your kernel source repository and applying the patches on that branch only.

> -在内核源代码存储库中创建一个机器分支，并仅在该分支上应用补丁。

- Creating a feature branch in your kernel source repository and merging that branch into your BSP when needed.

> -在内核源代码存储库中创建一个功能分支，并在需要时将该分支合并到 BSP 中。

The approach you take is entirely up to you and depends on what works best for your development model.

> 你所采取的方法完全取决于你自己，取决于什么最适合你的开发模型。

## Encapsulating Patches

If you are reusing patches from an external tree and are not working on the patches, you might find the encapsulated feature to be appropriate. Given this scenario, you do not need to create any branches in the source repository. Rather, you just take the static patches you need and encapsulate them within a feature description. Once you have the feature description, you simply include that into the BSP description as described in the \"`kernel-dev/advanced:bsp descriptions`{.interpreted-text role="ref"}\" section.

> 如果您正在重用外部树中的修补程序，而不处理修补程序，则可能会发现封装的功能是合适的。在这种情况下，您不需要在源存储库中创建任何分支。相反，您只需获取所需的静态补丁，并将它们封装在功能描述中。一旦您有了功能描述，您只需将其包含在 BSP 描述中，如“`kernel dev/advanced:BSP descriptions`｛.depreced text role=”ref“｝”部分所述。

You can find information on how to create patches and BSP descriptions in the \"`kernel-dev/advanced:patches`{.interpreted-text role="ref"}\" and \"`kernel-dev/advanced:bsp descriptions`{.interpreted-text role="ref"}\" sections.

> 您可以在\“`kernel dev/advanced:patches`｛.depreted text role=“ref”｝\”和\“`kernel dev/aadvanced:BSP descriptions`｛.epreted text role=“ref”}\”部分中找到有关如何创建修补程序和 BSP 说明的信息。

## Machine Branches

When you have multiple machines and architectures to support, or you are actively working on board support, it is more efficient to create branches in the repository based on individual machines. Having machine branches allows common source to remain in the development branch with any features specific to a machine stored in the appropriate machine branch. This organization method frees you from continually reintegrating your patches into a feature.

> 当您有多台机器和体系结构需要支持，或者您正在积极进行车载支持时，在存储库中基于单个机器创建分支会更高效。拥有机器分支允许公共源保留在开发分支中，并将特定于机器的任何特性存储在适当的机器分支中。这种组织方法使您免于不断地将修补程序重新集成到功能中。

Once you have a new branch, you can set up your kernel Metadata to use the branch a couple different ways. In the recipe, you can specify the new branch as the `KBRANCH`{.interpreted-text role="term"} to use for the board as follows:

> 一旦您有了一个新的分支，您就可以设置内核元数据，以几种不同的方式使用该分支。在配方中，您可以将新分支指定为“KBRANCH”｛.explored text role=“term”｝，用于板，如下所示：

```
KBRANCH = "mynewbranch"
```

Another method is to use the `branch` command in the BSP description:

> 另一种方法是在 BSP 描述中使用“branch”命令：

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

> 如果你发现自己有很多分支，你可以考虑使用一个类似于 Yocto Linux Kernel Git 存储库使用的分层分支系统：

```
common/kernel_type/machine
```

If you had two kernel types, \"standard\" and \"small\" for instance, three machines, and common as `mydir`, the branches in your Git repository might look like this:

> 如果您有两种内核类型，例如“标准”和“小型”，三台机器，并且通用为“mydir”，那么 Git 存储库中的分支可能如下所示：

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

> 该组织可以帮助澄清分支机构的关系。在这种情况下，“mydir/standard/machine_a”包括“mydir/base”和“mydir/standard/base”中的所有内容。“标准”和“小型”分支添加了特定于那些内核类型的源，无论出于何种原因，这些源都不适合其他分支。

::: note
::: title
Note
:::

The \"base\" branches are an artifact of the way Git manages its data internally on the filesystem: Git will not allow you to use `mydir/standard` and `mydir/standard/machine_a` because it would have to create a file and a directory named \"standard\".

> “基本”分支是 Git 在文件系统内部管理数据的方式的产物：Git 不允许使用“mydir/standard'”和“mydir/standard/machine_a'”，因为它必须创建一个名为“标准”的文件和目录。
> :::

## Feature Branches

When you are actively developing new features, it can be more efficient to work with that feature as a branch, rather than as a set of patches that have to be regularly updated. The Yocto Project Linux kernel tools provide for this with the `git merge` command.

> 当您正在积极开发新功能时，将该功能作为一个分支而不是作为一组必须定期更新的补丁来使用可能会更高效。Yocto Project Linux 内核工具通过“git merge”命令提供了这一功能。

To merge a feature branch into a BSP, insert the `git merge` command after any `branch` commands:

> 要将功能分支合并到 BSP 中，请在任何“分支”命令之后插入“git merge”命令：

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

> 本节简要介绍了可以在 SCC 描述文件（`.SCC`）中使用的命令：

- `branch [ref]`: Creates a new branch relative to the current branch (typically `${KTYPE}`) using the currently checked-out branch, or \"ref\" if specified.

> -`branch[ref]`：使用当前签出的分支或“ref\”（如果指定）创建相对于当前分支的新分支（通常为 `$｛KTYPE｝`）。

- `define`: Defines variables, such as `KMACHINE`{.interpreted-text role="term"}, `KTYPE`{.interpreted-text role="term"}, `KARCH`{.interpreted-text role="term"}, and `KFEATURE_DESCRIPTION`{.interpreted-text role="term"}.

> -`define`：定义变量，如 `KMACHINE`｛.depreted text role=“term”｝、`KTYPE`｛.epreted text role=“term“｝、` KARCH`｛.repreted text role=“term”｝和 `KFEATURE_DESCRIPTION`｛.expreted text 角色=“term”｝。

- `include SCC_FILE`: Includes an SCC file in the current file. The file is parsed as if you had inserted it inline.

> -`include SCC_FILE`：在当前文件中包括 SCC 文件。对该文件的分析就像您以内联方式插入它一样。

- `kconf [hardware|non-hardware] CFG_FILE`: Queues a configuration fragment for merging into the final Linux `.config` file.

> -`kconf[hardware|non-hardware]CFG_FILE`：排队将配置片段合并到最终的 Linux `.config` 文件中。

- `git merge GIT_BRANCH`: Merges the feature branch into the current branch.
- `patch PATCH_FILE`: Applies the patch to the current Git branch.

[^1]: `scc` stands for Series Configuration Control, but the naming has less significance in the current implementation of the tooling than it had in the past. Consider `scc` files to be description files.


> [^1]：“scc”代表系列配置控制，但与过去相比，该命名在当前工具实现中的重要性较小。将“scc”文件视为描述文件。
