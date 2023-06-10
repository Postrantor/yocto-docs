---
tip: translate by openai@2023-06-10 10:43:44
...
---
title: Kernel Maintenance
-------------------------

# Tree Construction

This section describes construction of the Yocto Project kernel source repositories as accomplished by the Yocto Project team to create Yocto Linux kernel repositories. These kernel repositories are found under the heading \"Yocto Linux Kernel\" at :yocto\_[git:%60/](git:%60/)[ and are shipped as part of a Yocto Project release. The team creates these repositories by compiling and executing the set of feature descriptions for every BSP and feature in the product. Those feature descriptions list all necessary patches, configurations, branches, tags, and feature divisions found in a Yocto Linux kernel. Thus, the Yocto Project Linux kernel repository (or tree) and accompanying Metadata in the ]{.title-ref}[yocto-kernel-cache]{.title-ref}\` are built.

> 这一节介绍了 Yocto 项目团队如何构建内核源代码仓库，以创建 Yocto Linux 内核仓库。这些内核仓库可以在“Yocto Linux 内核”标题下找到：yocto_[git：%60/]（git：%60/），并作为 Yocto 项目发布的一部分进行发送。团队通过编译和执行每个 BSP 和产品中的功能描述来创建这些仓库。这些功能描述列出了 Yocto Linux 内核中所有必要的补丁、配置、分支、标签和功能划分。因此，Yocto 项目 Linux 内核仓库（或树）及其附带的元数据在 yocto-kernel-cache 中被构建。

The existence of these repositories allow you to access and clone a particular Yocto Project Linux kernel repository and use it to build images based on their configurations and features.

> 这些存储库的存在使您可以访问和克隆特定的 Yocto Project Linux 内核存储库，并使用它来构建基于其配置和功能的映像。

You can find the files used to describe all the valid features and BSPs in the Yocto Project Linux kernel in any clone of the Yocto Project Linux kernel source repository and `yocto-kernel-cache` Git trees. For example, the following commands clone the Yocto Project baseline Linux kernel that branches off `linux.org` version 4.12 and the `yocto-kernel-cache`, which contains stores of kernel Metadata:

> 您可以在 Yocto Project Linux 内核的任何克隆源存储库中找到用于描述所有有效特性和 BSP 的文件，以及 yocto-kernel-cache Git 树。例如，以下命令克隆了基于 linux.org 版本 4.12 的 Yocto Project 基线 Linux 内核，以及包含内核元数据存储的 yocto-kernel-cache：

```
$ git clone git://git.yoctoproject.org/linux-yocto-4.12
$ git clone git://git.yoctoproject.org/linux-kernel-cache
```

For more information on how to set up a local Git repository of the Yocto Project Linux kernel files, see the \"`kernel-dev/common:preparing the build host to work on the kernel`{.interpreted-text role="ref"}\" section.

> 要了解有关如何设置 Yocto Project Linux 内核文件的本地 Git 存储库的更多信息，请参阅“kernel-dev/common：准备构建主机以处理内核”部分。

Once you have cloned the kernel Git repository and the cache of Metadata on your local machine, you can discover the branches that are available in the repository using the following Git command:

> 一旦你在本地机器上克隆了内核 Git 存储库和元数据缓存，你可以使用下面的 Git 命令发现存储库中可用的分支：

```
$ git branch -a
```

Checking out a branch allows you to work with a particular Yocto Linux kernel. For example, the following commands check out the \"standard/beagleboard\" branch of the Yocto Linux kernel repository and the \"yocto-4.12\" branch of the `yocto-kernel-cache` repository:

> 检出一个分支可以让你使用特定的 Yocto Linux 内核。例如，下面的命令检出 Yocto Linux 内核存储库的"standard/beagleboard"分支和"yocto-kernel-cache"存储库的"yocto-4.12"分支：

```
$ cd ~/linux-yocto-4.12
$ git checkout -b my-kernel-4.12 remotes/origin/standard/beagleboard
$ cd ~/linux-kernel-cache
$ git checkout -b my-4.12-metadata remotes/origin/yocto-4.12
```

::: note
::: title
Note
:::

