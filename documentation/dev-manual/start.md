---
tip: translate by openai@2023-06-10 12:23:37
...
---
title: Setting Up to Use the Yocto Project
------------------------------------------

This chapter provides guidance on how to prepare to use the Yocto Project. You can learn about creating a team environment to develop using the Yocto Project, how to set up a `build host <dev-manual/start:preparing the build host>`, how to locate Yocto Project source repositories, and how to create local Git repositories.

> 本章提供了有关如何准备使用 Yocto 项目的指导。您可以了解如何创建团队环境来使用 Yocto 项目开发，如何设置构建主机 <dev-manual/start:preparing the build host>，如何定位 Yocto 项目源代码存储库以及如何创建本地 Git 存储库。

# Creating a Team Development Environment

It might not be immediately clear how you can use the Yocto Project in a team development environment, or how to scale it for a large team of developers. You can adapt the Yocto Project to many different use cases and scenarios; however, this flexibility could cause difficulties if you are trying to create a working setup that scales effectively.

> 可能不会立即清楚如何在团队开发环境中使用 Yocto 项目，或者如何为大型开发团队扩展它。您可以将 Yocto 项目适应许多不同的用例和场景；但是，如果您试图创建一个有效的可扩展的工作设置，这种灵活性可能会带来困难。

To help you understand how to set up this type of environment, this section presents a procedure that gives you information that can help you get the results you want. The procedure is high-level and presents some of the project\'s most successful experiences, practices, solutions, and available technologies that have proved to work well in the past; however, keep in mind, the procedure here is simply a starting point. You can build off these steps and customize the procedure to fit any particular working environment and set of practices.

> 为了帮助您理解如何设置这种环境，本节提供了一个程序，为您提供可以帮助您获得所需结果的信息。该程序是高层次的，介绍了项目中最成功的经验、实践、解决方案和可用技术，这些技术已经在过去证明有效；但是，请记住，这里的程序仅仅是一个起点。您可以基于这些步骤，并自定义程序以适应任何特定的工作环境和实践。

1. *Determine Who is Going to be Developing:* You first need to understand who is going to be doing anything related to the Yocto Project and determine their roles. Making this determination is essential to completing subsequent steps, which are to get your equipment together and set up your development environment\'s hardware topology.

> 首先，您需要了解谁将参与与 Yocto 项目有关的所有事情，并确定他们的角色。确定这一点对于完成随后的步骤(即整理设备并设置开发环境的硬件拓扑)至关重要。

Here are possible roles:

- *Application Developer:* This type of developer does application level work on top of an existing software stack.
- *Core System Developer:* This type of developer works on the contents of the operating system image itself.
- *Build Engineer:* This type of developer manages Autobuilders and releases. Depending on the specifics of the environment, not all situations might need a Build Engineer.

> - *构建工程师：*这种类型的开发人员负责管理自动构建器和发布。根据环境的具体情况，并不是所有情况都需要构建工程师。

- *Test Engineer:* This type of developer creates and manages automated tests that are used to ensure all application and core system development meets desired quality standards.

> 测试工程师：这类开发人员创建和管理自动化测试，以确保所有应用程序和核心系统开发符合预期的质量标准。

2. *Gather the Hardware:* Based on the size and make-up of the team, get the hardware together. Ideally, any development, build, or test engineer uses a system that runs a supported Linux distribution. These systems, in general, should be high performance (e.g. dual, six-core Xeons with 24 Gbytes of RAM and plenty of disk space). You can help ensure efficiency by having any machines used for testing or that run Autobuilders be as high performance as possible.

> 2. 收集硬件：根据团队的规模和构成，收集硬件。理想情况下，任何开发，构建或测试工程师都应使用运行受支持的 Linux 发行版的系统。这些系统通常应具有高性能(例如双六核 Xeon，24G 字节内存和充足的磁盘空间)。通过使用用于测试的机器或运行自动构建器的机器尽可能高性能，可以帮助确保效率。

::: note
::: title
Note
:::

Given sufficient processing power, you might also consider building Yocto Project development containers to be run under Docker, which is described later.

> 如果有足够的处理能力，您还可以考虑在 Docker 下构建 Yocto 项目开发容器，稍后会有详细介绍。
> :::

3. *Understand the Hardware Topology of the Environment:* Once you understand the hardware involved and the make-up of the team, you can understand the hardware topology of the development environment. You can get a visual idea of the machines and their roles across the development environment.

> 了解硬件拓扑结构：一旦你理解了硬件设备和团队组成，你就可以理解开发环境的硬件拓扑结构。你可以对开发环境中的机器及其角色有一个直观的想法。

4. *Use Git as Your Source Control Manager (SCM):* Keeping your `Metadata`. Git is a distributed system that is easy to back up, allows you to work remotely, and then connects back to the infrastructure.

> 使用 Git 作为您的源代码控制管理器(SCM)：建议将您的元数据(即配方、配置文件、类等)以及您正在开发的任何软件都放在与 OpenEmbedded 构建系统兼容的 SCM 系统的控制之下。Yocto 项目团队强烈建议使用 overview-manual / development-environment：git。Git 是一个分布式系统，易于备份，允许您远程工作，然后连接回基础架构。

::: note
::: title
Note
:::

For information about BitBake, see the `bitbake:index`.
:::

