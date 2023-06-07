---
tip: translate by baidu@2023-06-07 10:57:42
...
---
title: Introduction
-------------------

# Overview

Regardless of how you intend to make use of the Yocto Project, chances are you will work with the Linux kernel. This manual describes how to set up your build host to support kernel development, introduces the kernel development process, provides background information on the Yocto Linux kernel `Metadata`{.interpreted-text role="term"}, describes common tasks you can perform using the kernel tools, shows you how to use the kernel Metadata needed to work with the kernel inside the Yocto Project, and provides insight into how the Yocto Project team develops and maintains Yocto Linux kernel Git repositories and Metadata.

> 不管您打算如何利用 Yocto 项目，您都有可能使用 Linux 内核。本手册介绍了如何设置构建主机以支持内核开发，介绍了内核开发过程，提供了 Yocto Linux 内核的背景信息 `Metadata`｛.explored text role=“term”｝，描述了可以使用内核工具执行的常见任务，向您展示了如何使用 Yocto 项目中使用内核所需的内核元数据，并深入了解 Yocto 项目团队如何开发和维护 Yocto Linux 内核 Git 存储库和元数据。

Each Yocto Project release has a set of Yocto Linux kernel recipes, whose Git repositories you can view in the Yocto :yocto\_[git:%60Source](git:%60Source) Repositories \<\>[ under the \"Yocto Linux Kernel\" heading. New recipes for the release track the latest Linux kernel upstream developments from [https://www.kernel.org](https://www.kernel.org) and introduce newly-supported platforms. Previous recipes in the release are refreshed and supported for at least one additional Yocto Project release. As they align, these previous releases are updated to include the latest from the Long Term Support Initiative (LTSI) project. You can learn more about Yocto Linux kernels and LTSI in the \":ref:\`kernel-dev/concepts-appx:yocto project kernel development and maintenance]{.title-ref}\" section.

> 每个 Yocto Project 版本都有一组 Yocto Linux 内核配方，您可以在 Yocto:Yocto\_[Git:%60Source](Git:%60Source)Repository\<\>[“Yocto Linux kernel”标题下查看其 Git 存储库。该版本的新配方跟踪 [https://www.kernel.org](https://www.kernel.org) 并引入新支持的平台。该版本中以前的配方将被刷新，并至少支持一个 Yocto Project 附加版本。随着它们的一致性，这些先前的版本将进行更新，以包括长期支持倡议（LTSI）项目的最新版本。您可以在\“：ref:\`kernel dev/contensions appx:Yocto project kernel development and maintenance]{.title-ref}\”部分了解更多关于 Yocto Linux 内核和 LTSI 的信息。

Also included is a Yocto Linux kernel development recipe (`linux-yocto-dev.bb`) should you want to work with the very latest in upstream Yocto Linux kernel development and kernel Metadata development.

> 如果您想使用最新的上游 Yocto Linux 内核开发和内核元数据开发，还包括 Yocto Linux 内核开发配方（“Linux-yoct-dev.bb”）。

::: note
::: title
Note
:::

For more on Yocto Linux kernels, see the \"`kernel-dev/concepts-appx:yocto project kernel development and maintenance`{.interpreted-text role="ref"}\" section.

> 有关 Yocto Linux 内核的更多信息，请参阅“`kernel dev/contensions appx:Yocto项目内核开发和维护`{.depreted text role=“ref”}\”一节。
> :::

The Yocto Project also provides a powerful set of kernel tools for managing Yocto Linux kernel sources and configuration data. You can use these tools to make a single configuration change, apply multiple patches, or work with your own kernel sources.

> Yocto 项目还提供了一套功能强大的内核工具，用于管理 YoctoLinux 内核源代码和配置数据。您可以使用这些工具进行单个配置更改、应用多个修补程序，或者使用自己的内核源代码。

In particular, the kernel tools allow you to generate configuration fragments that specify only what you must, and nothing more. Configuration fragments only need to contain the highest level visible `CONFIG` options as presented by the Yocto Linux kernel `menuconfig` system. Contrast this against a complete Yocto Linux kernel `.config` file, which includes all the automatically selected `CONFIG` options. This efficiency reduces your maintenance effort and allows you to further separate your configuration in ways that make sense for your project. A common split separates policy and hardware. For example, all your kernels might support the `proc` and `sys` filesystems, but only specific boards require sound, USB, or specific drivers. Specifying these configurations individually allows you to aggregate them together as needed, but maintains them in only one place. Similar logic applies to separating source changes.

> 特别是，内核工具允许您生成配置片段，这些片段只指定您必须指定的内容，而不是其他内容。配置片段只需要包含 Yocto Linux 内核“menuconfig”系统提供的最高级别的可见“CONFIG”选项。将其与完整的 Yocto Linux 内核“.config”文件进行对比，该文件包括所有自动选择的“config”选项。这种效率减少了维护工作量，并允许您以对项目有意义的方式进一步分离配置。策略和硬件之间存在共同的分离。例如，您的所有内核可能都支持“proc”和“sys”文件系统，但只有特定的板需要声音、USB 或特定的驱动程序。单独指定这些配置可以根据需要将它们聚合在一起，但只能在一个位置维护它们。类似的逻辑适用于分离源更改。

If you do not maintain your own kernel sources and need to make only minimal changes to the sources, the released recipes provide a vetted base upon which to layer your changes. Doing so allows you to benefit from the continual kernel integration and testing performed during development of the Yocto Project.

> 如果您不维护自己的内核源代码，并且只需要对源代码进行最小的更改，那么发布的配方将提供一个经过审查的基础，以便对更改进行分层。这样做可以让您受益于 Yocto 项目开发过程中不断进行的内核集成和测试。

If, instead, you have a very specific Linux kernel source tree and are unable to align with one of the official Yocto Linux kernel recipes, you have a way to use the Yocto Project Linux kernel tools with your own kernel sources.

> 相反，如果您有一个非常特定的 Linux 内核源代码树，并且无法与官方的 Yocto Linux 内核配方之一保持一致，那么您可以将 Yocto Project Linux 内核工具与自己的内核源代码一起使用。

The remainder of this manual provides instructions for completing specific Linux kernel development tasks. These instructions assume you are comfortable working with :oe_wiki:[BitBake \</Bitbake\>]{.title-ref} recipes and basic open-source development tools. Understanding these concepts will facilitate the process of working with the kernel recipes. If you find you need some additional background, please be sure to review and understand the following documentation:

> 本手册的其余部分提供了完成特定 Linux 内核开发任务的说明。这些说明假设您可以轻松地使用：oe_wiki:[BitBake\</BitBake\>]｛.title-ref｝配方和基本的开源开发工具。理解这些概念将有助于使用内核配方。如果您发现自己需要一些额外的背景知识，请务必查看并理解以下文档：

- `/brief-yoctoprojectqs/index`{.interpreted-text role="doc"} document.
- `/overview-manual/index`{.interpreted-text role="doc"}.
- ``devtool workflow <sdk-manual/extensible:using \`\`devtool\`\` in your sdk workflow>``{.interpreted-text role="ref"} as described in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.
- The \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> -Yocto 项目开发任务手册中的\“`devmanual/layers:understanding and createing layers`｛.depreced text role=“ref”｝\”部分。

- The \"`kernel-dev/intro:kernel modification workflow`{.interpreted-text role="ref"}\" section.

# Kernel Modification Workflow

Kernel modification involves changing the Yocto Project kernel, which could involve changing configuration options as well as adding new kernel recipes. Configuration changes can be added in the form of configuration fragments, while recipe modification comes through the kernel\'s `recipes-kernel` area in a kernel layer you create.

> 内核修改涉及更改 Yocto 项目内核，这可能涉及更改配置选项以及添加新的内核配方。配置更改可以以配置片段的形式添加，而配方修改则通过您创建的内核层中内核的“配方内核”区域进行。

This section presents a high-level overview of the Yocto Project kernel modification workflow. The illustration and accompanying list provide general information and references for further information.

> 本节介绍 Yocto 项目内核修改工作流程的高级概述。图示和随附的列表提供了一般信息和进一步信息的参考。

![image](figures/kernel-dev-flow.png){width="100.0%"}

1. *Set up Your Host Development System to Support Development Using the Yocto Project*: See the \"`/dev-manual/start`{.interpreted-text role="doc"}\" section in the Yocto Project Development Tasks Manual for options on how to get a build host ready to use the Yocto Project.

> 1.*设置您的主机开发系统以支持使用 Yocto 项目的开发*：有关如何使生成主机准备好使用 YoctoProject 的选项，请参阅 Yocto Project Development Tasks manual 中的\“`/dev/manual/start`｛.depreted text role=“doc”｝\”部分。

2. *Set Up Your Host Development System for Kernel Development:* It is recommended that you use `devtool` for kernel development. Alternatively, you can use traditional kernel development methods with the Yocto Project. Either way, there are steps you need to take to get the development environment ready.

> 2.*为内核开发设置主机开发系统：*建议您使用“devtool”进行内核开发。或者，您可以在 Yocto 项目中使用传统的内核开发方法。无论哪种方式，都需要采取一些步骤来为开发环境做好准备。

```
Using `devtool` requires that you have a clean build of the image. For more information, see the \"`` kernel-dev/common:getting ready to develop using \`\`devtool\`\` ``{.interpreted-text role="ref"}\" section.

Using traditional kernel development requires that you have the kernel source available in an isolated local Git repository. For more information, see the \"`kernel-dev/common:getting ready for traditional kernel development`{.interpreted-text role="ref"}\" section.
```

3. *Make Changes to the Kernel Source Code if applicable:* Modifying the kernel does not always mean directly changing source files. However, if you have to do this, you make the changes to the files in the Yocto\'s `Build Directory`{.interpreted-text role="term"} if you are using `devtool`. For more information, see the \"``kernel-dev/common:using \`\`devtool\`\` to patch the kernel``{.interpreted-text role="ref"}\" section.

> 3.*更改内核源代码（如果适用）：*修改内核并不总是意味着直接更改源文件。但是，如果必须这样做，如果您使用的是“devtool”，则可以对 Yocto 的“构建目录”｛.despered text role=“term”｝中的文件进行更改。有关更多信息，请参阅“``kernel dev/common:using \`` devtool\`\` to patch the kernel ``{.depredicted text role=“ref”}\”一节。

```
If you are using traditional kernel development, you edit the source files in the kernel\'s local Git repository. For more information, see the \"`kernel-dev/common:using traditional kernel development to patch the kernel`{.interpreted-text role="ref"}\" section.
```

4. *Make Kernel Configuration Changes if Applicable:* If your situation calls for changing the kernel\'s configuration, you can use ``menuconfig <kernel-dev/common:using \`\`menuconfig\`\`>``{.interpreted-text role="ref"}, which allows you to interactively develop and test the configuration changes you are making to the kernel. Saving changes you make with `menuconfig` updates the kernel\'s `.config` file.

> 4.*在适用的情况下更改内核配置：*如果您的情况需要更改内核的配置，您可以使用 ``menuconfig<Kernel dev/common:using\`\`menuconfig\`\`>``｛.explored text role=“ref”｝，它允许您交互式地开发和测试您对内核所做的配置更改。保存您使用“menuconfig”所做的更改会更新内核的“.config”文件。

```
::: note
::: title
Note
:::

Try to resist the temptation to directly edit an existing `.config` file, which is found in the `Build Directory`{.interpreted-text role="term"} among the source code used for the build. Doing so, can produce unexpected results when the OpenEmbedded build system regenerates the configuration file.
:::

Once you are satisfied with the configuration changes made using `menuconfig` and you have saved them, you can directly compare the resulting `.config` file against an existing original and gather those changes into a `configuration fragment file <kernel-dev/common:creating configuration fragments>`{.interpreted-text role="ref"} to be referenced from within the kernel\'s `.bbappend` file.

Additionally, if you are working in a BSP layer and need to modify the BSP\'s kernel\'s configuration, you can use `menuconfig`.
```

5. *Rebuild the Kernel Image With Your Changes:* Rebuilding the kernel image applies your changes. Depending on your target hardware, you can verify your changes on actual hardware or perhaps QEMU.

> 5.*使用您的更改重建内核映像：*重建内核映像应用您的更改。根据您的目标硬件，您可以在实际硬件或 QEMU 上验证您的更改。

The remainder of this developer\'s guide covers common tasks typically used during kernel development, advanced Metadata usage, and Yocto Linux kernel maintenance concepts.

> 本开发人员指南的其余部分涵盖了内核开发过程中通常使用的常见任务、高级元数据的使用以及 Yocto Linux 内核维护概念。
