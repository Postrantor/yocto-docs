---
tip: translate by baidu@2023-06-07 10:46:35
...
---
title: The Yocto Project Development Environment
------------------------------------------------

This chapter takes a look at the Yocto Project development environment. The chapter provides Yocto Project Development environment concepts that help you understand how work is accomplished in an open source environment, which is very different as compared to work accomplished in a closed, proprietary environment.

> 本章介绍 Yocto 项目的开发环境。本章提供了 Yocto 项目开发环境的概念，帮助您了解如何在开源环境中完成工作，这与在封闭的专有环境中完成的工作非常不同。

Specifically, this chapter addresses open source philosophy, source repositories, workflows, Git, and licensing.

> 具体而言，本章介绍了开源理念、源代码存储库、工作流、Git 和许可。

# Open Source Philosophy

Open source philosophy is characterized by software development directed by peer production and collaboration through an active community of developers. Contrast this to the more standard centralized development models used by commercial software companies where a finite set of developers produces a product for sale using a defined set of procedures that ultimately result in an end product whose architecture and source material are closed to the public.

> 开源哲学的特点是由同行生产和通过活跃的开发人员社区进行协作来指导软件开发。与之形成对比的是，商业软件公司使用的更标准的集中式开发模型，即有限的开发人员使用一组定义的过程来生产产品以供销售，最终产生的最终产品的架构和源材料不对公众开放。

Open source projects conceptually have differing concurrent agendas, approaches, and production. These facets of the development process can come from anyone in the public (community) who has a stake in the software project. The open source environment contains new copyright, licensing, domain, and consumer issues that differ from the more traditional development environment. In an open source environment, the end product, source material, and documentation are all available to the public at no cost.

> 开源项目在概念上有不同的并发议程、方法和生产。开发过程的这些方面可以来自公众（社区）中与软件项目有利害关系的任何人。开源环境包含不同于更传统的开发环境的新的版权、许可、域和消费者问题。在开源环境中，最终产品、源材料和文档都可以免费向公众提供。

A benchmark example of an open source project is the Linux kernel, which was initially conceived and created by Finnish computer science student Linus Torvalds in 1991. Conversely, a good example of a non-open source project is the Windows family of operating systems developed by Microsoft Corporation.

> 开源项目的一个基准示例是 Linux 内核，它最初是由芬兰计算机科学学生 Linus Torvalds 于 1991 年构思和创建的。相反，非开源项目的一个很好的例子是微软公司开发的 Windows 系列操作系统。

