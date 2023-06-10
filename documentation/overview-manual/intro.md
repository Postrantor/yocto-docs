---
tip: translate by openai@2023-06-10 10:50:06
...
---
title: The Yocto Project Overview and Concepts Manual
-----------------------------------------------------

# Welcome

Welcome to the Yocto Project Overview and Concepts Manual! This manual introduces the Yocto Project by providing concepts, software overviews, best-known-methods (BKMs), and any other high-level introductory information suitable for a new Yocto Project user.

> 欢迎来到 Yocto 项目概览和概念手册！本手册通过提供概念、软件概述、最佳实践（BKM）和其他适用于新 Yocto 项目用户的高级介绍信息，介绍了 Yocto 项目。

Here is what you can get from this manual:

- `overview-manual/yp-intro:introducing the yocto project`{.interpreted-text role="ref"}*:* This chapter provides an introduction to the Yocto Project. You will learn about features and challenges of the Yocto Project, the layer model, components and tools, development methods, the `Poky`{.interpreted-text role="term"} reference distribution, the OpenEmbedded build system workflow, and some basic Yocto terms.

> 本章提供了一个对 Yocto 项目的介绍。您将了解 Yocto 项目的特点和挑战，层次模型，组件和工具，开发方法，Poky 参考分发，OpenEmbedded 构建系统工作流程以及一些基本的 Yocto 术语。

- `overview-manual/development-environment:the yocto project development environment`{.interpreted-text role="ref"}*:* This chapter helps you get started understanding the Yocto Project development environment. You will learn about open source, development hosts, Yocto Project source repositories, workflows using Git and the Yocto Project, a Git primer, and information about licensing.

> 本章将帮助您开始了解 Yocto 项目开发环境。您将了解开源、开发主机、Yocto 项目源存储库、使用 Git 和 Yocto 项目的工作流程、Git 入门以及有关许可的信息。

- `/overview-manual/concepts`{.interpreted-text role="doc"} *:* This chapter presents various concepts regarding the Yocto Project. You can find conceptual information about components, development, cross-toolchains, and so forth.

> 这一章介绍了有关 Yocto 项目的各种概念。您可以找到有关组件，开发，跨工具链等的概念信息。

This manual does not give you the following:

- *Step-by-step Instructions for Development Tasks:* Instructional procedures reside in other manuals within the Yocto Project documentation set. For example, the `/dev-manual/index`{.interpreted-text role="doc"} provides examples on how to perform various development tasks. As another example, the `/sdk-manual/index`{.interpreted-text role="doc"} manual contains detailed instructions on how to install an SDK, which is used to develop applications for target hardware.

> 详细的开发任务步骤说明：可以在 Yocto 项目文档集中的其他手册中找到指令程序。例如，`/dev-manual/index`{.interpreted-text role="doc"} 提供了如何执行各种开发任务的示例。另一个例子是，`/sdk-manual/index`{.interpreted-text role="doc"} 手册包含了安装 SDK 的详细说明，SDK 用于为目标硬件开发应用程序。

- *Reference Material:* This type of material resides in an appropriate reference manual. For example, system variables are documented in the `/ref-manual/index`{.interpreted-text role="doc"}. As another example, the `/bsp-guide/index`{.interpreted-text role="doc"} contains reference information on BSPs.

> 参考资料：此类资料位于适当的参考手册中。例如，系统变量在 `/ref-manual/index`{.interpreted-text role="doc"} 中有文档说明。另一个例子是，`/bsp-guide/index`{.interpreted-text role="doc"} 包含有关 BSP 的参考信息。

- *Detailed Public Information Not Specific to the Yocto Project:* For example, exhaustive information on how to use the Source Control Manager Git is better covered with Internet searches and official Git Documentation than through the Yocto Project documentation.

> 详细的公共信息不特指 Yocto 项目：例如，如何使用源代码控制管理器 Git 的详尽信息，比通过 Yocto 项目文档更好地覆盖可以通过互联网搜索和官方 Git 文档获得。

# Other Information

Because this manual presents information for many different topics, supplemental information is recommended for full comprehension. For additional introductory information on the Yocto Project, see the :yocto_home:[Yocto Project Website \<\>]{.title-ref}. If you want to build an image with no knowledge of Yocto Project as a way of quickly testing it out, see the `/brief-yoctoprojectqs/index`{.interpreted-text role="doc"} document. For a comprehensive list of links and other documentation, see the \"`Links and Related Documentation <resources-links-and-related-documentation>`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual.

> 由于本手册涵盖了许多不同的主题，因此建议查阅补充资料以获得完全的理解。要了解有关 Yocto 项目的更多介绍信息，请参阅:yocto_home:[Yocto 项目网站\<\>]{.title-ref}。如果您想要构建一个没有 Yocto 项目知识的镜像，以快速测试它，请参阅 `/brief-yoctoprojectqs/index`{.interpreted-text role="doc"}文档。要获取链接和其他文档的完整列表，请参阅 Yocto 项目参考手册中的“`Links and Related Documentation <resources-links-and-related-documentation>`{.interpreted-text role="ref"}”部分。
