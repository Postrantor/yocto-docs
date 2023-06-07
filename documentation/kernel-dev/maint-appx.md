---
tip: translate by baidu@2023-06-07 10:57:49
...
---
title: Kernel Maintenance
-------------------------

# Tree Construction

This section describes construction of the Yocto Project kernel source repositories as accomplished by the Yocto Project team to create Yocto Linux kernel repositories. These kernel repositories are found under the heading \"Yocto Linux Kernel\" at :yocto\_[git:%60/](git:%60/)[ and are shipped as part of a Yocto Project release. The team creates these repositories by compiling and executing the set of feature descriptions for every BSP and feature in the product. Those feature descriptions list all necessary patches, configurations, branches, tags, and feature divisions found in a Yocto Linux kernel. Thus, the Yocto Project Linux kernel repository (or tree) and accompanying Metadata in the ]{.title-ref}[yocto-kernel-cache]{.title-ref}\` are built.

> 本节介绍 Yocto 项目团队为创建 Yocto Linux 内核存储库而完成的 Yocto Project 内核源代码存储库的构建。这些内核存储库位于“Yocto Linux kernel”标题下，网址为：Yocto\_[git:%60/](git:%60/)[和作为 Yocto Project 版本的一部分提供。该团队通过编译和执行产品中每个 BSP 和功能的一组功能描述来创建这些存储库。这些功能描述列出了 Yocto Linux 内核中所有必要的补丁、配置、分支、标记和功能划分。因此，Yocto 项目 Linux 内核存储库（或树）以及在]｛.title-ref｝[yocto 内核缓存]｛.title-rev｝\`中附带的元数据。

The existence of these repositories allow you to access and clone a particular Yocto Project Linux kernel repository and use it to build images based on their configurations and features.

> 这些存储库的存在允许您访问和克隆特定的 Yocto Project Linux 内核存储库，并使用它根据其配置和功能构建映像。

You can find the files used to describe all the valid features and BSPs in the Yocto Project Linux kernel in any clone of the Yocto Project Linux kernel source repository and `yocto-kernel-cache` Git trees. For example, the following commands clone the Yocto Project baseline Linux kernel that branches off `linux.org` version 4.12 and the `yocto-kernel-cache`, which contains stores of kernel Metadata:

> 您可以在 Yocto Project Linux 内核源代码库和“Yocto kernel cache”Git 树的任何克隆中找到用于描述 Yocto 项目 Linux 内核中所有有效功能和 BSP 的文件。例如，以下命令克隆 Yocto Project 基线 Linux 内核（从“Linux.org”版本 4.12 分支）和“Yocto 内核缓存”（包含内核元数据存储）：

```
$ git clone git://git.yoctoproject.org/linux-yocto-4.12
$ git clone git://git.yoctoproject.org/linux-kernel-cache
```

For more information on how to set up a local Git repository of the Yocto Project Linux kernel files, see the \"`kernel-dev/common:preparing the build host to work on the kernel`{.interpreted-text role="ref"}\" section.

> 有关如何设置 Yocto Project Linux 内核文件的本地 Git 存储库的更多信息，请参阅\“`kernel dev/common:准备构建主机以在内核上工作”｛.depreted text role=“ref”｝\“一节。

Once you have cloned the kernel Git repository and the cache of Metadata on your local machine, you can discover the branches that are available in the repository using the following Git command:

> 一旦您在本地机器上克隆了内核 Git 存储库和元数据缓存，您就可以使用以下 Git 命令发现存储库中可用的分支：

```
$ git branch -a
```

Checking out a branch allows you to work with a particular Yocto Linux kernel. For example, the following commands check out the \"standard/beagleboard\" branch of the Yocto Linux kernel repository and the \"yocto-4.12\" branch of the `yocto-kernel-cache` repository:

> 签出一个分支可以使用特定的 Yocto Linux 内核。例如，以下命令检查 Yocto Linux 内核存储库的\“standard/beagleboard”分支和“Yocto kernel cache”存储库的“Yocto-4.12”分支：

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

> “yocto kernel cache”存储库中的分支对应于 yocto Linux 内核版本（例如“yocto-4.12\”、“yocto-410\”、“yocto-4.9\”等）。
> :::

