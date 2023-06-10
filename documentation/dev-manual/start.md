---
tip: translate by baidu@2023-06-07 20:25:23
title: Setting Up to Use the Yocto Project
---
This chapter provides guidance on how to prepare to use the Yocto Project. You can learn about creating a team environment to develop using the Yocto Project, how to set up a `build host <dev-manual/start:preparing the build host>`{.interpreted-text role="ref"}, how to locate Yocto Project source repositories, and how to create local Git repositories.

> 本章提供了如何准备使用 Yocto 项目的指导。您可以了解如何创建使用 Yocto 项目进行开发的团队环境，如何设置 `build host<dev manual/start:prepare the build host>`{.depredicted text role=“ref”}，如何定位 Yocto Project 源存储库，以及如何创建本地 Git 存储库。

# Creating a Team Development Environment

It might not be immediately clear how you can use the Yocto Project in a team development environment, or how to scale it for a large team of developers. You can adapt the Yocto Project to many different use cases and scenarios; however, this flexibility could cause difficulties if you are trying to create a working setup that scales effectively.

> 目前可能还不清楚如何在团队开发环境中使用 Yocto 项目，或者如何为大型开发团队扩展它。您可以使 Yocto 项目适应许多不同的用例和场景；然而，如果您试图创建一个有效扩展的工作设置，这种灵活性可能会带来困难。

To help you understand how to set up this type of environment, this section presents a procedure that gives you information that can help you get the results you want. The procedure is high-level and presents some of the project\'s most successful experiences, practices, solutions, and available technologies that have proved to work well in the past; however, keep in mind, the procedure here is simply a starting point. You can build off these steps and customize the procedure to fit any particular working environment and set of practices.

> 为了帮助您了解如何设置这种类型的环境，本节介绍了一个过程，该过程为您提供了可以帮助您获得所需结果的信息。该程序是高水平的，介绍了该项目的一些最成功的经验、实践、解决方案和可用的技术，这些经验、实践和技术在过去被证明是有效的；但是，请记住，这里的程序只是一个起点。您可以在这些步骤的基础上进行构建，并自定义过程以适应任何特定的工作环境和实践。

1. _Determine Who is Going to be Developing:_ You first need to understand who is going to be doing anything related to the Yocto Project and determine their roles. Making this determination is essential to completing subsequent steps, which are to get your equipment together and set up your development environment\'s hardware topology.

> 1.*确定谁将要发展：*你首先需要了解谁将要做与 Yocto 项目相关的任何事情，并确定他们的角色。做出这一决定对于完成后续步骤至关重要，这些步骤是将设备组装在一起并设置开发环境的硬件拓扑结构。

Here are possible roles:

> 以下是可能的角色：

- _Application Developer:_ This type of developer does application level work on top of an existing software stack.

> -*应用程序开发人员：*这种类型的开发人员在现有软件堆栈的基础上进行应用程序级工作。

- _Core System Developer:_ This type of developer works on the contents of the operating system image itself.

> -*核心系统开发人员：*这类开发人员处理操作系统映像本身的内容。

- _Build Engineer:_ This type of developer manages Autobuilders and releases. Depending on the specifics of the environment, not all situations might need a Build Engineer.

> -*生成工程师：*这类开发人员管理 Autobuilder 和发布。根据环境的具体情况，并非所有情况都需要一名构建工程师。

- _Test Engineer:_ This type of developer creates and manages automated tests that are used to ensure all application and core system development meets desired quality standards.

> -*测试工程师：*这类开发人员创建和管理自动化测试，用于确保所有应用程序和核心系统开发符合所需的质量标准。

2. _Gather the Hardware:_ Based on the size and make-up of the team, get the hardware together. Ideally, any development, build, or test engineer uses a system that runs a supported Linux distribution. These systems, in general, should be high performance (e.g. dual, six-core Xeons with 24 Gbytes of RAM and plenty of disk space). You can help ensure efficiency by having any machines used for testing or that run Autobuilders be as high performance as possible.

> 2.*组装硬件：*根据团队的规模和组成，组装硬件。理想情况下，任何开发、构建或测试工程师都使用运行受支持的 Linux 发行版的系统。一般来说，这些系统应该是高性能的（例如，具有 24 GB RAM 和充足磁盘空间的双六核 Xeon）。您可以通过让任何用于测试或运行 Autobuilders 的机器尽可能具有高性能来帮助确保效率。

::: note
::: title

Note

:::

Given sufficient processing power, you might also consider building Yocto Project development containers to be run under Docker, which is described later.

> 如果有足够的处理能力，您还可以考虑构建在 Docker 下运行的 Yocto 项目开发容器，稍后将对此进行描述。
> :::

3. _Understand the Hardware Topology of the Environment:_ Once you understand the hardware involved and the make-up of the team, you can understand the hardware topology of the development environment. You can get a visual idea of the machines and their roles across the development environment.

> 3.*了解环境的硬件拓扑：*一旦了解了所涉及的硬件和团队的组成，就可以了解开发环境的硬件布局。您可以直观地了解机器及其在整个开发环境中的角色。

4. _Use Git as Your Source Control Manager (SCM):_ Keeping your `Metadata`{.interpreted-text role="term"} (i.e. recipes, configuration files, classes, and so forth) and any software you are developing under the control of an SCM system that is compatible with the OpenEmbedded build system is advisable. Of all of the SCMs supported by BitBake, the Yocto Project team strongly recommends using `overview-manual/development-environment:git`{.interpreted-text role="ref"}. Git is a distributed system that is easy to back up, allows you to work remotely, and then connects back to the infrastructure.

> 4.*使用 Git 作为源代码管理器（SCM）：*建议将您的“元数据”｛.explored text role=“term”｝（即配方、配置文件、类等）和您正在开发的任何软件置于与 OpenEmbedded 构建系统兼容的 SCM 系统的控制下。在 BitBake 支持的所有 SCMs 中，Yocto 项目团队强烈建议使用 `overview manual/development environment:git`{.depreted text role=“ref”}。Git 是一个易于备份的分布式系统，允许您远程工作，然后连接回基础设施。

::: note
::: title

Note

:::

For information about BitBake, see the `bitbake:index`{.interpreted-text role="doc"}.

> 有关 BitBake 的信息，请参阅 `BitBake:index`｛.explored text role=“doc”｝。
> :::

It is relatively easy to set up Git services and create infrastructure like :yocto\_[git:%60/](git:%60/)[, which is based on server software called ]{.title-ref}[gitolite]{.title-ref}[ with ]{.title-ref}[cgit]{.title-ref}[ being used to generate the web interface that lets you view the repositories. The ]{.title-ref}[gitolite]{.title-ref}\` software identifies users using SSH keys and allows branch-based access controls to repositories that you can control as little or as much as necessary.

> 设置 Git 服务和创建基础设施相对容易，如：yocto\_[Git:%60/]（Git:%60/对存储库的基于分支的访问控制，您可以尽可能少地或尽可能多地进行控制。

::: note
::: title

Note

:::

The setup of these services is beyond the scope of this manual. However, here are sites describing how to perform setup:

> 这些服务的设置超出了本手册的范围。但是，以下是描述如何执行设置的站点：

- [Gitolite](https://gitolite.com): Information for `gitolite`.

> -[吉托莱](https://gitolite.com)：“gitolite”的信息。

- [Interfaces, frontends, and tools](https://git.wiki.kernel.org/index.php/Interfaces,_frontends,_and_tools): Documentation on how to create interfaces and frontends for Git.

> -[接口、前端和工具]([https://git.wiki.kernel.org/index.php/Interfaces](https://git.wiki.kernel.org/index.php/Interfaces)，_frontends，_and_tools）：关于如何为 Git 创建接口和前端的文档。

```
 :::