It is relatively easy to set up Git services and create infrastructure like :yocto_[git:%60/](git:%60/)[, which is based on server software called ]\` software identifies users using SSH keys and allows branch-based access controls to repositories that you can control as little or as much as necessary.

> 这比较容易设置 Git 服务和创建基础架构，比如基于服务器软件 Gitolite 的 Yocto_git，使用 Cgit 来生成 Web 界面，让你查看存储库。Gitolite 软件使用 SSH 密钥来识别用户，允许根据分支进行访问控制，你可以根据需要控制的程度来控制存储库。

::: note
::: title
Note
:::

The setup of these services is beyond the scope of this manual. However, here are sites describing how to perform setup:

- [Gitolite](https://gitolite.com): Information for `gitolite`.
- [Interfaces, frontends, and tools](https://git.wiki.kernel.org/index.php/Interfaces,_frontends,_and_tools): Documentation on how to create interfaces and frontends for Git.

> -[接口、前端和工具](https://git.wiki.kernel.org/index.php/Interfaces,_frontends,_and_tools): 如何为 Git 创建接口和前端的文档。
> :::

5. *Set up the Application Development Machines:* As mentioned earlier, application developers are creating applications on top of existing software stacks. Following are some best practices for setting up machines used for application development:

> 5. *设置应用开发机器：* 正如前面提到的，应用开发人员正在创建基于现有软件堆栈的应用程序。以下是用于应用开发机器设置的一些最佳实践：

- Use a pre-built toolchain that contains the software stack itself. Then, develop the application code on top of the stack. This method works well for small numbers of relatively isolated applications.

> 使用包含软件堆栈本身的预构建工具链。然后，在堆栈的顶部开发应用程序代码。这种方法对于较少数量的相对孤立的应用程序非常有效。

- Keep your cross-development toolchains updated. You can do this through provisioning either as new toolchain downloads or as updates through a package update mechanism using `opkg` to provide updates to an existing toolchain. The exact mechanics of how and when to do this depend on local policy.

> 保持您的跨开发工具链的更新。您可以通过配置来完成，无论是作为新的工具链下载，还是通过使用 `opkg` 来提供现有工具链的更新，通过包更新机制来完成。如何以及何时进行更新取决于当地政策。

- Use multiple toolchains installed locally into different locations to allow development across versions.

6. *Set up the Core Development Machines:* As mentioned earlier, core developers work on the contents of the operating system itself. Following are some best practices for setting up machines used for developing images:

> 6. *设置核心开发机器：* 正如前面提到的，核心开发人员负责操作系统本身的内容。以下是用于开发镜像的机器设置的最佳实践：

- Have the `OpenEmbedded Build System` available on the developer workstations so developers can run their own builds and directly rebuild the software stack.

> 让开发人员的工作站上可以使用 OpenEmbedded 构建系统，这样开发人员就可以运行自己的构建，并直接重新构建软件堆栈。

- Keep the core system unchanged as much as possible and do your work in layers on top of the core system. Doing so gives you a greater level of portability when upgrading to new versions of the core system or Board Support Packages (BSPs).

> 尽可能保持核心系统不变，在核心系统之上做你的工作。这样做可以在升级到新版本的核心系统或板支持包(BSP)时获得更高的可移植性。

- Share layers amongst the developers of a particular project and contain the policy configuration that defines the project.

7. *Set up an Autobuilder:* Autobuilders are often the core of the development environment. It is here that changes from individual developers are brought together and centrally tested. Based on this automated build and test environment, subsequent decisions about releases can be made. Autobuilders also allow for \"continuous integration\" style testing of software components and regression identification and tracking.

> 设置自动构建器：自动构建器通常是开发环境的核心。在这里，来自各个开发人员的更改被结合在一起并进行中央测试。基于这种自动构建和测试环境，可以做出关于发布的后续决定。自动构建器还允许进行“持续集成”样式的软件组件测试和回归识别和跟踪。

See \":yocto_ab:[Yocto Project Autobuilder \<\>]\" for more information and links to buildbot. The Yocto Project team has found this implementation works well in this role. A public example of this is the Yocto Project Autobuilders, which the Yocto Project team uses to test the overall health of the project.

> 请参阅“Yocto 项目自动构建器”以获取更多信息和构建 bot 链接。Yocto 项目团队发现这种实现在这个角色中表现良好。Yocto 项目自动构建器就是一个公共示例，Yocto 项目团队使用它来测试项目的整体健康状况。

The features of this system are:

- Highlights when commits break the build.
- Populates an `sstate cache <overview-manual/concepts:shared state cache>` from which developers can pull rather than requiring local builds.

> 填充一个“sstate 缓存”(参见概览手册/概念：共享状态缓存)，开发人员可以从中拉取而不需要本地构建。

- Allows commit hook triggers, which trigger builds when commits are made.
- Allows triggering of automated image booting and testing under the QuickEMUlator (QEMU).
- Supports incremental build testing and from-scratch builds.
- Shares output that allows developer testing and historical regression investigation.
- Creates output that can be used for releases.
- Allows scheduling of builds so that resources can be used efficiently.

8. *Set up Test Machines:* Use a small number of shared, high performance systems for testing purposes. Developers can use these systems for wider, more extensive testing while they continue to develop locally using their primary development system.

> 设置测试机：使用少量共享的高性能系统用于测试目的。开发人员可以使用这些系统进行更广泛、更深入的测试，同时他们可以继续使用主要的开发系统进行本地开发。

9. *Document Policies and Change Flow:* The Yocto Project uses a hierarchical structure and a pull model. There are scripts to create and send pull requests (i.e. `create-pull-request` and `send-pull-request`). This model is in line with other open source projects where maintainers are responsible for specific areas of the project and a single maintainer handles the final \"top-of-tree\" merges.

> 9. *文档政策和变更流程：* Yocto 项目使用分层结构和拉取模型。有脚本可以创建和发送拉取请求(即 `create-pull-request` 和 `send-pull-request`)。这种模型与其他开源项目一致，其中维护者负责项目的特定区域，单个维护者负责最终的“顶层树”合并。

::: note
::: title
Note
:::

You can also use a more collective push model. The `gitolite` software supports both the push and pull models quite easily.
:::

As with any development environment, it is important to document the policy used as well as any main project guidelines so they are understood by everyone. It is also a good idea to have well-structured commit messages, which are usually a part of a project\'s guidelines. Good commit messages are essential when looking back in time and trying to understand why changes were made.

> 随着任何开发环境一样，记录并理解使用的政策以及主要项目指南非常重要。有良好结构的提交消息也是一个不错的主意，这通常是项目指南的一部分。当回顾过去并试图理解为什么做出更改时，良好的提交消息至关重要。

If you discover that changes are needed to the core layer of the project, it is worth sharing those with the community as soon as possible. Chances are if you have discovered the need for changes, someone else in the community needs them also.

> 如果你发现项目的核心层需要更改，最好尽快与社区分享这些更改。很有可能，如果你发现了需要更改的地方，社区中的其他人也需要这些更改。

10. *Development Environment Summary:* Aside from the previous steps, here are best practices within the Yocto Project development environment:

    - Use `overview-manual/development-environment:git` as the source control system.
    - Maintain your Metadata in layers that make sense for your situation. See the \"`overview-manual/yp-intro:the yocto project layer model`\" section for more information on layers.
    - Separate the project\'s Metadata and code by using separate Git repositories. See the \"`overview-manual/development-environment:yocto project source repositories`\" section for information on how to set up local Git repositories for related upstream Yocto Project Git repositories.
    - Set up the directory for the shared state cache (`SSTATE_DIR`) where it makes sense. For example, set up the sstate cache on a system used by developers in the same organization and share the same source directories on their machines.
    - Set up an Autobuilder and have it populate the sstate cache and source directories.
    - The Yocto Project community encourages you to send patches to the project to fix bugs or add features. If you do submit patches, follow the project commit guidelines for writing good commit messages. See the \"`dev-manual/changes:submitting a change to the yocto project`\" section.
    - Send changes to the core sooner than later as others are likely to run into the same issues. For some guidance on mailing lists to use, see the list in the \"`dev-manual/changes:submitting a change to the yocto project`\" section in the Yocto Project Reference Manual.

# Preparing the Build Host

This section provides procedures to set up a system to be used as your `Build Host` for development using the Yocto Project. Your build host can be a native Linux machine (recommended), it can be a machine (Linux, Mac, or Windows) that uses [CROPS](https://github.com/crops/poky-container), which leverages [Docker Containers](https://www.docker.com/) or it can be a Windows machine capable of running version 2 of Windows Subsystem For Linux (WSL 2).

> 这一节提供了步骤，用于设置一个系统以用作您的 Yocto 项目开发的“构建主机”。您的构建主机可以是本地 Linux 机器(推荐)，可以是使用 [CROPS](https://github.com/crops/poky-container) 的机器(Linux、Mac 或 Windows)，该机器利用 [Docker 容器](https://www.docker.com/)，或者可以是能够运行第 2 版 Windows 子系统(WSL 2)的 Windows 机器。

::: note
::: title
Note
:::

The Yocto Project is not compatible with version 1 of `Windows Subsystem for Linux <Windows_Subsystem_for_Linux>`. It is compatible but neither officially supported nor validated with WSL 2. If you still decide to use WSL please upgrade to [WSL 2](https://learn.microsoft.com/en-us/windows/wsl/install).

> 页克托项目不兼容 Windows Subsystem for Linux 1.0 版本。它可以兼容，但不受官方支持或验证 WSL 2。如果你仍然决定使用 WSL，请升级到 WSL 2([https://learn.microsoft.com/en-us/windows/wsl/install](https://learn.microsoft.com/en-us/windows/wsl/install))。
> :::

Once your build host is set up to use the Yocto Project, further steps are necessary depending on what you want to accomplish. See the following references for information on how to prepare for Board Support Package (BSP) development and kernel development:

> 一旦您的构建主机设置完成，可以使用 Yocto Project，根据您想要完成的任务，还需要进一步的步骤。有关如何准备板支持包(BSP)开发和内核开发的信息，请参阅以下参考资料：

- *BSP Development:* See the \"`bsp-guide/bsp:preparing your build host to work with bsp layers`\" section in the Yocto Project Board Support Package (BSP) Developer\'s Guide.

> 请参阅 Yocto 项目板级支持包(BSP)开发者指南中的“bsp-guide/bsp：准备构建主机以使用 bsp 层”部分。

- *Kernel Development:* See the \"`kernel-dev/common:preparing the build host to work on the kernel`\" section in the Yocto Project Linux Kernel Development Manual.

> 查看 Yocto 项目 Linux 内核开发手册中的“kernel-dev/common：准备构建主机以进行内核开发”部分。

## Setting Up a Native Linux Host

Follow these steps to prepare a native Linux machine as your Yocto Project Build Host:

1. *Use a Supported Linux Distribution:* You should have a reasonably current Linux-based host system. You will have the best results with a recent release of Fedora, openSUSE, Debian, Ubuntu, RHEL or CentOS as these releases are frequently tested against the Yocto Project and officially supported. For a list of the distributions under validation and their status, see the \"`Supported Linux Distributions <system-requirements-supported-distros>`.

> 你应该有一个相当新的基于 Linux 的主机系统。最好使用最近发布的 Fedora、openSUSE、Debian、Ubuntu、RHEL 或 CentOS，因为这些版本经常与 Yocto 项目进行测试，并获得正式支持。有关正在验证的发行版及其状态的列表，请参阅 Yocto 项目参考手册中的“支持的 Linux 发行版<system-requirements-supported-distros>”部分以及 Yocto wiki 页面：yocto_wiki：[分布支持\</Distribution_Support\>]。

2. *Have Enough Free Memory:* Your system should have at least 50 Gbytes of free disk space for building images.
3. *Meet Minimal Version Requirements:* The OpenEmbedded build system should be able to run on any modern distribution that has the following versions for Git, tar, Python, gcc and make.

> *满足最低版本要求：OpenEmbedded 构建系统应该能够在任何具有以下 Git、tar、Python、gcc 和 make 版本的现代发行版上运行。*

- Git &MIN_GIT_VERSION; or greater
- tar &MIN_TAR_VERSION; or greater
- Python &MIN_PYTHON_VERSION; or greater.
- gcc &MIN_GCC_VERSION; or greater.
- GNU make &MIN_MAKE_VERSION; or greater

If your build host does not meet any of these listed version requirements, you can take steps to prepare the system so that you can still use the Yocto Project. See the \"`ref-manual/system-requirements:required git, tar, python, make and gcc versions`\" section in the Yocto Project Reference Manual for information.

> 如果您的构建主机不符合这些列出的版本要求，您可以采取步骤来准备系统，以便您仍然可以使用 Yocto Project。有关信息，请参阅 Yocto Project 参考手册中的“ref-manual / system-requirements：所需的 git，tar，python，make 和 gcc 版本”部分。

4. *Install Development Host Packages:* Required development host packages vary depending on your build host and what you want to do with the Yocto Project. Collectively, the number of required packages is large if you want to be able to cover all cases.

> 4. *安装开发主机软件包：* 根据您的构建主机和您想用 Yocto Project 做什么，所需的开发主机软件包会有所不同。如果您想覆盖所有情况，所需的软件包总数会很大。

For lists of required packages for all scenarios, see the \"`ref-manual/system-requirements:required packages for the build host`\" section in the Yocto Project Reference Manual.

> 对于所有场景的所需软件包列表，请参见 Yocto 项目参考手册中“ref-manual/system-requirements：构建主机所需软件包”部分。

Once you have completed the previous steps, you are ready to continue using a given development path on your native Linux machine. If you are going to use BitBake, see the \"``dev-manual/start:cloning the \`\`poky\`\` repository``\" section in the Toaster User Manual.

> 完成前面的步骤后，您可以继续在本地 Linux 机器上使用给定的开发路径。如果您要使用 BitBake，请参阅“dev-manual / start：克隆 `poky` 存储库”部分。如果您要使用可扩展 SDK，请参阅 Yocto Project 应用程序开发和可扩展软件开发套件(eSDK)手册中的“/ sdk-manual / extensible”章节。如果要编辑内核，请参阅 `/kernel-dev/index`。如果要使用 Toaster，请参阅 Toaster 用户手册中的“/ toaster-manual / setup-and-use”部分。

## Setting Up to Use CROss PlatformS (CROPS)

With [CROPS](https://github.com/crops/poky-container), which leverages [Docker Containers](https://www.docker.com/), you can create a Yocto Project development environment that is operating system agnostic. You can set up a container in which you can develop using the Yocto Project on a Windows, Mac, or Linux machine.

> 使用利用 Docker 容器的 CROPS，您可以创建与操作系统无关的 Yocto 项目开发环境。您可以在 Windows、Mac 或 Linux 机器上设置一个容器，在其中使用 Yocto 项目进行开发。

Follow these general steps to prepare a Windows, Mac, or Linux machine as your Yocto Project build host:

1. *Determine What Your Build Host Needs:* [Docker](https://www.docker.com/what-docker) is a software container platform that you need to install on the build host. Depending on your build host, you might have to install different software to support Docker containers. Go to the Docker installation page and read about the platform requirements in \"[Supported Platforms](https://docs.docker.com/engine/install/#supported-platforms)\" your build host needs to run containers.

> 1. *确定您的构建主机所需的内容：* [Docker](https://www.docker.com/what-docker) 是一个软件容器平台，您需要在构建主机上安装。根据您的构建主机，您可能需要安装不同的软件来支持 Docker 容器。转到 Docker 安装页面，并阅读“[支持的平台](https://docs.docker.com/engine/install/#supported-platforms)”中您的构建主机需要运行容器的平台要求。

2. *Choose What To Install:* Depending on whether or not your build host meets system requirements, you need to install \"Docker CE Stable\" or the \"Docker Toolbox\". Most situations call for Docker CE. However, if you have a build host that does not meet requirements (e.g. Pre-Windows 10 or Windows 10 \"Home\" version), you must install Docker Toolbox instead.

> 根据构建主机是否满足系统要求，您需要安装“Docker CE Stable”或“Docker Toolbox”。大多数情况都需要 Docker CE。但是，如果您的构建主机不满足要求(例如 Pre-Windows 10 或 Windows 10“主版本”)，则必须安装 Docker Toolbox。

3. *Go to the Install Site for Your Platform:* Click the link for the Docker edition associated with your build host\'s native software. For example, if your build host is running Microsoft Windows Version 10 and you want the Docker CE Stable edition, click that link under \"Supported Platforms\".

> 3. *前往您的平台的安装站点：* 点击与您的构建主机的本地软件相关联的 Docker 版本的链接。例如，如果您的构建主机正在运行 Microsoft Windows 版本 10，并且您想要 Docker CE 稳定版本，请在“支持的平台”下点击该链接。

4. *Install the Software:* Once you have understood all the pre-requisites, you can download and install the appropriate software. Follow the instructions for your specific machine and the type of the software you need to install:

> 4. *安装软件：* 在您了解了所有的先决条件之后，您可以下载并安装适当的软件。根据您的特定机器和需要安装的软件类型，按照说明操作：

- Install [Docker Desktop on Windows](https://docs.docker.com/docker-for-windows/install/#install-docker-desktop-on-windows) for Windows build hosts that meet requirements.

> 安装符合要求的 Windows 构建主机上的 [Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/#install-docker-desktop-on-windows)。

- Install [Docker Desktop on MacOs](https://docs.docker.com/docker-for-mac/install/#install-and-run-docker-desktop-on-mac) for Mac build hosts that meet requirements.

> 安装符合要求的 Mac 构建主机上的 [Docker Desktop for Mac](https://docs.docker.com/docker-for-mac/install/#install-and-run-docker-desktop-on-mac)。

- Install [Docker Engine on CentOS](https://docs.docker.com/engine/install/centos/) for Linux build hosts running the CentOS distribution.
- Install [Docker Engine on Debian](https://docs.docker.com/engine/install/debian/) for Linux build hosts running the Debian distribution.
- Install [Docker Engine for Fedora](https://docs.docker.com/engine/install/fedora/) for Linux build hosts running the Fedora distribution.
- Install [Docker Engine for Ubuntu](https://docs.docker.com/engine/install/ubuntu/) for Linux build hosts running the Ubuntu distribution.

5. *Optionally Orient Yourself With Docker:* If you are unfamiliar with Docker and the container concept, you can learn more here -[https://docs.docker.com/get-started/](https://docs.docker.com/get-started/).

> 如果您不熟悉 Docker 和容器概念，可以在此处学习更多信息-[https://docs.docker.com/get-started/](https://docs.docker.com/get-started/)。

6. *Launch Docker or Docker Toolbox:* You should be able to launch Docker or the Docker Toolbox and have a terminal shell on your development host.
7. *Set Up the Containers to Use the Yocto Project:* Go to [https://github.com/crops/docker-win-mac-docs/wiki](https://github.com/crops/docker-win-mac-docs/wiki) and follow the directions for your particular build host (i.e. Linux, Mac, or Windows).

> 设置容器以使用 Yocto 项目：转到 [https://github.com/crops/docker-win-mac-docs/wiki](https://github.com/crops/docker-win-mac-docs/wiki) 并按照您的特定构建主机(即 Linux，Mac 或 Windows)的说明操作。

Once you complete the setup instructions for your machine, you have the Poky, Extensible SDK, and Toaster containers available. You can click those links from the page and learn more about using each of those containers.

> 一旦您完成机器的设置说明，您就可以使用 Poky、可扩展 SDK 和 Toaster 容器。您可以从页面上点击这些链接，了解有关使用每个容器的更多信息。

Once you have a container set up, everything is in place to develop just as if you were running on a native Linux machine. If you are going to use the Poky container, see the \"``dev-manual/start:cloning the \`\`poky\`\` repository``\" section in the Toaster User Manual.

> 一旦容器设置完成，就可以像在本地 Linux 机器上一样开发了。如果要使用 Poky 容器，请参阅“dev-manual/start:cloning the `poky` repository”部分。如果要使用可扩展 SDK 容器，请参阅 Yocto 项目应用程序开发和可扩展软件开发套件(eSDK)手册中的“/sdk-manual/extensible”章节。如果要使用 Toaster 容器，请参阅 Toaster 用户手册中的“/toaster-manual/setup-and-use”部分。

## Setting Up to Use Windows Subsystem For Linux (WSL 2)

With [Windows Subsystem for Linux (WSL 2)](https://learn.microsoft.com/en-us/windows/wsl/), you can create a Yocto Project development environment that allows you to build on Windows. You can set up a Linux distribution inside Windows in which you can develop using the Yocto Project.

> 使用 Windows 子系统 for Linux(WSL 2)，您可以创建一个 Yocto Project 开发环境，允许您在 Windows 上构建。您可以在 Windows 中设置一个 Linux 发行版，其中您可以使用 Yocto Project 进行开发。

Follow these general steps to prepare a Windows machine using WSL 2 as your Yocto Project build host:

1. *Make sure your Windows machine is capable of running WSL 2:*

   While all Windows 11 and Windows Server 2022 builds support WSL 2, the first versions of Windows 10 and Windows Server 2019 didn\'t. Check the minimum build numbers for [Windows 10](https://learn.microsoft.com/en-us/windows/wsl/install-manual#step-2---check-requirements-for-running-wsl-2) and for [Windows Server 2019](https://learn.microsoft.com/en-us/windows/wsl/install-on-server).

> 所有的 Windows 11 和 Windows Server 2022 版本都支持 WSL 2，但最初的 Windows 10 和 Windows Server 2019 版本不支持。检查 [Windows 10](https://learn.microsoft.com/en-us/windows/wsl/install-manual#step-2---check-requirements-for-running-wsl-2) 和 [Windows Server 2019](https://learn.microsoft.com/en-us/windows/wsl/install-on-server) 的最低构建号码。

To check which build version you are running, you may open a command prompt on Windows and execute the command \"ver\":

```
C:\Users\myuser> ver

