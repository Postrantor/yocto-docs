---
tip: translate by openai@2023-06-10 10:37:18
...
---
title: The Yocto Project Development Environment
------------------------------------------------

This chapter takes a look at the Yocto Project development environment. The chapter provides Yocto Project Development environment concepts that help you understand how work is accomplished in an open source environment, which is very different as compared to work accomplished in a closed, proprietary environment.

> 本章着眼于 Yocto 项目开发环境。本章提供了 Yocto 项目开发环境的概念，帮助您理解在开源环境中如何完成工作，这与在封闭的专有环境中完成的工作非常不同。

Specifically, this chapter addresses open source philosophy, source repositories, workflows, Git, and licensing.

# Open Source Philosophy

Open source philosophy is characterized by software development directed by peer production and collaboration through an active community of developers. Contrast this to the more standard centralized development models used by commercial software companies where a finite set of developers produces a product for sale using a defined set of procedures that ultimately result in an end product whose architecture and source material are closed to the public.

> 开源理念的特点是通过一个活跃的开发者社区，通过同行生产和协作来指导软件开发。与商业软件公司使用的标准集中开发模型形成对比，商业软件公司使用有限的开发者生产销售产品，使用定义的程序最终产生的最终产品的架构和源材料对公众不公开。

Open source projects conceptually have differing concurrent agendas, approaches, and production. These facets of the development process can come from anyone in the public (community) who has a stake in the software project. The open source environment contains new copyright, licensing, domain, and consumer issues that differ from the more traditional development environment. In an open source environment, the end product, source material, and documentation are all available to the public at no cost.

> 开源项目在概念上具有不同的并行议程、方法和生产。这些开发过程的方面可以来自公众(社区)中对软件项目有利益关系的任何人。开源环境包含与传统开发环境不同的新版权、许可、域和消费者问题。在开源环境中，最终产品、源材料和文档均可免费向公众提供。

A benchmark example of an open source project is the Linux kernel, which was initially conceived and created by Finnish computer science student Linus Torvalds in 1991. Conversely, a good example of a non-open source project is the Windows family of operating systems developed by Microsoft Corporation.

> 一个开源项目的基准示例是 Linux 内核，它最初由芬兰计算机科学学生 Linus Torvalds 在 1991 年构思和创建。相反，非开源项目的一个很好的例子是由微软公司开发的 Windows 操作系统家族。