```

5. _Set up the Application Development Machines:_ As mentioned earlier, application developers are creating applications on top of existing software stacks. Following are some best practices for setting up machines used for application development:

> 5.*设置应用程序开发机器：*如前所述，应用程序开发人员正在现有软件堆栈之上创建应用程序。以下是设置用于应用程序开发的机器的一些最佳实践：

- Use a pre-built toolchain that contains the software stack itself. Then, develop the application code on top of the stack. This method works well for small numbers of relatively isolated applications.

> -使用包含软件堆栈本身的预构建工具链。然后，在堆栈的顶部开发应用程序代码。这种方法适用于少量相对孤立的应用程序。

- Keep your cross-development toolchains updated. You can do this through provisioning either as new toolchain downloads or as updates through a package update mechanism using `opkg` to provide updates to an existing toolchain. The exact mechanics of how and when to do this depend on local policy.

> -保持跨开发工具链的更新。您可以通过提供新的工具链下载或通过使用“opkg”为现有工具链提供更新的包更新机制进行更新来实现这一点。如何以及何时做到这一点的确切机制取决于当地政策。

- Use multiple toolchains installed locally into different locations to allow development across versions.

> -使用本地安装到不同位置的多个工具链，以允许跨版本开发。

6. _Set up the Core Development Machines:_ As mentioned earlier, core developers work on the contents of the operating system itself. Following are some best practices for setting up machines used for developing images:

> 6.*设置核心开发机器：*如前所述，核心开发人员处理操作系统本身的内容。以下是设置用于开发图像的机器的一些最佳实践：

- Have the `OpenEmbedded Build System`{.interpreted-text role="term"} available on the developer workstations so developers can run their own builds and directly rebuild the software stack.

> -在开发人员工作站上提供“OpenEmbedded Build System”｛.explored text role=“term”｝，以便开发人员可以运行自己的构建并直接重建软件堆栈。

- Keep the core system unchanged as much as possible and do your work in layers on top of the core system. Doing so gives you a greater level of portability when upgrading to new versions of the core system or Board Support Packages (BSPs).

> -尽可能保持核心系统不变，并在核心系统之上分层进行工作。这样做可以在升级到新版本的核心系统或板支持包（BSP）时提供更高级别的可移植性。

- Share layers amongst the developers of a particular project and contain the policy configuration that defines the project.

> -在特定项目的开发人员之间共享层，并包含定义项目的策略配置。

7. _Set up an Autobuilder:_ Autobuilders are often the core of the development environment. It is here that changes from individual developers are brought together and centrally tested. Based on this automated build and test environment, subsequent decisions about releases can be made. Autobuilders also allow for \"continuous integration\" style testing of software components and regression identification and tracking.

> 7.*建立一个自动生成器：*自动生成器通常是开发环境的核心。正是在这里，来自各个开发人员的更改被汇集在一起并进行集中测试。基于这个自动化的构建和测试环境，可以做出关于发布的后续决策。Autobuilders 还允许软件组件的“持续集成”式测试以及回归识别和跟踪。

See \":yocto_ab:[Yocto Project Autobuilder \<\>]{.title-ref}\" for more information and links to buildbot. The Yocto Project team has found this implementation works well in this role. A public example of this is the Yocto Project Autobuilders, which the Yocto Project team uses to test the overall health of the project.

> 有关 buildbot 的更多信息和链接，请参阅“：yocto_ab:[yocto Project Autobuilder\<\>]｛.title ref｝\”。Yocto 项目团队发现，在这个角色中，这个实施效果很好。一个公开的例子是 Yocto Project Autobuilders，Yocto 项目团队使用它来测试项目的整体健康状况。

The features of this system are:

> 该系统的特点是：

- Highlights when commits break the build.

> -当提交破坏构建时高亮显示。

- Populates an `sstate cache <overview-manual/concepts:shared state cache>`{.interpreted-text role="ref"} from which developers can pull rather than requiring local builds.

> -填充 `sstate cache<overview manual/concepts:shared state cache>`{.depredicted text role=“ref”}，开发人员可以从中提取，而不需要本地构建。

- Allows commit hook triggers, which trigger builds when commits are made.

> -允许提交挂钩触发器，该触发器在进行提交时生成。

- Allows triggering of automated image booting and testing under the QuickEMUlator (QEMU).

> -允许在 QuickEMUlator（QEMU）下触发自动映像引导和测试。

- Supports incremental build testing and from-scratch builds.

> -支持增量构建测试和从头开始构建。

- Shares output that allows developer testing and historical regression investigation.

> -共享允许开发人员测试和历史回归调查的输出。

- Creates output that can be used for releases.

> -创建可用于发布的输出。

- Allows scheduling of builds so that resources can be used efficiently.

> -允许对生成进行调度，以便有效地使用资源。

8. _Set up Test Machines:_ Use a small number of shared, high performance systems for testing purposes. Developers can use these systems for wider, more extensive testing while they continue to develop locally using their primary development system.

> 8.*设置测试机器：*使用少量共享的高性能系统进行测试。开发人员可以使用这些系统进行更广泛、更广泛的测试，同时继续使用其主要开发系统进行本地开发。

9. _Document Policies and Change Flow:_ The Yocto Project uses a hierarchical structure and a pull model. There are scripts to create and send pull requests (i.e. `create-pull-request` and `send-pull-request`). This model is in line with other open source projects where maintainers are responsible for specific areas of the project and a single maintainer handles the final \"top-of-tree\" merges.

> 9.\_文档策略和变更流程：\_Yocto 项目使用分层结构和拉式模型。有创建和发送拉请求的脚本（即“创建拉请求”和“发送拉请求”）。该模型与其他开源项目一致，在这些项目中，维护人员负责项目的特定区域，由一名维护人员处理最终的“树顶”合并。

::: note
::: title

Note

:::

You can also use a more collective push model. The `gitolite` software supports both the push and pull models quite easily.

> 您还可以使用更集体的推送模型。“gitolite”软件非常容易地支持推送和拉取两种模式。
> :::

As with any development environment, it is important to document the policy used as well as any main project guidelines so they are understood by everyone. It is also a good idea to have well-structured commit messages, which are usually a part of a project\'s guidelines. Good commit messages are essential when looking back in time and trying to understand why changes were made.

> 与任何开发环境一样，重要的是要记录所使用的政策以及任何主要的项目指导方针，以便每个人都能理解它们。拥有结构良好的提交消息也是一个好主意，这通常是项目指导方针的一部分。当回顾过去并试图理解为什么要进行更改时，良好的提交消息是至关重要的。

If you discover that changes are needed to the core layer of the project, it is worth sharing those with the community as soon as possible. Chances are if you have discovered the need for changes, someone else in the community needs them also.

> 如果您发现需要对项目的核心层进行更改，那么值得尽快与社区分享这些更改。如果你发现了改变的必要性，那么社区中的其他人也很可能需要改变。

10. _Development Environment Summary:_ Aside from the previous steps, here are best practices within the Yocto Project development environment:

> 10.开发环境摘要：除了前面的步骤外，以下是 Yocto 项目开发环境中的最佳实践：

```
- Use `overview-manual/development-environment:git`{.interpreted-text role="ref"} as the source control system.
- Maintain your Metadata in layers that make sense for your situation. See the \"`overview-manual/yp-intro:the yocto project layer model`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual and the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section for more information on layers.
- Separate the project\'s Metadata and code by using separate Git repositories. See the \"`overview-manual/development-environment:yocto project source repositories`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual for information on these repositories. See the \"`dev-manual/start:locating yocto project source files`{.interpreted-text role="ref"}\" section for information on how to set up local Git repositories for related upstream Yocto Project Git repositories.
- Set up the directory for the shared state cache (`SSTATE_DIR`{.interpreted-text role="term"}) where it makes sense. For example, set up the sstate cache on a system used by developers in the same organization and share the same source directories on their machines.
- Set up an Autobuilder and have it populate the sstate cache and source directories.
- The Yocto Project community encourages you to send patches to the project to fix bugs or add features. If you do submit patches, follow the project commit guidelines for writing good commit messages. See the \"`dev-manual/changes:submitting a change to the yocto project`{.interpreted-text role="ref"}\" section.
- Send changes to the core sooner than later as others are likely to run into the same issues. For some guidance on mailing lists to use, see the list in the \"`dev-manual/changes:submitting a change to the yocto project`{.interpreted-text role="ref"}\" section. For a description of the available mailing lists, see the \"`resources-mailinglist`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual.
```

# Preparing the Build Host

This section provides procedures to set up a system to be used as your `Build Host`{.interpreted-text role="term"} for development using the Yocto Project. Your build host can be a native Linux machine (recommended), it can be a machine (Linux, Mac, or Windows) that uses [CROPS](https://github.com/crops/poky-container), which leverages [Docker Containers](https://www.docker.com/) or it can be a Windows machine capable of running version 2 of Windows Subsystem For Linux (WSL 2).

> 本节提供了设置一个系统的步骤，该系统将用作您的“构建主机”｛.explored text role=“term”｝，用于使用 Yocto 项目进行开发。您的构建主机可以是本地 Linux 机器（推荐），也可以是使用[CROPS]的机器（Linux、Mac 或 Windows）([https://github.com/crops/poky-container](https://github.com/crops/poky-container))，利用 [Docker Containers](https://www.docker.com/) 或者它可以是能够运行 Linux 的 Windows 子系统（WSL2）的版本 2 的 Windows 机器。

::: note
::: title
Note
:::

The Yocto Project is not compatible with version 1 of `Windows Subsystem for Linux <Windows_Subsystem_for_Linux>`{.interpreted-text role="wikipedia"}. It is compatible but neither officially supported nor validated with WSL 2. If you still decide to use WSL please upgrade to [WSL 2](https://learn.microsoft.com/en-us/windows/wsl/install).

> Yocto 项目与 `Windows Subsystem for Linux<Windows_Subsystem_for_Linux>`{.depreted text role=“wikipedia”}的版本 1 不兼容。它与 WSL2 兼容，但既没有得到官方支持，也没有经过验证。如果您仍然决定使用 WSL，请升级到 [WSL2](https://learn.microsoft.com/en-us/windows/wsl/install)。
> :::

Once your build host is set up to use the Yocto Project, further steps are necessary depending on what you want to accomplish. See the following references for information on how to prepare for Board Support Package (BSP) development and kernel development:

> 一旦您的构建主机设置为使用 Yocto 项目，就需要根据您想要实现的目标采取进一步的步骤。有关如何准备 Board Support Package（BSP）开发和内核开发的信息，请参阅以下参考资料：

- _BSP Development:_ See the \"`bsp-guide/bsp:preparing your build host to work with bsp layers`{.interpreted-text role="ref"}\" section in the Yocto Project Board Support Package (BSP) Developer\'s Guide.

> -*BSP 开发：*请参阅 Yocto 项目委员会支持包（BSP）开发人员指南中的“`nbsp guide/nbsp：准备您的构建主机以使用nbsp层`{.expreted text role=“ref”}\”部分。

- _Kernel Development:_ See the \"`kernel-dev/common:preparing the build host to work on the kernel`{.interpreted-text role="ref"}\" section in the Yocto Project Linux Kernel Development Manual.

> -*内核开发：*请参阅 Yocto Project Linux 内核开发手册中的\“`Kernel dev/common:preparating the build host to work on the Kernel`｛.explored text role=“ref”｝\”一节。

## Setting Up a Native Linux Host

Follow these steps to prepare a native Linux machine as your Yocto Project Build Host:

> 按照以下步骤准备一台本机 Linux 机器作为 Yocto 项目构建主机：

1. _Use a Supported Linux Distribution:_ You should have a reasonably current Linux-based host system. You will have the best results with a recent release of Fedora, openSUSE, Debian, Ubuntu, RHEL or CentOS as these releases are frequently tested against the Yocto Project and officially supported. For a list of the distributions under validation and their status, see the \"`Supported Linux Distributions <system-requirements-supported-distros>`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual and the wiki page at :yocto_wiki:[Distribution Support \</Distribution_Support\>]{.title-ref}.

> 1.使用支持的 Linux 发行版：\_您应该拥有一个合理的当前基于 Linux 的主机系统。Fedora、openSUSE、Debian、Ubuntu、RHEL 或 CentOS 的最新版本会给您带来最好的结果，因为这些版本经常针对 Yocto 项目进行测试并得到官方支持。有关正在验证的发行版及其状态的列表，请参阅 Yocto 项目参考手册中的“`支持的Linux发行版<系统要求支持的发行版>`｛.depreted text role=“ref”｝”部分和位于以下位置的 wiki 页面：Yocto_wiki:[Distribution Support\</Distribution_Support\>]｛.title ref｝。

2. _Have Enough Free Memory:_ Your system should have at least 50 Gbytes of free disk space for building images.

> 2.*有足够的可用内存：*您的系统应该至少有 50 GB 的可用磁盘空间用于构建映像。

3. _Meet Minimal Version Requirements:_ The OpenEmbedded build system should be able to run on any modern distribution that has the following versions for Git, tar, Python, gcc and make.

> 3._Meet 最低版本要求：_ OpenEmbedded 构建系统应该能够在任何具有以下 Git、tar、Python、gcc 和 make 版本的现代发行版上运行。

- Git &MIN_GIT_VERSION; or greater

> -Git 和 MIN_Git_VERSION；或更大

- tar &MIN_TAR_VERSION; or greater

> -焦油&MIN_tar_VERSION；或更大

- Python &MIN_PYTHON_VERSION; or greater.

> -Python 和 MIN_Python_VERSION；或更大。

- gcc &MIN_GCC_VERSION; or greater.

> -gcc&MIN_gcc_VERSION；或更大。

- GNU make &MIN_MAKE_VERSION; or greater

> -GNU 制造&MIN_make_VERSION；或更大

If your build host does not meet any of these listed version requirements, you can take steps to prepare the system so that you can still use the Yocto Project. See the \"`ref-manual/system-requirements:required git, tar, python, make and gcc versions`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual for information.

> 如果您的生成主机不满足这些列出的任何版本要求，您可以采取步骤准备系统，以便仍然可以使用 Yocto 项目。有关信息，请参阅 Yocto 项目参考手册中的\“`ref manual/system requirements:required git、tar、python、make和gcc version`｛.explored text role=“ref”｝\”一节。

4. _Install Development Host Packages:_ Required development host packages vary depending on your build host and what you want to do with the Yocto Project. Collectively, the number of required packages is large if you want to be able to cover all cases.

> 4.*安装开发主机包：*所需的开发主机包因您的构建主机和您想对 Yocto 项目执行的操作而异。总的来说，如果你想涵盖所有情况，所需的包数量是很大的。

For lists of required packages for all scenarios, see the \"`ref-manual/system-requirements:required packages for the build host`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual.

> 有关所有方案所需包的列表，请参阅 Yocto 项目参考手册中的\“`ref manual/system requirements:生成主机所需包`{.depreted text role=”ref“}\”一节。

Once you have completed the previous steps, you are ready to continue using a given development path on your native Linux machine. If you are going to use BitBake, see the \"``dev-manual/start:cloning the \`\`poky\`\` repository``{.interpreted-text role="ref"}\" section. If you are going to use the Extensible SDK, see the \"`/sdk-manual/extensible`{.interpreted-text role="doc"}\" Chapter in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual. If you want to work on the kernel, see the `/kernel-dev/index`{.interpreted-text role="doc"}. If you are going to use Toaster, see the \"`/toaster-manual/setup-and-use`{.interpreted-text role="doc"}\" section in the Toaster User Manual.

> 一旦您完成了前面的步骤，就可以在您的本地 Linux 机器上继续使用给定的开发路径了。如果要使用 BitBake，请参阅“``dev manual/start:cloning the \`\`poky \`\` repository``｛.depreted text role=”ref“｝\”一节。如果您要使用可扩展 SDK，请参阅 Yocto 项目应用程序开发和可扩展软件开发工具包（eSDK）手册中的“`/SDK manual/extensional`｛.depreted text role=“doc”｝\”一章。如果您想在内核上工作，请参阅 `/kernel dev/index`｛.explored text role=“doc”｝。如果您要使用 Toast，请参阅 Toast 用户手册中的\“`/Toaster manual/setup and use `{.depreted text role=“doc”}\”一节。

## Setting Up to Use CROss PlatformS (CROPS)

With [CROPS](https://github.com/crops/poky-container), which leverages [Docker Containers](https://www.docker.com/), you can create a Yocto Project development environment that is operating system agnostic. You can set up a container in which you can develop using the Yocto Project on a Windows, Mac, or Linux machine.

> 带 [CROPS](https://github.com/crops/poky-container)，利用 [Docker Containers](https://www.docker.com/)，您可以创建一个与操作系统无关的 Yocto Project 开发环境。您可以设置一个容器，在其中可以在 Windows、Mac 或 Linux 机器上使用 Yocto 项目进行开发。

Follow these general steps to prepare a Windows, Mac, or Linux machine as your Yocto Project build host:

> 按照以下常规步骤准备 Windows、Mac 或 Linux 机器作为 Yocto Project 构建主机：

1. _Determine What Your Build Host Needs:_ [Docker](https://www.docker.com/what-docker) is a software container platform that you need to install on the build host. Depending on your build host, you might have to install different software to support Docker containers. Go to the Docker installation page and read about the platform requirements in \"[Supported Platforms](https://docs.docker.com/engine/install/#supported-platforms)\" your build host needs to run containers.

> 1.确定构建主机需要什么：\_[Docker](https://www.docker.com/what-docker) 是一个需要安装在生成主机上的软件容器平台。根据您的构建主机，您可能需要安装不同的软件来支持 Docker 容器。转到 Docker 安装页面，阅读\“[Supported Platforms]中的平台要求([https://docs.docker.com/engine/install/#supported](https://docs.docker.com/engine/install/#supported)-平台）\“您的构建主机需要运行容器。

2. _Choose What To Install:_ Depending on whether or not your build host meets system requirements, you need to install \"Docker CE Stable\" or the \"Docker Toolbox\". Most situations call for Docker CE. However, if you have a build host that does not meet requirements (e.g. Pre-Windows 10 or Windows 10 \"Home\" version), you must install Docker Toolbox instead.

> 2.*选择安装内容：*根据您的构建主机是否满足系统要求，您需要安装“Docker CE Stable”或“Docker Toolbox”。大多数情况下都需要 Docker CE。但是，如果您的构建主机不符合要求（例如，Windows 10 之前或 Windows 10“Home”版本），则必须安装 Docker Toolbox。

3. _Go to the Install Site for Your Platform:_ Click the link for the Docker edition associated with your build host\'s native software. For example, if your build host is running Microsoft Windows Version 10 and you want the Docker CE Stable edition, click that link under \"Supported Platforms\".

> 3.*Go to the Install Site for Your Platform：*单击与构建主机的本机软件相关联的 Docker 版本的链接。例如，如果您的构建主机运行的是 Microsoft Windows Version 10，并且您想要 Docker CE Stable 版本，请单击“支持的平台”下的链接。

4. _Install the Software:_ Once you have understood all the pre-requisites, you can download and install the appropriate software. Follow the instructions for your specific machine and the type of the software you need to install:

> 4.*安装软件：*一旦您了解了所有先决条件，就可以下载并安装相应的软件。按照特定机器的说明和需要安装的软件类型进行操作：

- Install [Docker Desktop on Windows](https://docs.docker.com/docker-for-windows/install/#install-docker-desktop-on-windows) for Windows build hosts that meet requirements.

> -安装 [Windows 上的 Docker Desktop](%5Bhttps://docs.docker.com/docker-for-windows/install/#install-windows%5D(https://docs.docker.com/docker-for-windows/install/#install-windows)) 上的 docker 桌面），用于满足要求的 windows 构建主机。

- Install [Docker Desktop on MacOs](https://docs.docker.com/docker-for-mac/install/#install-and-run-docker-desktop-on-mac) for Mac build hosts that meet requirements.

> -安装[Mac 电脑上的 Docker Desktop]([https://docs.docker.com/docker-for-mac/install/#install](https://docs.docker.com/docker-for-mac/install/#install)-并在 mac 上运行 docker desktop）。

- Install [Docker Engine on CentOS](https://docs.docker.com/engine/install/centos/) for Linux build hosts running the CentOS distribution.

> -安装〔Docker Engine on CentOS〕([https://docs.docker.com/engine/install/centos/](https://docs.docker.com/engine/install/centos/))适用于运行 CentOS 发行版的 Linux 构建主机。

- Install [Docker Engine on Debian](https://docs.docker.com/engine/install/debian/) for Linux build hosts running the Debian distribution.

> -安装 [Debian 上的 Docker 引擎](https://docs.docker.com/engine/install/debian/)用于运行 Debian 发行版的 Linux 构建主机。

- Install [Docker Engine for Fedora](https://docs.docker.com/engine/install/fedora/) for Linux build hosts running the Fedora distribution.

> -安装[用于 Fedora 的 Docker 引擎](https://docs.docker.com/engine/install/fedora/)适用于运行 Fedora 发行版的 Linux 构建主机。

- Install [Docker Engine for Ubuntu](https://docs.docker.com/engine/install/ubuntu/) for Linux build hosts running the Ubuntu distribution.

> -安装 [Ubuntu 的 Docker 引擎](https://docs.docker.com/engine/install/ubuntu/)适用于运行 Ubuntu 发行版的 Linux 构建主机。

5. _Optionally Orient Yourself With Docker:_ If you are unfamiliar with Docker and the container concept, you can learn more here -[https://docs.docker.com/get-started/](https://docs.docker.com/get-started/).

> 5.*可选地用 Docker 定位自己：*如果你不熟悉 Docker 和容器概念，你可以在这里了解更多-[https://docs.docker.com/get-started/](https://docs.docker.com/get-started/)。

6. _Launch Docker or Docker Toolbox:_ You should be able to launch Docker or the Docker Toolbox and have a terminal shell on your development host.

> 6.*启动 Docker 或 Docker 工具箱：*你应该能够启动 Docker 或者 Docker 工具箱，并且在你的开发主机上有一个终端外壳。

7. _Set Up the Containers to Use the Yocto Project:_ Go to [https://github.com/crops/docker-win-mac-docs/wiki](https://github.com/crops/docker-win-mac-docs/wiki) and follow the directions for your particular build host (i.e. Linux, Mac, or Windows).

> 7.*设置要使用 Yocto 项目的容器：*转到 [https://github.com/crops/docker-win-mac-docs/wiki](https://github.com/crops/docker-win-mac-docs/wiki) 并遵循特定构建主机（即 Linux、Mac 或 Windows）的说明。

Once you complete the setup instructions for your machine, you have the Poky, Extensible SDK, and Toaster containers available. You can click those links from the page and learn more about using each of those containers.

> 一旦您完成了机器的设置说明，您就有了 Poky、可扩展 SDK 和 Toster 容器。您可以从页面中单击这些链接，了解有关使用每个容器的详细信息。

Once you have a container set up, everything is in place to develop just as if you were running on a native Linux machine. If you are going to use the Poky container, see the \"``dev-manual/start:cloning the \`\`poky\`\` repository``{.interpreted-text role="ref"}\" section. If you are going to use the Extensible SDK container, see the \"`/sdk-manual/extensible`{.interpreted-text role="doc"}\" Chapter in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual. If you are going to use the Toaster container, see the \"`/toaster-manual/setup-and-use`{.interpreted-text role="doc"}\" section in the Toaster User Manual.

> 一旦您设置了一个容器，就可以像在本地 Linux 机器上运行一样进行开发。如果您要使用 Poky 容器，请参阅\“`dev manual/start:cloning the \`\`Poky \`\` repository``｛.depreted text role=”ref“｝\”一节。如果要使用可扩展 SDK 容器，请参阅 Yocto 项目应用程序开发和可扩展软件开发工具包（eSDK）手册中的“`/SDK manual/extensional`｛.depreted text role=“doc”｝\”一章。如果要使用 Toaster 容器，请参阅 Toaster 用户手册中的\“`/toother manual/setup and use `{.depreted text role=“doc”}\”一节。

## Setting Up to Use Windows Subsystem For Linux (WSL 2)

With [Windows Subsystem for Linux (WSL 2)](https://learn.microsoft.com/en-us/windows/wsl/), you can create a Yocto Project development environment that allows you to build on Windows. You can set up a Linux distribution inside Windows in which you can develop using the Yocto Project.

> 带有 [Windows Subsystem for Linux（WSL 2）](https://learn.microsoft.com/en-us/windows/wsl/)，您可以创建一个 Yocto Project 开发环境，使您能够在 Windows 上进行构建。您可以在 Windows 中设置一个 Linux 发行版，在其中可以使用 Yocto 项目进行开发。

Follow these general steps to prepare a Windows machine using WSL 2 as your Yocto Project build host:

> 按照以下常规步骤准备使用 WSL2 作为 Yocto Project 构建主机的 Windows 计算机：

1. _Make sure your Windows machine is capable of running WSL 2:_

> 1.确保您的 Windows 计算机能够运行 WSL 2：\_

While all Windows 11 and Windows Server 2022 builds support WSL 2, the first versions of Windows 10 and Windows Server 2019 didn\'t. Check the minimum build numbers for [Windows 10](https://learn.microsoft.com/en-us/windows/wsl/install-manual#step-2---check-requirements-for-running-wsl-2) and for [Windows Server 2019](https://learn.microsoft.com/en-us/windows/wsl/install-on-server).

> 虽然所有 Windows 11 和 Windows Server 2022 版本都支持 WSL 2，但 Windows 10 和 Windows Server 2019 的第一个版本则不支持。检查[Windows 10]的最低内部版本号([https://learn.microsoft.com/en-us/windows/wsl/install-manual#step-2-](https://learn.microsoft.com/en-us/windows/wsl/install-manual#step-2-)-检查运行要求-wsl-2）和 [Windows Server 2019](https://learn.microsoft.com/en-us/windows/wsl/install-on-server)。

To check which build version you are running, you may open a command prompt on Windows and execute the command \"ver\":

> 要检查您正在运行的内部版本，您可以在 Windows 上打开命令提示符并执行命令“ver\”：

````

> ```

