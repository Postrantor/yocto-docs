---
tip: translate by baidu@2023-06-07 10:57:19
...
---
title: Advanced Kernel Concepts
-------------------------------

# Yocto Project Kernel Development and Maintenance

Kernels available through the Yocto Project (Yocto Linux kernels), like other kernels, are based off the Linux kernel releases from [https://www.kernel.org](https://www.kernel.org). At the beginning of a major Linux kernel development cycle, the Yocto Project team chooses a Linux kernel based on factors such as release timing, the anticipated release timing of final upstream `kernel.org` versions, and Yocto Project feature requirements. Typically, the Linux kernel chosen is in the final stages of development by the Linux community. In other words, the Linux kernel is in the release candidate or \"rc\" phase and has yet to reach final release. But, by being in the final stages of external development, the team knows that the `kernel.org` final release will clearly be within the early stages of the Yocto Project development window.

> Yocto 项目提供的内核（Yocto Linux 内核）与其他内核一样，基于 [https://www.kernel.org](https://www.kernel.org)。在一个主要的 Linux 内核开发周期开始时，Yocto 项目团队根据发布时间、最终上游“kernel.org”版本的预期发布时间和 Yocto Project 功能要求等因素选择 Linux 内核。通常，所选择的 Linux 内核正处于 Linux 社区开发的最后阶段。换句话说，Linux 内核正处于候选版本或“rc”阶段，尚未达到最终版本。但是，由于处于外部开发的最后阶段，团队知道“kernel.org”的最终版本显然将处于 Yocto 项目开发窗口的早期阶段。

This balance allows the Yocto Project team to deliver the most up-to-date Yocto Linux kernel possible, while still ensuring that the team has a stable official release for the baseline Linux kernel version.

> 这种平衡使 Yocto 项目团队能够提供最新的 Yocto Linux 内核，同时仍然确保团队有一个稳定的基线 Linux 内核版本的正式发布。

As implied earlier, the ultimate source for Yocto Linux kernels are released kernels from `kernel.org`. In addition to a foundational kernel from `kernel.org`, the available Yocto Linux kernels contain a mix of important new mainline developments, non-mainline developments (when no alternative exists), Board Support Package (BSP) developments, and custom features. These additions result in a commercially released Yocto Project Linux kernel that caters to specific embedded designer needs for targeted hardware.

> 正如前面所暗示的，Yocto Linux 内核的最终来源是“kernel.org”发布的内核。除了“kernel.org/”的基础内核外，可用的 Yocto Linux 内核还包含重要的新主线开发、非主线开发（当没有替代方案时）、板支持包（BSP）开发和自定义功能。这些添加产生了一个商业发布的 Yocto Project Linux 内核，该内核满足了嵌入式设计师对目标硬件的特定需求。

You can find a web interface to the Yocto Linux kernels in the `overview-manual/development-environment:yocto project source repositories`{.interpreted-text role="ref"} at :yocto\_[git:%60/](git:%60/)\`. If you look at the interface, you will see to the left a grouping of Git repositories titled \"Yocto Linux Kernel\". Within this group, you will find several Linux Yocto kernels developed and included with Yocto Project releases:

> 您可以在 `overview manual/development environment:Yocto project source repositories`｛.depreted text role=“ref”｝中找到 Yocto Linux 内核的 web 界面，网址为：Yocto\_[git:%60/](git:%60/)\`。如果你看一下界面，你会看到左边一组名为“Yocto Linux Kernel”的 Git 存储库。在这个小组中，您将发现 Yocto 项目发行版中开发并包含的几个 Linux Yocto 内核：

- *linux-yocto-4.1:* The stable Yocto Project kernel to use with the Yocto Project Release 2.0. This kernel is based on the Linux 4.1 released kernel.

> -*linux-octo-4.1:*与 yocto Project Release 2.0 一起使用的稳定 yocto 项目内核。该内核基于 Linux 4.1 发布的内核。

- *linux-yocto-4.4:* The stable Yocto Project kernel to use with the Yocto Project Release 2.1. This kernel is based on the Linux 4.4 released kernel.

> -*linux-octo-4.4:*与 yocto Project Release 2.1 一起使用的稳定 yocto 项目内核。该内核基于 Linux 4.4 发布的内核。

- *linux-yocto-4.6:* A temporary kernel that is not tied to any Yocto Project release.
- *linux-yocto-4.8:* The stable yocto Project kernel to use with the Yocto Project Release 2.2.
- *linux-yocto-4.9:* The stable Yocto Project kernel to use with the Yocto Project Release 2.3. This kernel is based on the Linux 4.9 released kernel.

> -*linux-octo-4.9:*与 yocto Project Release 2.3 一起使用的稳定 yocto 项目内核。该内核基于 Linux 4.9 发布的内核。

- *linux-yocto-4.10:* The default stable Yocto Project kernel to use with the Yocto Project Release 2.3. This kernel is based on the Linux 4.10 released kernel.

> -*linux-octo-4.10:*与 yocto Project Release 2.3 一起使用的默认稳定 yocto 项目内核。该内核基于 Linux 4.10 发布的内核。

- *linux-yocto-4.12:* The default stable Yocto Project kernel to use with the Yocto Project Release 2.4. This kernel is based on the Linux 4.12 released kernel.

> -*linux-octo-4.12:*与 yocto Project Release 2.4 一起使用的默认稳定 yocto 项目内核。该内核基于 Linux 4.12 发布的内核。

- *yocto-kernel-cache:* The `linux-yocto-cache` contains patches and configurations for the linux-yocto kernel tree. This repository is useful when working on the linux-yocto kernel. For more information on this \"Advanced Kernel Metadata\", see the \"`/kernel-dev/advanced`{.interpreted-text role="doc"}\" Chapter.

> -*yocto 内核缓存：*“linux yocto 缓存”包含 linux yocto-内核树的补丁和配置。在 linux yocto 内核上工作时，这个存储库非常有用。有关“高级内核元数据”的更多信息，请参阅“`/Kernel dev/Advanced`｛.depreted text role=“doc”｝”一章。

- *linux-yocto-dev:* A development kernel based on the latest upstream release candidate available.

> -*linux-yocto-dev:*基于最新上游候选版本的开发内核。

::: note
::: title
Note
:::

Long Term Support Initiative (LTSI) for Yocto Linux kernels is as follows:

> Yocto Linux 内核的长期支持计划（LTSI）如下：

- For Yocto Project releases 1.7, 1.8, and 2.0, the LTSI kernel is `linux-yocto-3.14`.
- For Yocto Project releases 2.1, 2.2, and 2.3, the LTSI kernel is `linux-yocto-4.1`.
- For Yocto Project release 2.4, the LTSI kernel is `linux-yocto-4.9`
- `linux-yocto-4.4` is an LTS kernel.
  :::

Once a Yocto Linux kernel is officially released, the Yocto Project team goes into their next development cycle, or upward revision (uprev) cycle, while still continuing maintenance on the released kernel. It is important to note that the most sustainable and stable way to include feature development upstream is through a kernel uprev process. Back-porting hundreds of individual fixes and minor features from various kernel versions is not sustainable and can easily compromise quality.

> 一旦 Yocto Linux 内核正式发布，Yocto 项目团队将进入下一个开发周期，即向上修订（uprev）周期，同时继续维护已发布的内核。需要注意的是，包括上游功能开发的最可持续和稳定的方式是通过内核升级过程。从各种内核版本反向移植数百个单独的修复程序和次要功能是不可持续的，很容易影响质量。

During the uprev cycle, the Yocto Project team uses an ongoing analysis of Linux kernel development, BSP support, and release timing to select the best possible `kernel.org` Linux kernel version on which to base subsequent Yocto Linux kernel development. The team continually monitors Linux community kernel development to look for significant features of interest. The team does consider back-porting large features if they have a significant advantage. User or community demand can also trigger a back-port or creation of new functionality in the Yocto Project baseline kernel during the uprev cycle.

> 在 uprev 周期中，Yocto 项目团队使用对 Linux 内核开发、BSP 支持和发布时间的持续分析，选择尽可能好的“kernel.org”Linux 内核版本，作为后续 Yocto Linux 内核开发的基础。该团队持续监控 Linux 社区内核开发，以寻找感兴趣的重要功能。如果大型功能具有显著优势，团队确实会考虑对其进行反向移植。用户或社区需求也可以在 uprev 周期期间触发 Yocto 项目基线内核中的回端口或新功能的创建。

Generally speaking, every new Linux kernel both adds features and introduces new bugs. These consequences are the basic properties of upstream Linux kernel development and are managed by the Yocto Project team\'s Yocto Linux kernel development strategy. It is the Yocto Project team\'s policy to not back-port minor features to the released Yocto Linux kernel. They only consider back-porting significant technological jumps \-\-- and, that is done after a complete gap analysis. The reason for this policy is that back-porting any small to medium sized change from an evolving Linux kernel can easily create mismatches, incompatibilities and very subtle errors.

> 一般来说，每一个新的 Linux 内核都会增加功能并引入新的错误。这些后果是上游 Linux 内核开发的基本属性，由 Yocto 项目团队的 Yocto Linux 内核开发策略管理。Yocto 项目团队的政策是不将次要功能移植到已发布的 Yocto Linux 内核。他们只考虑反向移植重大的技术跳跃，这是在进行完整的差距分析后完成的。这种策略的原因是，从一个不断发展的 Linux 内核反向移植任何中小型更改都很容易造成不匹配、不兼容和非常微妙的错误。

The policies described in this section result in both a stable and a cutting edge Yocto Linux kernel that mixes forward ports of existing Linux kernel features and significant and critical new functionality. Forward porting Linux kernel functionality into the Yocto Linux kernels available through the Yocto Project can be thought of as a \"micro uprev\". The many \"micro uprevs\" produce a Yocto Linux kernel version with a mix of important new mainline, non-mainline, BSP developments and feature integrations. This Yocto Linux kernel gives insight into new features and allows focused amounts of testing to be done on the kernel, which prevents surprises when selecting the next major uprev. The quality of these cutting edge Yocto Linux kernels is evolving and the kernels are used in leading edge feature and BSP development.

> 本节中描述的策略产生了一个稳定且前沿的 Yocto Linux 内核，该内核混合了现有 Linux 内核功能的前向端口和重要的新功能。通过 Yocto 项目将 Linux 内核功能向前移植到 Yocto Linux 内核中可以被认为是一种“微型升级”。许多“微升级”产生了 Yocto Linux 内核版本，其中混合了重要的新主线、非主线、BSP 开发和功能集成。这个 Yocto Linux 内核深入了解了新功能，并允许在内核上进行大量集中的测试，这可以防止在选择下一个主要的 uprev 时出现意外。这些前沿 Yocto Linux 内核的质量正在不断发展，这些内核用于前沿功能和 BSP 开发。

# Yocto Linux Kernel Architecture and Branching Strategies

As mentioned earlier, a key goal of the Yocto Project is to present the developer with a kernel that has a clear and continuous history that is visible to the user. The architecture and mechanisms, in particular the branching strategies, used achieve that goal in a manner similar to upstream Linux kernel development in `kernel.org`.

> 如前所述，Yocto 项目的一个关键目标是为开发人员提供一个对用户可见的具有清晰连续历史的内核。使用的体系结构和机制，特别是分支策略，以类似于“kernel.org”中的上游 Linux 内核开发的方式实现了这一目标。

You can think of a Yocto Linux kernel as consisting of a baseline Linux kernel with added features logically structured on top of the baseline. The features are tagged and organized by way of a branching strategy implemented by the Yocto Project team using the Source Code Manager (SCM) Git.

> 您可以将 Yocto Linux 内核看作是由一个基线 Linux 内核组成的，该内核在基线之上添加了逻辑结构的功能。Yocto 项目团队使用源代码管理器（SCM）Git 通过分支策略对这些功能进行标记和组织。

::: note
::: title
Note
:::

- Git is the obvious SCM for meeting the Yocto Linux kernel organizational and structural goals described in this section. Not only is Git the SCM for Linux kernel development in `kernel.org` but, Git continues to grow in popularity and supports many different work flows, front-ends and management techniques.

> -Git 显然是实现 YoctoLinux 内核组织和结构目标的 SCM，如本节所述。Git 不仅是“kernel.org”中用于 Linux 内核开发的 SCM，而且 Git 继续流行，并支持许多不同的工作流程、前端和管理技术。

- You can find documentation on Git at [https://git-scm.com/doc](https://git-scm.com/doc). You can also get an introduction to Git as it applies to the Yocto Project in the \"`overview-manual/development-environment:git`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual. The latter reference provides an overview of Git and presents a minimal set of Git commands that allows you to be functional using Git. You can use as much, or as little, of what Git has to offer to accomplish what you need for your project. You do not have to be a \"Git Expert\" in order to use it with the Yocto Project.

> -你可以在 Git 上找到文档 [https://git-scm.com/doc](https://git-scm.com/doc)。您还可以在 Yocto 项目概述和概念手册中的\“`overview manual/development environment:Git`｛.depreted text role=”ref“｝\”部分了解 Git 应用于 Yocto 的简介。后一个参考提供了 Git 的概述，并提供了一组最小的 Git 命令，允许您使用 Git 发挥功能。你可以使用 Git 所提供的尽可能多或尽可能少的东西来完成你的项目所需的东西。您不必是“Git 专家”就可以将其用于 Yocto 项目。
> :::

Using Git\'s tagging and branching features, the Yocto Project team creates kernel branches at points where functionality is no longer shared and thus, needs to be isolated. For example, board-specific incompatibilities would require different functionality and would require a branch to separate the features. Likewise, for specific kernel features, the same branching strategy is used.

> Yocto 项目团队使用 Git 的标记和分支功能，在功能不再共享的地方创建内核分支，因此需要隔离。例如，特定于板的不兼容性将需要不同的功能，并需要一个分支来分离这些功能。同样，对于特定的内核特性，使用相同的分支策略。

This \"tree-like\" architecture results in a structure that has features organized to be specific for particular functionality, single kernel types, or a subset of kernel types. Thus, the user has the ability to see the added features and the commits that make up those features. In addition to being able to see added features, the user can also view the history of what made up the baseline Linux kernel.

> 这种“树状”架构产生了一种结构，其特征被组织为特定于特定功能、单个内核类型或内核类型的子集。因此，用户能够看到添加的功能以及组成这些功能的提交。除了能够看到添加的功能外，用户还可以查看构成基准 Linux 内核的历史。

Another consequence of this strategy results in not having to store the same feature twice internally in the tree. Rather, the kernel team stores the unique differences required to apply the feature onto the kernel type in question.

> 该策略的另一个结果是不必在树中内部存储两次相同的特性。相反，内核团队存储将特性应用到所讨论的内核类型所需的独特差异。

::: note
::: title
Note
:::

The Yocto Project team strives to place features in the tree such that features can be shared by all boards and kernel types where possible. However, during development cycles or when large features are merged, the team cannot always follow this practice. In those cases, the team uses isolated branches to merge features.

> Yocto 项目团队努力将特性放置在树中，以便在可能的情况下，所有板和内核类型都可以共享特性。然而，在开发周期或合并大型功能时，团队不能总是遵循这种做法。在这些情况下，团队使用独立的分支来合并特征。
> :::

BSP-specific code additions are handled in a similar manner to kernel-specific additions. Some BSPs only make sense given certain kernel types. So, for these types, the team creates branches off the end of that kernel type for all of the BSPs that are supported on that kernel type. From the perspective of the tools that create the BSP branch, the BSP is really no different than a feature. Consequently, the same branching strategy applies to BSPs as it does to kernel features. So again, rather than store the BSP twice, the team only stores the unique differences for the BSP across the supported multiple kernels.

> BSP 特定的代码添加以与内核特定的添加类似的方式处理。某些 BSP 仅在给定某些内核类型的情况下才有意义。因此，对于这些类型，团队在该内核类型的末尾为该内核类型上支持的所有 BSP 创建分支。从创建 BSP 分支的工具的角度来看，BSP 实际上与一个功能没有什么不同。因此，相同的分支策略适用于 BSP，就像它适用于内核特性一样。因此，团队不再存储 BSP 两次，而是只存储 BSP 在支持的多个内核之间的唯一差异。

While this strategy can result in a tree with a significant number of branches, it is important to realize that from the developer\'s point of view, there is a linear path that travels from the baseline `kernel.org`, through a select group of features and ends with their BSP-specific commits. In other words, the divisions of the kernel are transparent and are not relevant to the developer on a day-to-day basis. From the developer\'s perspective, this path is the development branch. The developer does not need to be aware of the existence of any other branches at all. Of course, it can make sense to have these branches in the tree, should a person decide to explore them. For example, a comparison between two BSPs at either the commit level or at the line-by-line code `diff` level is now a trivial operation.

> 虽然这种策略可以产生一个具有大量分支的树，但重要的是要认识到，从开发人员的角度来看，有一条从基线“kernel.org”开始，经过一组选定的功能，并以其特定于 BSP 的提交结束的线性路径。换句话说，内核的划分是透明的，与开发人员的日常工作无关。从开发人员的角度来看，这条路就是开发分支。开发人员根本不需要知道任何其他分支的存在。当然，如果一个人决定探索这些树枝，那么在树上有这些树枝是有意义的。例如，在提交级别或逐行代码“diff”级别的两个 BSP 之间的比较现在是一个微不足道的操作。

The following illustration shows the conceptual Yocto Linux kernel.

> 下图显示了 Yocto Linux 内核的概念。

![image](figures/kernel-architecture-overview.png){width="100.0%"}

In the illustration, the \"Kernel.org Branch Point\" marks the specific spot (or Linux kernel release) from which the Yocto Linux kernel is created. From this point forward in the tree, features and differences are organized and tagged.

> 在图中，“Kernel.org 分支点”标记了创建 Yocto Linux 内核的特定位置（或 Linux 内核版本）。从这一点开始，在树中，对特征和差异进行组织和标记。

The \"Yocto Project Baseline Kernel\" contains functionality that is common to every kernel type and BSP that is organized further along in the tree. Placing these common features in the tree this way means features do not have to be duplicated along individual branches of the tree structure.

> “Yocto Project Baseline Kernel”包含每种内核类型和 BSP 共同的功能，BSP 在树中进一步组织。以这种方式将这些公共特征放置在树中意味着不必沿着树结构的各个分支复制特征。

From the \"Yocto Project Baseline Kernel\", branch points represent specific functionality for individual Board Support Packages (BSPs) as well as real-time kernels. The illustration represents this through three BSP-specific branches and a real-time kernel branch. Each branch represents some unique functionality for the BSP or for a real-time Yocto Linux kernel.

> 从“Yocto 项目基线内核”中，分支点代表单个板支持包（BSP）以及实时内核的特定功能。该图通过三个特定于 BSP 的分支和一个实时内核分支来表示这一点。每个分支代表 BSP 或实时 Yocto Linux 内核的一些独特功能。

In this example structure, the \"Real-time (rt) Kernel\" branch has common features for all real-time Yocto Linux kernels and contains more branches for individual BSP-specific real-time kernels. The illustration shows three branches as an example. Each branch points the way to specific, unique features for a respective real-time kernel as they apply to a given BSP.

> 在这个示例结构中，“实时（rt）内核”分支具有适用于所有实时 Yocto Linux 内核的通用功能，并包含适用于单个 BSP 特定实时内核的更多分支。图示以三个分支为例。每个分支在应用于给定 BSP 时，都为各自的实时内核指明了特定、独特的功能。

The resulting tree structure presents a clear path of markers (or branches) to the developer that, for all practical purposes, is the Yocto Linux kernel needed for any given set of requirements.

> 由此产生的树结构为开发人员提供了一条清晰的标记（或分支）路径，出于所有实际目的，它是任何给定需求集所需的 Yocto Linux 内核。

::: note
::: title
Note
:::

Keep in mind the figure does not take into account all the supported Yocto Linux kernels, but rather shows a single generic kernel just for conceptual purposes. Also keep in mind that this structure represents the `overview-manual/development-environment:yocto project source repositories`{.interpreted-text role="ref"} that are either pulled from during the build or established on the host development system prior to the build by either cloning a particular kernel\'s Git repository or by downloading and unpacking a tarball.

> 请记住，该图没有考虑所有支持的 Yocto Linux 内核，而是仅出于概念目的显示了一个通用内核。还要记住，此结构代表“概述手册/开发环境：yocto 项目源存储库”｛.depredicted text role=“ref”｝，这些存储库要么在构建期间从中提取，要么在构建之前通过克隆特定内核的 Git 存储库或下载并拆包 tarball 在主机开发系统上建立。
> :::

Working with the kernel as a structured tree follows recognized community best practices. In particular, the kernel as shipped with the product, should be considered an \"upstream source\" and viewed as a series of historical and documented modifications (commits). These modifications represent the development and stabilization done by the Yocto Project kernel development team.

> 将内核作为结构化的树进行处理遵循公认的社区最佳实践。特别是，产品附带的内核应被视为“上游源”，并被视为一系列历史和文档化的修改（提交）。这些修改代表了 Yocto 项目内核开发团队所做的开发和稳定工作。

Because commits only change at significant release points in the product life cycle, developers can work on a branch created from the last relevant commit in the shipped Yocto Project Linux kernel. As mentioned previously, the structure is transparent to the developer because the kernel tree is left in this state after cloning and building the kernel.

> 由于提交仅在产品生命周期的重要发布点发生变化，因此开发人员可以在 Yocto Project Linux 内核中处理从上次相关提交创建的分支。如前所述，该结构对开发人员是透明的，因为在克隆和构建内核之后，内核树仍处于这种状态。

# Kernel Build File Hierarchy

Upstream storage of all the available kernel source code is one thing, while representing and using the code on your host development system is another. Conceptually, you can think of the kernel source repositories as all the source files necessary for all the supported Yocto Linux kernels. As a developer, you are just interested in the source files for the kernel on which you are working. And, furthermore, you need them available on your host system.

> 所有可用内核源代码的上游存储是一回事，而在主机开发系统上表示和使用代码是另一回事。从概念上讲，您可以将内核源代码存储库视为所有受支持的 Yocto Linux 内核所需的所有源文件。作为一名开发人员，您只对正在工作的内核的源文件感兴趣。此外，您还需要在您的主机系统上提供它们。

Kernel source code is available on your host system several different ways:

> 内核源代码在您的主机系统上有几种不同的方式：

- *Files Accessed While using devtool:* `devtool`, which is available with the Yocto Project, is the preferred method by which to modify the kernel. See the \"`kernel-dev/intro:kernel modification workflow`{.interpreted-text role="ref"}\" section.

> -*使用 devtool 时访问的文件：*Yocto 项目提供的“devtool”是修改内核的首选方法。请参阅“`kernel dev/intro:kernel modification workflow`｛.explored text role=“ref”｝”一节。

- *Cloned Repository:* If you are working in the kernel all the time, you probably would want to set up your own local Git repository of the Yocto Linux kernel tree. For information on how to clone a Yocto Linux kernel Git repository, see the \"`kernel-dev/common:preparing the build host to work on the kernel`{.interpreted-text role="ref"}\" section.

> -*克隆的存储库：*如果你一直在内核中工作，你可能想在 Yocto Linux 内核树中建立自己的本地 Git 存储库。有关如何克隆 Yocto Linux 内核 Git 存储库的信息，请参阅\“`kernel dev/common:准备构建主机以在内核上工作”｛.depreted text role=“ref”｝\“一节。

- *Temporary Source Files from a Build:* If you just need to make some patches to the kernel using a traditional BitBake workflow (i.e. not using the `devtool`), you can access temporary kernel source files that were extracted and used during a kernel build.

> -*构建过程中的临时源文件：*如果您只需要使用传统的 BitBake 工作流（即不使用“devtool”）对内核进行一些修补，则可以访问在内核构建过程中提取和使用的临时内核源文件。

The temporary kernel source files resulting from a build using BitBake have a particular hierarchy. When you build the kernel on your development system, all files needed for the build are taken from the source repositories pointed to by the `SRC_URI`{.interpreted-text role="term"} variable and gathered in a temporary work area where they are subsequently used to create the unique kernel. Thus, in a sense, the process constructs a local source tree specific to your kernel from which to generate the new kernel image.

> 使用 BitBake 生成的临时内核源文件具有特定的层次结构。当您在开发系统上构建内核时，构建所需的所有文件都是从 `SRC_URI`｛.explored text role=“term”｝变量指向的源存储库中获取的，并收集在临时工作区中，随后用于创建唯一的内核。因此，从某种意义上说，进程构建了一个特定于内核的本地源树，从中生成新的内核映像。

The following figure shows the temporary file structure created on your host system when you build the kernel using BitBake. This `Build Directory`{.interpreted-text role="term"} contains all the source files used during the build.

> 下图显示了使用 BitBake 构建内核时在主机系统上创建的临时文件结构。此 `Build Directory`｛.explored text role=“term”｝包含生成过程中使用的所有源文件。

![image](figures/kernel-overview-2-generic.png){.align-center width="70.0%"}

Again, for additional information on the Yocto Project kernel\'s architecture and its branching strategy, see the \"`kernel-dev/concepts-appx:yocto linux kernel architecture and branching strategies`{.interpreted-text role="ref"}\" section. You can also reference the \"``kernel-dev/common:using \`\`devtool\`\` to patch the kernel``{.interpreted-text role="ref"}\" and \"`kernel-dev/common:using traditional kernel development to patch the kernel`{.interpreted-text role="ref"}\" sections for detailed example that modifies the kernel.

> 同样，有关 Yocto Project 内核体系结构及其分支策略的更多信息，请参阅“`kernel dev/contensions appx:Yocto-linux内核体系结构和分支策略`{.depreted text role=“ref”}\”一节。您还可以参考“``kernel dev/common:使用\`` devtool\`\`来修补内核 ``{.depredicted text role=“ref”}`”和“` kernel/dev/common:使用传统内核开发来修补内核 `{.epredicted textrole=”ref“}`”部分，以获取修改内核的详细示例。

# Determining Hardware and Non-Hardware Features for the Kernel Configuration Audit Phase

This section describes part of the kernel configuration audit phase that most developers can ignore. For general information on kernel configuration including `menuconfig`, `defconfig` files, and configuration fragments, see the \"`kernel-dev/common:configuring the kernel`{.interpreted-text role="ref"}\" section.

> 本节描述了大多数开发人员可以忽略的内核配置审核阶段的一部分。有关内核配置的一般信息，包括 `menuconfig`、`defconfig` 文件和配置片段，请参阅\“`kernel dev/common:configuration the kernel`｛.depredicted text role=“ref”｝\”一节。

During this part of the audit phase, the contents of the final `.config` file are compared against the fragments specified by the system. These fragments can be system fragments, distro fragments, or user-specified configuration elements. Regardless of their origin, the OpenEmbedded build system warns the user if a specific option is not included in the final kernel configuration.

> 在审计阶段的这一部分，将最后的“.config”文件的内容与系统指定的片段进行比较。这些片段可以是系统片段、发行版片段或用户指定的配置元素。无论其来源如何，如果最终内核配置中未包含特定选项，OpenEmbedded 构建系统都会警告用户。

By default, in order to not overwhelm the user with configuration warnings, the system only reports missing \"hardware\" options as they could result in a boot failure or indicate that important hardware is not available.

> 默认情况下，为了避免配置警告淹没用户，系统只报告丢失的“硬件”选项，因为它们可能导致启动失败或指示重要硬件不可用。

To determine whether or not a given option is \"hardware\" or \"non-hardware\", the kernel Metadata in `yocto-kernel-cache` contains files that classify individual or groups of options as either hardware or non-hardware. To better show this, consider a situation where the `yocto-kernel-cache` contains the following files:

> 为了确定给定的选项是“硬件”还是“非硬件”，“yocto 内核缓存”中的内核元数据包含将单个或组选项分类为硬件或非硬件的文件。为了更好地显示这一点，请考虑“yocto 内核缓存”包含以下文件的情况：

```
yocto-kernel-cache/features/drm-psb/hardware.cfg
yocto-kernel-cache/features/kgdb/hardware.cfg
yocto-kernel-cache/ktypes/base/hardware.cfg
yocto-kernel-cache/bsp/mti-malta32/hardware.cfg
yocto-kernel-cache/bsp/qemu-ppc32/hardware.cfg
yocto-kernel-cache/bsp/qemuarma9/hardware.cfg
yocto-kernel-cache/bsp/mti-malta64/hardware.cfg
yocto-kernel-cache/bsp/arm-versatile-926ejs/hardware.cfg
yocto-kernel-cache/bsp/common-pc/hardware.cfg
yocto-kernel-cache/bsp/common-pc-64/hardware.cfg
yocto-kernel-cache/features/rfkill/non-hardware.cfg
yocto-kernel-cache/ktypes/base/non-hardware.cfg
yocto-kernel-cache/features/aufs/non-hardware.kcf
yocto-kernel-cache/features/ocf/non-hardware.kcf
yocto-kernel-cache/ktypes/base/non-hardware.kcf
yocto-kernel-cache/ktypes/base/hardware.kcf
yocto-kernel-cache/bsp/qemu-ppc32/hardware.kcf
```

Here are explanations for the various files:

> 以下是对各种文件的解释：

- `hardware.kcf`: Specifies a list of kernel Kconfig files that contain hardware options only.
- `non-hardware.kcf`: Specifies a list of kernel Kconfig files that contain non-hardware options only.

> -`non-hardware.kcf`：指定仅包含非硬件选项的内核 Kconfig 文件的列表。

- `hardware.cfg`: Specifies a list of kernel `CONFIG_` options that are hardware, regardless of whether or not they are within a Kconfig file specified by a hardware or non-hardware Kconfig file (i.e. `hardware.kcf` or `non-hardware.kcf`).

> -`hardware.cfg`：指定作为硬件的内核 `CONFIG_` 选项的列表，无论它们是否在由硬件或非硬件 Kconfig 文件（即 `hardware.kcf` 或 `non-hardware.kcf'）指定的 Kconfig 文件中。

- `non-hardware.cfg`: Specifies a list of kernel `CONFIG_` options that are not hardware, regardless of whether or not they are within a Kconfig file specified by a hardware or non-hardware Kconfig file (i.e. `hardware.kcf` or `non-hardware.kcf`).

> -`non-hardware.cfg`：指定非硬件内核 `CONFIG_` 选项的列表，无论它们是否在由硬件或非硬件 Kconfig 文件（即 `hardware.kcf` 或 `non-hhardware.kcf `）指定的 Kconfig 文件中。

Here is a specific example using the `kernel-cache/bsp/mti-malta32/hardware.cfg`:

> 下面是一个使用“内核缓存/bsp/mti-malta32/hardware.cfg”的特定示例：

```
CONFIG_SERIAL_8250
CONFIG_SERIAL_8250_CONSOLE
CONFIG_SERIAL_8250_NR_UARTS
CONFIG_SERIAL_8250_PCI
CONFIG_SERIAL_CORE
CONFIG_SERIAL_CORE_CONSOLE
CONFIG_VGA_ARB
```

The kernel configuration audit automatically detects these files (hence the names must be exactly the ones discussed here), and uses them as inputs when generating warnings about the final `.config` file.

> 内核配置审核会自动检测这些文件（因此名称必须与此处讨论的名称完全相同），并在生成有关最终“.config”文件的警告时将其用作输入。

A user-specified kernel Metadata repository, or recipe space feature, can use these same files to classify options that are found within its `.cfg` files as hardware or non-hardware, to prevent the OpenEmbedded build system from producing an error or warning when an option is not in the final `.config` file.

> 用户指定的内核元数据存储库或配方空间功能可以使用这些相同的文件将其“.cfg”文件中的选项分类为硬件或非硬件，以防止 OpenEmbedded 构建系统在选项不在最终“.config”文件中时产生错误或警告。
