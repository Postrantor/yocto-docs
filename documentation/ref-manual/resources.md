---
tip: translate by openai@2023-06-07 22:27:09
...
---
title: Contributions and Additional Information
-----------------------------------------------

# Introduction

The Yocto Project team is happy for people to experiment with the Yocto Project. There is a number of places where you can find help if you run into difficulties or find bugs. This presents information about contributing and participating in the Yocto Project.

> 团队成员很高兴人们可以尝试使用 Yocto 项目。如果遇到困难或发现错误，您可以在多个地方寻求帮助。这里提供有关参与和参与 Yocto 项目的信息。

# Contributions

The Yocto Project gladly accepts contributions. You can submit changes to the project either by creating and sending pull requests, or by submitting patches through email. For information on how to do both as well as information on how to identify the maintainer for each area of code, see the \"`dev-manual/changes:submitting a change to the yocto project`\" section in the Yocto Project Development Tasks Manual.

> 项目 Yocto 很高兴接受贡献。您可以通过创建和发送拉取请求或通过电子邮件提交补丁来提交项目的更改。有关如何做到这一点以及如何确定每个代码区域的维护人员的信息，请参见 Yocto 项目开发任务手册中的“dev-manual/changes：向 Yocto 项目提交更改”部分。

# Yocto Project Bugzilla

The Yocto Project uses its own implementation of :yocto_bugs:[Bugzilla \<\>] to track defects (bugs). Implementations of Bugzilla work well for group development because they track bugs and code changes, can be used to communicate changes and problems with developers, can be used to submit and review patches, and can be used to manage quality assurance.

> 项目 Yocto 使用自己的实现：yocto_bugs:[Bugzilla \<\>] 来跟踪缺陷(bugs)。Bugzilla 的实现对于团队开发非常有效，因为它可以跟踪 bugs 和代码变化，可以用来与开发人员沟通变化和问题，可以用来提交和审核补丁，还可以用来管理质量保证。

Sometimes it is helpful to submit, investigate, or track a bug against the Yocto Project itself (e.g. when discovering an issue with some component of the build system that acts contrary to the documentation or your expectations).

> 有时候，提交、调查或跟踪对 Yocto 项目本身的错误(例如，当发现构建系统的某个组件与文档或您的期望相悖时)是有帮助的。

For a general procedure and guidelines on how to use Bugzilla to submit a bug against the Yocto Project, see the following:

- The \"`dev-manual/changes:submitting a defect against the yocto project`\" section in the Yocto Project Development Tasks Manual.

> 请参阅 Yocto Project 开发任务手册中的“dev-manual/changes：向 Yocto Project 提交缺陷”部分。

- The Yocto Project :yocto_wiki:[Bugzilla wiki page \</Bugzilla_Configuration_and_Bug_Tracking\>]