C:\Users\myuser> ver

> C： \Users\myuser>ver


Microsoft Windows [Version 10.0.19041.153]

> Microsoft Windows[版本10.0.19041.153]

````

> ```
>
> ```

2. _Install the Linux distribution of your choice inside WSL 2:_ Once you know your version of Windows supports WSL 2, you can install the distribution of your choice from the Microsoft Store. Open the Microsoft Store and search for Linux. While there are several Linux distributions available, the assumption is that your pick will be one of the distributions supported by the Yocto Project as stated on the instructions for using a native Linux host. After making your selection, simply click \"Get\" to download and install the distribution.

> 2.*在 WSL 2 中安装您选择的 Linux 发行版：*一旦您知道您的 Windows 版本支持 WSL 2，您就可以从 Microsoft 商店安装所选择的发行版。打开 Microsoft 商店并搜索 Linux。虽然有几个 Linux 发行版可用，但假设您选择的是 Yocto 项目支持的发行版之一，如使用本机 Linux 主机的说明所述。选择后，只需单击“获取”即可下载并安装分发版。

3. _Check which Linux distribution WSL 2 is using:_ Open a Windows PowerShell and run:

> 3.*检查 WSL 2 正在使用哪个 Linux 发行版：*打开 Windows PowerShell 并运行：