Once you have checked out and switched to appropriate branches, you can see a snapshot of all the kernel source files used to build that particular Yocto Linux kernel for a particular board.

> 一旦您签出并切换到适当的分支，您就可以看到用于为特定板构建特定 Yocto Linux 内核的所有内核源文件的快照。

To see the features and configurations for a particular Yocto Linux kernel, you need to examine the `yocto-kernel-cache` Git repository. As mentioned, branches in the `yocto-kernel-cache` repository correspond to Yocto Linux kernel versions (e.g. `yocto-4.12`). Branches contain descriptions in the form of `.scc` and `.cfg` files.

> 要查看特定 Yocto Linux 内核的功能和配置，您需要检查“Yocto 内核缓存”Git 存储库。如前所述，“yocto kernel cache”存储库中的分支对应于 yocto Linux 内核版本（例如“yocto-4.12”）。分支包含“.scc”和“.cfg”文件形式的描述。

You should realize, however, that browsing your local `yocto-kernel-cache` repository for feature descriptions and patches is not an effective way to determine what is in a particular kernel branch. Instead, you should use Git directly to discover the changes in a branch. Using Git is an efficient and flexible way to inspect changes to the kernel.

> 然而，您应该意识到，浏览本地的“yocto 内核缓存”存储库以获取功能描述和补丁并不是确定特定内核分支中的内容的有效方法。相反，您应该直接使用 Git 来发现分支中的更改。使用 Git 是检查内核更改的一种高效而灵活的方式。

::: note
::: title
Note
:::

Ground up reconstruction of the complete kernel tree is an action only taken by the Yocto Project team during an active development cycle. When you create a clone of the kernel Git repository, you are simply making it efficiently available for building and development.

> 完整内核树的地面重建是 Yocto 项目团队在活跃的开发周期中才采取的行动。当您创建内核 Git 存储库的克隆时，您只是将其高效地用于构建和开发。
> :::

