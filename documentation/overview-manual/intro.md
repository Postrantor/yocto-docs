---
tip: translate by baidu@2023-06-07 10:47:09
...
---
title: The Yocto Project Overview and Concepts Manual
-----------------------------------------------------

# Welcome

Welcome to the Yocto Project Overview and Concepts Manual! This manual introduces the Yocto Project by providing concepts, software overviews, best-known-methods (BKMs), and any other high-level introductory information suitable for a new Yocto Project user.

> 欢迎使用 Yocto 项目概述和概念手册！本手册通过提供适用于 Yocto 项目新用户的概念、软件概述、最佳方法（BKM）和任何其他高级介绍信息来介绍 Yocto Project。

Here is what you can get from this manual:

> 以下是您可以从本手册中获得的内容：

- `overview-manual/yp-intro:introducing the yocto project`{.interpreted-text role="ref"}*:* This chapter provides an introduction to the Yocto Project. You will learn about features and challenges of the Yocto Project, the layer model, components and tools, development methods, the `Poky`{.interpreted-text role="term"} reference distribution, the OpenEmbedded build system workflow, and some basic Yocto terms.

> -`overview manual/yp intro:介绍yocto项目`{.depredicted text role=“ref”}*：*本章介绍 yocto 项目。您将了解 Yocto 项目的特点和挑战、层模型、组件和工具、开发方法、“Poky”｛.explored text role=“term”｝参考分布、OpenEmbedded 构建系统工作流以及一些基本的 Yocto 术语。

- `overview-manual/development-environment:the yocto project development environment`{.interpreted-text role="ref"}*:* This chapter helps you get started understanding the Yocto Project development environment. You will learn about open source, development hosts, Yocto Project source repositories, workflows using Git and the Yocto Project, a Git primer, and information about licensing.

> -`overview manual/development environment:yocto项目开发环境`{.depreted text role=“ref”}*：*本章帮助您开始了解 yocto 项目的开发环境。您将了解开源、开发主机、Yocto Project 源代码库、使用 Git 和 Yocto 项目的工作流、Git 入门知识，以及有关许可的信息。

- `/overview-manual/concepts`{.interpreted-text role="doc"} *:* This chapter presents various concepts regarding the Yocto Project. You can find conceptual information about components, development, cross-toolchains, and so forth.

> -`/overview manual/concepts`{.depredicted text role=“doc”}*：*本章介绍了有关 Yocto 项目的各种概念。您可以找到有关组件、开发、跨工具链等的概念信息。

This manual does not give you the following:

> 本手册未提供以下内容：

- *Step-by-step Instructions for Development Tasks:* Instructional procedures reside in other manuals within the Yocto Project documentation set. For example, the `/dev-manual/index`{.interpreted-text role="doc"} provides examples on how to perform various development tasks. As another example, the `/sdk-manual/index`{.interpreted-text role="doc"} manual contains detailed instructions on how to install an SDK, which is used to develop applications for target hardware.

> -*开发任务的分步说明：*说明程序位于 Yocto 项目文档集中的其他手册中。例如，`/dev/manual/index`{.depredicted text role=“doc”}提供了有关如何执行各种开发任务的示例。另一个例子是，`/sdk manual/index`{.depreced text role=“doc”}手册包含关于如何安装 sdk 的详细说明，该 sdk 用于为目标硬件开发应用程序。

- *Reference Material:* This type of material resides in an appropriate reference manual. For example, system variables are documented in the `/ref-manual/index`{.interpreted-text role="doc"}. As another example, the `/bsp-guide/index`{.interpreted-text role="doc"} contains reference information on BSPs.

> -*参考资料：*此类资料存在于适当的参考手册中。例如，系统变量记录在 `/ref manual/index`{.depreted text role=“doc”}中。另一个例子是，`nbsp guide/index`｛.depredicted text role=“doc”｝包含 bsp 的参考信息。

- *Detailed Public Information Not Specific to the Yocto Project:* For example, exhaustive information on how to use the Source Control Manager Git is better covered with Internet searches and official Git Documentation than through the Yocto Project documentation.

> -*并非 Yocto 项目特有的详细公共信息：*例如，与 Yocto Project 文档相比，互联网搜索和官方 Git 文档更能涵盖有关如何使用 Source Control Manager Git 的详尽信息。

# Other Information

Because this manual presents information for many different topics, supplemental information is recommended for full comprehension. For additional introductory information on the Yocto Project, see the :yocto_home:[Yocto Project Website \<\>]{.title-ref}. If you want to build an image with no knowledge of Yocto Project as a way of quickly testing it out, see the `/brief-yoctoprojectqs/index`{.interpreted-text role="doc"} document. For a comprehensive list of links and other documentation, see the \"`Links and Related Documentation <resources-links-and-related-documentation>`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual.

> 由于本手册提供了许多不同主题的信息，因此建议您提供补充信息以便于全面理解。有关 Yocto 项目的其他介绍性信息，请参阅：Yocto_home:[Yocto Project Website\<\>]｛.title ref｝。如果您想在不了解 Yocto 工程的情况下构建一个图像，作为快速测试它的一种方式，请参阅 `/brief yoctoprojectqs/index`｛.depreted text role=“doc”｝文档。有关链接和其他文档的综合列表，请参阅 Yocto 项目参考手册中的\“`links and Related documentation<resources links and Related documentation>`{.depreted text role=”ref“}\”一节。