````

> ```

C:\WINDOWS\system32> wsl -l -v

> C： \WINDOWS\system32>wsl-l-v

NAME    STATE   VERSION

> 名称状态版本

*Ubuntu Running 2

> *Ubuntu运行2

````

> ```
>
> ```

Note that WSL 2 supports running as many different Linux distributions as you want to install.

> 请注意，WSL2 支持运行您想要安装的任意多个不同的 Linux 发行版。

4. _Optionally Get Familiar with WSL:_ You can learn more on [https://docs.microsoft.com/en-us/windows/wsl/wsl2-about](https://docs.microsoft.com/en-us/windows/wsl/wsl2-about).

> 4.*可选地熟悉 WSL:*您可以在上了解更多信息 [https://docs.microsoft.com/en-us/windows/wsl/wsl2-about](https://docs.microsoft.com/en-us/windows/wsl/wsl2-about)。

5. _Launch your WSL Distibution:_ From the Windows start menu simply launch your WSL distribution just like any other application.

> 5.*启动您的 WSL 分发：*从 Windows 开始菜单，像启动任何其他应用程序一样，简单地启动您的 WSL 分发。

6. _Optimize your WSL 2 storage often:_ Due to the way storage is handled on WSL 2, the storage space used by the underlying Linux distribution is not reflected immediately, and since BitBake heavily uses storage, after several builds, you may be unaware you are running out of space. As WSL 2 uses a VHDX file for storage, this issue can be easily avoided by regularly optimizing this file in a manual way:

> 6.*经常优化 WSL2 存储：*由于 WSL2 上处理存储的方式，底层 Linux 发行版使用的存储空间不会立即反映出来，而且由于 BitBake 大量使用存储，经过几次构建后，您可能不会意识到空间不足。由于 WSL2 使用 VHDX 文件进行存储，因此可以通过手动方式定期优化此文件来轻松避免此问题：

1. _Find the location of your VHDX file:_

> 1.查找 VHDX 文件的位置（_F）：_

```
  First you need to find the distro app package directory, to achieve this open a Windows Powershell as Administrator and run:

```

C:\WINDOWS\system32> Get-AppxPackage -Name "*Ubuntu*" | Select PackageFamilyName
PackageFamilyName
-----------------

CanonicalGroupLimited.UbuntuonWindows_79abcdefgh

```

You should now replace the PackageFamilyName and your user on the following path to find your VHDX file:

```

ls C:\Users\myuser\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79abcdefgh\LocalState
Mode                 LastWriteTime         Length Name
-a----         3/14/2020   9:52 PM    57418973184 ext4.vhdx

```

Your VHDX file path is: `C:\Users\myuser\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79abcdefgh\LocalState\ext4.vhdx`
```

2a. _Optimize your VHDX file using Windows Powershell:_

> 2a 中*使用 Windows Powershell 优化 VHDX 文件：*

> To use the `optimize-vhd` cmdlet below, first install the Hyper-V option on Windows. Then, open a Windows Powershell as Administrator to optimize your VHDX file, shutting down WSL first:

>> 若要使用下面的“optimize vhd”cmdlet，请首先在 Windows 上安装 Hyper-V 选项。然后，以管理员身份打开 Windows Powershell 以优化 VHDX 文件，首先关闭 WSL：
>>

>

>>

> ```
>
> ```

>> ```
>>
>> ```
>>

> C:\WINDOWS\system32> wsl --shutdown

>> C:\WINDOWS\system32>wsl—关闭
>>

> C:\WINDOWS\system32> optimize-vhd -Path C:\Users\myuser\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79abcdefgh\LocalState\ext4.vhdx -Mode full

>> C:\WINDOWS\system32> 优化 vhd-路径 C:\Users\myuser\AppData\Local\Packages\CanonicalGroupLimited。UbuntuonDows_79abcdefgh\LocalState\ext4.vhdx-模式已满
>>

> ```
>
> ```

>> ```
>>
>> ```
>>

>

>>

> A progress bar should be shown while optimizing the VHDX file, and storage should now be reflected correctly on the Windows Explorer.

>> 优化 VHDX 文件时应显示进度条，并且存储现在应正确反映在 Windows 资源管理器上。
>>

2b. _Optimize your VHDX file using DiskPart:_

> 2b 中*使用 DiskPart 优化 VHDX 文件：*

> The `optimize-vhd` cmdlet noted in step 2a above is provided by Hyper-V. Not all SKUs of Windows can install Hyper-V. As an alternative, use the DiskPart tool. To start, open a Windows command prompt as Administrator to optimize your VHDX file, shutting down WSL first:

>> 上面步骤 2a 中提到的“optimize vhd”cmdlet 由 Hyper-V 提供。并非所有 Windows 的 SKU 都可以安装 Hyper-V。或者，使用 DiskPart 工具。要启动，请以管理员身份打开 Windows 命令提示符以优化 VHDX 文件，首先关闭 WSL：
>>

>

>>

> ```
>
> ```

>> ```
>>
>> ```
>>

> C:\WINDOWS\system32> wsl --shutdown

>> C:\WINDOWS\system32>wsl—关闭
>>

> C:\WINDOWS\system32> diskpart

>> C:\WINDOWS\system32> 磁盘部件
>>

>

>>

> DISKPART> select vdisk file="<path_to_VHDX_file>"

>> DISKPART> 选择 vdisk file=“<path_to_VHDX_file>”
>>

> DISKPART> attach vdisk readonly

>> DISKPART> 附加 vdisk 只读
>>

> DISKPART> compact vdisk

>> DISKPART> 压缩 vdisk
>>

> DISKPART> exit

>> DISKPART> 退出
>>

> ```
>
> ```

>> ```
>>
>> ```
>>

::: note
::: title
Note
:::

The current implementation of WSL 2 does not have out-of-the-box access to external devices such as those connected through a USB port, but it automatically mounts your `C:` drive on `/mnt/c/` (and others), which you can use to share deploy artifacts to be later flashed on hardware through Windows, but your `Build Directory`{.interpreted-text role="term"} should not reside inside this mountpoint.

> WSL 2 的当前实现无法开箱即用地访问外部设备，例如通过 USB 端口连接的设备，但它会自动将您的 `C:` 驱动器安装在 `/mnt/C/`（和其他）上，您可以使用该驱动器共享稍后通过 Windows 在硬件上闪存的部署工件，但您的 `Build Directory`｛.depreted text role=“term”｝不应位于此安装点内。
> :::

Once you have WSL 2 set up, everything is in place to develop just as if you were running on a native Linux machine. If you are going to use the Extensible SDK container, see the \"`/sdk-manual/extensible`{.interpreted-text role="doc"}\" Chapter in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual. If you are going to use the Toaster container, see the \"`/toaster-manual/setup-and-use`{.interpreted-text role="doc"}\" section in the Toaster User Manual.

> 一旦设置了 WSL2，一切都准备就绪，可以像在本地 Linux 机器上运行一样进行开发。如果要使用可扩展 SDK 容器，请参阅 Yocto 项目应用程序开发和可扩展软件开发工具包（eSDK）手册中的“`/SDK manual/extensional`｛.depreted text role=“doc”｝\”一章。如果要使用 Toaster 容器，请参阅 Toaster 用户手册中的\“`/toother manual/setup and use `{.depreted text role=“doc”}\”一节。

# Locating Yocto Project Source Files

This section shows you how to locate, fetch and configure the source files you\'ll need to work with the Yocto Project.

> 本节介绍如何定位、获取和配置 Yocto 项目所需的源文件。

::: note
::: title
Note
:::

- For concepts and introductory information about Git as it is used in the Yocto Project, see the \"`overview-manual/development-environment:git`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual.

> -有关 Yocto 项目中使用的 Git 的概念和介绍信息，请参阅 Yocto Project overview and concepts manual 中的\“`overview manual/development environment:Git`｛.depreted text role=”ref“｝\”一节。

- For concepts on Yocto Project source repositories, see the \"`overview-manual/development-environment:yocto project source repositories`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual.\"

> -有关 Yocto 项目源代码库的概念，请参阅 Yocto Project overview and concepts manual 中的\“`overview manual/development environment:Yocto Project source repositories`｛.depreted text role=”ref“｝\”一节\”
> :::

## Accessing Source Repositories

Working from a copy of the upstream `dev-manual/start:accessing source repositories`{.interpreted-text role="ref"} is the preferred method for obtaining and using a Yocto Project release. You can view the Yocto Project Source Repositories at :yocto\_[git:%60/](git:%60/)[. In particular, you can find the ]{.title-ref}[poky]{.title-ref}[ repository at :yocto_git:]{.title-ref}/poky\`.

> 使用上游 `dev manual/start:访问源存储库`{.depredicted text role=“ref”}的副本是获取和使用 Yocto Project 版本的首选方法。您可以在以下位置查看 Yocto 项目源存储库：Yocto\_[git:%60/]（git:%60/）[。特别是，您可以在下面的位置找到]｛.title-ref｝[poky]｛.title-ref｝[存储库：Yocto_git:]｛\title-rev｝/poky\`。

Use the following procedure to locate the latest upstream copy of the `poky` Git repository:

> 使用以下过程查找“poky”Git 存储库的最新上游副本：

1. _Access Repositories:_ Open a browser and go to :yocto\_[git:%60/](git:%60/)\` to access the GUI-based interface into the Yocto Project source repositories.

> 1.*访问存储库：*打开浏览器并转到：yocto\_[git:%60/]（git:%60/）\`以访问 yocto Project 源存储库中基于 GUI 的界面。

2. _Select the Repository:_ Click on the repository in which you are interested (e.g. `poky`).

> 2.选择存储库：\_单击您感兴趣的存储库（例如“poky”）。

3. _Find the URL Used to Clone the Repository:_ At the bottom of the page, note the URL used to clone that repository (e.g. :yocto\_[git:%60/poky](git:%60/poky)\`).

> 3.*查找用于克隆存储库的 URL：*在页面底部，注意用于克隆该存储库的网址（例如：yocto\_[git:%60/poky]（git:%60/poky）\`）。

::: note
::: title

Note

:::

For information on cloning a repository, see the \"``dev-manual/start:cloning the \`\`poky\`\` repository``{.interpreted-text role="ref"}\" section.

> 有关克隆存储库的信息，请参阅\“`dev manual/start:cloning the \`\`poky \`\` repository``｛.depreted text role=”ref“｝\”一节。
> :::

## Accessing Source Archives

The Yocto Project also provides source archives of its releases, which are available on :yocto_dl:[/releases/yocto/]{.title-ref}. Then, choose the subdirectory containing the release you wish to use, for example :yocto_dl:[yocto-&DISTRO; \</releases/yocto/yocto-&DISTRO;/\>]{.title-ref}.

> Yocto 项目还提供了其版本的源档案，这些档案可在以下位置获得：Yocto_dl:[/releases/Yocto/]｛.title-ref｝。然后，选择包含您希望使用的版本的子目录，例如：Yocto_dl:[Yocto-&DISTRO；\</releases/Yocto/Yocto-&DISTRO，/\>]｛.title-rev｝。

You will find there source archives of individual components (if you wish to use them individually), and of the corresponding Poky release bundling a selection of these components.

> 你会发现单个组件的源文件（如果你想单独使用它们），以及捆绑这些组件的相应 Poky 版本的源文件。

::: note
::: title
Note
:::

The recommended method for accessing Yocto Project components is to use Git to clone the upstream repository and work from within that locally cloned repository.

> 访问 Yocto Project 组件的推荐方法是使用 Git 克隆上游存储库，并在本地克隆的存储库中工作。
> :::

## Using the Downloads Page

The :yocto_home:[Yocto Project Website \<\>]{.title-ref} uses a \"DOWNLOADS\" page from which you can locate and download tarballs of any Yocto Project release. Rather than Git repositories, these files represent snapshot tarballs similar to the tarballs located in the Index of Releases described in the \"`dev-manual/start:accessing source archives`{.interpreted-text role="ref"}\" section.

> ：yocto_home:[yocto Project Website\<\>]｛.title-ref｝使用一个“下载”页面，您可以从中查找和下载任何 yocto 项目版本的 tarball。这些文件代表的不是 Git 存储库，而是快照 tarball，类似于“devmanual/start:accessing source archives”一节中描述的发布索引中的 tarball。

1. _Go to the Yocto Project Website:_ Open The :yocto_home:[Yocto Project Website \<\>]{.title-ref} in your browser.

> 1.转到 Yocto 项目网站：\_在浏览器中打开：Yocto_home:[Yocto Project Website\<\>]{.title-ref}。

2. _Get to the Downloads Area:_ Select the \"DOWNLOADS\" item from the pull-down \"SOFTWARE\" tab menu near the top of the page.

> 2.*设置到下载区域：*从页面顶部附近的下拉式“软件”选项卡菜单中选择“下载”项目。

3. _Select a Yocto Project Release:_ Use the menu next to \"RELEASE\" to display and choose a recent or past supported Yocto Project release (e.g. &DISTRO_NAME_NO_CAP;, &DISTRO_NAME_NO_CAP_MINUS_ONE;, and so forth).

> 3.*选择 Yocto 项目版本：*使用“Release\”旁边的菜单显示并选择最近或过去支持的 Yocto Project 版本（例如&DISTRO_NAME_NO_CAP；、&DISTR_NAME_NO_CAP_MINUS_ONE；等）。

::: note
::: title

Note

:::

For a \"map\" of Yocto Project releases to version numbers, see the :yocto_wiki:[Releases \</Releases\>]{.title-ref} wiki page.

> 有关 Yocto Project 版本到版本号的“映射”，请参阅：Yocto_wiki:[Realeases\</releases\>]｛.title-ref｝wiki 页面。
> :::

You can use the \"RELEASE ARCHIVE\" link to reveal a menu of all Yocto Project releases.

> 您可以使用“RELEASE ARCHIVE”链接来显示所有 Yocto Project 版本的菜单。

4. _Download Tools or Board Support Packages (BSPs):_ From the \"DOWNLOADS\" page, you can download tools or BSPs as well. Just scroll down the page and look for what you need.

> 4.*下载工具或板支持包（BSP）：*从“下载”页面，您也可以下载工具或 BSP。只需向下滚动页面，查找您需要的内容。

# Cloning and Checking Out Branches

To use the Yocto Project for development, you need a release locally installed on your development system. This locally installed set of files is referred to as the `Source Directory`{.interpreted-text role="term"} in the Yocto Project documentation.

> 要使用 Yocto 项目进行开发，您需要在开发系统上本地安装一个版本。这组本地安装的文件在 Yocto 项目文档中被称为“源目录”｛.depreted text role=“term”｝。

The preferred method of creating your Source Directory is by using `overview-manual/development-environment:git`{.interpreted-text role="ref"} to clone a local copy of the upstream `poky` repository. Working from a cloned copy of the upstream repository allows you to contribute back into the Yocto Project or to simply work with the latest software on a development branch. Because Git maintains and creates an upstream repository with a complete history of changes and you are working with a local clone of that repository, you have access to all the Yocto Project development branches and tag names used in the upstream repository.

> 创建源目录的首选方法是使用 `overview manual/development environment:git`｛.depreted text role=“ref”｝克隆上游 `poky` 存储库的本地副本。通过使用上游存储库的克隆副本，您可以为 Yocto 项目做出贡献，也可以简单地使用开发分支上的最新软件。由于 Git 维护并创建了一个具有完整更改历史的上游存储库，并且您正在使用该存储库的本地克隆，因此您可以访问上游存储库中使用的所有 Yocto 项目开发分支和标记名称。

## Cloning the `poky` Repository

Follow these steps to create a local version of the upstream `Poky`{.interpreted-text role="term"} Git repository.

> 按照以下步骤创建上游 `Poky `{.depredicted text role=“term”}Git 存储库的本地版本。

1. _Set Your Directory:_ Change your working directory to where you want to create your local copy of `poky`.

> 1.*设置目录：*将工作目录更改为要创建“poky”本地副本的位置。

2. _Clone the Repository:_ The following example command clones the `poky` repository and uses the default name \"poky\" for your local repository:

> 2.克隆存储库：\_以下示例命令克隆“poky”存储库，并使用本地存储库的默认名称“poky \”：

````

> ```