Microsoft Windows [Version 10.0.19041.153]
```

2. *Install the Linux distribution of your choice inside WSL 2:* Once you know your version of Windows supports WSL 2, you can install the distribution of your choice from the Microsoft Store. Open the Microsoft Store and search for Linux. While there are several Linux distributions available, the assumption is that your pick will be one of the distributions supported by the Yocto Project as stated on the instructions for using a native Linux host. After making your selection, simply click \"Get\" to download and install the distribution.

> 如果您知道自己的 Windows 版本支持 WSL 2，您可以从 Microsoft Store 安装自己选择的发行版。打开 Microsoft Store 并搜索 Linux。虽然有几个 Linux 发行版可用，但假设您的选择将是 Yocto Project 指示中指定的受支持的发行版之一。在做出选择后，只需单击“获取”即可下载并安装发行版。

3. *Check which Linux distribution WSL 2 is using:* Open a Windows PowerShell and run:

   ```
   C:\WINDOWS\system32> wsl -l -v
   NAME    STATE   VERSION
   *Ubuntu Running 2
   ```

   Note that WSL 2 supports running as many different Linux distributions as you want to install.
4. *Optionally Get Familiar with WSL:* You can learn more on [https://docs.microsoft.com/en-us/windows/wsl/wsl2-about](https://docs.microsoft.com/en-us/windows/wsl/wsl2-about).

> *可选择了解 WSL：您可以在[[https://docs.microsoft.com/en-us/windows/wsl/wsl2-about](https://docs.microsoft.com/en-us/windows/wsl/wsl2-about)]([https://docs.microsoft.com/en-us/windows/wsl/wsl2-about](https://docs.microsoft.com/en-us/windows/wsl/wsl2-about))上了解更多信息。

5. *Launch your WSL Distibution:* From the Windows start menu simply launch your WSL distribution just like any other application.
6. *Optimize your WSL 2 storage often:* Due to the way storage is handled on WSL 2, the storage space used by the underlying Linux distribution is not reflected immediately, and since BitBake heavily uses storage, after several builds, you may be unaware you are running out of space. As WSL 2 uses a VHDX file for storage, this issue can be easily avoided by regularly optimizing this file in a manual way:

> *经常优化您的 WSL 2 存储：*由于处理存储的方式，底层 Linux 发行版所使用的存储空间不会立即反映出来，而 BitBake 严重依赖存储，经过几次构建后，您可能不知道空间已经用完了。由于 WSL 2 使用 VHDX 文件存储，可以通过定期以手动方式优化此文件来轻松避免此问题：

1. *Find the location of your VHDX file:*

   First you need to find the distro app package directory, to achieve this open a Windows Powershell as Administrator and run:

   ```
   C:\WINDOWS\system32> Get-AppxPackage -Name "*Ubuntu*" | Select PackageFamilyName
   PackageFamilyName
   -----------------
   CanonicalGroupLimited.UbuntuonWindows_79abcdefgh
   ```

   You should now replace the PackageFamilyName and your user on the following path to find your VHDX file:

   ```
   ls C:\Users\myuser\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79abcdefgh\LocalState\
   Mode                 LastWriteTime         Length Name
   -a----         3/14/2020   9:52 PM    57418973184 ext4.vhdx
   ```

   Your VHDX file path is: `C:\Users\myuser\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79abcdefgh\LocalState\ext4.vhdx`

2a. *Optimize your VHDX file using Windows Powershell:*

> To use the `optimize-vhd` cmdlet below, first install the Hyper-V option on Windows. Then, open a Windows Powershell as Administrator to optimize your VHDX file, shutting down WSL first:

> 要使用下面的 `optimize-vhd` 命令，首先在 Windows 上安装 Hyper-V 选项。然后，以管理员身份打开 Windows Powershell，优化 VHDX 文件，首先关闭 WSL：
>
> ```
> C:\WINDOWS\system32> wsl --shutdown
> ```

> C:\WINDOWS\system32> optimize-vhd -Path C:\Users\myuser\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79abcdefgh\LocalState\ext4.vhdx -Mode full

>> C:\WINDOWS\system32> 优化 VHD -路径 C:\Users\myuser\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_79abcdefgh\LocalState\ext4.vhdx -模式 全面
>>
>
> ```
>
> A progress bar should be shown while optimizing the VHDX file, and storage should now be reflected correctly on the Windows Explorer.
>
> ```

2b. *Optimize your VHDX file using DiskPart:*

> The `optimize-vhd` cmdlet noted in step 2a above is provided by Hyper-V. Not all SKUs of Windows can install Hyper-V. As an alternative, use the DiskPart tool. To start, open a Windows command prompt as Administrator to optimize your VHDX file, shutting down WSL first:

> 在上面的步骤 2a 中提到的 `optimize-vhd` 命令行工具由 Hyper-V 提供。并非所有 Windows SKU 都可以安装 Hyper-V。作为替代方案，请使用 DiskPart 工具。首先，关闭 WSL，作为管理员打开 Windows 命令提示符以优化 VHDX 文件：
>
> ```
> C:\WINDOWS\system32> wsl --shutdown
> C:\WINDOWS\system32> diskpart
>
> DISKPART> select vdisk file="<path_to_VHDX_file>"
> DISKPART> attach vdisk readonly
> DISKPART> compact vdisk
> DISKPART> exit
> ```

::: note
::: title
Note
:::

The current implementation of WSL 2 does not have out-of-the-box access to external devices such as those connected through a USB port, but it automatically mounts your `C:` drive on `/mnt/c/` (and others), which you can use to share deploy artifacts to be later flashed on hardware through Windows, but your `Build Directory` should not reside inside this mountpoint.

> 当前的 WSL 2 实现不支持通过 USB 端口连接的外部设备的即时访问，但它会自动将你的 `C：` 驱动器挂载到 `/mnt/c/`(和其他驱动器)，你可以使用它来共享部署构件，以便在 Windows 上稍后刷新硬件，但你的 `构建目录` 不应该位于此挂载点内。
> :::

Once you have WSL 2 set up, everything is in place to develop just as if you were running on a native Linux machine. If you are going to use the Extensible SDK container, see the \"`/sdk-manual/extensible`\" section in the Toaster User Manual.

> 一旦你安装了 WSL 2，一切就绪，就像在本地 Linux 机器上开发一样。如果你要使用可扩展 SDK 容器，请参阅 Yocto 项目应用开发和可扩展软件开发套件(eSDK)手册中的“/sdk-manual/extensible”章节。如果你要使用 Toaster 容器，请参阅 Toaster 用户手册中的“/toaster-manual/setup-and-use”部分。

# Locating Yocto Project Source Files

This section shows you how to locate, fetch and configure the source files you\'ll need to work with the Yocto Project.

::: note
::: title
Note
:::

- For concepts and introductory information about Git as it is used in the Yocto Project, see the \"`overview-manual/development-environment:git`\" section in the Yocto Project Overview and Concepts Manual.

> 对于关于 Yocto 项目中使用的 Git 的概念和介绍信息，请参阅 Yocto 项目概述和概念手册中的“overview-manual/development-environment:git”部分。

- For concepts on Yocto Project source repositories, see the \"`overview-manual/development-environment:yocto project source repositories`\" section in the Yocto Project Overview and Concepts Manual.\"

> 对于 Yocto Project 源代码存储库的概念，请参阅 Yocto Project 概览和概念手册中的“概览手册/开发环境：Yocto Project 源代码存储库”部分。
> :::

## Accessing Source Repositories

Working from a copy of the upstream `dev-manual/start:accessing source repositories`/poky\`.

> 从上游的 dev-manual/start：访问源存储库的副本开始工作是使用 Yocto 项目发行版的首选方法。您可以在 yocto_git 上查看 Yocto 项目源存储库：/。特别是，您可以在 yocto_git 上找到 poky 存储库：/poky`。