The following steps describe what happens when the Yocto Project Team constructs the Yocto Project kernel source Git repository (or tree) found at :yocto\_[git:%60/](git:%60/)\` given the introduction of a new top-level kernel feature or BSP. The following actions effectively provide the Metadata and create the tree that includes the new feature, patch, or BSP:

> 以下步骤描述了 Yocto 项目团队在引入新的顶级内核功能或 BSP 的情况下构建 Yocto Project 内核源 Git 存储库（或树）时会发生的情况，该存储库位于：Yocto\_[Git:%60/](Git:%60/)\`。以下操作有效地提供元数据并创建包含新功能、修补程序或 BSP 的树：

1. *Pass Feature to the OpenEmbedded Build System:* A top-level kernel feature is passed to the kernel build subsystem. Normally, this feature is a BSP for a particular kernel type.

> 1.*将功能传递给 OpenEmbedded 构建系统：*将顶级内核功能传递给内核构建子系统。通常，此功能是用于特定内核类型的 BSP。

2. *Locate Feature:* The file that describes the top-level feature is located by searching these system directories:

> 2.*定位功能：*通过搜索以下系统目录来定位描述顶级功能的文件：

```
-   The in-tree kernel-cache directories, which are located in the :yocto\_<git:%60yocto-kernel-cache> \</yocto-kernel-cache/tree/bsp\>[ repository organized under the \"Yocto Linux Kernel\" heading in the :yocto_git:\`Yocto Project Source Repositories \<\>]{.title-ref}.
-   Areas pointed to by `SRC_URI`{.interpreted-text role="term"} statements found in kernel recipes.

For a typical build, the target of the search is a feature description in an `.scc` file whose name follows this format (e.g. `beaglebone-standard.scc` and `beaglebone-preempt-rt.scc`):

    bsp_root_name-kernel_type.scc
```

3. *Expand Feature:* Once located, the feature description is either expanded into a simple script of actions, or into an existing equivalent script that is already part of the shipped kernel.

> 3.*扩展功能：*定位后，功能描述要么扩展为一个简单的操作脚本，要么扩展为已经是附带内核一部分的现有等效脚本。

4. *Append Extra Features:* Extra features are appended to the top-level feature description. These features can come from the `KERNEL_FEATURES`{.interpreted-text role="term"} variable in recipes.

> 4.*附加额外功能：*额外功能附加到顶级功能描述中。这些功能可以来自配方中的 `KERNEL_FEATURE`｛.explored text role=“term”｝变量。

5. *Locate, Expand, and Append Each Feature:* Each extra feature is located, expanded and appended to the script as described in step three.

> 5.*定位、展开和附加每个功能：*如步骤三所述，每个额外的功能都会定位、展开并附加到脚本中。

6. *Execute the Script:* The script is executed to produce files `.scc` and `.cfg` files in appropriate directories of the `yocto-kernel-cache` repository. These files are descriptions of all the branches, tags, patches and configurations that need to be applied to the base Git repository to completely create the source (build) branch for the new BSP or feature.

> 6.*执行脚本：*执行脚本以在“yocto kernel cache”存储库的适当目录中生成文件“.scc”和“.cfg”文件。这些文件描述了所有分支、标记、修补程序和配置，这些分支、标记和配置需要应用于基本 Git 存储库，以完全创建新 BSP 或功能的源（构建）分支。

7. *Clone Base Repository:* The base repository is cloned, and the actions listed in the `yocto-kernel-cache` directories are applied to the tree.

> 7.*克隆基本存储库：*基本存储库被克隆，“yocto kernel cache”目录中列出的操作将应用于树。

8. *Perform Cleanup:* The Git repositories are left with the desired branches checked out and any required branching, patching and tagging has been performed.

> 8.*执行清理：*Git 存储库留下了所需的分支，并执行了任何所需的分枝、修补和标记。

The kernel tree and cache are ready for developer consumption to be locally cloned, configured, and built into a Yocto Project kernel specific to some target hardware.

> 内核树和缓存已经准备好供开发人员使用，可以在本地克隆、配置并构建到特定于某些目标硬件的 Yocto Project 内核中。

::: note
::: title
Note
:::

- The generated `yocto-kernel-cache` repository adds to the kernel as shipped with the Yocto Project release. Any add-ons and configuration data are applied to the end of an existing branch. The full repository generation that is found in the official Yocto Project kernel repositories at :yocto\_[git:%60/](git:%60/)\` is the combination of all supported boards and configurations.

> -生成的“yocto 内核缓存”存储库将添加到 yocto Project 版本附带的内核中。任何附加组件和配置数据都将应用于现有分支的末尾。Yocto Project 官方内核存储库中的完整存储库生成是所有支持的板和配置的组合。

- The technique the Yocto Project team uses is flexible and allows for seamless blending of an immutable history with additional patches specific to a deployment. Any additions to the kernel become an integrated part of the branches.

> -Yocto 项目团队使用的技术是灵活的，允许将不可变的历史与特定于部署的附加补丁无缝混合。对内核的任何添加都将成为分支的集成部分。

- The full kernel tree that you see on :yocto\_[git:%60/](git:%60/)[ is generated through repeating the above steps for all valid BSPs. The end result is a branched, clean history tree that makes up the kernel for a given release. You can see the script (]{.title-ref}[kgit-scc]{.title-ref}[) responsible for this in the :yocto_git:\`yocto-kernel-tools \</yocto-kernel-tools/tree/tools\>]{.title-ref} repository.

> -您在：yocto\_[git:%60/](git:%60/)[上看到的完整内核树是通过对所有有效 BSP 重复上述步骤生成的。最终结果是一个分支的、干净的历史树，它构成了给定版本的内核。您可以在：yoecto_git:\`yocto kernel tools\</yocto kernel tools/tree/tools\>]｛.title-ref｝储存库。

- The steps used to construct the full kernel tree are the same steps that BitBake uses when it builds a kernel image.

> -用于构建完整内核树的步骤与 BitBake 在构建内核映像时使用的步骤相同。
> :::

# Build Strategy

Once you have cloned a Yocto Linux kernel repository and the cache repository (`yocto-kernel-cache`) onto your development system, you can consider the compilation phase of kernel development, which is building a kernel image. Some prerequisites are validated by the build process before compilation starts:

> 一旦您将 Yocto Linux 内核存储库和缓存存储库（`Yocto kernel cache`）克隆到您的开发系统中，您就可以考虑内核开发的编译阶段，即构建内核映像。编译开始前，构建过程会验证一些先决条件：

- The `SRC_URI`{.interpreted-text role="term"} points to the kernel Git repository.
- A BSP build branch with Metadata exists in the `yocto-kernel-cache` repository. The branch is based on the Yocto Linux kernel version and has configurations and features grouped under the `yocto-kernel-cache/bsp` directory. For example, features and configurations for the BeagleBone Board assuming a `linux-yocto_4.12` kernel reside in the following area of the `yocto-kernel-cache` repository: yocto-kernel-cache/bsp/beaglebone

> -带有元数据的 BSP 构建分支存在于“yocto 内核缓存”存储库中。该分支基于 Yocto Linux 内核版本，其配置和功能分组在“Yocto kernel cache/bbsp”目录下。例如，BeagleBone 板的功能和配置假设“linux-yocto_4.12”内核位于“yocto-kernel cache”存储库的以下区域：yocto-kernel cache/BeagleBone

```
::: note
::: title
Note
:::

In the previous example, the \"yocto-4.12\" branch is checked out in the `yocto-kernel-cache` repository.
:::
```

The OpenEmbedded build system makes sure these conditions are satisfied before attempting compilation. Other means, however, do exist, such as bootstrapping a BSP.

> OpenEmbedded 构建系统确保在尝试编译之前满足这些条件。然而，确实存在其他方式，例如引导 BSP。

Before building a kernel, the build process verifies the tree and configures the kernel by processing all of the configuration \"fragments\" specified by feature descriptions in the `.scc` files. As the features are compiled, associated kernel configuration fragments are noted and recorded in the series of directories in their compilation order. The fragments are migrated, pre-processed and passed to the Linux Kernel Configuration subsystem (`lkc`) as raw input in the form of a `.config` file. The `lkc` uses its own internal dependency constraints to do the final processing of that information and generates the final `.config` file that is used during compilation.

> 在构建内核之前，构建过程会验证树，并通过处理“.scc'”文件中功能描述指定的所有配置“片段”来配置内核。在编译这些特性时，相关的内核配置片段会按其编译顺序记录在一系列目录中。这些片段被迁移、预处理并以“.config”文件的形式作为原始输入传递给 Linux 内核配置子系统（“lkc”）。“lkc”使用其自己的内部依赖关系约束来对该信息进行最终处理，并生成编译过程中使用的最终“.config”文件。

Using the board\'s architecture and other relevant values from the board\'s template, kernel compilation is started and a kernel image is produced.

> 使用板的体系结构和板模板中的其他相关值，启动内核编译并生成内核映像。

The other thing that you notice once you configure a kernel is that the build process generates a build tree that is separate from your kernel\'s local Git source repository tree. This build tree has a name that uses the following form, where `${MACHINE}` is the metadata name of the machine (BSP) and \"kernel_type\" is one of the Yocto Project supported kernel types (e.g. \"standard\"):

> 配置内核后，您会注意到的另一件事是，构建过程会生成一个与内核的本地 Git 源存储库树分离的构建树。此构建树的名称使用以下形式，其中“$｛MACHINE｝”是机器（BSP）的元数据名称，“kernel_type”是 Yocto Project 支持的内核类型之一（例如“standard”）：

```
linux-${MACHINE}-kernel_type-build
```

The existing support in the `kernel.org` tree achieves this default functionality.

> “kernel.org”树中现有的支持实现了这一默认功能。

This behavior means that all the generated files for a particular machine or BSP are now in the build tree directory. The files include the final `.config` file, all the `.o` files, the `.a` files, and so forth. Since each machine or BSP has its own separate `Build Directory`{.interpreted-text role="term"} in its own separate branch of the Git repository, you can easily switch between different builds.

> 这种行为意味着为特定机器或 BSP 生成的所有文件现在都在构建树目录中。这些文件包括最终的“.config”文件、所有的“.o”文件、“.a”文件等等。由于每台机器或 BSP 在其 Git 存储库的单独分支中都有自己单独的“构建目录”｛.depredicted text role=“term”｝，因此您可以轻松地在不同的构建之间切换。