$ git clone git://git.yoctoproject.org/poky

> $git克隆git://git.yoctoproject.org/poky

Cloning into 'poky'...

> 克隆到“poky”。。。

remote: Counting objects: 432160, done.

> 远程：计数对象：432160，已完成。

remote: Compressing objects: 100% (102056/102056), done.

> 远程：压缩对象：100%（102056/10256），已完成。

remote: Total 432160 (delta 323116), reused 432037 (delta 323000)

> 远程：总计432160（增量323116），重复使用432037（增量323000）

Receiving objects: 100% (432160/432160), 153.81 MiB | 8.54 MiB/s, done.

> 接收对象：100%（432160/432160），153.81 MiB|8.54 MiB/s，完成。

Resolving deltas: 100% (323116/323116), done.

> 解决增量：100%（323116/323116），已完成。

Checking connectivity... done.

> 正在检查连接。。。完成。

````

> ```
>
> ```

Unless you specify a specific development branch or tag name, Git clones the \"master\" branch, which results in a snapshot of the latest development changes for \"master\". For information on how to check out a specific development branch or on how to check out a local branch based on a tag name, see the \"`dev-manual/start:checking out by branch in poky`{.interpreted-text role="ref"}\" and \"`dev-manual/start:checking out by tag in poky`{.interpreted-text role="ref"}\" sections, respectively.