Use the following procedure to locate the latest upstream copy of the `poky` Git repository:

1. *Access Repositories:* Open a browser and go to :yocto_[git:%60/](git:%60/)\` to access the GUI-based interface into the Yocto Project source repositories.

> 访问存储库：打开浏览器，转到 yocto_[git:%60/](git:%60/)访问基于 GUI 的 Yocto Project 源存储库界面。

2. *Select the Repository:* Click on the repository in which you are interested (e.g. `poky`).
3. *Find the URL Used to Clone the Repository:* At the bottom of the page, note the URL used to clone that repository (e.g. :yocto_[git:%60/poky](git:%60/poky)\`).

> 3. *找到用于克隆存储库的 URL：*在页面底部，注意用于克隆该存储库的 URL(例如：yocto_[git:%60/poky](git:%60/poky)\)。

::: note
::: title
Note
:::

For information on cloning a repository, see the \"``dev-manual/start:cloning the \`\`poky\`\` repository``\" section.

> 要了解克隆存储库的信息，请参阅“dev-manual/start：克隆 `poky` 存储库”部分。
> :::

## Accessing Source Archives

The Yocto Project also provides source archives of its releases, which are available on :yocto_dl:[/releases/yocto/].

> 页克托项目还提供其发行版的源存档，可在:yocto_dl:[/releases/yocto/]。

You will find there source archives of individual components (if you wish to use them individually), and of the corresponding Poky release bundling a selection of these components.

> 你会在那里找到各个组件的源存档(如果你想单独使用它们)，以及捆绑了这些组件的 Poky 发行版。

::: note
::: title
Note
:::

The recommended method for accessing Yocto Project components is to use Git to clone the upstream repository and work from within that locally cloned repository.

> 推荐使用 Git 克隆上游存储库，并从本地克隆存储库中访问 Yocto 项目组件的方法。
> :::

## Using the Downloads Page

The :yocto_home:[Yocto Project Website \<\>]\" section.

> 网站 :yocto_home:[Yocto 项目 \<\>] 使用一个“下载”页面，您可以在其中找到并下载任何 Yocto 项目发行版的 tarball。这些文件不是 Git 存储库，而是类似于“dev-manual/start：访问源存档”部分中描述的发行版索引中的 tarball 的快照 tarball。

1. *Go to the Yocto Project Website:* Open The :yocto_home:[Yocto Project Website \<\>] in your browser.
2. *Get to the Downloads Area:* Select the \"DOWNLOADS\" item from the pull-down \"SOFTWARE\" tab menu near the top of the page.
3. *Select a Yocto Project Release:* Use the menu next to \"RELEASE\" to display and choose a recent or past supported Yocto Project release (e.g. &DISTRO_NAME_NO_CAP;, &DISTRO_NAME_NO_CAP_MINUS_ONE;, and so forth).

> 选择 Yocto 项目发行版：使用“发行版”旁边的菜单显示并选择最近或以往的受支持的 Yocto 项目发行版(例如&DISTRO_NAME_NO_CAP;、&DISTRO_NAME_NO_CAP_MINUS_ONE;等)。

::: note
::: title
Note
:::

For a \"map\" of Yocto Project releases to version numbers, see the :yocto_wiki:[Releases \</Releases\>] wiki page.
:::

You can use the \"RELEASE ARCHIVE\" link to reveal a menu of all Yocto Project releases.

4. *Download Tools or Board Support Packages (BSPs):* From the \"DOWNLOADS\" page, you can download tools or BSPs as well. Just scroll down the page and look for what you need.

> 4. *下载工具或板支持包(BSPs)：*从“下载”页面，您也可以下载工具或 BSPs。只需向下滚动页面，查找您所需要的内容即可。

# Cloning and Checking Out Branches

To use the Yocto Project for development, you need a release locally installed on your development system. This locally installed set of files is referred to as the `Source Directory` in the Yocto Project documentation.

> 要使用 Yocto Project 进行开发，您需要在开发系统上安装一个发行版本。Yocto Project 文档中将此本地安装的文件集称为“源目录”。

The preferred method of creating your Source Directory is by using `overview-manual/development-environment:git` to clone a local copy of the upstream `poky` repository. Working from a cloned copy of the upstream repository allows you to contribute back into the Yocto Project or to simply work with the latest software on a development branch. Because Git maintains and creates an upstream repository with a complete history of changes and you are working with a local clone of that repository, you have access to all the Yocto Project development branches and tag names used in the upstream repository.

> 最佳的创建源目录的方法是使用 `overview-manual/development-environment:git` 来克隆一个本地的上游 `poky` 存储库副本。从克隆的上游存储库副本开始工作，可以让您回馈到 Yocto Project 中，或者只是在开发分支上使用最新的软件。由于 Git 维护和创建一个具有完整更改历史记录的上游存储库，而您正在使用该存储库的本地克隆，因此您可以访问在上游存储库中使用的所有 Yocto Project 开发分支和标签名称。

## Cloning the `poky` Repository

Follow these steps to create a local version of the upstream `Poky` Git repository.

1. *Set Your Directory:* Change your working directory to where you want to create your local copy of `poky`.
2. *Clone the Repository:* The following example command clones the `poky` repository and uses the default name \"poky\" for your local repository:

   ```
   $ git clone git://git.yoctoproject.org/poky
   Cloning into 'poky'...
   remote: Counting objects: 432160, done.
   remote: Compressing objects: 100% (102056/102056), done.
   remote: Total 432160 (delta 323116), reused 432037 (delta 323000)
   Receiving objects: 100% (432160/432160), 153.81 MiB | 8.54 MiB/s, done.
   Resolving deltas: 100% (323116/323116), done.
   Checking connectivity... done.
   ```

   Unless you specify a specific development branch or tag name, Git clones the \"master\" branch, which results in a snapshot of the latest development changes for \"master\". For information on how to check out a specific development branch or on how to check out a local branch based on a tag name, see the \"`dev-manual/start:checking out by branch in poky`\" sections, respectively.

> 除非您指定特定的开发分支或标签名称，否则 Git 会克隆“master”分支，这将导致“master”的最新开发变更的快照。有关如何检出特定的开发分支或如何检出基于标签名称的本地分支的信息，请参阅“`dev-manual/start:checking out by branch in poky`”部分，分别。

Once the local repository is created, you can change to that directory and check its status. The `master` branch is checked out by default:

```
$ cd poky
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
$ git branch
* master
```

Your local repository of poky is identical to the upstream poky repository at the time from which it was cloned. As you work with the local branch, you can periodically use the `git pull --rebase` command to be sure you are up-to-date with the upstream branch.

> 你本地的 Poky 存储库与克隆时的上游 Poky 存储库完全相同。当你使用本地分支时，你可以定期使用 `git pull --rebase` 命令来确保你与上游分支保持同步。

## Checking Out by Branch in Poky

When you clone the upstream poky repository, you have access to all its development branches. Each development branch in a repository is unique as it forks off the \"master\" branch. To see and use the files of a particular development branch locally, you need to know the branch name and then specifically check out that development branch.

> 当你克隆上游 Poky 存储库时，你就可以访问它的所有开发分支。存储库中的每个开发分支都是独一无二的，因为它们分叉自“主”分支。要在本地看到并使用特定开发分支的文件，你需要知道该分支的名称，然后特别检查该开发分支。

::: note
::: title
Note
:::

Checking out an active development branch by branch name gives you a snapshot of that particular branch at the time you check it out. Further development on top of the branch that occurs after check it out can occur.

> 检出活动开发分支的名称可以让您在检出时获得该分支的快照。在检出之后，可以在该分支上进行进一步的开发。
> :::

1. *Switch to the Poky Directory:* If you have a local poky Git repository, switch to that directory. If you do not have the local copy of poky, see the \"``dev-manual/start:cloning the \`\`poky\`\` repository``\" section.

> 1. *切换到 Poky 目录：* 如果您有本地的 poky Git 存储库，请切换到该目录。如果没有本地的 poky 副本，请参见“dev-manual / start：克隆 `poky` 存储库”部分。

2. *Determine Existing Branch Names:* :

   ```
   $ git branch -a
   * master
   remotes/origin/1.1_M1
   remotes/origin/1.1_M2
   remotes/origin/1.1_M3
   remotes/origin/1.1_M4
   remotes/origin/1.2_M1
   remotes/origin/1.2_M2
   remotes/origin/1.2_M3
   . . .
   remotes/origin/thud
   remotes/origin/thud-next
   remotes/origin/warrior
   remotes/origin/warrior-next
   remotes/origin/zeus
   remotes/origin/zeus-next
   ... and so on ...
   ```
3. *Check out the Branch:* Check out the development branch in which you want to work. For example, to access the files for the Yocto Project &DISTRO; Release (&DISTRO_NAME;), use the following command:

> 3. *检出分支:* 检出你想要工作的开发分支。例如，访问 Yocto Project &DISTRO; Release (&DISTRO_NAME;) 的文件，使用以下命令：

```
$ git checkout -b &DISTRO_NAME_NO_CAP; origin/&DISTRO_NAME_NO_CAP;
Branch &DISTRO_NAME_NO_CAP; set up to track remote branch &DISTRO_NAME_NO_CAP; from origin.
Switched to a new branch '&DISTRO_NAME_NO_CAP;'
```

The previous command checks out the \"&DISTRO_NAME_NO_CAP;\" development branch and reports that the branch is tracking the upstream \"origin/&DISTRO_NAME_NO_CAP;\" branch.

> 上一个命令检查“&DISTRO_NAME_NO_CAP;”开发分支，并报告该分支正在跟踪上游的“origin / &DISTRO_NAME_NO_CAP;”分支。

The following command displays the branches that are now part of your local poky repository. The asterisk character indicates the branch that is currently checked out for work:

> 以下命令显示现在已经成为本地 poky 库的分支。星号字符表示当前检出用于工作的分支：

```
$ git branch
  master
  * &DISTRO_NAME_NO_CAP;