Wikipedia has a good `historical description of the Open Source Philosophy <Open_source>`{.interpreted-text role="wikipedia"}. You can also find helpful information on how to participate in the Linux Community [here](https://www.kernel.org/doc/html/latest/process/index.html).

> 维基百科对开源哲学有很好的历史描述。您还可以在这里找到有关如何参与 Linux 社区的有用信息([https://www.kernel.org/doc/html/latest/process/index.html](https://www.kernel.org/doc/html/latest/process/index.html))。

# The Development Host

A development host or `Build Host`{.interpreted-text role="term"} is key to using the Yocto Project. Because the goal of the Yocto Project is to develop images or applications that run on embedded hardware, development of those images and applications generally takes place on a system not intended to run the software \-\-- the development host.

> 开发主机或“构建主机”｛.explored text role=“term”｝是使用 Yocto 项目的关键。由于 Yocto 项目的目标是开发在嵌入式硬件上运行的图像或应用程序，因此这些图像和应用程序的开发通常在不打算运行软件的系统上进行。

You need to set up a development host in order to use it with the Yocto Project. Most find that it is best to have a native Linux machine function as the development host. However, it is possible to use a system that does not run Linux as its operating system as your development host. When you have a Mac or Windows-based system, you can set it up as the development host by using [CROPS](https://github.com/crops/poky-container), which leverages [Docker Containers](https://www.docker.com/). Once you take the steps to set up a CROPS machine, you effectively have access to a shell environment that is similar to what you see when using a Linux-based development host. For the steps needed to set up a system using CROPS, see the \"`dev-manual/start:setting up to use cross platforms (crops)`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 您需要设置一个开发主机，以便将其用于 Yocto 项目。大多数人发现最好让一台本地 Linux 机器作为开发主机。但是，可以使用不运行 Linux 作为操作系统的系统作为开发主机。当你有一个基于 Mac 或 Windows 的系统时，你可以使用[CROPS]将其设置为开发主机([https://github.com/crops/poky-container](https://github.com/crops/poky-container))，利用 [Docker Containers](https://www.docker.com/)。一旦您采取了设置 CROPS 机器的步骤，您就可以有效地访问 shell 环境，该环境与使用基于 Linux 的开发主机时看到的环境类似。有关使用 CROPS 设置系统所需的步骤，请参阅 Yocto 项目开发任务手册中的\“`dev manual/start:设置以使用跨平台（CROPS）`{.depreted text role=“ref”}\”一节。

If your development host is going to be a system that runs a Linux distribution, you must still take steps to prepare the system for use with the Yocto Project. You need to be sure that the Linux distribution on the system is one that supports the Yocto Project. You also need to be sure that the correct set of host packages are installed that allow development using the Yocto Project. For the steps needed to set up a development host that runs Linux, see the \"`dev-manual/start:setting up a native linux host`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 如果您的开发主机将是一个运行 Linux 发行版的系统，那么您仍然必须采取措施准备该系统用于 Yocto 项目。您需要确保系统上的 Linux 发行版支持 Yocto 项目。您还需要确保安装了一组正确的主机包，允许使用 Yocto 项目进行开发。有关设置运行 Linux 的开发主机所需的步骤，请参阅 Yocto Project development Tasks manual 中的\“`dev manual/start:setting a native Linux host`｛.depreted text role=“ref”｝\”一节。

Once your development host is set up to use the Yocto Project, there are several ways of working in the Yocto Project environment:

> 一旦您的开发主机设置为使用 Yocto 项目，在 Yocto Project 环境中有几种工作方式：

- *Command Lines, BitBake, and Shells:* Traditional development in the Yocto Project involves using the `OpenEmbedded Build System`{.interpreted-text role="term"}, which uses BitBake, in a command-line environment from a shell on your development host. You can accomplish this from a host that is a native Linux machine or from a host that has been set up with CROPS. Either way, you create, modify, and build images and applications all within a shell-based environment using components and tools available through your Linux distribution and the Yocto Project.

> -*命令行、BitBake 和外壳：*Yocto 项目中的传统开发涉及在开发主机上的外壳的命令行环境中使用 `OpenEmbedded Build System`｛.explored text role=“term”｝，该系统使用 BitBake。您可以从本机 Linux 机器的主机或使用 CROPS 设置的主机上完成此操作。无论哪种方式，您都可以使用 Linux 发行版和 Yocto 项目中提供的组件和工具，在基于 shell 的环境中创建、修改和构建映像和应用程序。

For a general flow of the build procedures, see the \"`dev-manual/building:building a simple image`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 有关构建过程的一般流程，请参阅 Yocto 项目开发任务手册中的\“`dev manual/building:building a simple image`｛.depredicted text role=“ref”｝\”一节。

- *Board Support Package (BSP) Development:* Development of BSPs involves using the Yocto Project to create and test layers that allow easy development of images and applications targeted for specific hardware. To development BSPs, you need to take some additional steps beyond what was described in setting up a development host.

> -*板支持包（BSP）开发：*BSP 的开发包括使用 Yocto 项目创建和测试层，以便轻松开发针对特定硬件的图像和应用程序。要开发 BSP，除了在设置开发主机时所描述的步骤之外，还需要采取一些额外的步骤。

The `/bsp-guide/index`{.interpreted-text role="doc"} provides BSP-related development information. For specifics on development host preparation, see the \"`bsp-guide/bsp:preparing your build host to work with bsp layers`{.interpreted-text role="ref"}\" section in the Yocto Project Board Support Package (BSP) Developer\'s Guide.

> `nbsp指南/index`｛.explored text role=“doc”｝提供了与 bsp 相关的开发信息。有关开发主机准备的详细信息，请参阅 Yocto 项目委员会支持包（bsp）开发人员指南中的“`nbsp guide/nbsp：准备您的构建主机以使用 nbsp 层”｛.depreted text role=“ref”｝\”部分。

- *Kernel Development:* If you are going to be developing kernels using the Yocto Project you likely will be using `devtool`. A workflow using `devtool` makes kernel development quicker by reducing iteration cycle times.

> -*内核开发：*如果你打算使用 Yocto 项目开发内核，你可能会使用“devtool”。使用“devtool”的工作流通过减少迭代周期来加快内核开发。

The `/kernel-dev/index`{.interpreted-text role="doc"} provides kernel-related development information. For specifics on development host preparation, see the \"`kernel-dev/common:preparing the build host to work on the kernel`{.interpreted-text role="ref"}\" section in the Yocto Project Linux Kernel Development Manual.

> `/kernel dev/index`｛.explored text role=“doc”｝提供了与内核相关的开发信息。有关开发主机准备的详细信息，请参阅 Yocto Project Linux 内核开发手册中的\“`kernel dev/common:preparing the build host to work on the kernel`｛.explored text role=“ref”｝\”一节。

- *Using Toaster:* The other Yocto Project development method that involves an interface that effectively puts the Yocto Project into the background is Toaster. Toaster provides an interface to the OpenEmbedded build system. The interface enables you to configure and run your builds. Information about builds is collected and stored in a database. You can use Toaster to configure and start builds on multiple remote build servers.

> -*使用 Toaster：*Yocto 项目的另一种开发方法是 Toaster，它涉及一个有效地将 Yocto Project 置于后台的接口。Toster 为 OpenEmbedded 构建系统提供了一个接口。该界面使您能够配置和运行构建。有关生成的信息被收集并存储在数据库中。您可以使用 Toast 在多个远程生成服务器上配置和启动生成。

For steps that show you how to set up your development host to use Toaster and on how to use Toaster in general, see the `/toaster-manual/index`{.interpreted-text role="doc"}.

> 有关如何将开发主机设置为使用 Toster 以及如何一般使用 Toster 的步骤，请参阅 `/toother manual/index`{.depredicted text role=“doc”}。

# Yocto Project Source Repositories

The Yocto Project team maintains complete source repositories for all Yocto Project files at :yocto\_[git:%60/](git:%60/)[. This web-based source code browser is organized into categories by function such as IDE Plugins, Matchbox, Poky, Yocto Linux Kernel, and so forth. From the interface, you can click on any particular item in the \"Name\" column and see the URL at the bottom of the page that you need to clone a Git repository for that particular item. Having a local Git repository of the :term:\`Source Directory]{.title-ref}, which is usually named \"poky\", allows you to make changes, contribute to the history, and ultimately enhance the Yocto Project\'s tools, Board Support Packages, and so forth.

> Yocto Project 团队为所有 Yocto 项目文件维护完整的源代码存储库，其位置为：Yocto\_[git:%60/]（git:%60/）[。此基于 web 的源代码浏览器按 IDE 插件、Matchbox、Poky、Yocto Linux 内核等功能分类。在界面中，您可以单击“名称”中的任何特定项目列，并查看页面底部的 URL，您需要为该特定项目克隆 Git 存储库。拥有一个本地 Git 存储库：term:\`Source Directory]｛.title ref｝，通常名为“poky”，可以让您进行更改，为历史做出贡献，并最终增强 Yocto 项目的工具、Board Support Packages 等。

For any supported release of Yocto Project, you can also go to the :yocto_home:[Yocto Project Website \<\>]{.title-ref} and select the \"DOWNLOADS\" item from the \"SOFTWARE\" menu and get a released tarball of the `poky` repository, any supported BSP tarball, or Yocto Project tools. Unpacking these tarballs gives you a snapshot of the released files.

> 对于任何受支持的 Yocto Project 版本，您也可以访问：Yocto_home:[Yocto ProjectWebsite\<\>]｛.title-ref｝并从“软件”菜单中选择“下载”项，然后获取“poky”存储库的已发布 tarball、任何受支持 BSP tarball 或 Yocto 项目工具。打开这些 tarball，您可以获得已发布文件的快照。

::: note
::: title
Note
:::

- The recommended method for setting up the Yocto Project `Source Directory`{.interpreted-text role="term"} and the files for supported BSPs (e.g., `meta-intel`) is to use `overview-manual/development-environment:git`{.interpreted-text role="ref"} to create a local copy of the upstream repositories.

> -设置 Yocto 项目“源目录”｛.depredicted text role=“term”｝和支持的 BSP 的文件（例如，“meta intel”）的建议方法是使用“概述手册/开发环境：git”｛.epredicted textrole=”ref“｝创建上游存储库的本地副本。

- Be sure to always work in matching branches for both the selected BSP repository and the Source Directory (i.e. `poky`) repository. For example, if you have checked out the \"&DISTRO_NAME_NO_CAP;\" branch of `poky` and you are going to use `meta-intel`, be sure to checkout the \"&DISTRO_NAME_NO_CAP;\" branch of `meta-intel`.

> -请确保始终在所选 BSP 存储库和源目录（即“poky”）存储库的匹配分支中工作。例如，如果您已签出“poky”的\“&DISTRO_NAME_NO_CAP；\”分支，并且您将使用“meta intel”，请确保签出“meta-intel”的\。

:::

> ：：：

In summary, here is where you can get the project files needed for development:

> 总之，在这里您可以获得开发所需的项目文件：

- :yocto\_[git:%60Source](git:%60Source) Repositories: \<\>\` This area contains Poky, Yocto documentation, metadata layers, and Linux kernel. You can create local copies of Git repositories for each of these areas.

> -：yocto\_[git:%60Source]（git:%60Source）存储库：\<\>\`此区域包含 Poky、yocto 文档、元数据层和 Linux 内核。您可以为每个区域创建 Git 存储库的本地副本。

![image](figures/source-repos.png){width="100.0%"}

> ！[image]（数字/来源 repos.png）｛width=“100.0%”｝

For steps on how to view and access these upstream Git repositories, see the \"`dev-manual/start:accessing source repositories`{.interpreted-text role="ref"}\" Section in the Yocto Project Development Tasks Manual.

> 有关如何查看和访问这些上游 Git 存储库的步骤，请参阅 Yocto 项目开发任务手册中的\“`dev manual/start:访问源存储库`{.expreted text role=“ref”}\”一节。

- :yocto_dl:[Yocto release archives: \</releases/yocto\>]{.title-ref} This is where you can download tarballs corresponding to each Yocto Project release. Downloading and extracting these files does not produce a local copy of a Git repository but rather a snapshot corresponding to a particular release.

> -：yocto_dl:[yocto 发布档案：\</releases/yocto\>]｛.title-ref｝您可以在这里下载与每个 yocto 项目发布相对应的 tarball。下载和提取这些文件不会生成 Git 存储库的本地副本，而是生成与特定版本相对应的快照。

- :yocto_home:\`DOWNLOADS page \</software-overview/downloads/\>[: The :yocto_home:\`Yocto Project website \<\>]{.title-ref} includes a \"DOWNLOADS\" page accessible through the \"SOFTWARE\" menu that allows you to download any Yocto Project release, tool, and Board Support Package (BSP) in tarball form. The hyperlinks point to the tarballs under :yocto_dl:[/releases/yocto/]{.title-ref}.

> -：yocto_home:\`DOWNLOADS page\</software overview/DOWNLOADS/\>[：yocte_home:\`yocto Project website\<\>]｛.title ref｝包括一个可通过“软件”菜单访问的“下载”页面，该页面允许您以 tarball 形式下载任何 yocto 项目版本、工具和板支持包（BSP）。超链接指向：yocto_dl:[/releases/yocto/]｛.title ref}下的 tarball。

![image](figures/yp-download.png){width="100.0%"}

> ！[image]（数字/yp 下载.png）｛width=“100.0%”｝

For steps on how to use the \"DOWNLOADS\" page, see the \"`dev-manual/start:using the downloads page`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 有关如何使用“下载”页面的步骤，请参阅 Yocto 项目开发任务手册中的“`dev manual/start:using the DOWNLOADS page`｛.depreted text role=“ref”｝”一节。

# Git Workflows and the Yocto Project

Developing using the Yocto Project likely requires the use of `overview-manual/development-environment:git`{.interpreted-text role="ref"}. Git is a free, open source distributed version control system used as part of many collaborative design environments. This section provides workflow concepts using the Yocto Project and Git. In particular, the information covers basic practices that describe roles and actions in a collaborative development environment.

> 使用 Yocto 项目进行开发可能需要使用 `overview manual/development environment:git`｛.explored text role=“ref”｝。Git 是一个免费的、开源的分布式版本控制系统，用作许多协作设计环境的一部分。本节提供了使用 Yocto 项目和 Git 的工作流概念。特别是，该信息涵盖了描述协作开发环境中的角色和行动的基本实践。

::: note
::: title
Note
:::

If you are familiar with this type of development environment, you might not want to read this section.

> 如果您熟悉这种类型的开发环境，您可能不想阅读本节。
> :::

The Yocto Project files are maintained using Git in \"branches\" whose Git histories track every change and whose structures provide branches for all diverging functionality. Although there is no need to use Git, many open source projects do so.

> Yocto 项目文件在“branches”中使用 Git 进行维护，其 Git 历史记录跟踪每一次更改，其结构为所有不同的功能提供分支。尽管没有必要使用 Git，但许多开源项目都是这样做的。

For the Yocto Project, a key individual called the \"maintainer\" is responsible for the integrity of the development branch of a given Git repository. The development branch is the \"upstream\" repository from which final or most recent builds of a project occur. The maintainer is responsible for accepting changes from other developers and for organizing the underlying branch structure to reflect release strategies and so forth.

> 对于 Yocto 项目，一个名为“维护者”的关键人员负责给定 Git 存储库的开发分支的完整性。开发分支是“上游”存储库，项目的最终构建或最新构建都来自该存储库。维护人员负责接受来自其他开发人员的更改，并组织底层分支结构以反映发布策略等等。

::: note
::: title
Note
:::

For information on finding out who is responsible for (maintains) a particular area of code in the Yocto Project, see the \"`dev-manual/changes:submitting a change to the yocto project`{.interpreted-text role="ref"}\" section of the Yocto Project Development Tasks Manual.

> 有关找出谁负责（维护）Yocto 项目中某个特定代码区域的信息，请参阅 Yocto Project Development Tasks manual 的\“`dev manual/changes:submit a change to the Yocto Project`｛.explored text role=“ref”｝\”一节。
> :::

The Yocto Project `poky` Git repository also has an upstream contribution Git repository named `poky-contrib`. You can see all the branches in this repository using the web interface of the :yocto\_[git:%60Source](git:%60Source) Repositories \<\>\` organized within the \"Poky Support\" area. These branches hold changes (commits) to the project that have been submitted or committed by the Yocto Project development team and by community members who contribute to the project. The maintainer determines if the changes are qualified to be moved from the \"contrib\" branches into the \"master\" branch of the Git repository.

> Yocto 项目的“poky”Git 存储库也有一个名为“poky contrib”的上游贡献 Git 存储。您可以使用“Poky Support”区域内组织的：yocto\_[git:%60Source]（git:%60Source）Repositorys\<\>\`的 web 界面查看此存储库中的所有分支。这些分支持有 Yocto 项目开发团队和为项目做出贡献的社区成员提交或提交的项目变更（提交）。维护人员确定更改是否有资格从 Git 存储库的“contrib”分支移动到“master”分支。

Developers (including contributing community members) create and maintain cloned repositories of upstream branches. The cloned repositories are local to their development platforms and are used to develop changes. When a developer is satisfied with a particular feature or change, they \"push\" the change to the appropriate \"contrib\" repository.

> 开发人员（包括社区成员）创建并维护上游分支的克隆存储库。克隆的存储库是其开发平台的本地存储库，用于开发更改。当开发人员对某个特定功能或更改感到满意时，他们会将更改“推送”到相应的“contrib”存储库。

Developers are responsible for keeping their local repository up-to-date with whatever upstream branch they are working against. They are also responsible for straightening out any conflicts that might arise within files that are being worked on simultaneously by more than one person. All this work is done locally on the development host before anything is pushed to a \"contrib\" area and examined at the maintainer\'s level.

> 开发人员负责使他们的本地存储库与他们所针对的任何上游分支保持最新。他们还负责解决多人同时处理的文件中可能出现的任何冲突。所有这些工作都是在开发主机上本地完成的，然后将任何东西推到“contrib”区域并在维护人员级别进行检查。

There is a somewhat formal method by which developers commit changes and push them into the \"contrib\" area and subsequently request that the maintainer include them into an upstream branch. This process is called \"submitting a patch\" or \"submitting a change.\" For information on submitting patches and changes, see the \"`dev-manual/changes:submitting a change to the yocto project`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 有一种形式化的方法，开发人员通过这种方法提交更改并将其推送到“contrib”区域，然后请求维护人员将其包含在上游分支中。此过程称为“提交修补程序”或“提交更改”。有关提交修补程序和更改的信息，请参阅 yocto 项目开发任务手册中的“`dev manual/changes:submit a change to the yocto project`｛.depreted text role=“ref”｝”一节。

In summary, there is a single point of entry for changes into the development branch of the Git repository, which is controlled by the project\'s maintainer. A set of developers independently develop, test, and submit changes to \"contrib\" areas for the maintainer to examine. The maintainer then chooses which changes are going to become a permanent part of the project.

> 总之，Git 存储库的开发分支中有一个单一的变更入口，由项目的维护人员控制。一组开发人员独立开发、测试并提交对“contrib”区域的更改，供维护人员检查。然后，维护人员选择哪些更改将成为项目的永久组成部分。

![image](svg/git-workflow.*){width="100.0%"}

While each development environment is unique, there are some best practices or methods that help development run smoothly. The following list describes some of these practices. For more information about Git workflows, see the workflow topics in the [Git Community Book](https://book.git-scm.com).

> 虽然每个开发环境都是独特的，但有一些最佳实践或方法可以帮助开发顺利运行。以下列表介绍了其中的一些做法。有关 Git 工作流的更多信息，请参阅[Git 社区书]中的工作流主题(https://book.git-scm.com)。

- *Make Small Changes:* It is best to keep the changes you commit small as compared to bundling many disparate changes into a single commit. This practice not only keeps things manageable but also allows the maintainer to more easily include or refuse changes.

> -*进行小的更改：*与将许多不同的更改捆绑到一个提交中相比，最好保持提交的更改较小。这种做法不仅使事情易于管理，而且允许维护人员更容易地包含或拒绝更改。

- *Make Complete Changes:* It is also good practice to leave the repository in a state that allows you to still successfully build your project. In other words, do not commit half of a feature, then add the other half as a separate, later commit. Each commit should take you from one buildable project state to another buildable state.

> -*进行完全更改：*将存储库保持在允许您仍然成功构建项目的状态也是一种良好的做法。换句话说，不要提交一个特性的一半，然后将另一半添加为单独的稍后提交。每次提交都应该将您从一个可构建项目状态带到另一个可生成状态。

- *Use Branches Liberally:* It is very easy to create, use, and delete local branches in your working Git repository on the development host. You can name these branches anything you like. It is helpful to give them names associated with the particular feature or change on which you are working. Once you are done with a feature or change and have merged it into your local development branch, simply discard the temporary branch.

> -*自由使用分支：*在开发主机上的工作 Git 存储库中创建、使用和删除本地分支非常容易。你可以随意命名这些树枝。为它们提供与您正在处理的特定功能或更改相关联的名称是很有帮助的。一旦您完成了一个功能或更改，并将其合并到本地开发分支中，只需放弃临时分支即可。

- *Merge Changes:* The `git merge` command allows you to take the changes from one branch and fold them into another branch. This process is especially helpful when more than a single developer might be working on different parts of the same feature. Merging changes also automatically identifies any collisions or \"conflicts\" that might happen as a result of the same lines of code being altered by two different developers.

> -*合并更改：*“git Merge”命令允许您从一个分支中获取更改并将其折叠到另一个分支。当多个开发人员可能正在处理同一功能的不同部分时，这个过程尤其有用。合并更改还可以自动识别由于两个不同的开发人员更改了同一行代码而可能发生的任何冲突或“冲突”。

- *Manage Branches:* Because branches are easy to use, you should use a system where branches indicate varying levels of code readiness. For example, you can have a \"work\" branch to develop in, a \"test\" branch where the code or change is tested, a \"stage\" branch where changes are ready to be committed, and so forth. As your project develops, you can merge code across the branches to reflect ever-increasing stable states of the development.

> -*管理分支：*因为分支很容易使用，所以应该使用一个分支指示不同级别代码准备的系统。例如，您可以有一个“工作”分支来进行开发，一个“测试”分支来测试代码或更改，还有一个“阶段”分支来提交更改，等等。随着项目的发展，您可以跨分支合并代码，以反映不断增长的稳定开发状态。

- *Use Push and Pull:* The push-pull workflow is based on the concept of developers \"pushing\" local commits to a remote repository, which is usually a contribution repository. This workflow is also based on developers \"pulling\" known states of the project down into their local development repositories. The workflow easily allows you to pull changes submitted by other developers from the upstream repository into your work area ensuring that you have the most recent software on which to develop. The Yocto Project has two scripts named `create-pull-request` and `send-pull-request` that ship with the release to facilitate this workflow. You can find these scripts in the `scripts` folder of the `Source Directory`{.interpreted-text role="term"}. For information on how to use these scripts, see the \"`dev-manual/changes:using scripts to push a change upstream and request a pull`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> -*使用推拉：*推拉工作流基于开发人员“推送”本地提交到远程存储库的概念，远程存储库通常是贡献存储库。这个工作流程也是基于开发人员将项目的已知状态“拉入”到他们的本地开发库中。工作流程使您能够轻松地将其他开发人员提交的更改从上游存储库拉到您的工作区域，确保您拥有最新的软件进行开发。Yocto 项目有两个名为“创建请求”和“发送请求”的脚本，这两个脚本随发布一起提供，以促进此工作流。您可以在“源目录”的“脚本”文件夹中找到这些脚本｛.explored text role=“term”｝。有关如何使用这些脚本的信息，请参阅 Yocto 项目开发任务手册中的“`dev manual/changes:using scripts to push a change upstream and request a pull`｛.depreted text role=“ref”｝”一节。

- *Patch Workflow:* This workflow allows you to notify the maintainer through an email that you have a change (or patch) you would like considered for the development branch of the Git repository. To send this type of change, you format the patch and then send the email using the Git commands `git format-patch` and `git send-email`. For information on how to use these scripts, see the \"`dev-manual/changes:submitting a change to the yocto project`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> -*补丁工作流程：*此工作流程允许您通过电子邮件通知维护人员，您有一个更改（或补丁）需要考虑 Git 存储库的开发分支。要发送这种类型的更改，请格式化补丁，然后使用 Git 命令“Git format patch”和“Git send email”发送电子邮件。有关如何使用这些脚本的信息，请参阅 yocto 项目开发任务手册中的\“`dev manual/changes:submit a change to the yocto project`｛.depreted text role=“ref”｝\”一节。

# Git

The Yocto Project makes extensive use of Git, which is a free, open source distributed version control system. Git supports distributed development, non-linear development, and can handle large projects. It is best that you have some fundamental understanding of how Git tracks projects and how to work with Git if you are going to use the Yocto Project for development. This section provides a quick overview of how Git works and provides you with a summary of some essential Git commands.

> Yocto 项目广泛使用了 Git，这是一个免费的、开源的分布式版本控制系统。Git 支持分布式开发、非线性开发，可以处理大型项目。如果你打算使用 Yocto 项目进行开发，最好对 Git 如何跟踪项目以及如何使用 Git 有一些基本的了解。本节快速概述了 Git 的工作原理，并为您提供了一些基本 Git 命令的摘要。

::: note
::: title
Note
:::

- For more information on Git, see [https://git-scm.com/documentation](https://git-scm.com/documentation).

> -有关 Git 的更多信息，请参阅 [https://git-scm.com/documentation](https://git-scm.com/documentation)。

- If you need to download Git, it is recommended that you add Git to your system through your distribution\'s \"software store\" (e.g. for Ubuntu, use the Ubuntu Software feature). For the Git download page, see [https://git-scm.com/download](https://git-scm.com/download).

> -如果你需要下载 Git，建议你通过发行版的“软件商店”将 Git 添加到你的系统中（例如，对于 Ubuntu，使用 Ubuntu 软件功能）。有关 Git 下载页面，请参阅 [https://git-scm.com/download](https://git-scm.com/download)。

- For information beyond the introductory nature in this section, see the \"`dev-manual/start:locating yocto project source files`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> -有关本节介绍性以外的信息，请参阅 yocto 项目开发任务手册中的\“`dev manual/start:locating yocto项目源文件`{.depreted text role=“ref”}\”一节。

:::

> ：：：

## Repositories, Tags, and Branches

As mentioned briefly in the previous section and also in the \"`overview-manual/development-environment:git workflows and the yocto project`{.interpreted-text role="ref"}\" section, the Yocto Project maintains source repositories at :yocto\_[git:%60/](git:%60/)\`. If you look at this web-interface of the repositories, each item is a separate Git repository.

> 如前一节和“概述手册/开发环境：git 工作流和 yocto 项目”一节所述，yocto 项目维护的源存储库位于：yocto\_[git:%60/]（git:%60/）\`。如果你看看这个存储库的 web 界面，每个项目都是一个单独的 Git 存储库。

Git repositories use branching techniques that track content change (not files) within a project (e.g. a new feature or updated documentation). Creating a tree-like structure based on project divergence allows for excellent historical information over the life of a project. This methodology also allows for an environment from which you can do lots of local experimentation on projects as you develop changes or new features.

> Git 存储库使用分支技术来跟踪项目中的内容更改（而不是文件）（例如，新功能或更新的文档）。基于项目差异创建树状结构可以在项目生命周期中提供优秀的历史信息。这种方法还允许在开发更改或新功能时，在项目上进行大量本地实验。

A Git repository represents all development efforts for a given project. For example, the Git repository `poky` contains all changes and developments for that repository over the course of its entire life. That means that all changes that make up all releases are captured. The repository maintains a complete history of changes.

> Git 存储库代表了给定项目的所有开发工作。例如，Git 存储库“poky”包含该存储库在其整个生命周期中的所有更改和开发。这意味着构成所有发布的所有更改都将被捕获。存储库维护完整的更改历史记录。

You can create a local copy of any repository by \"cloning\" it with the `git clone` command. When you clone a Git repository, you end up with an identical copy of the repository on your development system. Once you have a local copy of a repository, you can take steps to develop locally. For examples on how to clone Git repositories, see the \"`dev-manual/start:locating yocto project source files`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 您可以通过使用“git clone”命令“克隆”任何存储库来创建其本地副本。当你克隆一个 Git 存储库时，你最终会在你的开发系统上得到一个相同的存储库副本。一旦有了存储库的本地副本，就可以采取步骤在本地进行开发。有关如何克隆 Git 存储库的示例，请参阅 yocto 项目开发任务手册中的\“`dev manual/start:locating yocto项目源文件`{.depreted text role=“ref”}\”一节。

It is important to understand that Git tracks content change and not files. Git uses \"branches\" to organize different development efforts. For example, the `poky` repository has several branches that include the current \"&DISTRO_NAME_NO_CAP;\" branch, the \"master\" branch, and many branches for past Yocto Project releases. You can see all the branches by going to :yocto\_[git:%60/poky/](git:%60/poky/)[ and clicking on the ]{.title-ref}[\[\...\]]{.title-ref}\` link beneath the \"Branch\" heading.

> 重要的是要理解 Git 跟踪的是内容更改，而不是文件。Git 使用“分支”来组织不同的开发工作。例如，“poky”存储库有多个分支，包括当前的“&DISTRO_NAME_NO_CAP；”分支、“master”分支，以及过去 Yocto Project 版本的许多分支。您可以通过转到：yocto\_[git:%60/poky/]（git:%60/poky/）[并单击\“Branch\”标题下的]｛.title-ref｝[\[\…\]]｛.title-ref}\`链接来查看所有分支。

Each of these branches represents a specific area of development. The \"master\" branch represents the current or most recent development. All other branches represent offshoots of the \"master\" branch.

> 这些分支中的每一个都代表了一个特定的发展领域。“master”分支代表当前或最近的发展。所有其他分支代表“主”分支的分支。

When you create a local copy of a Git repository, the copy has the same set of branches as the original. This means you can use Git to create a local working area (also called a branch) that tracks a specific development branch from the upstream source Git repository. In other words, you can define your local Git environment to work on any development branch in the repository. To help illustrate, consider the following example Git commands:

> 当您创建 Git 存储库的本地副本时，该副本与原始副本具有相同的分支集。这意味着您可以使用 Git 创建一个本地工作区（也称为分支），用于跟踪上游源 Git 存储库中的特定开发分支。换句话说，您可以定义本地 Git 环境来处理存储库中的任何开发分支。为了便于说明，请考虑以下 Git 命令示例：

```
$ cd ~
$ git clone git://git.yoctoproject.org/poky -b &DISTRO_NAME_NO_CAP;
```

In the previous example after moving to the home directory, the `git clone` command creates a local copy of the upstream `poky` Git repository and checks out a local branch named \"&DISTRO_NAME_NO_CAP;\", which tracks the upstream \"origin/&DISTRO_NAME_NO_CAP;\" branch. Changes you make while in this branch would ultimately affect the upstream \"&DISTRO_NAME_NO_CAP;\" branch of the `poky` repository.

> 在上一个示例中，移动到主目录后，“git clone”命令创建上游“poky”git 存储库的本地副本，并签出名为“&DISTRO_NAME_NO_CAP；”的本地分支，该分支跟踪上游“origin/&DISTR_NAME_NO_CAP！”分支。您在此分支中所做的更改最终会影响“poky”存储库的上游“&DISTRO_NAME_NO_CAP；”分支。

It is important to understand that when you create and checkout a local working branch based on a branch name, your local environment matches the \"tip\" of that particular development branch at the time you created your local branch, which could be different from the files in the \"master\" branch of the upstream repository. In other words, creating and checking out a local branch based on the \"&DISTRO_NAME_NO_CAP;\" branch name is not the same as checking out the \"master\" branch in the repository. Keep reading to see how you create a local snapshot of a Yocto Project Release.

> 重要的是要理解，当您根据分支名称创建和签出本地工作分支时，您的本地环境与您创建本地分支时特定开发分支的“提示”相匹配，这可能与上游存储库的“主”分支中的文件不同。换句话说，基于“&DISTRO_NAME_NO_CAP；”分支名称创建和签出本地分支与签出存储库中的“master”分支不同。继续阅读，了解如何创建 Yocto 项目发布的本地快照。

Git uses \"tags\" to mark specific changes in a repository branch structure. Typically, a tag is used to mark a special point such as the final change (or commit) before a project is released. You can see the tags used with the `poky` Git repository by going to :yocto\_[git:%60/poky/](git:%60/poky/)[ and clicking on the ]{.title-ref}[\[\...\]]{.title-ref}\` link beneath the \"Tag\" heading.

> Git 使用“标记”来标记存储库分支结构中的特定更改。通常，标记用于标记一个特殊点，例如项目发布前的最终更改（或提交）。您可以通过转到：yocto\_[Git:%60/poky/]（Git:%60/poky/）[并单击\“Tag\”标题下的]｛.title-ref｝[\[\…\]]｛.title-ref}\`链接来查看与“poky”Git 存储库一起使用的标记。

Some key tags for the `poky` repository are `jethro-14.0.3`, `morty-16.0.1`, `pyro-17.0.0`, and `&DISTRO_NAME_NO_CAP;-&DISTRO;`. These tags represent Yocto Project releases.

> “poky”存储库的一些关键标签是“jethro-14.0.3”、“morty-16.0.1”、“pyro-17.0.0”和“&DISTRO_NAME_NO_CAP-&发行版；`。这些标记表示 Yocto 项目的发布。

When you create a local copy of the Git repository, you also have access to all the tags in the upstream repository. Similar to branches, you can create and checkout a local working Git branch based on a tag name. When you do this, you get a snapshot of the Git repository that reflects the state of the files when the change was made associated with that tag. The most common use is to checkout a working branch that matches a specific Yocto Project release. Here is an example:

> 当您创建 Git 存储库的本地副本时，您还可以访问上游存储库中的所有标签。与分支类似，您可以基于标记名称创建和签出本地工作的 Git 分支。当您这样做时，您将获得 Git 存储库的快照，该快照反映了与该标记相关联的更改时文件的状态。最常见的用途是签出与特定 Yocto 项目版本相匹配的工作分支。以下是一个示例：

```
$ cd ~
$ git clone git://git.yoctoproject.org/poky
$ cd poky
$ git fetch --tags
$ git checkout tags/rocko-18.0.0 -b my_rocko-18.0.0
```

In this example, the name of the top-level directory of your local Yocto Project repository is `poky`. After moving to the `poky` directory, the `git fetch` command makes all the upstream tags available locally in your repository. Finally, the `git checkout` command creates and checks out a branch named \"my-rocko-18.0.0\" that is based on the upstream branch whose \"HEAD\" matches the commit in the repository associated with the \"rocko-18.0.0\" tag. The files in your repository now exactly match that particular Yocto Project release as it is tagged in the upstream Git repository. It is important to understand that when you create and checkout a local working branch based on a tag, your environment matches a specific point in time and not the entire development branch (i.e. from the \"tip\" of the branch backwards).

> 在本例中，本地 Yocto 项目存储库的顶级目录的名称为“poky”。移动到“poky”目录后，“git fetch”命令会使存储库中的所有上游标记在本地可用。最后，“git-checkout”命令创建并签出一个名为“my-rocko-18.0.0\”的分支，该分支基于上游分支，该上游分支的“HEAD\”与“rocko-18.0\”标记关联的存储库中的提交相匹配。您的存储库中的文件现在与特定的 Yocto Project 版本完全匹配，因为它在上游 Git 存储库中被标记。重要的是要理解，当您基于标记创建和签出本地工作分支时，您的环境匹配特定的时间点，而不是整个开发分支（即从分支的“提示”向后）。

## Basic Commands

Git has an extensive set of commands that lets you manage changes and perform collaboration over the life of a project. Conveniently though, you can manage with a small set of basic operations and workflows once you understand the basic philosophy behind Git. You do not have to be an expert in Git to be functional. A good place to look for instruction on a minimal set of Git commands is [here](https://git-scm.com/documentation).

> Git 有一套广泛的命令，可以让您在项目的整个生命周期中管理更改和执行协作。不过，一旦您了解了 Git 背后的基本原理，您就可以方便地使用一小部分基本操作和工作流进行管理。你不必是 Git 方面的专家就能发挥作用。一个很好的地方来寻找关于最小 Git 命令集的指令是[这里](https://git-scm.com/documentation)。

The following list of Git commands briefly describes some basic Git operations as a way to get started. As with any set of commands, this list (in most cases) simply shows the base command and omits the many arguments it supports. See the Git documentation for complete descriptions and strategies on how to use these commands:

> 下面的 Git 命令列表简要介绍了一些基本的 Git 操作，作为入门的一种方式。与任何一组命令一样，此列表（在大多数情况下）只显示基本命令，并省略了它支持的许多参数。有关如何使用这些命令的完整描述和策略，请参阅 Git 文档：

- *git init:* Initializes an empty Git repository. You cannot use Git commands unless you have a `.git` repository.

> -*git-init:*初始化一个空的 git 存储库。除非有“.Git”存储库，否则无法使用 Git 命令。

- *git clone:* Creates a local clone of a Git repository that is on equal footing with a fellow developer\'s Git repository or an upstream repository.

> -*git clone:*创建一个 git 存储库的本地克隆，该存储库与其他开发人员的 git 存储池或上游存储库处于同等地位。

- *git add:* Locally stages updated file contents to the index that Git uses to track changes. You must stage all files that have changed before you can commit them.

> -*git add:*本地将更新后的文件内容暂存到 git 用来跟踪更改的索引中。必须先暂存所有已更改的文件，然后才能提交它们。

- *git commit:* Creates a local \"commit\" that documents the changes you made. Only changes that have been staged can be committed. Commits are used for historical purposes, for determining if a maintainer of a project will allow the change, and for ultimately pushing the change from your local Git repository into the project\'s upstream repository.

> -*git-commit:*创建一个本地“提交”，记录您所做的更改。只有已暂存的更改才能提交。提交用于历史目的，用于确定项目的维护者是否允许更改，并最终将更改从本地 Git 存储库推送到项目的上游存储库。

- *git status:* Reports any modified files that possibly need to be staged and gives you a status of where you stand regarding local commits as compared to the upstream repository.

> -*git status：*报告任何可能需要暂存的修改文件，并向您提供与上游存储库相比本地提交的状态。

- *git checkout branch-name:* Changes your local working branch and in this form assumes the local branch already exists. This command is analogous to \"cd\".

> -*git checkout 分支名称：*更改本地工作分支，并在此表单中假定本地分支已经存在。这个命令类似于“cd”。

- *git checkout -b working-branch upstream-branch:* Creates and checks out a working branch on your local machine. The local branch tracks the upstream branch. You can use your local branch to isolate your work. It is a good idea to use local branches when adding specific features or changes. Using isolated branches facilitates easy removal of changes if they do not work out.

> -*git checkout-b 工作分支上游分支：*在本地计算机上创建并签出一个工作分支。本地分支跟踪上游分支。您可以使用本地分支机构隔离您的工作。在添加特定功能或更改时，最好使用局部分支。如果更改不起作用，使用孤立的分支可以方便地删除更改。

- *git branch:* Displays the existing local branches associated with your local repository. The branch that you have currently checked out is noted with an asterisk character.

> -*git branch:*显示与本地存储库关联的现有本地分支。您当前已签出的分支将用星号标记。

- *git branch -D branch-name:* Deletes an existing local branch. You need to be in a local branch other than the one you are deleting in order to delete branch-name.

> -*git branch-D 分支名称：*删除一个现有的本地分支。要删除分支名称，您需要位于要删除的分支之外的本地分支中。

- *git pull \--rebase*: Retrieves information from an upstream Git repository and places it in your local Git repository. You use this command to make sure you are synchronized with the repository from which you are basing changes (e.g. the \"&DISTRO_NAME_NO_CAP;\" branch). The `--rebase` option ensures that any local commits you have in your branch are preserved at the top of your local branch.

> -*git pull \--rebase*：从上游 git 存储库中检索信息，并将其放置在本地 git 存储中。您可以使用此命令来确保与基于其进行更改的存储库（例如，\“&DISTRO_NAME_NO_CAP；\”分支）同步。“--rebase”选项确保分支中的任何本地提交都保留在本地分支的顶部。

- *git push repo-name local-branch:upstream-branch:* Sends all your committed local changes to the upstream Git repository that your local repository is tracking (e.g. a contribution repository). The maintainer of the project draws from these repositories to merge changes (commits) into the appropriate branch of project\'s upstream repository.

> -*git push repo name local branch:upstream branch:*将您提交的所有本地更改发送到您的本地存储库正在跟踪的上游 git 存储库（例如贡献存储库）。项目的维护者从这些存储库中提取，将更改（提交）合并到项目上游存储库的适当分支中。

- *git merge:* Combines or adds changes from one local branch of your repository with another branch. When you create a local Git repository, the default branch may be named \"main\". A typical workflow is to create a temporary branch that is based off \"main\" that you would use for isolated work. You would make your changes in that isolated branch, stage and commit them locally, switch to the \"main\" branch, and then use the `git merge` command to apply the changes from your isolated branch into the currently checked out branch (e.g. \"main\"). After the merge is complete and if you are done with working in that isolated branch, you can safely delete the isolated branch.

> -*gitmerge:*将存储库的一个本地分支与另一个分支的更改组合或添加。当您创建本地 Git 存储库时，默认分支可能命名为\“main\”。典型的工作流程是创建一个基于“main”的临时分支，用于隔离工作。您可以在该隔离分支中进行更改，在本地暂存并提交，切换到“main”分支，然后使用“git merge”命令将来自隔离分支的更改应用到当前签出的分支（例如“main”）。合并完成后，如果您已完成对该隔离分支的操作，则可以安全地删除该隔离分支。

- *git cherry-pick commits:* Choose and apply specific commits from one branch into another branch. There are times when you might not be able to merge all the changes in one branch with another but need to pick out certain ones.

> -*gitcherry-pick-commit:*选择特定的提交并将其从一个分支应用到另一个分支。有时，您可能无法将一个分支中的所有更改与另一个分支合并，但需要挑选某些更改。

- *gitk:* Provides a GUI view of the branches and changes in your local Git repository. This command is a good way to graphically see where things have diverged in your local repository.

> -*gitk:*提供本地 Git 存储库中分支和更改的 GUI 视图。这个命令是一个很好的方法，可以图形化地查看本地存储库中的分歧。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

You need to install the gitk package on your development system to use this command.

> 您需要在开发系统上安装 gitk 包才能使用此命令。

:::

> ：：：

- *git log:* Reports a history of your commits to the repository. This report lists all commits regardless of whether you have pushed them upstream or not.

> -*git log:*向存储库报告提交的历史记录。此报告列出了所有提交，无论您是否已将它们向上游推送。

- *git diff:* Displays line-by-line differences between a local working file and the same file as understood by Git. This command is useful to see what you have changed in any given file.

> -*git-diff:*显示本地工作文件和 git 理解的同一文件之间的逐行差异。此命令可用于查看您在任何给定文件中所做的更改。

# Licensing

Because open source projects are open to the public, they have different licensing structures in place. License evolution for both Open Source and Free Software has an interesting history. If you are interested in this history, you can find basic information here:

> 因为开源项目是向公众开放的，所以它们有不同的许可结构。开源软件和自由软件的许可证演变有着有趣的历史。如果你对这段历史感兴趣，你可以在这里找到基本信息：

- `Open source license history <Open-source_license>`{.interpreted-text role="wikipedia"}
- `Free software license history <Free_software_license>`{.interpreted-text role="wikipedia"}

In general, the Yocto Project is broadly licensed under the Massachusetts Institute of Technology (MIT) License. MIT licensing permits the reuse of software within proprietary software as long as the license is distributed with that software. Patches to the Yocto Project follow the upstream licensing scheme. You can find information on the MIT license `here <MIT_License>`{.interpreted-text role="wikipedia"}.

> 一般来说，Yocto 项目是根据麻省理工学院（MIT）许可证获得广泛许可的。麻省理工学院许可证允许在专有软件中重复使用软件，只要许可证与该软件一起分发。Yocto 项目的补丁遵循上游许可方案。您可以在此处找到有关 MIT 许可证的信息 `<MIT_license>`{.depreted text role=“wikipedia”}。

When you build an image using the Yocto Project, the build process uses a known list of licenses to ensure compliance. You can find this list in the `Source Directory`{.interpreted-text role="term"} at `meta/files/common-licenses`. Once the build completes, the list of all licenses found and used during that build are kept in the `Build Directory`{.interpreted-text role="term"} at `tmp/deploy/licenses`.

> 使用 Yocto 项目构建图像时，构建过程会使用已知的许可证列表来确保合规性。您可以在 `meta/files/common license` 的 `Source Directory`｛.depreted text role=“term”｝中找到此列表。生成完成后，在该生成过程中找到并使用的所有许可证的列表将保存在 `tmp/deploy/license` 的 `build Directory`｛.respered text role=“term”｝中。

If a module requires a license that is not in the base list, the build process generates a warning during the build. These tools make it easier for a developer to be certain of the licenses with which their shipped products must comply. However, even with these tools it is still up to the developer to resolve potential licensing issues.

> 如果模块需要的许可证不在基本列表中，则生成过程会在生成过程中生成警告。这些工具使开发人员更容易确定其附带产品必须遵守的许可证。然而，即使有了这些工具，解决潜在的许可问题仍然取决于开发人员。

The base list of licenses used by the build process is a combination of the Software Package Data Exchange (SPDX) list and the Open Source Initiative (OSI) projects. [SPDX Group](https://spdx.org) is a working group of the Linux Foundation that maintains a specification for a standard format for communicating the components, licenses, and copyrights associated with a software package. [OSI](https://opensource.org) is a corporation dedicated to the Open Source Definition and the effort for reviewing and approving licenses that conform to the Open Source Definition (OSD).

> 构建过程使用的许可证基本列表是软件包数据交换（SPDX）列表和开放源代码计划（OSI）项目的组合。SPDX 集团(https://spdx.org)是 Linux 基金会的一个工作组，负责维护与软件包相关的组件、许可证和版权通信的标准格式规范。[操作系统集成](https://opensource.org)是一家致力于开源定义以及审查和批准符合开源定义（OSD）的许可证的公司。

You can find a list of the combined SPDX and OSI licenses that the Yocto Project uses in the `meta/files/common-licenses` directory in your `Source Directory`{.interpreted-text role="term"}.

> 您可以在您的“源目录”中的“meta/files/common licenses”目录中找到 Yocto 项目使用的 SPDX 和 OSI 许可证的组合列表｛.depreted text role=“term”｝。

For information that can help you maintain compliance with various open source licensing during the lifecycle of a product created using the Yocto Project, see the \"`dev-manual/licenses:maintaining open source license compliance during your product's lifecycle`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 有关可以帮助您在使用 Yocto 项目创建的产品的生命周期内遵守各种开源许可的信息，请参阅 Yocto Project Development Tasks manual 中的\“`dev manual/licenses:在您的产品生命周期内保持开源许可的合规性`{.depreted text role=“ref”}\”一节。