> 除非指定特定的开发分支或标记名称，否则 Git 会克隆“master”分支，这会导致“master”的最新开发更改的快照。有关如何签出特定开发分支或如何根据标记名称签出本地分支的信息，请分别参阅“`dev manual/start:按poky中的分支签出`{.depreted text role=“ref”}\”和“`devmanual/start：按poky `{.depreced text role=“ref”}中的标记签出”部分。

Once the local repository is created, you can change to that directory and check its status. The `master` branch is checked out by default:

> 创建本地存储库后，您可以更改到该目录并检查其状态。“master”分支在默认情况下被签出：

````

> ```

$ cd poky

> $cd波基

$ git status

> $git状态

On branch master

> 在分支主机上

Your branch is up-to-date with 'origin/master'.

> 您的分支机构是最新的“origin/master”。

nothing to commit, working directory clean

> 无需提交，工作目录已清理

$ git branch

> $git分行

* master

> *船长

````

> ```
>
> ```

Your local repository of poky is identical to the upstream poky repository at the time from which it was cloned. As you work with the local branch, you can periodically use the `git pull --rebase` command to be sure you are up-to-date with the upstream branch.

> 您的 poky 本地存储库与克隆时的上游 poky 存储库相同。当您使用本地分支时，您可以定期使用“gitpull--rebase”命令来确保您了解上游分支的最新情况。

## Checking Out by Branch in Poky

When you clone the upstream poky repository, you have access to all its development branches. Each development branch in a repository is unique as it forks off the \"master\" branch. To see and use the files of a particular development branch locally, you need to know the branch name and then specifically check out that development branch.

> 当您克隆上游 poky 存储库时，您可以访问它的所有开发分支。存储库中的每个开发分支都是唯一的，因为它从“master”分支分叉。要在本地查看和使用特定开发分支的文件，您需要知道分支名称，然后专门签出该开发分支。

::: note
::: title
Note
:::

Checking out an active development branch by branch name gives you a snapshot of that particular branch at the time you check it out. Further development on top of the branch that occurs after check it out can occur.

> 通过分支名称签出活动的开发分支，可以在签出时获得该特定分支的快照。在签出分支之后，可能会在分支的顶部进行进一步的开发。
> :::

1. _Switch to the Poky Directory:_ If you have a local poky Git repository, switch to that directory. If you do not have the local copy of poky, see the \"``dev-manual/start:cloning the \`\`poky\`\` repository``{.interpreted-text role="ref"}\" section.

> 1.*切换到 Poky 目录：*如果您有本地的 Poky Git 存储库，请切换到该目录。如果您没有 poky 的本地副本，请参阅\“`dev manual/start:cloning the \``poky\` repository``｛.depreted text role=”ref“｝\”一节。

2. _Determine Existing Branch Names:_ :

> 2.确定现有分支机构名称：\_：

````

> ```

