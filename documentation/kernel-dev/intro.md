---
tip: translate by openai@2023-06-10 10:40:46
...
---
title: Introduction
-------------------

# Overview

Regardless of how you intend to make use of the Yocto Project, chances are you will work with the Linux kernel. This manual describes how to set up your build host to support kernel development, introduces the kernel development process, provides background information on the Yocto Linux kernel `Metadata`, describes common tasks you can perform using the kernel tools, shows you how to use the kernel Metadata needed to work with the kernel inside the Yocto Project, and provides insight into how the Yocto Project team develops and maintains Yocto Linux kernel Git repositories and Metadata.

> 无论您如何打算使用 Yocto Project，您都可能会使用 Linux 内核。本手册描述了如何设置构建主机以支持内核开发，介绍了内核开发过程，提供了有关 Yocto Linux 内核“Metadata”的背景信息，描述了使用内核工具可以执行的常见任务，向您展示了如何使用 Yocto Project 内部工作所需的内核 Metadata，并提供了有关 Yocto Project 团队如何开发和维护 Yocto Linux 内核 Git 存储库和 Metadata 的洞察。

Each Yocto Project release has a set of Yocto Linux kernel recipes, whose Git repositories you can view in the Yocto :yocto_[git:%60Source](git:%60Source) Repositories \<\>[ under the \"Yocto Linux Kernel\" heading. New recipes for the release track the latest Linux kernel upstream developments from [https://www.kernel.org](https://www.kernel.org) and introduce newly-supported platforms. Previous recipes in the release are refreshed and supported for at least one additional Yocto Project release. As they align, these previous releases are updated to include the latest from the Long Term Support Initiative (LTSI) project. You can learn more about Yocto Linux kernels and LTSI in the \":ref:\`kernel-dev/concepts-appx:yocto project kernel development and maintenance]\" section.

> 每个 Yocto 项目发行版都有一组 Yocto Linux 内核配方，您可以在 Yocto：yocto \ _ [git：％60Source](git%EF%BC%9A%EF%BC%8560Source) 存储库下的“ Yocto Linux 内核”标题下查看它们的 Git 存储库。新版本的发行跟踪最新的 Linux 内核上游开发，从 [https://www.kernel.org](https://www.kernel.org) 引入新支持的平台。发行版中的先前版本将至少支持一个额外的 Yocto 项目发行版。随着它们的一致性，这些先前的版本将更新以包括来自长期支持倡议(LTSI)项目的最新内容。您可以在“：ref：\`kernel-dev / concepts-appx：yocto 项目内核开发和维护] ”部分了解更多关于 Yocto Linux 内核和 LTSI 的信息。

Also included is a Yocto Linux kernel development recipe (`linux-yocto-dev.bb`) should you want to work with the very latest in upstream Yocto Linux kernel development and kernel Metadata development.

> 也包括一个 Yocto Linux 内核开发配方(`linux-yocto-dev.bb`)，如果您想要使用最新的上游 Yocto Linux 内核开发和内核元数据开发。

::: note
::: title
Note
:::

For more on Yocto Linux kernels, see the \"`kernel-dev/concepts-appx:yocto project kernel development and maintenance`\" section.

> 对于关于 Yocto Linux 内核的更多信息，请参阅“kernel-dev/concepts-appx：Yocto Project 内核开发和维护”一节。
> :::

The Yocto Project also provides a powerful set of kernel tools for managing Yocto Linux kernel sources and configuration data. You can use these tools to make a single configuration change, apply multiple patches, or work with your own kernel sources.

> 驗證項目也提供了一套強大的核心工具，用於管理 Yocto Linux 核心源代碼和配置數據。您可以使用這些工具進行單個配置更改，應用多個補丁，或者使用自己的核心源代碼進行工作。

In particular, the kernel tools allow you to generate configuration fragments that specify only what you must, and nothing more. Configuration fragments only need to contain the highest level visible `CONFIG` options as presented by the Yocto Linux kernel `menuconfig` system. Contrast this against a complete Yocto Linux kernel `.config` file, which includes all the automatically selected `CONFIG` options. This efficiency reduces your maintenance effort and allows you to further separate your configuration in ways that make sense for your project. A common split separates policy and hardware. For example, all your kernels might support the `proc` and `sys` filesystems, but only specific boards require sound, USB, or specific drivers. Specifying these configurations individually allows you to aggregate them together as needed, but maintains them in only one place. Similar logic applies to separating source changes.

> 特别是，内核工具允许您生成仅指定必须的配置片段，而不指定其他内容。配置片段只需包含由 Yocto Linux 内核 menuconfig 系统提供的最高级可见的 CONFIG 选项。与完整的 Yocto Linux 内核.config 文件相比，其中包括所有自动选择的 CONFIG 选项。这种效率降低了您的维护工作，并允许您以为您的项目更有意义的方式进一步分离您的配置。常见的分割将政策和硬件分开。例如，所有内核都可以支持 proc 和 sys 文件系统，但只有特定的板才需要声音，USB 或特定的驱动程序。单独指定这些配置可以根据需要将它们聚合起来，但仅保留一个位置。类似的逻辑也适用于分离源代码更改。

If you do not maintain your own kernel sources and need to make only minimal changes to the sources, the released recipes provide a vetted base upon which to layer your changes. Doing so allows you to benefit from the continual kernel integration and testing performed during development of the Yocto Project.

> 如果您不维护自己的内核源码，只需对源码进行最小的更改，发布的配方提供了一个审查过的基础，可以在此基础上构建您的更改。这样做可以使您受益于在开发 Yocto 项目期间进行的持续内核集成和测试。

If, instead, you have a very specific Linux kernel source tree and are unable to align with one of the official Yocto Linux kernel recipes, you have a way to use the Yocto Project Linux kernel tools with your own kernel sources.

> 如果您有一个非常特定的 Linux 内核源树，无法与官方 Yocto Linux 内核配方对齐，您可以使用 Yocto Project Linux 内核工具与您自己的内核源码一起使用。

The remainder of this manual provides instructions for completing specific Linux kernel development tasks. These instructions assume you are comfortable working with :oe_wiki:[BitBake \</Bitbake\>] recipes and basic open-source development tools. Understanding these concepts will facilitate the process of working with the kernel recipes. If you find you need some additional background, please be sure to review and understand the following documentation:

> 本手册的其余部分提供了完成特定 Linux 内核开发任务的说明。这些说明假定您熟悉使用 BitBake 配方和基本开源开发工具。了解这些概念将有助于处理内核配方。如果您发现需要一些额外的背景，请务必查看并理解以下文档：

- `/brief-yoctoprojectqs/index` document.
- `/overview-manual/index`.
- ``devtool workflow <sdk-manual/extensible:using \`\`devtool\`\` in your sdk workflow>`` as described in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.

> 根据 Yocto 项目应用开发和可扩展软件开发套件(eSDK)手册中所述，使用“devtool”在您的 SDK 工作流中进行工作流程。

- The \"`dev-manual/layers:understanding and creating layers`\" section in the Yocto Project Development Tasks Manual.
- The \"`kernel-dev/intro:kernel modification workflow`\" section.

# Kernel Modification Workflow

Kernel modification involves changing the Yocto Project kernel, which could involve changing configuration options as well as adding new kernel recipes. Configuration changes can be added in the form of configuration fragments, while recipe modification comes through the kernel\'s `recipes-kernel` area in a kernel layer you create.

> 修改内核涉及更改 Yocto Project 内核，可能涉及更改配置选项以及添加新的内核配方。可以通过配置片段的形式添加配置更改，而配方修改则通过在您创建的内核层中的 `recipes-kernel` 区域实现。

This section presents a high-level overview of the Yocto Project kernel modification workflow. The illustration and accompanying list provide general information and references for further information.

> 这一节提供了 Yocto 项目内核修改工作流程的高级概述。该插图和附带的列表提供了一般信息和进一步参考信息。

![image](figures/kernel-dev-flow.png)

1. *Set up Your Host Development System to Support Development Using the Yocto Project*: See the \"`/dev-manual/start`\" section in the Yocto Project Development Tasks Manual for options on how to get a build host ready to use the Yocto Project.

> 请参阅 Yocto Project 开发任务手册中的“/dev-manual/start”部分，了解如何设置主机开发系统以支持使用 Yocto Project 进行开发的选项。

2. *Set Up Your Host Development System for Kernel Development:* It is recommended that you use `devtool` for kernel development. Alternatively, you can use traditional kernel development methods with the Yocto Project. Either way, there are steps you need to take to get the development environment ready.

> 为内核开发设置您的主机开发系统：建议您使用 devtool 进行内核开发。或者，您也可以使用 Yocto Project 中的传统内核开发方法。无论哪种方式，您都需要采取步骤来准备开发环境。

Using `devtool` requires that you have a clean build of the image. For more information, see the \"``kernel-dev/common:getting ready to develop using \`\`devtool\`\` ``\" section.

> 使用 `devtool` 需要您有一个镜像的干净构建。有关更多信息，请参阅“kernel-dev / common：准备使用 `devtool` 开发”部分。

Using traditional kernel development requires that you have the kernel source available in an isolated local Git repository. For more information, see the \"`kernel-dev/common:getting ready for traditional kernel development`\" section.

> 使用传统的内核开发需要您在一个隔离的本地 Git 存储库中拥有内核源代码。有关更多信息，请参见“kernel-dev / common：准备传统内核开发”部分。

3. *Make Changes to the Kernel Source Code if applicable:* Modifying the kernel does not always mean directly changing source files. However, if you have to do this, you make the changes to the files in the Yocto\'s `Build Directory`\" section.

> 如果需要，修改内核源代码：修改内核并不总是意味着直接修改源文件。但是，如果你必须这样做，你可以在使用 devtool 时，在 Yocto 的构建目录中修改文件。有关更多信息，请参见“使用 devtool 来修补内核”部分。

If you are using traditional kernel development, you edit the source files in the kernel\'s local Git repository. For more information, see the \"`kernel-dev/common:using traditional kernel development to patch the kernel`\" section.

> 如果您使用传统的内核开发，则可以在内核的本地 Git 存储库中编辑源文件。有关详细信息，请参阅“kernel-dev/common：使用传统内核开发来修补内核”部分。

4. *Make Kernel Configuration Changes if Applicable:* If your situation calls for changing the kernel\'s configuration, you can use ``menuconfig <kernel-dev/common:using \`\`menuconfig\`\`>``, which allows you to interactively develop and test the configuration changes you are making to the kernel. Saving changes you make with `menuconfig` updates the kernel\'s `.config` file.

> 如果需要更改内核配置，您可以使用 ``menuconfig <kernel-dev/common:using \`\`menuconfig\`\`>``，它允许您交互式地开发和测试对内核所做的配置更改。使用 `menuconfig` 保存的更改会更新内核的 `.config` 文件。

::: note
::: title
Note
:::

Try to resist the temptation to directly edit an existing `.config` file, which is found in the `Build Directory` among the source code used for the build. Doing so, can produce unexpected results when the OpenEmbedded build system regenerates the configuration file.

> 试着抵制直接编辑现有的 `.config` 文件的诱惑，这个文件位于构建使用的源代码中的 `构建目录` 中。这样做可能会在 OpenEmbedded 构建系统重新生成配置文件时产生意想不到的结果。
> :::

Once you are satisfied with the configuration changes made using `menuconfig` and you have saved them, you can directly compare the resulting `.config` file against an existing original and gather those changes into a `configuration fragment file <kernel-dev/common:creating configuration fragments>` to be referenced from within the kernel\'s `.bbappend` file.

> 一旦您使用 `menuconfig` 对配置进行了更改并保存，您就可以直接将生成的 `.config` 文件与现有原始文件进行比较，并将这些更改收集到 `配置片段文件<kernel-dev/common:creating configuration fragments>` 中，以便在内核的 `.bbappend` 文件中引用。

Additionally, if you are working in a BSP layer and need to modify the BSP\'s kernel\'s configuration, you can use `menuconfig`.

5. *Rebuild the Kernel Image With Your Changes:* Rebuilding the kernel image applies your changes. Depending on your target hardware, you can verify your changes on actual hardware or perhaps QEMU.

> 重建内核映像以应用您的更改。根据您的目标硬件，您可以在实际硬件上或 QEMU 上验证更改。

The remainder of this developer\'s guide covers common tasks typically used during kernel development, advanced Metadata usage, and Yocto Linux kernel maintenance concepts.

> 本开发者指南的其体部分涵盖了在内核开发过程中通常使用的常见任务、高级元数据使用以及 Yocto Linux 内核维护概念。