```

## Checking Out by Tag in Poky

Similar to branches, the upstream repository uses tags to mark specific commits associated with significant points in a development branch (i.e. a release point or stage of a release). You might want to set up a local branch based on one of those points in the repository. The process is similar to checking out by branch name except you use tag names.

> 类似分支，上游存储库使用标签来标记与开发分支中的重要点(即发布点或发布阶段)相关联的特定提交。您可能希望基于存储库中的其中一个点建立一个本地分支。该过程与按分支名称检出类似，只是使用标签名称。

::: note
::: title
Note
:::

Checking out a branch based on a tag gives you a stable set of files not affected by development on the branch above the tag.
:::

1. *Switch to the Poky Directory:* If you have a local poky Git repository, switch to that directory. If you do not have the local copy of poky, see the \"``dev-manual/start:cloning the \`\`poky\`\` repository``\" section.

> 1. *切换到 Poky 目录：*如果您有本地的 Poky Git 存储库，请切换到该目录。如果没有本地的 Poky 副本，请参见“dev-manual/start：克隆 `poky` 存储库”部分。

2. *Fetch the Tag Names:* To checkout the branch based on a tag name, you need to fetch the upstream tags into your local repository:

   ```
   $ git fetch --tags
   $
   ```
3. *List the Tag Names:* You can list the tag names now:

   ```
   $ git tag
   1.1_M1.final
   1.1_M1.rc1
   1.1_M1.rc2
   1.1_M2.final
   1.1_M2.rc1
      .
      .
      .
   yocto-2.5
   yocto-2.5.1
   yocto-2.5.2
   yocto-2.5.3
   yocto-2.6
   yocto-2.6.1
   yocto-2.6.2
   yocto-2.7
   yocto_1.5_M5.rc8
   ```
4. *Check out the Branch:* :

   ```
   $ git checkout tags/yocto-&DISTRO; -b my_yocto_&DISTRO;
   Switched to a new branch 'my_yocto_&DISTRO;'
   $ git branch
     master
   * my_yocto_&DISTRO;
   ```

   The previous command creates and checks out a local branch named \"[my_yocto]()&DISTRO;\", which is based on the commit in the upstream poky repository that has the same tag. In this example, the files you have available locally as a result of the `checkout` command are a snapshot of the \"&DISTRO_NAME_NO_CAP;\" development branch at the point where Yocto Project &DISTRO; was released.

> 上一个命令创建并检出名为"[my_yocto]()&DISTRO;"的本地分支，该分支基于具有相同标签的上游 poky 存储库中的提交。在本例中，您由于 `checkout` 命令而在本地获得的文件是“&DISTRO_NAME_NO_CAP;”开发分支在发布 Yocto Project &DISTRO;时的快照。