$ git branch -a

> $git分支-a

* master

> *船长

remotes/origin/1.1_M1

> 遥控器/原点/1.1_M1

remotes/origin/1.1_M2

> 遥控器/原点/1.1_M2

remotes/origin/1.1_M3

> 遥控器/原点/1.1_M3

remotes/origin/1.1_M4

> 遥控器/原点/1.1_M4

remotes/origin/1.2_M1

> 远程/原点/1.2_M1

remotes/origin/1.2_M2

> 远程/原点/1.2_M2

remotes/origin/1.2_M3

> 远程/原点/1.2_M3

. . .

> 。

remotes/origin/thud

> 遥控器/原点/thud

remotes/origin/thud-next

> 遥控器/原点/下一个

remotes/origin/warrior

> 遥控器/起源/战士

remotes/origin/warrior-next

> 遥控器/起源/战士下一个

remotes/origin/zeus

> 遥控器/原点/宙斯

remotes/origin/zeus-next

> 遥控器/原点/宙斯下一个

... and so on ...

> 等等

````

> ```
>
> ```

3. _Check out the Branch:_ Check out the development branch in which you want to work. For example, to access the files for the Yocto Project &DISTRO; Release (&DISTRO_NAME;), use the following command:

> 3.*检查分支：*检查您想要工作的开发分支。例如，访问 Yocto 项目的文件&DISTRO；释放（&DISTRO_NAME；），使用以下命令：

````

> ```

$ git checkout -b &DISTRO_NAME_NO_CAP; origin/&DISTRO_NAME_NO_CAP;

> $git结账-b&DISTRO_NAME_NO_CAP；原点/&DISTRO_NAME_NO_CAP；

Branch &DISTRO_NAME_NO_CAP; set up to track remote branch &DISTRO_NAME_NO_CAP; from origin.

> 分支机构&DISTRO_NAME_NO_CAP；设置为跟踪远程分支&DISTRO_NAME_NO_CAP；来自原产地。

Switched to a new branch '&DISTRO_NAME_NO_CAP;'

> 已切换到新分支'&DISTRO_NAME_NO_CAP；'

````

> ```
>
> ```

The previous command checks out the \"&DISTRO_NAME_NO_CAP;\" development branch and reports that the branch is tracking the upstream \"origin/&DISTRO_NAME_NO_CAP;\" branch.

> 上一个命令检出“&DISTRO_NAME_NO_CAP；”开发分支，并报告该分支正在跟踪上游的“origin/&DISTRO_NAME_NO_CAP\”分支。

The following command displays the branches that are now part of your local poky repository. The asterisk character indicates the branch that is currently checked out for work:

> 以下命令显示现在属于本地 poky 存储库的分支。星号字符表示当前签出工作的分支：

````

> ```

$ git branch

> $git分行
  master
  * &DISTRO_NAME_NO_CAP;

````

> ```
>
> ```

## Checking Out by Tag in Poky

Similar to branches, the upstream repository uses tags to mark specific commits associated with significant points in a development branch (i.e. a release point or stage of a release). You might want to set up a local branch based on one of those points in the repository. The process is similar to checking out by branch name except you use tag names.

> 与分支类似，上游存储库使用标记来标记与开发分支中的重要点（即发布点或发布阶段）相关联的特定提交。您可能希望基于存储库中的其中一个点来设置本地分支。该过程类似于按分支名称签出，只是使用标记名称。

::: note
::: title
Note
:::

Checking out a branch based on a tag gives you a stable set of files not affected by development on the branch above the tag.

> 签出基于标记的分支可以为您提供一组稳定的文件，这些文件不受标记上方分支上开发的影响。
> :::

1. _Switch to the Poky Directory:_ If you have a local poky Git repository, switch to that directory. If you do not have the local copy of poky, see the \"``dev-manual/start:cloning the \`\`poky\`\` repository``{.interpreted-text role="ref"}\" section.

> 1.*切换到 Poky 目录：*如果您有本地的 Poky Git 存储库，请切换到该目录。如果您没有 poky 的本地副本，请参阅\“`dev manual/start:cloning the \``poky\` repository``｛.depreted text role=”ref“｝\”一节。

2. _Fetch the Tag Names:_ To checkout the branch based on a tag name, you need to fetch the upstream tags into your local repository:

> 2.*提取标记名称：*要根据标记名称签出分支，您需要将上游标记提取到本地存储库中：

````

> ```

$ git fetch --tags

> $git fetch--标记

$

> $

````

> ```
>
> ```

3. _List the Tag Names:_ You can list the tag names now:

> 3.*列出标签名称：*现在可以列出标签名称了：

````

> ```

$ git tag

> $git标记

1.1_M1.final

> 1.1_M1.最终

1.1_M1.rc1

> 1.1_M1.rc1

1.1_M1.rc2

> 1.1_M1.rc2

1.1_M2.final

> 1.1_M2.最终

1.1_M2.rc1

> 1.1_M2.rc1
   .
   .
   .

yocto-2.5

> 约2.5

yocto-2.5.1

> 约克托-2.5.1

yocto-2.5.2

> 约-2.5.2

yocto-2.5.3

> 约-2.5.3

yocto-2.6

> 约2.6

yocto-2.6.1

> 约克托-2.6.1

yocto-2.6.2

> 约-2.6.2

yocto-2.7

> 约2.7

yocto_1.5_M5.rc8

> 约1.5_M5.rc8

````

> ```
>
> ```

4. _Check out the Branch:_ :

> 4._检出分支：_：

````

> ```

$ git checkout tags/yocto-&DISTRO; -b my_yocto_&DISTRO;

> $git结账标签/yocto-&DISTRO-b my_yocto_&DISTRO；

Switched to a new branch 'my_yocto_&DISTRO;'

> 已切换到新分支“my_yocto_&DISTRO；”

$ git branch

> $git分行
  master

* my_yocto_&DISTRO;

> *my_yocto_&DISTRO；

````

> ```
>
> ```

The previous command creates and checks out a local branch named \"[my_yocto]()&DISTRO;\", which is based on the commit in the upstream poky repository that has the same tag. In this example, the files you have available locally as a result of the `checkout` command are a snapshot of the \"&DISTRO_NAME_NO_CAP;\" development branch at the point where Yocto Project &DISTRO; was released.

> 上一个命令创建并签出一个名为“[my_yocto]（）&DISTRO；\”的本地分支，该分支基于具有相同标记的上游 poky 存储库中的提交。在本例中，您通过“checkout”命令在本地可用的文件是 Yocto Project&DISTRO；被释放。