Wikipedia has a good `historical description of the Open Source Philosophy <Open_source>`. You can also find helpful information on how to participate in the Linux Community [here](https://www.kernel.org/doc/html/latest/process/index.html).

> 维基百科有一个很好的有关开源哲学的历史描述 <Open_source>。您还可以在[这里](https://www.kernel.org/doc/html/latest/process/index.html)找到有关如何参与 Linux 社区的有用信息。

# The Development Host

A development host or `Build Host` is key to using the Yocto Project. Because the goal of the Yocto Project is to develop images or applications that run on embedded hardware, development of those images and applications generally takes place on a system not intended to run the software \-\-- the development host.

> 开发主机或“构建主机”对于使用 Yocto 项目至关重要。因为 Yocto 项目的目标是开发在嵌入式硬件上运行的镜像或应用程序，所以这些镜像和应用程序的开发通常是在不用于运行软件的系统上进行的-开发主机。

You need to set up a development host in order to use it with the Yocto Project. Most find that it is best to have a native Linux machine function as the development host. However, it is possible to use a system that does not run Linux as its operating system as your development host. When you have a Mac or Windows-based system, you can set it up as the development host by using [CROPS](https://github.com/crops/poky-container), which leverages [Docker Containers](https://www.docker.com/). Once you take the steps to set up a CROPS machine, you effectively have access to a shell environment that is similar to what you see when using a Linux-based development host. For the steps needed to set up a system using CROPS, see the \"`dev-manual/start:setting up to use cross platforms (crops)`\" section in the Yocto Project Development Tasks Manual.

> 你需要设置一个开发主机，才能用 Yocto Project。大多数人发现，最好使用一个本地的 Linux 机器作为开发主机。但是，也可以使用一个不使用 Linux 作为操作系统的系统作为您的开发主机。当你有一个基于 Mac 或 Windows 的系统时，你可以通过使用 [CROPS](https://github.com/crops/poky-container) 来将其设置为开发主机，它利用 [Docker 容器](https://www.docker.com/)。一旦您采取了设置 CROPS 机器的步骤，就可以获得一个类似于使用基于 Linux 的开发主机时看到的 shell 环境。有关设置使用 CROPS 的系统所需的步骤，请参见 Yocto Project 开发任务手册中的“dev-manual/start：设置使用跨平台(crops)”部分。

If your development host is going to be a system that runs a Linux distribution, you must still take steps to prepare the system for use with the Yocto Project. You need to be sure that the Linux distribution on the system is one that supports the Yocto Project. You also need to be sure that the correct set of host packages are installed that allow development using the Yocto Project. For the steps needed to set up a development host that runs Linux, see the \"`dev-manual/start:setting up a native linux host`\" section in the Yocto Project Development Tasks Manual.

> 如果您的开发主机是运行 Linux 发行版的系统，您仍然需要采取措施来准备该系统以便与 Yocto 项目一起使用。您需要确保系统上的 Linux 发行版支持 Yocto 项目。您还需要确保安装了正确的主机软件包，以允许使用 Yocto 项目进行开发。有关在 Linux 上设置开发主机的步骤，请参见 Yocto 项目开发任务手册中的“dev-manual/start：设置本地 Linux 主机”部分。

Once your development host is set up to use the Yocto Project, there are several ways of working in the Yocto Project environment:

- *Command Lines, BitBake, and Shells:* Traditional development in the Yocto Project involves using the `OpenEmbedded Build System`, which uses BitBake, in a command-line environment from a shell on your development host. You can accomplish this from a host that is a native Linux machine or from a host that has been set up with CROPS. Either way, you create, modify, and build images and applications all within a shell-based environment using components and tools available through your Linux distribution and the Yocto Project.

> 使用 Yocto 项目中的传统开发涉及使用 `OpenEmbedded构建系统`，该系统使用 BitBake，在开发主机上的命令行环境中使用 shell。您可以从本机 Linux 机器或 CROPS 设置的主机上完成此操作。无论如何，您都可以使用 Linux 发行版和 Yocto 项目提供的组件和工具，在基于 shell 的环境中创建、修改和构建镜像和应用程序。

For a general flow of the build procedures, see the \"`dev-manual/building:building a simple image`\" section in the Yocto Project Development Tasks Manual.

> 对于构建流程的一般流程，请参见 Yocto 项目开发任务手册中的“dev-manual/building：构建一个简单的镜像”部分。

- *Board Support Package (BSP) Development:* Development of BSPs involves using the Yocto Project to create and test layers that allow easy development of images and applications targeted for specific hardware. To development BSPs, you need to take some additional steps beyond what was described in setting up a development host.

> 开发板支持包(BSP)开发：开发 BSP 涉及使用 Yocto 项目创建和测试层，以便轻松开发针对特定硬件的映像和应用程序。要开发 BSP，您需要采取一些除设置开发主机之外的其他步骤。

The `/bsp-guide/index`\" section in the Yocto Project Board Support Package (BSP) Developer\'s Guide.

> 索引页 `/bsp-guide/index` 提供了与 BSP 相关的开发信息。关于开发主机准备的具体信息，请参见 Yocto 项目板支持包(BSP)开发者指南中的“bsp-guide/bsp：准备您的构建主机以与 bsp 层面一起工作”部分。

- *Kernel Development:* If you are going to be developing kernels using the Yocto Project you likely will be using `devtool`. A workflow using `devtool` makes kernel development quicker by reducing iteration cycle times.

> 如果您要使用 Yocto Project 开发内核，您可能会使用“devtool”。使用“devtool”的工作流程可以通过减少迭代周期时间来加快内核开发速度。

The `/kernel-dev/index`\" section in the Yocto Project Linux Kernel Development Manual.

> /kernel-dev/index 提供有关内核的开发信息。有关开发主机准备的具体信息，请参阅 Yocto Project Linux 内核开发手册中的“kernel-dev/common：准备构建主机以在内核上工作”部分。

- *Using Toaster:* The other Yocto Project development method that involves an interface that effectively puts the Yocto Project into the background is Toaster. Toaster provides an interface to the OpenEmbedded build system. The interface enables you to configure and run your builds. Information about builds is collected and stored in a database. You can use Toaster to configure and start builds on multiple remote build servers.

> 使用烤面包机：另一种 Yocto 项目开发方法涉及到一个界面，可以有效地把 Yocto 项目放到背景中。烤面包机提供了一个 OpenEmbedded 构建系统的界面。该界面可以让您配置和运行构建。构建的信息会被收集并存储在数据库中。您可以使用烤面包机在多个远程构建服务器上配置和启动构建。

For steps that show you how to set up your development host to use Toaster and on how to use Toaster in general, see the `/toaster-manual/index`.

> 要了解如何设置开发主机以使用 Toaster 以及如何一般使用 Toaster，请参阅/toaster-manual/index。

# Yocto Project Source Repositories

The Yocto Project team maintains complete source repositories for all Yocto Project files at :yocto_[git:%60/](git:%60/)[. This web-based source code browser is organized into categories by function such as IDE Plugins, Matchbox, Poky, Yocto Linux Kernel, and so forth. From the interface, you can click on any particular item in the \"Name\" column and see the URL at the bottom of the page that you need to clone a Git repository for that particular item. Having a local Git repository of the :term:\`Source Directory], which is usually named \"poky\", allows you to make changes, contribute to the history, and ultimately enhance the Yocto Project\'s tools, Board Support Packages, and so forth.

> 团队维护 Yocto 项目的完整源仓库，位于 yocto_git:/。这个基于网络的源代码浏览器按功能分类，如 IDE 插件、Matchbox、Poky、Yocto Linux 内核等。从界面上，您可以单击“名称”列中的任何特定项目，并查看页面底部的 URL，以克隆该特定项目的 Git 存储库。拥有通常命名为“poky”的源目录的本地 Git 存储库可以让您进行更改、贡献历史，并最终改进 Yocto 项目的工具、板支持包等。

For any supported release of Yocto Project, you can also go to the :yocto_home:[Yocto Project Website \<\>] and select the \"DOWNLOADS\" item from the \"SOFTWARE\" menu and get a released tarball of the `poky` repository, any supported BSP tarball, or Yocto Project tools. Unpacking these tarballs gives you a snapshot of the released files.

> 对于 Yocto Project 的任何支持版本，您也可以访问：Yocto Project 网站\<\>，从“软件”菜单中选择“下载”项，获取已发布的 poky 存储库 tarball，任何支持的 BSP tarball 或 Yocto Project 工具。解压这些 tarball 给您一个发布文件的快照。

::: note
::: title
Note
:::

- The recommended method for setting up the Yocto Project `Source Directory` to create a local copy of the upstream repositories.

> 推荐的方法来设置 Yocto Project 源代码目录和支持的 BSP(例如，meta-intel)的文件是使用 overview-manual/development-environment:git 来创建上游存储库的本地副本。

- Be sure to always work in matching branches for both the selected BSP repository and the Source Directory (i.e. `poky`) repository. For example, if you have checked out the \"&DISTRO_NAME_NO_CAP;\" branch of `poky` and you are going to use `meta-intel`, be sure to checkout the \"&DISTRO_NAME_NO_CAP;\" branch of `meta-intel`.

> 请确保始终在所选 BSP 存储库和源目录(即 `poky`)存储库中使用匹配的分支进行工作。例如，如果您已经检出了 `poky` 的“&DISTRO_NAME_NO_CAP;”分支，并且要使用 `meta-intel`，请确保检出 `meta-intel` 的“&DISTRO_NAME_NO_CAP;”分支。
> :::

In summary, here is where you can get the project files needed for development:

- :yocto_[git:%60Source](git:%60Source) Repositories: \<\>\` This area contains Poky, Yocto documentation, metadata layers, and Linux kernel. You can create local copies of Git repositories for each of these areas.

> 这个区域包含 Poky、Yocto 文档、元数据层和 Linux 内核。您可以为这些区域创建 Git 存储库的本地副本。

![image](figures/source-repos.png)

For steps on how to view and access these upstream Git repositories, see the \"`dev-manual/start:accessing source repositories`\" Section in the Yocto Project Development Tasks Manual.

> 要查看和访问这些上游 Git 存储库的步骤，请参见 Yocto 项目开发任务手册中的“dev-manual / start：访问源存储库”部分。

- :yocto_dl:[Yocto release archives: \</releases/yocto\>] This is where you can download tarballs corresponding to each Yocto Project release. Downloading and extracting these files does not produce a local copy of a Git repository but rather a snapshot corresponding to a particular release.

> 这里是您可以下载与 Yocto 项目发布相对应的 tarball 的地方。下载和解压缩这些文件不会产生本地 Git 存储库的副本，而是对应于特定发布的快照。

- :yocto_home:\`DOWNLOADS page \</software-overview/downloads/\>[: The :yocto_home:\`Yocto Project website \<\>].

> 网站：Yocto Project 网站([http://www.yoctoproject.org](http://www.yoctoproject.org))包含一个可以通过“软件”菜单访问的“下载”页面，允许您以 tarball 形式下载任何 Yocto Project 发行版、工具和板支持包(BSP)。超链接指向：yocto_dl：[/releases/yocto/]中的 tarball。

![image](figures/yp-download.png)

For steps on how to use the \"DOWNLOADS\" page, see the \"`dev-manual/start:using the downloads page`\" section in the Yocto Project Development Tasks Manual.

> 要了解如何使用“下载”页面的步骤，请参阅 Yocto Project 开发任务手册中的“dev-manual / start：使用下载页面”部分。

# Git Workflows and the Yocto Project

Developing using the Yocto Project likely requires the use of `overview-manual/development-environment:git`. Git is a free, open source distributed version control system used as part of many collaborative design environments. This section provides workflow concepts using the Yocto Project and Git. In particular, the information covers basic practices that describe roles and actions in a collaborative development environment.

> 使用 Yocto 项目开发可能需要使用“overview-manual/development-environment：git”。Git 是一种免费的开源分布式版本控制系统，用作许多协作设计环境的一部分。本节介绍使用 Yocto 项目和 Git 的工作流程概念。特别是，该信息涵盖了描述协作开发环境中角色和动作的基本实践。

::: note
::: title
Note
:::

If you are familiar with this type of development environment, you might not want to read this section.
:::

The Yocto Project files are maintained using Git in \"branches\" whose Git histories track every change and whose structures provide branches for all diverging functionality. Although there is no need to use Git, many open source projects do so.

> Yocto 项目文件使用 Git 进行维护，其 Git 历史记录跟踪每一次更改，并且结构提供了分支以支持所有分歧功能。虽然不需要使用 Git，但许多开源项目都这样做。

For the Yocto Project, a key individual called the \"maintainer\" is responsible for the integrity of the development branch of a given Git repository. The development branch is the \"upstream\" repository from which final or most recent builds of a project occur. The maintainer is responsible for accepting changes from other developers and for organizing the underlying branch structure to reflect release strategies and so forth.

> 在 Yocto 项目中，一个叫做“维护者”的关键人物负责给定 Git 存储库的开发分支的完整性。开发分支是“上游”存储库，从中发生项目的最终或最新构建。维护者负责接受其他开发人员的更改，并组织基础分支结构，以反映发布策略等。

::: note
::: title
Note
:::

For information on finding out who is responsible for (maintains) a particular area of code in the Yocto Project, see the \"`dev-manual/changes:submitting a change to the yocto project`\" section of the Yocto Project Development Tasks Manual.

> 要了解有关在 Yocto 项目中找出负责(维护)特定代码区域的人员信息，请参阅 Yocto 项目开发任务手册中的“ dev-manual/changes：提交更改到 Yocto 项目”部分。
> :::

The Yocto Project `poky` Git repository also has an upstream contribution Git repository named `poky-contrib`. You can see all the branches in this repository using the web interface of the :yocto_[git:%60Source](git:%60Source) Repositories \<\>\` organized within the \"Poky Support\" area. These branches hold changes (commits) to the project that have been submitted or committed by the Yocto Project development team and by community members who contribute to the project. The maintainer determines if the changes are qualified to be moved from the \"contrib\" branches into the \"master\" branch of the Git repository.

> 项目 Yocto 的 Poky Git 存储库也有一个上游贡献 Git 存储库，名为 Poky-contrib。您可以使用“Yocto_[git:％60Source](git:%EF%BC%8560Source) 存储库\<\>\`中的“Poky Support”区域中的网页界面查看此存储库中的所有分支。这些分支包含已由 Yocto 项目开发团队和贡献项目的社区成员提交或提交的项目更改(提交)。维护人员确定是否将更改从“contrib”分支移至 Git 存储库的“master”分支。

Developers (including contributing community members) create and maintain cloned repositories of upstream branches. The cloned repositories are local to their development platforms and are used to develop changes. When a developer is satisfied with a particular feature or change, they \"push\" the change to the appropriate \"contrib\" repository.

> 开发人员(包括贡献社区成员)创建和维护上游分支的克隆存储库。克隆存储库本地化为他们的开发平台，用于开发更改。当开发人员对特定功能或更改满意时，他们将更改“推送”到适当的“贡献”存储库中。

Developers are responsible for keeping their local repository up-to-date with whatever upstream branch they are working against. They are also responsible for straightening out any conflicts that might arise within files that are being worked on simultaneously by more than one person. All this work is done locally on the development host before anything is pushed to a \"contrib\" area and examined at the maintainer\'s level.

> 开发人员负责确保本地存储库与他们正在处理的上游分支保持同步。他们还负责解决由多个人同时处理的文件中可能出现的任何冲突。所有这些工作都是在推送到“contrib”区域并在维护者级别进行检查之前在开发主机上完成的。

There is a somewhat formal method by which developers commit changes and push them into the \"contrib\" area and subsequently request that the maintainer include them into an upstream branch. This process is called \"submitting a patch\" or \"submitting a change.\" For information on submitting patches and changes, see the \"`dev-manual/changes:submitting a change to the yocto project`\" section in the Yocto Project Development Tasks Manual.

> 有一种相对正式的方法，可以让开发人员提交变更并将其推送到“contrib”区域，然后要求维护者将其包含到上游分支中。这个过程称为“提交补丁”或“提交变更”。有关提交补丁和变更的信息，请参见 Yocto 项目开发任务手册中的“dev-manual/changes：向 Yocto 项目提交变更”部分。

In summary, there is a single point of entry for changes into the development branch of the Git repository, which is controlled by the project\'s maintainer. A set of developers independently develop, test, and submit changes to \"contrib\" areas for the maintainer to examine. The maintainer then chooses which changes are going to become a permanent part of the project.

> 总之，变更只能通过 Git 仓库的开发分支进行单一的入口。该分支由项目维护者控制。一组开发人员独立开发、测试并提交变更到“contrib”区域，由维护者审查。然后维护者选择哪些变更将成为项目的永久组成部分。

![image](svg/git-workflow.*)

While each development environment is unique, there are some best practices or methods that help development run smoothly. The following list describes some of these practices. For more information about Git workflows, see the workflow topics in the [Git Community Book](https://book.git-scm.com).

> 每个开发环境都是独一无二的，但有一些最佳实践或方法可以帮助开发顺利进行。以下列表描述了其中的一些实践。有关 Git 工作流程的更多信息，请参阅 Git 社区书籍中的工作流程主题 [Git Community Book](https://book.git-scm.com)。

- *Make Small Changes:* It is best to keep the changes you commit small as compared to bundling many disparate changes into a single commit. This practice not only keeps things manageable but also allows the maintainer to more easily include or refuse changes.

> *做出小的更改：*相比于把许多不同的更改打包到一个提交中，最好保持您提交的更改较小。这种做法不仅使事情可管理，而且还使维护者更容易包括或拒绝更改。

- *Make Complete Changes:* It is also good practice to leave the repository in a state that allows you to still successfully build your project. In other words, do not commit half of a feature, then add the other half as a separate, later commit. Each commit should take you from one buildable project state to another buildable state.

> 完成更改：保持存储库处于可以成功构建项目的状态也是一种好的实践。换句话说，不要提交一个功能的一半，然后将另一半作为单独的、后续提交的内容。每次提交都应该从一个可构建的项目状态带到另一个可构建的状态。

- *Use Branches Liberally:* It is very easy to create, use, and delete local branches in your working Git repository on the development host. You can name these branches anything you like. It is helpful to give them names associated with the particular feature or change on which you are working. Once you are done with a feature or change and have merged it into your local development branch, simply discard the temporary branch.

> 使用分支大量：在开发主机上，在你的工作 Git 存储库中创建、使用和删除本地分支非常容易。你可以给这些分支任何你喜欢的名字。给它们起与你正在处理的特定功能或更改相关的名字是有帮助的。一旦你完成了一个功能或更改，并将其合并到你的本地开发分支中，只需丢弃临时分支即可。

- *Merge Changes:* The `git merge` command allows you to take the changes from one branch and fold them into another branch. This process is especially helpful when more than a single developer might be working on different parts of the same feature. Merging changes also automatically identifies any collisions or \"conflicts\" that might happen as a result of the same lines of code being altered by two different developers.

> `git merge` 命令可以让你将一个分支的更改合并到另一个分支中。当多个开发者可能正在处理同一个特性的不同部分时，这个过程尤其有用。合并更改还会自动识别由于两个不同开发者改变同一行代码而可能发生的任何冲突或“冲突”。

- *Manage Branches:* Because branches are easy to use, you should use a system where branches indicate varying levels of code readiness. For example, you can have a \"work\" branch to develop in, a \"test\" branch where the code or change is tested, a \"stage\" branch where changes are ready to be committed, and so forth. As your project develops, you can merge code across the branches to reflect ever-increasing stable states of the development.

> 管理分支：由于分支使用起来很容易，因此您应该使用一个系统，其中分支表示不同级别的代码准备度。例如，您可以有一个“工作”分支用于开发，一个“测试”分支用于测试代码或更改，一个“阶段”分支，其中更改准备提交，等等。随着项目的发展，您可以在分支之间合并代码，以反映不断增加的开发稳定状态。

- *Use Push and Pull:* The push-pull workflow is based on the concept of developers \"pushing\" local commits to a remote repository, which is usually a contribution repository. This workflow is also based on developers \"pulling\" known states of the project down into their local development repositories. The workflow easily allows you to pull changes submitted by other developers from the upstream repository into your work area ensuring that you have the most recent software on which to develop. The Yocto Project has two scripts named `create-pull-request` and `send-pull-request` that ship with the release to facilitate this workflow. You can find these scripts in the `scripts` folder of the `Source Directory`\" section in the Yocto Project Development Tasks Manual.

> 使用推送和拉取：推送-拉取工作流基于开发人员将本地提交推送到远程存储库(通常是贡献存储库)的概念。该工作流也基于开发人员从上游存储库“拉取”已知项目状态到其本地开发存储库。该工作流可轻松地允许您从上游存储库拉取其他开发人员提交的更改，以确保您的开发区域具有最新的软件。Yocto Project 发布了两个名为 `create-pull-request` 和 `send-pull-request` 的脚本，用于促进此工作流。您可以在“源目录”的“脚本”文件夹中找到这些脚本。有关如何使用这些脚本的信息，请参见 Yocto Project 开发任务手册中的“dev-manual/changes：使用脚本将更改推送到上游并请求拉取”部分。

- *Patch Workflow:* This workflow allows you to notify the maintainer through an email that you have a change (or patch) you would like considered for the development branch of the Git repository. To send this type of change, you format the patch and then send the email using the Git commands `git format-patch` and `git send-email`. For information on how to use these scripts, see the \"`dev-manual/changes:submitting a change to the yocto project`\" section in the Yocto Project Development Tasks Manual.

> *补丁工作流：* 这个工作流允许您通过电子邮件通知维护者，您有一个变更(或补丁)，您希望考虑 Git 存储库的开发分支。要发送此类变更，您需要格式化补丁，然后使用 Git 命令 `git format-patch` 和 `git send-email` 发送电子邮件。有关如何使用这些脚本的信息，请参见 Yocto 项目开发任务手册中的“dev-manual / changes：向 Yocto 项目提交变更”部分。

# Git

The Yocto Project makes extensive use of Git, which is a free, open source distributed version control system. Git supports distributed development, non-linear development, and can handle large projects. It is best that you have some fundamental understanding of how Git tracks projects and how to work with Git if you are going to use the Yocto Project for development. This section provides a quick overview of how Git works and provides you with a summary of some essential Git commands.

> 项目 Yocto 广泛使用 Git，这是一个免费的、开源的分布式版本控制系统。Git 支持分布式开发、非线性开发，可以处理大型项目。如果您要使用 Yocto 项目进行开发，最好先基本了解 Git 如何跟踪项目以及如何使用 Git。本节将快速介绍 Git 的工作原理，并提供一些基本的 Git 命令概述。

::: note
::: title
Note
:::

- For more information on Git, see [https://git-scm.com/documentation](https://git-scm.com/documentation).
- If you need to download Git, it is recommended that you add Git to your system through your distribution\'s \"software store\" (e.g. for Ubuntu, use the Ubuntu Software feature). For the Git download page, see [https://git-scm.com/download](https://git-scm.com/download).

> 如果你需要下载 Git，建议你通过你的发行版的“软件商店”(例如，对于 Ubuntu，使用 Ubuntu 软件功能)将 Git 添加到系统中。有关 Git 下载页面，请参阅 [https://git-scm.com/download](https://git-scm.com/download)。

- For information beyond the introductory nature in this section, see the \"`dev-manual/start:locating yocto project source files`\" section in the Yocto Project Development Tasks Manual.

> 要了解本节介绍性质之外的信息，请参阅 Yocto Project Development Tasks Manual 中的“dev-manual/start：定位 Yocto Project 源文件”部分。
> :::

## Repositories, Tags, and Branches

As mentioned briefly in the previous section and also in the \"`overview-manual/development-environment:git workflows and the yocto project`\" section, the Yocto Project maintains source repositories at :yocto_[git:%60/](git:%60/)\`. If you look at this web-interface of the repositories, each item is a separate Git repository.

> 按照前面部分的简要介绍，以及“概述手册/开发环境：Git 工作流程和 Yocto 项目”部分的介绍，Yocto 项目在 yocto_[git:%60/](git:%60/)上维护源代码仓库。如果您查看这些仓库的网页界面，每个项目都是一个单独的 Git 仓库。

Git repositories use branching techniques that track content change (not files) within a project (e.g. a new feature or updated documentation). Creating a tree-like structure based on project divergence allows for excellent historical information over the life of a project. This methodology also allows for an environment from which you can do lots of local experimentation on projects as you develop changes or new features.

> Git 存储库使用分支技术，可跟踪项目(例如新功能或更新文档)中的内容变化(而不是文件)。基于项目分歧创建树状结构可以获得项目整个生命周期的出色历史信息。这种方法还允许您在开发变更或新功能时在本地进行大量实验。

A Git repository represents all development efforts for a given project. For example, the Git repository `poky` contains all changes and developments for that repository over the course of its entire life. That means that all changes that make up all releases are captured. The repository maintains a complete history of changes.

> 一个 Git 存储库代表着给定项目的所有开发工作。例如，Git 存储库“poky”包含了该存储库在其整个生命周期中的所有变更和开发。这意味着所有组成所有发布版本的更改都被捕获。该存储库保留了变更的完整历史记录。

You can create a local copy of any repository by \"cloning\" it with the `git clone` command. When you clone a Git repository, you end up with an identical copy of the repository on your development system. Once you have a local copy of a repository, you can take steps to develop locally. For examples on how to clone Git repositories, see the \"`dev-manual/start:locating yocto project source files`\" section in the Yocto Project Development Tasks Manual.

> 你可以通过使用 `git clone` 命令“克隆”任何存储库来创建其本地副本。当你克隆一个 Git 存储库时，你最终会在你的开发系统上得到一个完全相同的存储库副本。一旦你有了存储库的本地副本，你就可以采取步骤在本地开发。有关如何克隆 Git 存储库的示例，请参见 Yocto 项目开发任务手册中的“dev-manual/start：定位 Yocto 项目源文件”部分。

It is important to understand that Git tracks content change and not files. Git uses \"branches\" to organize different development efforts. For example, the `poky` repository has several branches that include the current \"&DISTRO_NAME_NO_CAP;\" branch, the \"master\" branch, and many branches for past Yocto Project releases. You can see all the branches by going to :yocto_[git:%60/poky/](git:%60/poky/)[ and clicking on the ]\` link beneath the \"Branch\" heading.

> 重要的是要明白 Git 跟踪内容的变化而不是文件。Git 使用“分支”来组织不同的开发工作。例如，“poky”存储库有几个分支，包括当前的“&DISTRO_NAME_NO_CAP;”分支，“master”分支和许多 Yocto Project 发行版的分支。您可以通过转到 yocto_[git：％60/poky/](git%EF%BC%9A%EF%BC%8560/poky/)[并单击]  下面的“分支”标题来查看所有分支。

Each of these branches represents a specific area of development. The \"master\" branch represents the current or most recent development. All other branches represent offshoots of the \"master\" branch.

> 每个分支代表一个特定的发展领域。“主分支”代表当前或最新的发展。所有其他分支都代表“主分支”的分支。

When you create a local copy of a Git repository, the copy has the same set of branches as the original. This means you can use Git to create a local working area (also called a branch) that tracks a specific development branch from the upstream source Git repository. In other words, you can define your local Git environment to work on any development branch in the repository. To help illustrate, consider the following example Git commands:

> 当您创建 Git 存储库的本地副本时，副本具有与原始副本相同的一组分支。这意味着您可以使用 Git 来创建本地工作区(也称为分支)，以跟踪来自上游源 Git 存储库的特定开发分支。换句话说，您可以定义本地 Git 环境，以在存储库中的任何开发分支上进行工作。为了帮助说明，请考虑以下示例 Git 命令：

```
$ cd ~
$ git clone git://git.yoctoproject.org/poky -b &DISTRO_NAME_NO_CAP;
```

In the previous example after moving to the home directory, the `git clone` command creates a local copy of the upstream `poky` Git repository and checks out a local branch named \"&DISTRO_NAME_NO_CAP;\", which tracks the upstream \"origin/&DISTRO_NAME_NO_CAP;\" branch. Changes you make while in this branch would ultimately affect the upstream \"&DISTRO_NAME_NO_CAP;\" branch of the `poky` repository.

> 在前面的示例中，移动到家目录后，`git clone` 命令创建了 `poky` Git 存储库的本地副本，并检出名为“&DISTRO_NAME_NO_CAP;”的本地分支，该分支跟踪上游“origin/&DISTRO_NAME_NO_CAP;”分支。在此分支中进行的更改最终会影响 `poky` 存储库的上游“&DISTRO_NAME_NO_CAP;”分支。

It is important to understand that when you create and checkout a local working branch based on a branch name, your local environment matches the \"tip\" of that particular development branch at the time you created your local branch, which could be different from the files in the \"master\" branch of the upstream repository. In other words, creating and checking out a local branch based on the \"&DISTRO_NAME_NO_CAP;\" branch name is not the same as checking out the \"master\" branch in the repository. Keep reading to see how you create a local snapshot of a Yocto Project Release.

> 重要的是要理解，当您基于分支名称创建和检出本地工作分支时，您的本地环境与在创建本地分支时该特定开发分支的“提示”相匹配，这可能与上游存储库的“主”分支中的文件不同。换句话说，基于“&DISTRO_NAME_NO_CAP;”分支名称创建和检出本地分支与检出存储库中的“主”分支并不相同。继续阅读以了解如何创建 Yocto 项目发行版的本地快照。

Git uses \"tags\" to mark specific changes in a repository branch structure. Typically, a tag is used to mark a special point such as the final change (or commit) before a project is released. You can see the tags used with the `poky` Git repository by going to :yocto_[git:%60/poky/](git:%60/poky/)[ and clicking on the ]\` link beneath the \"Tag\" heading.

> Git 使用“标签”来标记存储库分支结构中的特定更改。通常，标签用于标记在发布项目之前的最终更改(或提交)。您可以通过转到 yocto_[git:`/poky/](git:`/poky/)[并单击“标签”下面的]]来查看使用的 poky Git 存储库中的标签。

Some key tags for the `poky` repository are `jethro-14.0.3`, `morty-16.0.1`, `pyro-17.0.0`, and `&DISTRO_NAME_NO_CAP;-&DISTRO;`. These tags represent Yocto Project releases.

> 对于 `poky` 仓库来说，一些关键标签是 `jethro-14.0.3`、`morty-16.0.1`、`pyro-17.0.0` 和 `&DISTRO_NAME_NO_CAP;-&DISTRO;`。这些标签代表 Yocto 项目的发布。

When you create a local copy of the Git repository, you also have access to all the tags in the upstream repository. Similar to branches, you can create and checkout a local working Git branch based on a tag name. When you do this, you get a snapshot of the Git repository that reflects the state of the files when the change was made associated with that tag. The most common use is to checkout a working branch that matches a specific Yocto Project release. Here is an example:

> 当你创建一个 Git 仓库的本地副本时，你也可以访问上游仓库中的所有标签。与分支类似，你可以基于标签名称创建和检出一个本地工作 Git 分支。当你这样做时，你会得到一个 Git 仓库的快照，反映出与该标签关联的更改时文件的状态。最常见的用法是检出一个与特定 Yocto 项目发行版匹配的工作分支。这里有一个例子：

```
$ cd ~
$ git clone git://git.yoctoproject.org/poky
$ cd poky
$ git fetch --tags
$ git checkout tags/rocko-18.0.0 -b my_rocko-18.0.0
```

In this example, the name of the top-level directory of your local Yocto Project repository is `poky`. After moving to the `poky` directory, the `git fetch` command makes all the upstream tags available locally in your repository. Finally, the `git checkout` command creates and checks out a branch named \"my-rocko-18.0.0\" that is based on the upstream branch whose \"HEAD\" matches the commit in the repository associated with the \"rocko-18.0.0\" tag. The files in your repository now exactly match that particular Yocto Project release as it is tagged in the upstream Git repository. It is important to understand that when you create and checkout a local working branch based on a tag, your environment matches a specific point in time and not the entire development branch (i.e. from the \"tip\" of the branch backwards).

> 在这个例子中，本地 Yocto 项目存储库的顶级目录的名称是“poky”。移动到“poky”目录后，“git fetch”命令会在本地存储库中使所有上游标签可用。最后，“git checkout”命令会创建并检出一个名为“my-rocko-18.0.0”的分支，该分支基于其“HEAD”与与“rocko-18.0.0”标签关联的存储库中的提交匹配的上游分支。现在，您的存储库中的文件与上游 Git 存储库中标记的特定 Yocto 项目发行版完全匹配。重要的是要理解，当您创建并检出基于标签的本地工作分支时，您的环境匹配特定的时间点而不是整个开发分支(即从分支的“提示”向后)。

## Basic Commands

Git has an extensive set of commands that lets you manage changes and perform collaboration over the life of a project. Conveniently though, you can manage with a small set of basic operations and workflows once you understand the basic philosophy behind Git. You do not have to be an expert in Git to be functional. A good place to look for instruction on a minimal set of Git commands is [here](https://git-scm.com/documentation).

> Git 拥有一组广泛的命令，可以让您在项目的整个生命周期中管理更改并执行协作。不过，一旦您理解了 Git 背后的基本理念，就可以方便地使用一小组基本操作和工作流程来管理。您不必成为 Git 的专家才能有效地使用它。有关最小 Git 命令集的指令的好地方可以在[这里](https://git-scm.com/documentation)找到。

The following list of Git commands briefly describes some basic Git operations as a way to get started. As with any set of commands, this list (in most cases) simply shows the base command and omits the many arguments it supports. See the Git documentation for complete descriptions and strategies on how to use these commands:

> 以下 Git 命令列表简要描述了一些基本的 Git 操作，以便开始使用。与任何一组命令一样，此列表(在大多数情况下)仅显示基本命令，并省略了它支持的许多参数。有关完整描述和策略，请参阅 Git 文档，以了解如何使用这些命令：

- *git init:* Initializes an empty Git repository. You cannot use Git commands unless you have a `.git` repository.
- *git clone:* Creates a local clone of a Git repository that is on equal footing with a fellow developer\'s Git repository or an upstream repository.

> *git clone：* 创建一个与其他开发人员的 Git 存储库或上游存储库平等的本地 Git 存储库的副本。

- *git add:* Locally stages updated file contents to the index that Git uses to track changes. You must stage all files that have changed before you can commit them.

> *git add：*将更新后的文件内容本地阶段提交到 Git 使用的索引中。在提交之前，您必须先将所有已更改的文件都暂存起来。

- *git commit:* Creates a local \"commit\" that documents the changes you made. Only changes that have been staged can be committed. Commits are used for historical purposes, for determining if a maintainer of a project will allow the change, and for ultimately pushing the change from your local Git repository into the project\'s upstream repository.

> *git 提交：* 创建一个本地“提交”，记录您所做的更改。只有已阶段的更改才能提交。提交用于历史目的，用于确定项目维护者是否允许更改，以及最终将更改从本地 Git 存储库推送到项目的上游存储库中。

- *git status:* Reports any modified files that possibly need to be staged and gives you a status of where you stand regarding local commits as compared to the upstream repository.

> *git 状态：*报告任何可能需要阶段性处理的修改文件，并给出您在本地提交与上游存储库相比的状态。

- *git checkout branch-name:* Changes your local working branch and in this form assumes the local branch already exists. This command is analogous to \"cd\".

> *git checkout branch-name:* 更改您的本地工作分支，并以此形式假定本地分支已经存在。此命令类似于“cd”。

- *git checkout -b working-branch upstream-branch:* Creates and checks out a working branch on your local machine. The local branch tracks the upstream branch. You can use your local branch to isolate your work. It is a good idea to use local branches when adding specific features or changes. Using isolated branches facilitates easy removal of changes if they do not work out.

> - *git checkout -b working-branch upstream-branch:* 在本地机器上创建并检出一个工作分支。本地分支跟踪上游分支。您可以使用本地分支来隔离您的工作。在添加特定功能或更改时使用隔离分支是一个好主意。使用隔离分支可以方便地删除如果它们不起作用的更改。

- *git branch:* Displays the existing local branches associated with your local repository. The branch that you have currently checked out is noted with an asterisk character.

> *git 分支：* 显示与您的本地存储库相关联的现有本地分支。您当前检查出的分支用星号字符标注。

- *git branch -D branch-name:* Deletes an existing local branch. You need to be in a local branch other than the one you are deleting in order to delete branch-name.

> *git branch -D branch-name:* 删除一个现有的本地分支。您需要处于一个本地分支，而不是要删除的分支，才能删除 branch-name。

- *git pull \--rebase*: Retrieves information from an upstream Git repository and places it in your local Git repository. You use this command to make sure you are synchronized with the repository from which you are basing changes (e.g. the \"&DISTRO_NAME_NO_CAP;\" branch). The `--rebase` option ensures that any local commits you have in your branch are preserved at the top of your local branch.

> *git pull --rebase*: 从上游 Git 存储库检索信息，并将其放入本地 Git 存储库中。您使用此命令以确保与您正在基于更改的存储库(例如“DISTRO_NAME_NO_CAP”分支)保持同步。`--rebase` 选项确保您在本地分支中拥有的任何本地提交都保留在本地分支的顶部。

- *git push repo-name local-branch:upstream-branch:* Sends all your committed local changes to the upstream Git repository that your local repository is tracking (e.g. a contribution repository). The maintainer of the project draws from these repositories to merge changes (commits) into the appropriate branch of project\'s upstream repository.

> 将所有提交的本地更改发送到本地存储库正在跟踪的上游 Git 存储库(例如贡献存储库)。项目的维护者从这些存储库中提取，将更改(提交)合并到项目上游存储库的适当分支中。

- *git merge:* Combines or adds changes from one local branch of your repository with another branch. When you create a local Git repository, the default branch may be named \"main\". A typical workflow is to create a temporary branch that is based off \"main\" that you would use for isolated work. You would make your changes in that isolated branch, stage and commit them locally, switch to the \"main\" branch, and then use the `git merge` command to apply the changes from your isolated branch into the currently checked out branch (e.g. \"main\"). After the merge is complete and if you are done with working in that isolated branch, you can safely delete the isolated branch.

> git 合并：将您存储库中一个本地分支的更改与另一个分支合并或添加。当您创建本地 Git 存储库时，默认分支可能命名为“主”。典型的工作流程是创建一个基于“主”的临时分支，用于隔离工作。您将在该隔离分支中进行更改，在本地阶段和提交，切换到“主”分支，然后使用 `git merge` 命令将来自隔离分支的更改应用到当前检出的分支(例如“主”)。合并完成后，如果您完成了在该隔离分支中的工作，则可以安全删除该隔离分支。

- *git cherry-pick commits:* Choose and apply specific commits from one branch into another branch. There are times when you might not be able to merge all the changes in one branch with another but need to pick out certain ones.

> *Git Cherry-Pick 提交：*从一个分支选择并应用特定的提交到另一个分支。有时候你可能无法将一个分支的所有更改合并到另一个分支，但需要选择某些更改。

- *gitk:* Provides a GUI view of the branches and changes in your local Git repository. This command is a good way to graphically see where things have diverged in your local repository.

> *gitk：*提供本地 Git 存储库中分支和变更的 GUI 视图。该命令是查看本地存储库中的分歧情况的一个很好的图形化方法。

::: note
::: title
Note
:::

You need to install the gitk package on your development system to use this command.
:::

- *git log:* Reports a history of your commits to the repository. This report lists all commits regardless of whether you have pushed them upstream or not.

> git log：报告您提交到存储库的提交历史。此报告列出所有提交，无论您是否已将它们推送到上游。

- *git diff:* Displays line-by-line differences between a local working file and the same file as understood by Git. This command is useful to see what you have changed in any given file.

> *git diff：* 显示本地工作文件与 Git 所理解的同一文件之间的逐行差异。此命令有助于查看您在任何给定文件中所做的更改。

# Licensing

Because open source projects are open to the public, they have different licensing structures in place. License evolution for both Open Source and Free Software has an interesting history. If you are interested in this history, you can find basic information here:

> 因为开源项目对公众开放，它们有不同的许可结构。开源和自由软件的许可证演变有一段有趣的历史。如果您对此历史感兴趣，可以在此处找到基本信息：

- `Open source license history <Open-source_license>`
- `Free software license history <Free_software_license>`

In general, the Yocto Project is broadly licensed under the Massachusetts Institute of Technology (MIT) License. MIT licensing permits the reuse of software within proprietary software as long as the license is distributed with that software. Patches to the Yocto Project follow the upstream licensing scheme. You can find information on the MIT license `here <MIT_License>`.

> 一般来说，Yocto 项目基本上是根据麻省理工学院(MIT)许可证授权的。MIT 许可允许在专有软件中重用软件，只要该许可证与该软件一起分发。对 Yocto 项目的补丁遵循上游许可方案。您可以在此处找到有关 MIT 许可证的信息 <MIT_License>。

When you build an image using the Yocto Project, the build process uses a known list of licenses to ensure compliance. You can find this list in the `Source Directory` at `tmp/deploy/licenses`.

> 当您使用 Yocto 项目构建镜像时，构建过程会使用已知的许可证列表以确保遵守。您可以在 `Source Directory` 中的 `tmp/deploy/licenses` 中保留了构建期间发现和使用的所有许可证列表。

If a module requires a license that is not in the base list, the build process generates a warning during the build. These tools make it easier for a developer to be certain of the licenses with which their shipped products must comply. However, even with these tools it is still up to the developer to resolve potential licensing issues.

> 如果模块需要的许可证不在基础列表中，则在构建过程中会产生警告。这些工具可以使开发人员更容易确定其发布产品必须遵守的许可证。但是，即使使用这些工具，开发人员仍需要解决潜在的许可证问题。

The base list of licenses used by the build process is a combination of the Software Package Data Exchange (SPDX) list and the Open Source Initiative (OSI) projects. [SPDX Group](https://spdx.org) is a working group of the Linux Foundation that maintains a specification for a standard format for communicating the components, licenses, and copyrights associated with a software package. [OSI](https://opensource.org) is a corporation dedicated to the Open Source Definition and the effort for reviewing and approving licenses that conform to the Open Source Definition (OSD).

> 建立过程使用的基础许可证列表是软件包数据交换(SPDX)列表和开源初始(OSI)项目的组合。[SPDX Group](https://spdx.org) 是 Linux 基金会的一个工作组，负责维护一种标准格式，用于传达与软件包相关的组件、许可证和版权信息。[OSI](https://opensource.org) 是一家致力于开源定义和审查和批准符合开源定义(OSD)的许可证的公司。

You can find a list of the combined SPDX and OSI licenses that the Yocto Project uses in the `meta/files/common-licenses` directory in your `Source Directory`.

> 你可以在源代码目录中的 `meta/files/common-licenses` 目录中找到 Yocto Project 使用的 SPDX 和 OSI 许可证的组合列表。

For information that can help you maintain compliance with various open source licensing during the lifecycle of a product created using the Yocto Project, see the \"`dev-manual/licenses:maintaining open source license compliance during your product's lifecycle`\" section in the Yocto Project Development Tasks Manual.

> 要了解有关使用 Yocto 项目创建产品的整个生命周期中如何保持各种开源许可协议的合规性，请参阅 Yocto 项目开发任务手册中的“dev-manual/licenses：在产品生命周期中保持开源许可协议合规”部分。