For information on Bugzilla in general, see [https://www.bugzilla.org/about/](https://www.bugzilla.org/about/).

# Mailing lists

There are multiple mailing lists maintained by the Yocto Project as well as related OpenEmbedded mailing lists for discussion, patch submission and announcements. To subscribe to one of the following mailing lists, click on the appropriate URL in the following list and follow the instructions:

> Yocto 项目以及相关的 OpenEmbedded 邮件列表维护了多个邮件列表，用于讨论、提交补丁和公告。要订阅以下邮件列表之一，请点击以下列表中的适当网址，并按照说明操作：

- :yocto_lists:[/g/yocto] \-\-- general Yocto Project discussion mailing list.
- :oe_lists:[/g/openembedded-core] \-\-- discussion mailing list about OpenEmbedded-Core (the core metadata).
- :oe_lists:[/g/openembedded-devel] \-\-- discussion mailing list about OpenEmbedded.
- :oe_lists:[/g/bitbake-devel] build tool.
- :yocto_lists:[/g/poky].
- :yocto_lists:[/g/yocto-announce] \-\-- mailing list to receive official Yocto Project release and milestone announcements.
- :yocto_lists:[/g/docs] \-\-- discussion mailing list about the Yocto Project documentation.

See also :yocto_home:[the description of all mailing lists \</community/mailing-lists/\>].

# Internet Relay Chat (IRC)

Two IRC channels on [Libera Chat](https://libera.chat/) are available for the Yocto Project and OpenEmbedded discussions:

- `#yocto`
- `#oe`

# Links and Related Documentation

Here is a list of resources you might find helpful:

- :yocto_home:\`The Yocto Project Website \<\>\`: The home site for the Yocto Project.
- :yocto_wiki:\`The Yocto Project Main Wiki Page \<\>\`: The main wiki page for the Yocto Project. This page contains information about project planning, release engineering, QA & automation, a reference site map, and other resources related to the Yocto Project.

> 主页面：Yocto 项目的主要维基页面。本页面包含有关项目规划、发布工程、QA 和自动化、参考站点地图以及与 Yocto 项目相关的其他资源的信息。

- :oe_home:\`OpenEmbedded \<\>\`: The build system used by the Yocto Project. This project is the upstream, generic, embedded distribution from which the Yocto Project derives its build system (Poky) and to which it contributes.

> - :oe_home: OpenEmbedded <>: 这是 Yocto 项目使用的构建系统。该项目是 Yocto 项目从中获得其构建系统(Poky)的上游通用嵌入式分发，并且它也对其进行贡献。

- :oe_wiki:\`BitBake \</BitBake\>\`: The tool used to process metadata.
- `BitBake User Manual <bitbake:index>`: A comprehensive guide to the BitBake tool. If you want information on BitBake, see this manual.

> 《BitBake 用户手册《bitbake:index》：一份全面的 BitBake 工具指南。如果你想了解有关 BitBake 的信息，请参阅本手册。

- `/brief-yoctoprojectqs/index`: This short document lets you experience building an image using the Yocto Project without having to understand any concepts or details.

> 这篇简短的文档让您在不需要了解任何概念或细节的情况下，使用 Yocto Project 构建镜像。

- `/overview-manual/index`: This manual provides overview and conceptual information about the Yocto Project.
- `/dev-manual/index`: This manual is a \"how-to\" guide that presents procedures useful to both application and system developers who use the Yocto Project.

> 这份手册是一本“如何”指南，为使用 Yocto Project 的应用程序和系统开发者提供有用的程序。

- `/sdk-manual/index` manual: This guide provides information that lets you get going with the standard or extensible SDK. An SDK, with its cross-development toolchains, allows you to develop projects inside or outside of the Yocto Project environment.

> 这份指南提供了有关如何使用标准或可扩展 SDK 的信息，SDK 和其交叉开发工具链允许您在 Yocto 项目环境内或外开发项目。

- `/bsp-guide/bsp`: This guide defines the structure for BSP components. Having a commonly understood structure encourages standardization.

> 这个指南定义了 BSP 组件的结构。拥有一个普遍理解的结构有助于标准化。

- `/kernel-dev/index`: This manual describes how to work with Linux Yocto kernels as well as provides a bit of conceptual information on the construction of the Yocto Linux kernel tree.

> 这个手册描述了如何使用 Linux Yocto 内核以及提供了一些关于构建 Yocto Linux 内核树的概念信息。

- `/ref-manual/index`: This manual provides reference material such as variable, task, and class descriptions.
- :yocto_docs:\`Yocto Project Mega-Manual \</singleindex.html\>\`: This manual is simply a single HTML file comprised of the bulk of the Yocto Project manuals. It makes it easy to search for phrases and terms used in the Yocto Project documentation set.

> 这本手册只是一个单一的 HTML 文件，由 Yocto 项目手册的大部分组成。它可以轻松地搜索 Yocto 项目文档集中使用的短语和术语。

- `/profile-manual/index`: This manual presents a set of common and generally useful tracing and profiling schemes along with their applications (as appropriate) to each tool.

> 这本手册介绍了一系列常见且通常有用的跟踪和分析方案，并适当地将其应用于每个工具。

- `/toaster-manual/index`, which uses BitBake, that reports build information.

> 这份手册介绍并描述了如何设置和使用烤面包机。烤面包机是一个应用程序编程接口(API)和基于 Web 的界面，用于报告构建信息的 OpenEmbedded 构建系统，它使用 BitBake。

- :yocto_wiki:\`FAQ \</FAQ\>\`: A list of commonly asked questions and their answers.
- `Release Information </migration-guides/index>`: Migration guides, release notes, new features, updates and known issues for the current and past releases of the Yocto Project.

> 发布信息 <migration-guides/index>：Yocto 项目当前和过去发布版本的迁移指南、发布注记、新功能、更新和已知问题。

- :yocto_bugs:\`Bugzilla \<\>\`: The bug tracking application the Yocto Project uses. If you find problems with the Yocto Project, you should report them using this application.

> Bugzilla：Yocto 项目使用的错误跟踪应用程序。如果您发现 Yocto 项目存在问题，请使用此应用程序进行报告。

- :yocto_wiki:\`Bugzilla Configuration and Bug Tracking Wiki Page \</Bugzilla_Configuration_and_Bug_Tracking\>\`: Information on how to get set up and use the Yocto Project implementation of Bugzilla for logging and tracking Yocto Project defects.

> Bugzilla 配置和错误跟踪维基页面\</Bugzilla_Configuration_and_Bug_Tracking\>\`：有关如何设置并使用 Yocto 项目实现的 Bugzilla 来记录和跟踪 Yocto 项目缺陷的信息。

- Internet Relay Chat (IRC): Two IRC channels on [Libera Chat](https://libera.chat/) are available for Yocto Project and OpenEmbeddded discussions: `#yocto` and `#oe`, respectively.

> 在 [Libera Chat](https://libera.chat/) 上有两个 Internet Relay Chat(IRC)频道可用于 Yocto Project 和 OpenEmbeddded 讨论：分别为 `#yocto` 和 `#oe`。

- [Quick EMUlator (QEMU)](https://wiki.qemu.org/Index.html): An open-source machine emulator and virtualizer.
