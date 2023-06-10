---
tip: translate by openai@2023-06-10 10:33:03
...
---
title: Advanced Kernel Concepts
-------------------------------

# Yocto Project Kernel Development and Maintenance

Kernels available through the Yocto Project (Yocto Linux kernels), like other kernels, are based off the Linux kernel releases from [https://www.kernel.org](https://www.kernel.org). At the beginning of a major Linux kernel development cycle, the Yocto Project team chooses a Linux kernel based on factors such as release timing, the anticipated release timing of final upstream `kernel.org` versions, and Yocto Project feature requirements. Typically, the Linux kernel chosen is in the final stages of development by the Linux community. In other words, the Linux kernel is in the release candidate or \"rc\" phase and has yet to reach final release. But, by being in the final stages of external development, the team knows that the `kernel.org` final release will clearly be within the early stages of the Yocto Project development window.

> Yocto 项目（Yocto Linux 内核）提供的内核和其它内核一样，基于 [https://www.kernel.org](https://www.kernel.org) 上的 Linux 内核版本。在一个主要的 Linux 内核开发周期开始时，Yocto 项目团队会根据发布时间、最终上游 `kernel.org` 版本预期发布时间以及 Yocto 项目功能要求等因素，选择一个 Linux 内核。通常，选择的 Linux 内核正处于 Linux 社区的最终开发阶段。换句话说，Linux 内核处于发布候选版本或“rc”阶段，尚未达到最终发布。但是，由于处于外部开发的最终阶段，团队知道 `kernel.org` 的最终版本显然将在 Yocto 项目开发窗口的初期。

This balance allows the Yocto Project team to deliver the most up-to-date Yocto Linux kernel possible, while still ensuring that the team has a stable official release for the baseline Linux kernel version.

> 这种平衡使 Yocto 项目团队能够提供最新的 Yocto Linux 内核，同时仍然确保团队有一个稳定的基线 Linux 内核版本的官方发布版本。

As implied earlier, the ultimate source for Yocto Linux kernels are released kernels from `kernel.org`. In addition to a foundational kernel from `kernel.org`, the available Yocto Linux kernels contain a mix of important new mainline developments, non-mainline developments (when no alternative exists), Board Support Package (BSP) developments, and custom features. These additions result in a commercially released Yocto Project Linux kernel that caters to specific embedded designer needs for targeted hardware.

> 正如之前暗示的，Yocto Linux 内核的最终来源是来自 kernel.org 的发布内核。除了来自 kernel.org 的基础内核之外，可用的 Yocto Linux 内核包含了重要的新主线开发、非主线开发（当没有其他选择时）、板支持包（BSP）开发以及自定义功能。这些增加物导致了一个商业发布的 Yocto 项目 Linux 内核，可以满足针对特定硬件的嵌入式设计师的需求。

You can find a web interface to the Yocto Linux kernels in the `overview-manual/development-environment:yocto project source repositories`{.interpreted-text role="ref"} at :yocto\_[git:%60/](git:%60/)\`. If you look at the interface, you will see to the left a grouping of Git repositories titled \"Yocto Linux Kernel\". Within this group, you will find several Linux Yocto kernels developed and included with Yocto Project releases:

> 您可以在 yocto 项目源代码存储库的 overview-manual/development-environment:yocto 项目源代码存储库中找到一个 Yocto Linux 内核的 Web 界面。如果您查看该界面，您将在左侧看到一组名为“Yocto Linux 内核”的 Git 存储库。在这个组中，您将找到几个由 Yocto 项目发布的 Linux Yocto 内核。

- *linux-yocto-4.1:* The stable Yocto Project kernel to use with the Yocto Project Release 2.0. This kernel is based on the Linux 4.1 released kernel.

> Linux-Yocto-4.1：与 Yocto Project Release 2.0 一起使用的稳定的 Yocto Project 内核，此内核基于发布的 Linux 4.1 内核。

- *linux-yocto-4.4:* The stable Yocto Project kernel to use with the Yocto Project Release 2.1. This kernel is based on the Linux 4.4 released kernel.

> Linux-Yocto-4.4：与 Yocto Project Release 2.1 一起使用的稳定的 Yocto Project 内核。该内核基于发布的 Linux 4.4 内核。

- *linux-yocto-4.6:* A temporary kernel that is not tied to any Yocto Project release.
- *linux-yocto-4.8:* The stable yocto Project kernel to use with the Yocto Project Release 2.2.
- *linux-yocto-4.9:* The stable Yocto Project kernel to use with the Yocto Project Release 2.3. This kernel is based on the Linux 4.9 released kernel.

> linux-yocto-4.9：与 Yocto Project Release 2.3 一起使用的稳定的 Yocto Project 内核。该内核基于 Linux 4.9 发布的内核。

- *linux-yocto-4.10:* The default stable Yocto Project kernel to use with the Yocto Project Release 2.3. This kernel is based on the Linux 4.10 released kernel.

> - *Linux-Yocto-4.10：*使用 Yocto Project Release 2.3 的默认稳定 Yocto 项目内核。此内核基于 Linux 4.10 发布的内核。

- *linux-yocto-4.12:* The default stable Yocto Project kernel to use with the Yocto Project Release 2.4. This kernel is based on the Linux 4.12 released kernel.

> - *Linux-Yocto-4.12：* 默认的稳定的 Yocto 项目内核，可用于 Yocto 项目发布 2.4。 该内核基于发布的 Linux 4.12 内核。

- *yocto-kernel-cache:* The `linux-yocto-cache` contains patches and configurations for the linux-yocto kernel tree. This repository is useful when working on the linux-yocto kernel. For more information on this \"Advanced Kernel Metadata\", see the \"`/kernel-dev/advanced`{.interpreted-text role="doc"}\" Chapter.

> - *Yocto 内核缓存：* linux-yocto 缓存中包含了 linux-yocto 内核树的补丁和配置。当处理 linux-yocto 内核时，这个存储库很有用。有关此“高级内核元数据”的更多信息，请参见“/kernel-dev/advanced”章节。

- *linux-yocto-dev:* A development kernel based on the latest upstream release candidate available.

::: note
::: title
Note
:::

Long Term Support Initiative (LTSI) for Yocto Linux kernels is as follows:

- For Yocto Project releases 1.7, 1.8, and 2.0, the LTSI kernel is `linux-yocto-3.14`.
- For Yocto Project releases 2.1, 2.2, and 2.3, the LTSI kernel is `linux-yocto-4.1`.
- For Yocto Project release 2.4, the LTSI kernel is `linux-yocto-4.9`
- `linux-yocto-4.4` is an LTS kernel.
  :::

Once a Yocto Linux kernel is officially released, the Yocto Project team goes into their next development cycle, or upward revision (uprev) cycle, while still continuing maintenance on the released kernel. It is important to note that the most sustainable and stable way to include feature development upstream is through a kernel uprev process. Back-porting hundreds of individual fixes and minor features from various kernel versions is not sustainable and can easily compromise quality.

> 一旦 Yocto Linux 内核正式发布，Yocto 项目团队就会进入下一个开发周期或上游修订（uprev）周期，同时继续维护已发布的内核。重要的是要注意，最可持续和稳定的方式是通过内核 uprev 过程来包括功能开发上游。从各个内核版本回退数百个单独的修复和小特性不可持续，可能很容易破坏质量。

During the uprev cycle, the Yocto Project team uses an ongoing analysis of Linux kernel development, BSP support, and release timing to select the best possible `kernel.org` Linux kernel version on which to base subsequent Yocto Linux kernel development. The team continually monitors Linux community kernel development to look for significant features of interest. The team does consider back-porting large features if they have a significant advantage. User or community demand can also trigger a back-port or creation of new functionality in the Yocto Project baseline kernel during the uprev cycle.

> 在 uprev 周期中，Yocto 项目团队使用对 Linux 内核开发、BSP 支持和发布时间的持续分析，来选择最佳的 `kernel.org` Linux 内核版本作为后续 Yocto Linux 内核开发的基础。团队不断监控 Linux 社区内核开发，以寻找有意义的特性。如果有重要优势，团队会考虑将大型特性回退。用户或社区需求也可以在 uprev 周期中触发 Yocto 项目基线内核的回退或创建新功能。

Generally speaking, every new Linux kernel both adds features and introduces new bugs. These consequences are the basic properties of upstream Linux kernel development and are managed by the Yocto Project team\'s Yocto Linux kernel development strategy. It is the Yocto Project team\'s policy to not back-port minor features to the released Yocto Linux kernel. They only consider back-porting significant technological jumps \-\-- and, that is done after a complete gap analysis. The reason for this policy is that back-porting any small to medium sized change from an evolving Linux kernel can easily create mismatches, incompatibilities and very subtle errors.

> 一般来说，每个新的 Linux 内核都会添加新功能并引入新的错误。这些后果是上游 Linux 内核开发的基本属性，由 Yocto 项目团队的 Yocto Linux 内核开发策略管理。Yocto 项目团队的政策是不将小型功能回退到发布的 Yocto Linux 内核中。他们只考虑重大技术跳跃 - 这是在完成完整的差距分析之后完成的。这项政策的原因是，从不断发展的 Linux 内核中回退任何小型到中型的变化可能会导致不匹配，不兼容和非常微妙的错误。

The policies described in this section result in both a stable and a cutting edge Yocto Linux kernel that mixes forward ports of existing Linux kernel features and significant and critical new functionality. Forward porting Linux kernel functionality into the Yocto Linux kernels available through the Yocto Project can be thought of as a \"micro uprev\". The many \"micro uprevs\" produce a Yocto Linux kernel version with a mix of important new mainline, non-mainline, BSP developments and feature integrations. This Yocto Linux kernel gives insight into new features and allows focused amounts of testing to be done on the kernel, which prevents surprises when selecting the next major uprev. The quality of these cutting edge Yocto Linux kernels is evolving and the kernels are used in leading edge feature and BSP development.

> 此部分中描述的政策导致稳定的 Yocto Linux 内核与前沿的 Yocto Linux 内核相结合，其中包含现有 Linux 内核功能的转发端口和重要且关键的新功能。将 Linux 内核功能转发到 Yocto 项目提供的 Yocto Linux 内核可以被认为是“微小的升级”。许多“微小的升级”产生了一个 Yocto Linux 内核版本，其中包含重要的主线、非主线、BSP 开发和功能整合。这个 Yocto Linux 内核提供了对新功能的认识，并允许在内核上进行有针对性的测试，以防止在选择下一个主要升级时出现意外情况。这些前沿的 Yocto Linux 内核的质量正在不断提高，这些内核被用于引领潮流的功能和 BSP 开发。

# Yocto Linux Kernel Architecture and Branching Strategies

As mentioned earlier, a key goal of the Yocto Project is to present the developer with a kernel that has a clear and continuous history that is visible to the user. The architecture and mechanisms, in particular the branching strategies, used achieve that goal in a manner similar to upstream Linux kernel development in `kernel.org`.

> 正如前面提到的，Yocto 项目的一个关键目标是向开发者提供一个具有清晰且连续的历史记录，用户可以清楚地看到。为了实现这一目标，使用的架构和机制，特别是分支策略，与 kernel.org 上的 Linux 内核开发类似。

You can think of a Yocto Linux kernel as consisting of a baseline Linux kernel with added features logically structured on top of the baseline. The features are tagged and organized by way of a branching strategy implemented by the Yocto Project team using the Source Code Manager (SCM) Git.

> 你可以把 Yocto Linux 内核想象成一个基线 Linux 内核，在基线上面逻辑地添加了一些特性。这些特性由 Yocto Project 团队使用源代码管理器（SCM）Git 实施的分支策略进行标记和组织。

::: note
::: title
Note
:::

- Git is the obvious SCM for meeting the Yocto Linux kernel organizational and structural goals described in this section. Not only is Git the SCM for Linux kernel development in `kernel.org` but, Git continues to grow in popularity and supports many different work flows, front-ends and management techniques.

> Git 显然是满足本节描述的 Yocto Linux 内核组织和结构目标的最佳源代码管理（SCM）工具。不仅是 kernel.org 上 Linux 内核开发的 SCM，而且 Git 的流行度正在不断增加，支持多种工作流程、前端和管理技术。

- You can find documentation on Git at [https://git-scm.com/doc](https://git-scm.com/doc). You can also get an introduction to Git as it applies to the Yocto Project in the \"`overview-manual/development-environment:git`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual. The latter reference provides an overview of Git and presents a minimal set of Git commands that allows you to be functional using Git. You can use as much, or as little, of what Git has to offer to accomplish what you need for your project. You do not have to be a \"Git Expert\" in order to use it with the Yocto Project.

> 你可以在 [https://git-scm.com/doc](https://git-scm.com/doc) 上找到 Git 的文档。您还可以在 Yocto 项目概述和概念手册中的“overview-manual/development-environment：git”部分获得有关 Git 在 Yocto 项目中的介绍。后一个参考提供了 Git 的概述，并提出了一组最小的 Git 命令，使您可以使用 Git 进行功能操作。您可以根据自己的项目需要，使用 Git 提供的尽可能多或尽可能少的功能。您不必成为“Git 专家”才能在 Yocto 项目中使用它。
> :::

Using Git\'s tagging and branching features, the Yocto Project team creates kernel branches at points where functionality is no longer shared and thus, needs to be isolated. For example, board-specific incompatibilities would require different functionality and would require a branch to separate the features. Likewise, for specific kernel features, the same branching strategy is used.

> 使用 Git 的标记和分支功能，Yocto 项目团队在功能不再共享的点上创建内核分支，因此需要隔离。例如，板级不兼容性需要不同的功能，因此需要一个分支来分离这些功能。同样，对于特定的内核特性，也使用相同的分支策略。

This \"tree-like\" architecture results in a structure that has features organized to be specific for particular functionality, single kernel types, or a subset of kernel types. Thus, the user has the ability to see the added features and the commits that make up those features. In addition to being able to see added features, the user can also view the history of what made up the baseline Linux kernel.

> 这种“树状”架构导致的结构具有特定功能、单个内核类型或内核类型子集的特征。因此，用户可以看到添加的功能和组成这些功能的提交。除了能够看到添加的功能外，用户还可以查看构成基线 Linux 内核的历史。

Another consequence of this strategy results in not having to store the same feature twice internally in the tree. Rather, the kernel team stores the unique differences required to apply the feature onto the kernel type in question.

> 这种策略的另一个结果是，不必在树内部两次存储相同的特征。相反，内核团队存储了应用到相应内核类型上所需的唯一差异。

::: note
::: title
Note
:::

The Yocto Project team strives to place features in the tree such that features can be shared by all boards and kernel types where possible. However, during development cycles or when large features are merged, the team cannot always follow this practice. In those cases, the team uses isolated branches to merge features.

> 团队努力将特性放置在树中，以便尽可能多的板子和内核类型可以共享这些特性。但是，在开发周期或者合并大特性时，团队不能总是遵循这一做法。在这种情况下，团队会使用隔离的分支来合并特性。
> :::

BSP-specific code additions are handled in a similar manner to kernel-specific additions. Some BSPs only make sense given certain kernel types. So, for these types, the team creates branches off the end of that kernel type for all of the BSPs that are supported on that kernel type. From the perspective of the tools that create the BSP branch, the BSP is really no different than a feature. Consequently, the same branching strategy applies to BSPs as it does to kernel features. So again, rather than store the BSP twice, the team only stores the unique differences for the BSP across the supported multiple kernels.

> 特定于 BSP 的代码添加处理方式与特定于内核的添加类似。有些 BSP 只有在某些内核类型下才有意义。因此，为了支持这些类型，团队为该内核类型下支持的所有 BSP 创建了分支。从创建 BSP 分支的工具的角度来看，BSP 实际上与功能没有什么不同。因此，BSP 的分支策略与内核功能的分支策略相同。因此，团队不再存储 BSP 两次，而是只存储支持多个内核的 BSP 的独特差异。

While this strategy can result in a tree with a significant number of branches, it is important to realize that from the developer\'s point of view, there is a linear path that travels from the baseline `kernel.org`, through a select group of features and ends with their BSP-specific commits. In other words, the divisions of the kernel are transparent and are not relevant to the developer on a day-to-day basis. From the developer\'s perspective, this path is the development branch. The developer does not need to be aware of the existence of any other branches at all. Of course, it can make sense to have these branches in the tree, should a person decide to explore them. For example, a comparison between two BSPs at either the commit level or at the line-by-line code `diff` level is now a trivial operation.

> 虽然这种策略可能会导致一棵具有大量分支的树，但重要的是要意识到，从开发者的角度来看，从基线“kernel.org”到一组特定的功能，再到其 BSP 特定的提交，存在着一条线性路径。换句话说，内核的划分对于开发者来说是透明的，并且在日常工作中不相关。从开发者的角度来看，这条路径就是开发分支。开发者根本不需要意识到其他分支的存在。当然，如果有人决定探索它们，在树中保留这些分支是有意义的。例如，现在可以轻松地在提交级别或行代码“diff”级别上比较两个 BSP。

The following illustration shows the conceptual Yocto Linux kernel.

![image](figures/kernel-architecture-overview.png){width="100.0%"}

In the illustration, the \"Kernel.org Branch Point\" marks the specific spot (or Linux kernel release) from which the Yocto Linux kernel is created. From this point forward in the tree, features and differences are organized and tagged.

> 在图示中，“Kernel.org 分支点”标记了从中创建 Yocto Linux 内核的特定位置（或 Linux 内核版本）。从这一点开始，树中的功能和差异被组织和标记。

The \"Yocto Project Baseline Kernel\" contains functionality that is common to every kernel type and BSP that is organized further along in the tree. Placing these common features in the tree this way means features do not have to be duplicated along individual branches of the tree structure.

> "Yocto 项目基线内核"包含每种内核类型和 BSP 共有的功能，这些功能进一步在树结构中组织。将这些共同特征放在树结构中的这种方式意味着特征不必在树结构的各个分支中重复。

From the \"Yocto Project Baseline Kernel\", branch points represent specific functionality for individual Board Support Packages (BSPs) as well as real-time kernels. The illustration represents this through three BSP-specific branches and a real-time kernel branch. Each branch represents some unique functionality for the BSP or for a real-time Yocto Linux kernel.

> 从“Yocto 项目基线内核”开始，分支点代表个别板支持包（BSP）以及实时内核的特定功能。该插图通过三个特定于 BSP 的分支和一个实时内核分支来表示这一点。每个分支都代表了 BSP 或 Yocto Linux 实时内核的一些独特功能。

In this example structure, the \"Real-time (rt) Kernel\" branch has common features for all real-time Yocto Linux kernels and contains more branches for individual BSP-specific real-time kernels. The illustration shows three branches as an example. Each branch points the way to specific, unique features for a respective real-time kernel as they apply to a given BSP.

> 在这个示例结构中，“实时（rt）内核”分支具有所有实时 Yocto Linux 内核的共同特征，并包含更多用于各个 BSP 特定实时内核的分支。图示中以三个分支为例。每个分支都指向特定于给定 BSP 的特定实时内核的特定特性。

The resulting tree structure presents a clear path of markers (or branches) to the developer that, for all practical purposes, is the Yocto Linux kernel needed for any given set of requirements.

> 结果树结构为开发者提供了一条清晰的标记（或分支）路径，几乎可以满足所有需求，以获得所需的 Yocto Linux 内核。

::: note
::: title
Note
:::

Keep in mind the figure does not take into account all the supported Yocto Linux kernels, but rather shows a single generic kernel just for conceptual purposes. Also keep in mind that this structure represents the `overview-manual/development-environment:yocto project source repositories`{.interpreted-text role="ref"} that are either pulled from during the build or established on the host development system prior to the build by either cloning a particular kernel\'s Git repository or by downloading and unpacking a tarball.

> 请记住，图中没有考虑所有支持的 Yocto Linux 内核，而只是为了概念目的而显示一个通用内核。此外，请记住，该结构代表的是在构建期间拉取的 `overview-manual/development-environment:yocto project source repositories`，或者是在构建之前通过克隆特定内核的 Git 存储库或下载并解压缩 tarball 来建立在主机开发系统上的存储库。
> :::

Working with the kernel as a structured tree follows recognized community best practices. In particular, the kernel as shipped with the product, should be considered an \"upstream source\" and viewed as a series of historical and documented modifications (commits). These modifications represent the development and stabilization done by the Yocto Project kernel development team.

> 在结构化的树中使用内核遵循公认的社区最佳实践。特别是，随产品发布的内核应被视为“上游源”，并被视为一系列历史和文档纪录的修改（提交）。这些修改代表了 Yocto 项目内核开发团队的开发和稳定工作。

Because commits only change at significant release points in the product life cycle, developers can work on a branch created from the last relevant commit in the shipped Yocto Project Linux kernel. As mentioned previously, the structure is transparent to the developer because the kernel tree is left in this state after cloning and building the kernel.

> 因为提交只会在产品生命周期中的重要发布点上发生变化，开发人员可以在从已发布的 Yocto Project Linux 内核中的最后一个相关提交中创建的分支上工作。正如之前提到的，开发人员对结构透明，因为在克隆和构建内核后，内核树会保持在这种状态。

# Kernel Build File Hierarchy

Upstream storage of all the available kernel source code is one thing, while representing and using the code on your host development system is another. Conceptually, you can think of the kernel source repositories as all the source files necessary for all the supported Yocto Linux kernels. As a developer, you are just interested in the source files for the kernel on which you are working. And, furthermore, you need them available on your host system.

> 上游存储所有可用的内核源代码是一回事，而在您的主机开发系统上表示和使用代码又是另一回事。从概念上讲，您可以将内核源代码仓库视为所有支持 Yocto Linux 内核所需的源文件。作为开发人员，您只对您正在处理的内核的源文件感兴趣。而且，您还需要它们在您的主机系统上可用。

Kernel source code is available on your host system several different ways:

- *Files Accessed While using devtool:* `devtool`, which is available with the Yocto Project, is the preferred method by which to modify the kernel. See the \"`kernel-dev/intro:kernel modification workflow`{.interpreted-text role="ref"}\" section.

> 使用 devtool 时访问的文件：Yocto Project 提供的 devtool 是修改内核的首选方法。请参阅“kernel-dev/intro：内核修改工作流程”部分。

- *Cloned Repository:* If you are working in the kernel all the time, you probably would want to set up your own local Git repository of the Yocto Linux kernel tree. For information on how to clone a Yocto Linux kernel Git repository, see the \"`kernel-dev/common:preparing the build host to work on the kernel`{.interpreted-text role="ref"}\" section.

> 如果您一直在内核中工作，您可能会想要设置自己的 Yocto Linux 内核树的本地 Git 存储库。有关如何克隆 Yocto Linux 内核 Git 存储库的信息，请参阅“kernel-dev / common：准备构建主机以在内核上工作”部分。

- *Temporary Source Files from a Build:* If you just need to make some patches to the kernel using a traditional BitBake workflow (i.e. not using the `devtool`), you can access temporary kernel source files that were extracted and used during a kernel build.

> 如果您只需要使用传统的 BitBake 工作流程（即不使用 `devtool`）来对内核进行一些补丁，您可以访问在内核构建期间提取并使用的临时内核源文件。

The temporary kernel source files resulting from a build using BitBake have a particular hierarchy. When you build the kernel on your development system, all files needed for the build are taken from the source repositories pointed to by the `SRC_URI`{.interpreted-text role="term"} variable and gathered in a temporary work area where they are subsequently used to create the unique kernel. Thus, in a sense, the process constructs a local source tree specific to your kernel from which to generate the new kernel image.

> 当使用 BitBake 构建时，生成的临时内核源文件具有特定的层次结构。当您在开发系统上构建内核时，所有构建所需的文件都将从 `SRC_URI` 变量指向的源存储库中获取，并聚集在一个临时工作区中，然后用于创建唯一的内核。因此，从某种意义上说，该过程会从中生成新的内核映像，从而构建一个特定于您的内核的本地源树。

The following figure shows the temporary file structure created on your host system when you build the kernel using BitBake. This `Build Directory`{.interpreted-text role="term"} contains all the source files used during the build.

> 以下图示展示了使用 BitBake 构建内核时在您的主机系统上创建的临时文件结构。此 `构建目录` 包含在构建期间使用的所有源文件。

![image](figures/kernel-overview-2-generic.png){.align-center width="70.0%"}

Again, for additional information on the Yocto Project kernel\'s architecture and its branching strategy, see the \"`kernel-dev/concepts-appx:yocto linux kernel architecture and branching strategies`{.interpreted-text role="ref"}\" section. You can also reference the \"``kernel-dev/common:using \`\`devtool\`\` to patch the kernel``{.interpreted-text role="ref"}\" and \"`kernel-dev/common:using traditional kernel development to patch the kernel`{.interpreted-text role="ref"}\" sections for detailed example that modifies the kernel.

> 再次，有关 Yocto Project 内核架构和分支策略的更多信息，请参阅“kernel-dev / concepts-appx：yocto linux 内核架构和分支策略”部分。您还可以参考“kernel-dev / common：使用 devtool 修补内核”和“kernel-dev / common：使用传统的内核开发来修补内核”部分，以获取修改内核的详细示例。

# Determining Hardware and Non-Hardware Features for the Kernel Configuration Audit Phase

This section describes part of the kernel configuration audit phase that most developers can ignore. For general information on kernel configuration including `menuconfig`, `defconfig` files, and configuration fragments, see the \"`kernel-dev/common:configuring the kernel`{.interpreted-text role="ref"}\" section.

> 这一节描述了大多数开发人员可以忽略的内核配置审核阶段的一部分。有关内核配置（包括 menuconfig、defconfig 文件和配置片段）的一般信息，请参阅“内核开发/共同：配置内核”部分。

During this part of the audit phase, the contents of the final `.config` file are compared against the fragments specified by the system. These fragments can be system fragments, distro fragments, or user-specified configuration elements. Regardless of their origin, the OpenEmbedded build system warns the user if a specific option is not included in the final kernel configuration.

> 在这个审核阶段，最终的 `.config` 文件的内容与系统指定的片段进行比较。这些片段可以是系统片段，发行版片段，或用户指定的配置元素。无论它们的来源是什么，OpenEmbedded 构建系统都会警告用户，如果某个特定选项没有包含在最终的内核配置中。

By default, in order to not overwhelm the user with configuration warnings, the system only reports missing \"hardware\" options as they could result in a boot failure or indicate that important hardware is not available.

> 默认情况下，为了不让用户被配置警告淹没，系统只报告缺少的"硬件"选项，因为它们可能会导致引导失败或指示重要硬件不可用。

To determine whether or not a given option is \"hardware\" or \"non-hardware\", the kernel Metadata in `yocto-kernel-cache` contains files that classify individual or groups of options as either hardware or non-hardware. To better show this, consider a situation where the `yocto-kernel-cache` contains the following files:

> 要确定给定选项是“硬件”还是“非硬件”，`yocto-kernel-cache` 中的内核元数据包含文件，将单个或多个选项分类为硬件或非硬件。为了更好地展示这一点，考虑一个 `yocto-kernel-cache` 包含以下文件的情况：

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

- `hardware.kcf`: Specifies a list of kernel Kconfig files that contain hardware options only.
- `non-hardware.kcf`: Specifies a list of kernel Kconfig files that contain non-hardware options only.
- `hardware.cfg`: Specifies a list of kernel `CONFIG_` options that are hardware, regardless of whether or not they are within a Kconfig file specified by a hardware or non-hardware Kconfig file (i.e. `hardware.kcf` or `non-hardware.kcf`).

> `hardware.cfg`：指定一组内核 `CONFIG_` 选项，无论它们是否位于由硬件或非硬件 Kconfig 文件（即 `hardware.kcf` 或 `non-hardware.kcf`）指定的 Kconfig 文件中，它们都是硬件。

- `non-hardware.cfg`: Specifies a list of kernel `CONFIG_` options that are not hardware, regardless of whether or not they are within a Kconfig file specified by a hardware or non-hardware Kconfig file (i.e. `hardware.kcf` or `non-hardware.kcf`).

> `- non-hardware.cfg`：指定一个内核 `CONFIG_` 选项列表，它们不是硬件，无论它们是否在由硬件或非硬件 Kconfig 文件（即 `hardware.kcf` 或 `non-hardware.kcf`）指定的 Kconfig 文件中。

Here is a specific example using the `kernel-cache/bsp/mti-malta32/hardware.cfg`:

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

> 审核内核配置会自动检测这些文件（因此文件名必须完全符合这里讨论的内容），并在生成有关最终 ` .config` 文件的警告时使用它们作为输入。

A user-specified kernel Metadata repository, or recipe space feature, can use these same files to classify options that are found within its `.cfg` files as hardware or non-hardware, to prevent the OpenEmbedded build system from producing an error or warning when an option is not in the final `.config` file.

> 用户指定的内核元数据存储库或配方空间功能可以使用这些相同的文件将其 `.cfg` 文件中发现的选项分类为硬件或非硬件，以防止 OpenEmbedded 构建系统在最终的 `.config` 文件中不存在选项时产生错误或警告。