Branches in the `yocto-kernel-cache` repository correspond to Yocto Linux kernel versions (e.g. \"yocto-4.12\", \"yocto-4.10\", \"yocto-4.9\", and so forth).

> 在 `yocto-kernel-cache` 存储库中的分支对应于 Yocto Linux 内核版本（例如“yocto-4.12”、“yocto-4.10”、“yocto-4.9”等）。
> :::

Once you have checked out and switched to appropriate branches, you can see a snapshot of all the kernel source files used to build that particular Yocto Linux kernel for a particular board.

> 一旦您已检出并切换到适当的分支，您就可以看到用于为特定板构建该特定 Yocto Linux 内核的所有内核源文件的快照。

To see the features and configurations for a particular Yocto Linux kernel, you need to examine the `yocto-kernel-cache` Git repository. As mentioned, branches in the `yocto-kernel-cache` repository correspond to Yocto Linux kernel versions (e.g. `yocto-4.12`). Branches contain descriptions in the form of `.scc` and `.cfg` files.

> 要查看特定 Yocto Linux 内核的特性和配置，您需要检查 `yocto-kernel-cache` Git 存储库。正如所提到的，`yocto-kernel-cache` 存储库中的分支对应于 Yocto Linux 内核版本（例如 `yocto-4.12`）。分支中包含以 ` .scc` 和 ` .cfg` 文件形式的描述。

You should realize, however, that browsing your local `yocto-kernel-cache` repository for feature descriptions and patches is not an effective way to determine what is in a particular kernel branch. Instead, you should use Git directly to discover the changes in a branch. Using Git is an efficient and flexible way to inspect changes to the kernel.

> 你应该意识到，浏览本地的 `yocto-kernel-cache` 存储库以查找特性描述和补丁并不是确定某个内核分支中包含什么的有效方式。相反，你应该直接使用 Git 来发现分支中的变化。使用 Git 是检查内核变化的有效且灵活的方法。

::: note
::: title
Note
:::

Ground up reconstruction of the complete kernel tree is an action only taken by the Yocto Project team during an active development cycle. When you create a clone of the kernel Git repository, you are simply making it efficiently available for building and development.

> 重新构建完整的内核树是 Yocto 项目团队在活跃的开发周期中所采取的行动。当您创建内核 Git 存储库的克隆时，您只是使其高效地用于构建和开发。
> :::

The following steps describe what happens when the Yocto Project Team constructs the Yocto Project kernel source Git repository (or tree) found at :yocto\_[git:%60/](git:%60/)\` given the introduction of a new top-level kernel feature or BSP. The following actions effectively provide the Metadata and create the tree that includes the new feature, patch, or BSP:

> 以下步骤描述了当 Yocto 项目团队构建位于 yocto_[git:%60/](git:%60/)的 Yocto 项目内核源 Git 存储库（或树）时发生的情况，这是在引入新的顶级内核功能或 BSP 时。以下动作有效地提供了元数据并创建包含新功能、补丁或 BSP 的树：

1. *Pass Feature to the OpenEmbedded Build System:* A top-level kernel feature is passed to the kernel build subsystem. Normally, this feature is a BSP for a particular kernel type.

> 将特性传递给 OpenEmbedded 构建系统：顶级内核特性被传递给内核构建子系统。通常，这个特性是特定内核类型的 BSP。

2. *Locate Feature:* The file that describes the top-level feature is located by searching these system directories:

   - The in-tree kernel-cache directories, which are located in the :yocto\_[git:%60yocto-kernel-cache](git:%60yocto-kernel-cache) \</yocto-kernel-cache/tree/bsp\>[ repository organized under the \"Yocto Linux Kernel\" heading in the :yocto_git:\`Yocto Project Source Repositories \<\>]{.title-ref}.

> 在树内内核缓存目录位于 Yocto[git:`yocto-kernel-cache`](git:%60yocto-kernel-cache%60) </ yocto-kernel-cache/tree/bsp> 仓库，该仓库位于 Yocto Project 源代码仓库中的“Yocto Linux 内核”标题下。

- Areas pointed to by `SRC_URI`{.interpreted-text role="term"} statements found in kernel recipes.

For a typical build, the target of the search is a feature description in an `.scc` file whose name follows this format (e.g. `beaglebone-standard.scc` and `beaglebone-preempt-rt.scc`):

> 对于一个典型的构建，搜索的目标是 `.scc` 文件中的特征描述，其名称遵循这种格式（例如 `beaglebone-standard.scc` 和 `beaglebone-preempt-rt.scc`）：

```
bsp_root_name-kernel_type.scc
```

3. *Expand Feature:* Once located, the feature description is either expanded into a simple script of actions, or into an existing equivalent script that is already part of the shipped kernel.

> 3. *展开功能：*一旦定位，功能描述要么展开成一系列简单的操作脚本，要么展开成已经包含在发布内核中的现有等效脚本。

4. *Append Extra Features:* Extra features are appended to the top-level feature description. These features can come from the `KERNEL_FEATURES`{.interpreted-text role="term"} variable in recipes.

> 4. *添加额外功能：*额外的功能被添加到顶级功能描述中。这些功能可以来自配方中的 `KERNEL_FEATURES` 变量。

5. *Locate, Expand, and Append Each Feature:* Each extra feature is located, expanded and appended to the script as described in step three.
6. *Execute the Script:* The script is executed to produce files `.scc` and `.cfg` files in appropriate directories of the `yocto-kernel-cache` repository. These files are descriptions of all the branches, tags, patches and configurations that need to be applied to the base Git repository to completely create the source (build) branch for the new BSP or feature.

> 执行脚本：脚本将在 yocto-kernel-cache 存储库的适当目录中生成 `.scc` 和 `.cfg` 文件。这些文件描述了所有需要应用于基础 Git 存储库的分支、标签、补丁和配置，以完全创建新 BSP 或功能的源（构建）分支。

7. *Clone Base Repository:* The base repository is cloned, and the actions listed in the `yocto-kernel-cache` directories are applied to the tree.
8. *Perform Cleanup:* The Git repositories are left with the desired branches checked out and any required branching, patching and tagging has been performed.

> *执行清理：*Git 存储库已经检出所需的分支，并且已经执行了任何必要的分支，补丁和标记。

The kernel tree and cache are ready for developer consumption to be locally cloned, configured, and built into a Yocto Project kernel specific to some target hardware.

> 内核树和缓存已准备好供开发者在本地克隆、配置和构建为针对某些目标硬件的 Yocto 项目内核。

::: note
::: title
Note
:::

- The generated `yocto-kernel-cache` repository adds to the kernel as shipped with the Yocto Project release. Any add-ons and configuration data are applied to the end of an existing branch. The full repository generation that is found in the official Yocto Project kernel repositories at :yocto\_[git:%60/](git:%60/)\` is the combination of all supported boards and configurations.

> 生成的“yocto-kernel-cache”存储库添加到与 Yocto Project 发行版一起提供的内核中。任何附加组件和配置数据都将应用于现有分支的末尾。在官方 Yocto Project 内核存储库中发现的完整存储库生成是所有支持的板和配置的组合：yocto\_[git:%60/](git:%60/)。

- The technique the Yocto Project team uses is flexible and allows for seamless blending of an immutable history with additional patches specific to a deployment. Any additions to the kernel become an integrated part of the branches.

> Yocto 项目团队使用的技术是灵活的，可以无缝地将不可变的历史与特定部署的附加补丁融合在一起。对内核的任何添加都会成为分支的一部分。

- The full kernel tree that you see on :yocto\_[git:%60/](git:%60/)[ is generated through repeating the above steps for all valid BSPs. The end result is a branched, clean history tree that makes up the kernel for a given release. You can see the script (]{.title-ref}[kgit-scc]{.title-ref}[) responsible for this in the :yocto_git:\`yocto-kernel-tools \</yocto-kernel-tools/tree/tools\>]{.title-ref} repository.

> 你在 yocto_git 上看到的完整内核树是通过重复上述步骤为所有有效的 BSP 生成的。最终结果是一个分支清洁的历史树，构成了给定发布版本的内核。你可以在 yocto_git：yocto-kernel-tools/yocto-kernel-tools/tree/tools/仓库中看到负责这一过程的脚本（kgit-scc）。

- The steps used to construct the full kernel tree are the same steps that BitBake uses when it builds a kernel image.
  :::

# Build Strategy

Once you have cloned a Yocto Linux kernel repository and the cache repository (`yocto-kernel-cache`) onto your development system, you can consider the compilation phase of kernel development, which is building a kernel image. Some prerequisites are validated by the build process before compilation starts:

> 一旦您已经克隆了 Yocto Linux 内核存储库和缓存存储库（`yocto-kernel-cache`）到您的开发系统，您就可以考虑内核开发的编译阶段，即构建内核映像。编译开始前，构建过程会验证一些先决条件：

- The `SRC_URI`{.interpreted-text role="term"} points to the kernel Git repository.
- A BSP build branch with Metadata exists in the `yocto-kernel-cache` repository. The branch is based on the Yocto Linux kernel version and has configurations and features grouped under the `yocto-kernel-cache/bsp` directory. For example, features and configurations for the BeagleBone Board assuming a `linux-yocto_4.12` kernel reside in the following area of the `yocto-kernel-cache` repository: yocto-kernel-cache/bsp/beaglebone

> 在 yocto-kernel-cache 存储库中存在一个具有元数据的 BSP 构建分支。该分支基于 Yocto Linux 内核版本，并在 yocto-kernel-cache/bsp 目录下分组了配置和功能。例如，假设使用 linux-yocto_4.12 内核的 BeagleBone 板的功能和配置位于 yocto-kernel-cache 存储库的以下区域：yocto-kernel-cache/bsp/beaglebone。

::: note
::: title
Note
:::

In the previous example, the \"yocto-4.12\" branch is checked out in the `yocto-kernel-cache` repository.
:::

The OpenEmbedded build system makes sure these conditions are satisfied before attempting compilation. Other means, however, do exist, such as bootstrapping a BSP.

> 开放嵌入式构建系统确保在尝试编译之前满足这些条件。然而，其他方式也存在，例如引导 BSP。

Before building a kernel, the build process verifies the tree and configures the kernel by processing all of the configuration \"fragments\" specified by feature descriptions in the `.scc` files. As the features are compiled, associated kernel configuration fragments are noted and recorded in the series of directories in their compilation order. The fragments are migrated, pre-processed and passed to the Linux Kernel Configuration subsystem (`lkc`) as raw input in the form of a `.config` file. The `lkc` uses its own internal dependency constraints to do the final processing of that information and generates the final `.config` file that is used during compilation.

> 在构建内核之前，构建过程会验证树并通过处理 `.scc` 文件中指定的所有配置“片段”来配置内核。随着特性的编译，相关的内核配置片段会被记录在按照编译顺序的一系列目录中。这些片段会被迁移、预处理，并以 `.config` 文件的原始输入形式传递给 Linux 内核配置子系统（`lkc`）。`lkc` 会使用其内部的依赖性约束来对该信息进行最终处理，并生成最终的 `.config` 文件，该文件在编译期间会被使用。

Using the board\'s architecture and other relevant values from the board\'s template, kernel compilation is started and a kernel image is produced.

The other thing that you notice once you configure a kernel is that the build process generates a build tree that is separate from your kernel\'s local Git source repository tree. This build tree has a name that uses the following form, where `${MACHINE}` is the metadata name of the machine (BSP) and \"kernel_type\" is one of the Yocto Project supported kernel types (e.g. \"standard\"):

> 当你配置内核时，你会注意到另一件事，那就是构建过程会生成一个独立于内核本地 Git 源代码仓库树的构建树。这个构建树的名称使用以下形式，其中 `${MACHINE}` 是机器（BSP）的元数据名称，“kernel_type”是 Yocto 项目支持的内核类型之一（例如“标准”）：

```
linux-${MACHINE}-kernel_type-build
```

The existing support in the `kernel.org` tree achieves this default functionality.

This behavior means that all the generated files for a particular machine or BSP are now in the build tree directory. The files include the final `.config` file, all the `.o` files, the `.a` files, and so forth. Since each machine or BSP has its own separate `Build Directory`{.interpreted-text role="term"} in its own separate branch of the Git repository, you can easily switch between different builds.

> 这种行为意味着特定机器或 BSP 的所有生成文件现在都在构建树目录中。文件包括最终的 `.config` 文件，所有的 `.o` 文件，`.a` 文件等等。由于每台机器或 BSP 在其自己的 Git 存储库分支中都有自己的“构建目录”，您可以轻松地在不同的构建之间切换。
