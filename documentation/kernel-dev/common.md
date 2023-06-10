---
tip: translate by baidu@2023-06-07 10:56:09
...
---
title: Common Tasks
-------------------

This chapter presents several common tasks you perform when you work with the Yocto Project Linux kernel. These tasks include preparing your host development system for kernel development, preparing a layer, modifying an existing recipe, patching the kernel, configuring the kernel, iterative development, working with your own sources, and incorporating out-of-tree modules.

> 本章介绍使用 Yocto Project Linux 内核时执行的几个常见任务。这些任务包括为内核开发准备主机开发系统、准备层、修改现有配方、修补内核、配置内核、迭代开发、使用自己的源代码以及合并树外模块。

::: note
::: title
Note
:::

The examples presented in this chapter work with the Yocto Project 2.4 Release and forward.

> 本章中介绍的示例适用于 Yocto Project 2.4 的发布和转发。
> :::

# Preparing the Build Host to Work on the Kernel

Before you can do any kernel development, you need to be sure your build host is set up to use the Yocto Project. For information on how to get set up, see the \"`/dev-manual/start`{.interpreted-text role="doc"}\" section in the Yocto Project Development Tasks Manual. Part of preparing the system is creating a local Git repository of the `Source Directory`{.interpreted-text role="term"} (`poky`) on your system. Follow the steps in the \"``dev-manual/start:cloning the \`\`poky\`\` repository``{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual to set up your Source Directory.

> 在进行任何内核开发之前，您需要确保您的构建主机已设置为使用 Yocto 项目。有关如何设置的信息，请参阅 Yocto 项目开发任务手册中的\“`/dev/manual/start`｛.depreted text role=“doc”｝\”部分。准备系统的一部分是在您的系统上创建一个“源目录”的本地 Git 存储库{.depredicted text role=“term”}（`poky`）。按照 Yocto 项目开发任务手册中“``devmanual/start:cloning the \`\`poky\`\` repository``｛.depredicted text role=“ref”｝”部分中的步骤设置源目录。

::: note
::: title
Note
:::

Be sure you check out the appropriate development branch or you create your local branch by checking out a specific tag to get the desired version of Yocto Project. See the \"`dev-manual/start:checking out by branch in poky`{.interpreted-text role="ref"}\" and \"`dev-manual/start:checking out by tag in poky`{.interpreted-text role="ref"}\" sections in the Yocto Project Development Tasks Manual for more information.

> 请确保您签出了适当的开发分支，或者通过签出特定的标记来创建本地分支，以获得所需的 Yocto 项目版本。有关详细信息，请参阅 Yocto 项目开发任务手册中的\“`dev manual/start:按poky中的分支签出`｛.explored text role=“ref”｝\”和\“`dev manual-start:按poky中的标记签出`{.explered text rol=“ref”}\”部分。
> :::

Kernel development is best accomplished using ``devtool <sdk-manual/extensible:using \`\`devtool\`\` in your sdk workflow>``{.interpreted-text role="ref"} and not through traditional kernel workflow methods. The remainder of this section provides information for both scenarios.

> 内核开发最好使用 ``devtool<sdk manual/extensible：在您的sdk工作流中使用\`\`devtool\`\`>``{.depredicted text role=“ref”}，而不是通过传统的内核工作流方法。本节的其余部分提供了这两种情况的信息。

## Getting Ready to Develop Using `devtool`

Follow these steps to prepare to update the kernel image using `devtool`. Completing this procedure leaves you with a clean kernel image and ready to make modifications as described in the \"``kernel-dev/common:using \`\`devtool\`\` to patch the kernel``{.interpreted-text role="ref"}\" section:

> 按照以下步骤准备使用“devtool”更新内核映像。完成此过程后，您将获得一个干净的内核映像，并可以按照“``kernel dev/common:using \`` devtool\`\` to patch the kernel ``｛.explored text role=“ref”｝\”部分中所述进行修改：

1. *Initialize the BitBake Environment:* you need to initialize the BitBake build environment by sourcing the build environment script (i.e. `structure-core-script`{.interpreted-text role="ref"}):

> 1.*初始化 BitBake 环境：*您需要通过获取构建环境脚本（即“结构核心脚本”｛.depredicted text role=“ref”｝）来初始化 BitBake 构建环境：

```
    $ cd poky
    $ source oe-init-build-env

::: note
::: title
Note
:::

The previous commands assume the `overview-manual/development-environment:yocto project source repositories`{.interpreted-text role="ref"} (i.e. `poky`) have been cloned using Git and the local repository is named \"poky\".
:::
```

2. *Prepare Your local.conf File:* By default, the `MACHINE`{.interpreted-text role="term"} variable is set to \"qemux86-64\", which is fine if you are building for the QEMU emulator in 64-bit mode. However, if you are not, you need to set the `MACHINE`{.interpreted-text role="term"} variable appropriately in your `conf/local.conf` file found in the `Build Directory`{.interpreted-text role="term"} (i.e. `poky/build` in this example).

> 2.*准备您的 local.conf 文件：*默认情况下，`MACHINE`｛.explored text role=“term”｝变量设置为\“qemux86-64\”，如果您在 64 位模式下为 QEMU 模拟器构建，则这是可以的。但是，如果不是，则需要在 `Build Directory` 中的 `conf/local.conf` 文件中适当地设置 `MACHINE`{.expreted text role=“term”}变量（即本例中的 `poky/Build`）。

```
Also, since you are preparing to work on the kernel image, you need to set the `MACHINE_ESSENTIAL_EXTRA_RRECOMMENDS`{.interpreted-text role="term"} variable to include kernel modules.

In this example we wish to build for qemux86 so we must set the `MACHINE`{.interpreted-text role="term"} variable to \"qemux86\" and also add the \"kernel-modules\". As described we do this by appending to `conf/local.conf`:

    MACHINE = "qemux86"
    MACHINE_ESSENTIAL_EXTRA_RRECOMMENDS += "kernel-modules"
```

3. *Create a Layer for Patches:* You need to create a layer to hold patches created for the kernel image. You can use the `bitbake-layers create-layer` command as follows:

> 3.*为补丁创建层：*您需要创建一个层来保存为内核图像创建的补丁。您可以使用“bitbake layers create layer”命令，如下所示：

```
    $ cd poky/build
    $ bitbake-layers create-layer ../../meta-mylayer
    NOTE: Starting bitbake server...
    Add your new layer with 'bitbake-layers add-layer ../../meta-mylayer'
    $

::: note
::: title
Note
:::

For background information on working with common and BSP layers, see the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual and the \"`bsp-guide/bsp:bsp layers`{.interpreted-text role="ref"}\" section in the Yocto Project Board Support (BSP) Developer\'s Guide, respectively. For information on how to use the `bitbake-layers create-layer` command to quickly set up a layer, see the \"`` dev-manual/layers:creating a general layer using the \`\`bitbake-layers\`\` script ``{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.
:::
```

4. *Inform the BitBake Build Environment About Your Layer:* As directed when you created your layer, you need to add the layer to the `BBLAYERS`{.interpreted-text role="term"} variable in the `bblayers.conf` file as follows:

> 4.*通知 BitBake 构建环境关于您的层：*按照创建层时的指示，您需要将层添加到 `BBLAYERS.conf` 文件中的 `BBLAYERS`｛.explored text role=“term”｝变量中，如下所示：

```
    $ cd poky/build
    $ bitbake-layers add-layer ../../meta-mylayer
    NOTE: Starting bitbake server...
    $
```

5. *Build the Clean Image:* The final step in preparing to work on the kernel is to build an initial image using `bitbake`:

> 5.*构建干净的图像：*准备处理内核的最后一步是使用“bitbake”构建初始图像：

```
    $ bitbake core-image-minimal
    Parsing recipes: 100% |##########################################| Time: 0:00:05
    Parsing of 830 .bb files complete (0 cached, 830 parsed). 1299 targets, 47 skipped, 0 masked, 0 errors.
    WARNING: No packages to add, building image core-image-minimal unmodified
    Loading cache: 100% |############################################| Time: 0:00:00
    Loaded 1299 entries from dependency cache.
    NOTE: Resolving any missing task queue dependencies
    Initializing tasks: 100% |#######################################| Time: 0:00:07
    Checking sstate mirror object availability: 100% |###############| Time: 0:00:00
    NOTE: Executing SetScene Tasks
    NOTE: Executing RunQueue Tasks
    NOTE: Tasks Summary: Attempted 2866 tasks of which 2604 didn't need to be rerun and all succeeded.

If you were building for actual hardware and not for emulation, you could flash the image to a USB stick on `/dev/sdd` and boot your device. For an example that uses a Minnowboard, see the :yocto_wiki:[TipsAndTricks/KernelDevelopmentWithEsdk \</TipsAndTricks/KernelDevelopmentWithEsdk\>]{.title-ref} Wiki page.
```

At this point you have set up to start making modifications to the kernel. For a continued example, see the \"``kernel-dev/common:using \`\`devtool\`\` to patch the kernel``{.interpreted-text role="ref"}\" section.

> 在这一点上，您已经设置好开始对内核进行修改。有关继续的示例，请参阅“``kernel dev/common:using \`` devtool\`\` to patch the kernel ``{.depreted text role=“ref”}\”部分。

## Getting Ready for Traditional Kernel Development

Getting ready for traditional kernel development using the Yocto Project involves many of the same steps as described in the previous section. However, you need to establish a local copy of the kernel source since you will be editing these files.

> 使用 Yocto 项目为传统内核开发做好准备涉及到许多与上一节中所述相同的步骤。但是，您需要建立内核源代码的本地副本，因为您将编辑这些文件。

Follow these steps to prepare to update the kernel image using traditional kernel development flow with the Yocto Project. Completing this procedure leaves you ready to make modifications to the kernel source as described in the \"`kernel-dev/common:using traditional kernel development to patch the kernel`{.interpreted-text role="ref"}\" section:

> 按照以下步骤准备使用 Yocto 项目的传统内核开发流程更新内核映像。完成此过程后，您就可以按照\“`kernel dev/common:使用传统内核开发来修补内核”｛.depreted text role=“ref”｝\“部分中的描述对内核源代码进行修改了：

1. *Initialize the BitBake Environment:* Before you can do anything using BitBake, you need to initialize the BitBake build environment by sourcing the build environment script (i.e. `structure-core-script`{.interpreted-text role="ref"}). Also, for this example, be sure that the local branch you have checked out for `poky` is the Yocto Project &DISTRO_NAME; branch. If you need to checkout out the &DISTRO_NAME; branch, see the \"`dev-manual/start:checking out by branch in poky`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual:

> 1.*初始化 BitBake 环境：*在您可以使用 BitBake 执行任何操作之前，您需要通过获取构建环境脚本（即“结构核心脚本”｛.depredicted text role=“ref”｝）来初始化 BitBake 构建环境。此外，对于本例，请确保您为“poky”签出的本地分支机构是 Yocto Project&DISTRO_NAME；树枝如果您需要签出&DISTRO_NAME；分支，请参阅 Yocto 项目开发任务手册中的“`dev manual/start:checking by branch in poky `{.depreted text role=“ref”}\”部分：

```
    $ cd poky
    $ git branch
    master
    * &DISTRO_NAME_NO_CAP;
    $ source oe-init-build-env

::: note
::: title
Note
:::

The previous commands assume the `overview-manual/development-environment:yocto project source repositories`{.interpreted-text role="ref"} (i.e. `poky`) have been cloned using Git and the local repository is named \"poky\".
:::
```

2. *Prepare Your local.conf File:* By default, the `MACHINE`{.interpreted-text role="term"} variable is set to \"qemux86-64\", which is fine if you are building for the QEMU emulator in 64-bit mode. However, if you are not, you need to set the `MACHINE`{.interpreted-text role="term"} variable appropriately in your `conf/local.conf` file found in the `Build Directory`{.interpreted-text role="term"} (i.e. `poky/build` in this example).

> 2.*准备您的 local.conf 文件：*默认情况下，`MACHINE`｛.explored text role=“term”｝变量设置为\“qemux86-64\”，如果您在 64 位模式下为 QEMU 模拟器构建，则这是可以的。但是，如果不是，则需要在 `Build Directory` 中的 `conf/local.conf` 文件中适当地设置 `MACHINE`{.expreted text role=“term”}变量（即本例中的 `poky/Build`）。

```
Also, since you are preparing to work on the kernel image, you need to set the `MACHINE_ESSENTIAL_EXTRA_RRECOMMENDS`{.interpreted-text role="term"} variable to include kernel modules.

In this example we wish to build for qemux86 so we must set the `MACHINE`{.interpreted-text role="term"} variable to \"qemux86\" and also add the \"kernel-modules\". As described we do this by appending to `conf/local.conf`:

    MACHINE = "qemux86"
    MACHINE_ESSENTIAL_EXTRA_RRECOMMENDS += "kernel-modules"
```

3. *Create a Layer for Patches:* You need to create a layer to hold patches created for the kernel image. You can use the `bitbake-layers create-layer` command as follows:

> 3.*为补丁创建层：*您需要创建一个层来保存为内核图像创建的补丁。您可以使用“bitbake layers create layer”命令，如下所示：

```
    $ cd poky/build
    $ bitbake-layers create-layer ../../meta-mylayer
    NOTE: Starting bitbake server...
    Add your new layer with 'bitbake-layers add-layer ../../meta-mylayer'

::: note
::: title
Note
:::

For background information on working with common and BSP layers, see the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual and the \"`bsp-guide/bsp:bsp layers`{.interpreted-text role="ref"}\" section in the Yocto Project Board Support (BSP) Developer\'s Guide, respectively. For information on how to use the `bitbake-layers create-layer` command to quickly set up a layer, see the \"`` dev-manual/layers:creating a general layer using the \`\`bitbake-layers\`\` script ``{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.
:::
```

4. *Inform the BitBake Build Environment About Your Layer:* As directed when you created your layer, you need to add the layer to the `BBLAYERS`{.interpreted-text role="term"} variable in the `bblayers.conf` file as follows:

> 4.*通知 BitBake 构建环境关于您的层：*按照创建层时的指示，您需要将层添加到 `BBLAYERS.conf` 文件中的 `BBLAYERS`｛.explored text role=“term”｝变量中，如下所示：

```
    $ cd poky/build
    $ bitbake-layers add-layer ../../meta-mylayer
    NOTE: Starting bitbake server ...
    $
```

5. *Create a Local Copy of the Kernel Git Repository:* You can find Git repositories of supported Yocto Project kernels organized under \"Yocto Linux Kernel\" in the Yocto Project Source Repositories at :yocto\_[git:%60/](git:%60/)\`.

> 5.*创建内核 Git 存储库的本地副本：*您可以在 Yocto 项目源存储库中的“Yocto Linux Kernel”下找到受支持的 Yocto Project 内核的 Git 存储，网址为：Yocto\_[Git:%60/](Git:%60/)\`。

```
For simplicity, it is recommended that you create your copy of the kernel Git repository outside of the `Source Directory`{.interpreted-text role="term"}, which is usually named `poky`. Also, be sure you are in the `standard/base` branch.

The following commands show how to create a local copy of the `linux-yocto-4.12` kernel and be in the `standard/base` branch:

    $ cd ~
    $ git clone git://git.yoctoproject.org/linux-yocto-4.12 --branch standard/base
    Cloning into 'linux-yocto-4.12'...
    remote: Counting objects: 6097195, done.
    remote: Compressing objects: 100% (901026/901026), done.
    remote: Total 6097195 (delta 5152604), reused 6096847 (delta 5152256)
    Receiving objects: 100% (6097195/6097195), 1.24 GiB | 7.81 MiB/s, done.
    Resolving deltas: 100% (5152604/5152604), done. Checking connectivity... done.
    Checking out files: 100% (59846/59846), done.

::: note
::: title
Note
:::

The `linux-yocto-4.12` kernel can be used with the Yocto Project 2.4 release and forward. You cannot use the `linux-yocto-4.12` kernel with releases prior to Yocto Project 2.4.
:::
```

6. *Create a Local Copy of the Kernel Cache Git Repository:* For simplicity, it is recommended that you create your copy of the kernel cache Git repository outside of the `Source Directory`{.interpreted-text role="term"}, which is usually named `poky`. Also, for this example, be sure you are in the `yocto-4.12` branch.

> 6.*创建内核缓存 Git 存储库的本地副本：*为了简单起见，建议您在“源目录”｛.depredicted text role=“term”｝之外创建内核缓存 Git 存储库副本，该目录通常命名为“poky”。此外，对于这个示例，请确保您处于“yocto-4.12”分支中。

```
The following commands show how to create a local copy of the `yocto-kernel-cache` and switch to the `yocto-4.12` branch:

    $ cd ~
    $ git clone git://git.yoctoproject.org/yocto-kernel-cache --branch yocto-4.12
    Cloning into 'yocto-kernel-cache'...
    remote: Counting objects: 22639, done.
    remote: Compressing objects: 100% (9761/9761), done.
    remote: Total 22639 (delta 12400), reused 22586 (delta 12347)
    Receiving objects: 100% (22639/22639), 22.34 MiB | 6.27 MiB/s, done.
    Resolving deltas: 100% (12400/12400), done.
    Checking connectivity... done.
```

At this point, you are ready to start making modifications to the kernel using traditional kernel development steps. For a continued example, see the \"`kernel-dev/common:using traditional kernel development to patch the kernel`{.interpreted-text role="ref"}\" section.

> 此时，您已经准备好开始使用传统的内核开发步骤对内核进行修改了。有关继续的示例，请参阅“`kernel dev/common:使用传统的内核开发来修补内核`{.depreted text role=“ref”}\”部分。

# Creating and Preparing a Layer

If you are going to be modifying kernel recipes, it is recommended that you create and prepare your own layer in which to do your work. Your layer contains its own `BitBake`{.interpreted-text role="term"} append files (`.bbappend`) and provides a convenient mechanism to create your own recipe files (`.bb`) as well as store and use kernel patch files. For background information on working with layers, see the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 如果你要修改内核配方，建议你创建并准备自己的层来完成你的工作。您的层包含自己的 `BitBake`｛.depredicted text role=“term”｝附加文件（`.bappend`），并提供了一种方便的机制来创建自己的配方文件（`/bb`）以及存储和使用内核补丁文件。有关使用层的背景信息，请参阅 Yocto 项目开发任务手册中的\“`dev manual/layers:understanding and createing layers`{.depreted text role=“ref”}\”一节。

::: note
::: title
Note
:::

The Yocto Project comes with many tools that simplify tasks you need to perform. One such tool is the `bitbake-layers create-layer` command, which simplifies creating a new layer. See the \"``dev-manual/layers:creating a general layer using the \`\`bitbake-layers\`\` script``{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual for information on how to use this script to quick set up a new layer.

> Yocto 项目附带了许多工具，可以简化您需要执行的任务。其中一个工具是“bitbake layers create layer”命令，它简化了创建新层的过程。有关如何使用此脚本快速设置新层的信息，请参阅 Yocto 项目开发任务手册中的“``dev manual/layers:create a general layers using the \`\`bitbake layers\`\script``{.depreted text role=“ref”}\”一节。
> :::

To better understand the layer you create for kernel development, the following section describes how to create a layer without the aid of tools. These steps assume creation of a layer named `mylayer` in your home directory:

> 为了更好地理解为内核开发创建的层，以下部分描述了如何在没有工具帮助的情况下创建层。这些步骤假定在主目录中创建一个名为“mylayer”的层：

1. *Create Structure*: Create the layer\'s structure:

> 1.*创建结构*：创建层的结构：

```
    $ mkdir meta-mylayer
    $ mkdir meta-mylayer/conf
    $ mkdir meta-mylayer/recipes-kernel
    $ mkdir meta-mylayer/recipes-kernel/linux
    $ mkdir meta-mylayer/recipes-kernel/linux/linux-yocto

The `conf` directory holds your configuration files, while the `recipes-kernel` directory holds your append file and eventual patch files.
```

2. *Create the Layer Configuration File*: Move to the `meta-mylayer/conf` directory and create the `layer.conf` file as follows:

> 2.*创建层配置文件*：移动到“meta mylayer/conf”目录并创建“Layer.conf”文件，如下所示：

```
    # We have a conf and classes directory, add to BBPATH
    BBPATH .= ":${LAYERDIR}"

    # We have recipes-* directories, add to BBFILES
    BBFILES += "${LAYERDIR}/recipes-*/*/*.bb \
                ${LAYERDIR}/recipes-*/*/*.bbappend"

    BBFILE_COLLECTIONS += "mylayer"
    BBFILE_PATTERN_mylayer = "^${LAYERDIR}/"
    BBFILE_PRIORITY_mylayer = "5"

Notice `mylayer` as part of the last three statements.
```

3. *Create the Kernel Recipe Append File*: Move to the `meta-mylayer/recipes-kernel/linux` directory and create the kernel\'s append file. This example uses the `linux-yocto-4.12` kernel. Thus, the name of the append file is `linux-yocto_4.12.bbappend`:

> 3.*创建内核配方附加文件*：移动到“meta mylayer/precipes Kernel/linux”目录并创建内核的附加文件。此示例使用“linux-yoct-4.12”内核。因此，追加文件的名称为“linux-octo_4.12.bappend”：

```
    FILESEXTRAPATHS:prepend := "${THISDIR}/${PN}:"

    SRC_URI += "file://patch-file-one.patch"
    SRC_URI += "file://patch-file-two.patch"
    SRC_URI += "file://patch-file-three.patch"

The `FILESEXTRAPATHS`{.interpreted-text role="term"} and `SRC_URI`{.interpreted-text role="term"} statements enable the OpenEmbedded build system to find patch files. For more information on using append files, see the \"`dev-manual/layers:appending other layers metadata with your layer`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.
```

# Modifying an Existing Recipe

In many cases, you can customize an existing linux-yocto recipe to meet the needs of your project. Each release of the Yocto Project provides a few Linux kernel recipes from which you can choose. These are located in the `Source Directory`{.interpreted-text role="term"} in `meta/recipes-kernel/linux`.

> 在许多情况下，您可以自定义现有的 linux yocto 配方来满足您的项目需求。Yocto 项目的每个版本都提供了一些 Linux 内核配方，您可以从中进行选择。它们位于 `meta/precipes kernel/linux` 中的 `Source Directory`｛.depreced text role=“term”｝中。

Modifying an existing recipe can consist of the following:

> 修改现有配方可以包括以下内容：

- `kernel-dev/common:creating the append file`{.interpreted-text role="ref"}
- `kernel-dev/common:applying patches`{.interpreted-text role="ref"}
- `kernel-dev/common:changing the configuration`{.interpreted-text role="ref"}

Before modifying an existing recipe, be sure that you have created a minimal, custom layer from which you can work. See the \"`kernel-dev/common:creating and preparing a layer`{.interpreted-text role="ref"}\" section for information.

> 在修改现有配方之前，请确保您已经创建了一个可以使用的最小自定义层。有关信息，请参阅“`kernel dev/common:创建和准备层`{.depreted text role=“ref”}\”一节。

## Creating the Append File

You create this file in your custom layer. You also name it accordingly based on the linux-yocto recipe you are using. For example, if you are modifying the `meta/recipes-kernel/linux/linux-yocto_4.12.bb` recipe, the append file will typically be located as follows within your custom layer:

> 您可以在自定义图层中创建此文件。您还可以根据您正在使用的 linux yocto 配方对其进行相应的命名。例如，如果您正在修改 `meta/precipes kernel/linux/linux-octo_4.12.bb` 配方，则附加文件通常位于您的自定义层中，如下所示：

```none
your-layer/recipes-kernel/linux/linux-yocto_4.12.bbappend
```

The append file should initially extend the `FILESPATH`{.interpreted-text role="term"} search path by prepending the directory that contains your files to the `FILESEXTRAPATHS`{.interpreted-text role="term"} variable as follows:

> 附加文件最初应扩展 `FILESPATH`｛.respered text role=“term”｝搜索路径，方法是将包含文件的目录置于 `FILESEXTRAPATHS`｛.espered text role=“term“｝变量前，如下所示：

```
FILESEXTRAPATHS:prepend := "${THISDIR}/${PN}:"
```

The path `${``THISDIR`{.interpreted-text role="term"}`}/${``PN`{.interpreted-text role="term"}`}` expands to \"linux-yocto\" in the current directory for this example. If you add any new files that modify the kernel recipe and you have extended `FILESPATH`{.interpreted-text role="term"} as described above, you must place the files in your layer in the following area:

> 在本例中，路径 `$｛``THISDIR`｛.explored text role=“term”｝`｝/$｛` `PN`｛.explored text role=”term“｝`}` 在当前目录中扩展为\“linux yocto\”。如果您添加了任何修改内核配方的新文件，并且您已经如上所述扩展了 `FILESPATH`｛.explored text role=“term”｝，则必须将文件放在以下区域的层中：

```
your-layer/recipes-kernel/linux/linux-yocto/
```

::: note
::: title
Note
:::

If you are working on a new machine Board Support Package (BSP), be sure to refer to the `/bsp-guide/index`{.interpreted-text role="doc"}.

> 如果您正在使用一个新的机器板支持包（BSP），请务必参阅 `nbsp guide/index`｛.depreted text role=“doc”｝。
> :::

As an example, consider the following append file used by the BSPs in `meta-yocto-bsp`:

> 例如，考虑 bsp 在“meta yocto bsp”中使用的以下附加文件：

```none
meta-yocto-bsp/recipes-kernel/linux/linux-yocto_4.12.bbappend
```

Here are the contents of this file. Be aware that the actual commit ID strings in this example listing might be different than the actual strings in the file from the `meta-yocto-bsp` layer upstream:

> 这是这个文件的内容。请注意，此示例列表中的实际提交 ID 字符串可能与上游“meta yocto bsp”层文件中的实际字符串不同：

```
KBRANCH:genericx86  = "standard/base"
KBRANCH:genericx86-64  = "standard/base"

KMACHINE:genericx86 ?= "common-pc"
KMACHINE:genericx86-64 ?= "common-pc-64"
KBRANCH:edgerouter = "standard/edgerouter"
KBRANCH:beaglebone = "standard/beaglebone"

SRCREV_machine:genericx86    ?= "d09f2ce584d60ecb7890550c22a80c48b83c2e19"
SRCREV_machine:genericx86-64 ?= "d09f2ce584d60ecb7890550c22a80c48b83c2e19"
SRCREV_machine:edgerouter ?= "b5c8cfda2dfe296410d51e131289fb09c69e1e7d"
SRCREV_machine:beaglebone ?= "b5c8cfda2dfe296410d51e131289fb09c69e1e7d"


COMPATIBLE_MACHINE:genericx86 = "genericx86"
COMPATIBLE_MACHINE:genericx86-64 = "genericx86-64"
COMPATIBLE_MACHINE:edgerouter = "edgerouter"
COMPATIBLE_MACHINE:beaglebone = "beaglebone"

LINUX_VERSION:genericx86 = "4.12.7"
LINUX_VERSION:genericx86-64 = "4.12.7"
LINUX_VERSION:edgerouter = "4.12.10"
LINUX_VERSION:beaglebone = "4.12.10"
```

This append file contains statements used to support several BSPs that ship with the Yocto Project. The file defines machines using the `COMPATIBLE_MACHINE`{.interpreted-text role="term"} variable and uses the `KMACHINE`{.interpreted-text role="term"} variable to ensure the machine name used by the OpenEmbedded build system maps to the machine name used by the Linux Yocto kernel. The file also uses the optional `KBRANCH`{.interpreted-text role="term"} variable to ensure the build process uses the appropriate kernel branch.

> 这个附加文件包含用于支持 Yocto 项目附带的几个 BSP 的语句。该文件使用 `COMPATIBLEMACHINE`｛.depreced text role=“term”｝变量定义机器，并使用 `KMACHINE`｛.epreced textrole=”term“｝变量确保 OpenEmbedded 构建系统使用的机器名称映射到 Linux Yocto 内核使用的机器名。该文件还使用可选的 `KBRANCH`｛.explored text role=“term”｝变量来确保构建过程使用适当的内核分支。

Although this particular example does not use it, the `KERNEL_FEATURES`{.interpreted-text role="term"} variable could be used to enable features specific to the kernel. The append file points to specific commits in the `Source Directory`{.interpreted-text role="term"} Git repository and the `meta` Git repository branches to identify the exact kernel needed to build the BSP.

> 尽管这个特定的例子没有使用它，但 `KERNEL_FEATURE`｛.explored text role=“term”｝变量可以用于启用内核特定的功能。附加文件指向“源目录”｛.explored text role=“term”｝Git 存储库和“meta”Git 存储库中的特定提交，以确定构建 BSP 所需的确切内核。

One thing missing in this particular BSP, which you will typically need when developing a BSP, is the kernel configuration file (`.config`) for your BSP. When developing a BSP, you probably have a kernel configuration file or a set of kernel configuration files that, when taken together, define the kernel configuration for your BSP. You can accomplish this definition by putting the configurations in a file or a set of files inside a directory located at the same level as your kernel\'s append file and having the same name as the kernel\'s main recipe file. With all these conditions met, simply reference those files in the `SRC_URI`{.interpreted-text role="term"} statement in the append file.

> 在开发 BSP 时，您通常需要在这个特定的 BSP 中缺少一件事，那就是 BSP 的内核配置文件（“.config”）。在开发 BSP 时，您可能有一个内核配置文件或一组内核配置文件，当这些文件结合在一起时，可以定义 BSP 的内核配置。您可以通过将配置放在一个文件或一组文件中来完成此定义，这些文件位于与内核的附加文件相同级别的目录中，并且与内核的主配方文件具有相同的名称。在满足所有这些条件的情况下，只需在附加文件中的 `SRC_URI`｛.explored text role=“term”｝语句中引用这些文件。

For example, suppose you had some configuration options in a file called `network_configs.cfg`. You can place that file inside a directory named `linux-yocto` and then add a `SRC_URI`{.interpreted-text role="term"} statement such as the following to the append file. When the OpenEmbedded build system builds the kernel, the configuration options are picked up and applied:

> 例如，假设您在一个名为“network_configs.cfg”的文件中有一些配置选项。您可以将该文件放置在名为“linux yocto”的目录中，然后在附加文件中添加一个“SRC_URI”{.depreced text role=“term”}语句，例如以下语句。当 OpenEmbedded 构建系统构建内核时，会选择并应用配置选项：

```
SRC_URI += "file://network_configs.cfg"
```

To group related configurations into multiple files, you perform a similar procedure. Here is an example that groups separate configurations specifically for Ethernet and graphics into their own files and adds the configurations by using a `SRC_URI`{.interpreted-text role="term"} statement like the following in your append file:

> 要将相关配置分组到多个文件中，可以执行类似的过程。下面是一个示例，它将专门针对以太网和图形的单独配置分组到它们自己的文件中，并通过在附加文件中使用 `SRC_URI`｛.explored text role=“term”｝语句添加配置，如下所示：

```
SRC_URI += "file://myconfig.cfg \
            file://eth.cfg \
            file://gfx.cfg"
```

Another variable you can use in your kernel recipe append file is the `FILESEXTRAPATHS`{.interpreted-text role="term"} variable. When you use this statement, you are extending the locations used by the OpenEmbedded system to look for files and patches as the recipe is processed.

> 您可以在内核配方附加文件中使用的另一个变量是 `FILESEXTRAPATHS`｛.respered text role=“term”｝变量。当您使用此语句时，您正在扩展 OpenEmbedded 系统使用的位置，以便在处理配方时查找文件和补丁。

::: note
::: title
Note
:::

There are other ways of grouping and defining configuration options. For example, if you are working with a local clone of the kernel repository, you could checkout the kernel\'s `meta` branch, make your changes, and then push the changes to the local bare clone of the kernel. The result is that you directly add configuration options to the `meta` branch for your BSP. The configuration options will likely end up in that location anyway if the BSP gets added to the Yocto Project.

> 还有其他方式可以对配置选项进行分组和定义。例如，如果您正在使用内核存储库的本地克隆，您可以签出内核的“元”分支，进行更改，然后将更改推送到内核的本地裸克隆。结果是，您可以直接将配置选项添加到 BSP 的“元”分支中。如果 BSP 被添加到 Yocto 项目中，配置选项很可能最终会出现在那个位置。

In general, however, the Yocto Project maintainers take care of moving the `SRC_URI`{.interpreted-text role="term"}-specified configuration options to the kernel\'s `meta` branch. Not only is it easier for BSP developers not to have to put those configurations in the branch, but having the maintainers do it allows them to apply \'global\' knowledge about the kinds of common configuration options multiple BSPs in the tree are typically using. This allows for promotion of common configurations into common features.

> 然而，一般来说，Yocto 项目的维护人员会负责将“SRC_URI”｛.explored text role=“term”｝指定的配置选项移动到内核的“meta”分支。BSP 开发人员不仅更容易不必将这些配置放在分支中，而且让维护人员这样做可以让他们应用关于树中多个 BSP 通常使用的常见配置选项的“全局”知识。这允许将通用配置提升为通用功能。
> :::

## Applying Patches

If you have a single patch or a small series of patches that you want to apply to the Linux kernel source, you can do so just as you would with any other recipe. You first copy the patches to the path added to `FILESEXTRAPATHS`{.interpreted-text role="term"} in your `.bbappend` file as described in the previous section, and then reference them in `SRC_URI`{.interpreted-text role="term"} statements.

> 如果您有一个补丁或一小系列补丁想要应用到 Linux 内核源代码，那么您可以像使用任何其他配方一样执行此操作。如前一节所述，您首先将修补程序复制到添加到 `.bappend` 文件中 `FILESEXTRAPATHS`｛.expected text role=“term”｝的路径，然后在 `SRC_URI`｛.dexpected textrole=”term“｝语句中引用它们。

For example, you can apply a three-patch series by adding the following lines to your linux-yocto `.bbappend` file in your layer:

> 例如，您可以通过将以下行添加到您所在层中的 linux-yocto `.bappend` 文件中来应用三个补丁系列：

```
SRC_URI += "file://0001-first-change.patch"
SRC_URI += "file://0002-second-change.patch"
SRC_URI += "file://0003-third-change.patch"
```

The next time you run BitBake to build the Linux kernel, BitBake detects the change in the recipe and fetches and applies the patches before building the kernel.

> 下次运行 BitBake 构建 Linux 内核时，BitBake 会检测到配方中的更改，并在构建内核之前获取并应用补丁。

For a detailed example showing how to patch the kernel using `devtool`, see the \"``kernel-dev/common:using \`\`devtool\`\` to patch the kernel``{.interpreted-text role="ref"}\" and \"`kernel-dev/common:using traditional kernel development to patch the kernel`{.interpreted-text role="ref"}\" sections.

> 有关如何使用“devtool”修补内核的详细示例，请参阅“``kernel dev/common:使用`` devtool\`\`修补内核 ``｛.depreted text role=“ref”｝”和“`kernel dev/common：使用传统内核开发修补内核`{.depreted textrole=”ref“｝”部分。

## Changing the Configuration

You can make wholesale or incremental changes to the final `.config` file used for the eventual Linux kernel configuration by including a `defconfig` file and by specifying configuration fragments in the `SRC_URI`{.interpreted-text role="term"} to be applied to that file.

> 您可以对用于最终 Linux 内核配置的最终 `.config` 文件进行大规模或增量更改，方法是包括 `defconfig` 文件，并在要应用于该文件的 `SRC_URI`{.expreted text role=“term”}中指定配置片段。

If you have a complete, working Linux kernel `.config` file you want to use for the configuration, as before, copy that file to the appropriate `${PN}` directory in your layer\'s `recipes-kernel/linux` directory, and rename the copied file to \"defconfig\". Then, add the following lines to the linux-yocto `.bbappend` file in your layer:

> 如果您有一个完整的、可工作的 Linux 内核“.config”文件要用于配置，如前所述，请将该文件复制到层的“recipes kernel/Linux”目录中相应的“$｛PN｝”目录中，并将复制的文件重命名为“defconfig\”。然后，将以下行添加到层中的 linux-yocto `.bappend` 文件中：

```
FILESEXTRAPATHS:prepend := "${THISDIR}/${PN}:"
SRC_URI += "file://defconfig"
```

The `SRC_URI`{.interpreted-text role="term"} tells the build system how to search for the file, while the `FILESEXTRAPATHS`{.interpreted-text role="term"} extends the `FILESPATH`{.interpreted-text role="term"} variable (search directories) to include the `${PN}` directory you created to hold the configuration changes.

> `SRC_URI`{.interplated-text role=“term”}告诉生成系统如何搜索该文件，而 `FILESEXTRAPATHS`{.enterplated_ted-text 角色=“tern”}扩展了 `FILESPATH`{.einterpreted-text role=”term“}变量（搜索目录），以包括为保存配置更改而创建的 `${PN}` 目录。

You can also use a regular `defconfig` file, as generated by the `ref-tasks-savedefconfig`{.interpreted-text role="ref"} task instead of a complete `.config` file. This only specifies the non-default configuration values. You need to additionally set `KCONFIG_MODE`{.interpreted-text role="term"} in the linux-yocto `.bbappend` file in your layer:

> 您还可以使用由 `ref tasks savedefconfig`｛.respered text role=“ref”｝任务生成的常规 `defconfig` 文件，而不是完整的 `.config` 文件。这仅指定非默认配置值。您需要在您的层中的 linux yocto `.bappend` 文件中额外设置 `KCONFIG_MODE`{.depreted text role=“term”}：

```
KCONFIG_MODE = "alldefconfig"
```

::: note
::: title
Note
:::

The build system applies the configurations from the `defconfig` file before applying any subsequent configuration fragments. The final kernel configuration is a combination of the configurations in the `defconfig` file and any configuration fragments you provide. You need to realize that if you have any configuration fragments, the build system applies these on top of and after applying the existing `defconfig` file configurations.

> 在应用任何后续配置片段之前，构建系统将应用“defconfig”文件中的配置。最终的内核配置是“defconfig”文件中的配置和您提供的任何配置片段的组合。您需要意识到，如果您有任何配置片段，构建系统会在应用现有的“defconfig”文件配置之前和之后应用这些片段。
> :::

Generally speaking, the preferred approach is to determine the incremental change you want to make and add that as a configuration fragment. For example, if you want to add support for a basic serial console, create a file named `8250.cfg` in the `${PN}` directory with the following content (without indentation):

> 一般来说，首选的方法是确定要进行的增量更改，并将其添加为配置片段。例如，如果要添加对基本串行控制台的支持，请在“$｛PN｝”目录中创建一个名为“8250.cfg”的文件，其中包含以下内容（不带缩进）：

```
CONFIG_SERIAL_8250=y
CONFIG_SERIAL_8250_CONSOLE=y
CONFIG_SERIAL_8250_PCI=y
CONFIG_SERIAL_8250_NR_UARTS=4
CONFIG_SERIAL_8250_RUNTIME_UARTS=4
CONFIG_SERIAL_CORE=y
CONFIG_SERIAL_CORE_CONSOLE=y
```

Next, include this configuration fragment and extend the `FILESPATH`{.interpreted-text role="term"} variable in your `.bbappend` file:

> 接下来，包括此配置片段，并扩展 `.bappend` 文件中的 `FILESPATH`｛.respered text role=“term”｝变量：

```
FILESEXTRAPATHS:prepend := "${THISDIR}/${PN}:"
SRC_URI += "file://8250.cfg"
```

The next time you run BitBake to build the Linux kernel, BitBake detects the change in the recipe and fetches and applies the new configuration before building the kernel.

> 下次运行 BitBake 构建 Linux 内核时，BitBake 会检测到配方中的更改，并在构建内核之前获取并应用新配置。

For a detailed example showing how to configure the kernel, see the \"`kernel-dev/common:configuring the kernel`{.interpreted-text role="ref"}\" section.

> 有关如何配置内核的详细示例，请参阅“`kernel dev/common:configurating the kernel`｛.depreted text role=“ref”｝\”一节。

## Using an \"In-Tree\"  `defconfig` File

It might be desirable to have kernel configuration fragment support through a `defconfig` file that is pulled from the kernel source tree for the configured machine. By default, the OpenEmbedded build system looks for `defconfig` files in the layer used for Metadata, which is \"out-of-tree\", and then configures them using the following:

> 可能需要通过从已配置机器的内核源树中提取的“defconfig”文件来支持内核配置片段。默认情况下，OpenEmbedded 构建系统在用于元数据的层中查找“defconfig”文件，该层为“树外”，然后使用以下方法对其进行配置：

```
SRC_URI += "file://defconfig"
```

If you do not want to maintain copies of `defconfig` files in your layer but would rather allow users to use the default configuration from the kernel tree and still be able to add configuration fragments to the `SRC_URI`{.interpreted-text role="term"} through, for example, append files, you can direct the OpenEmbedded build system to use a `defconfig` file that is \"in-tree\".

> 如果您不想在您的层中维护 `defconfig` 文件的副本，而是希望允许用户使用内核树中的默认配置，并且仍然能够通过例如附加文件将配置片段添加到 `SRC_URI`｛.explored text role=“term”｝，则可以指示 OpenEmbedded 构建系统使用“在树中”的 `defconfig'文件。

To specify an \"in-tree\" `defconfig` file, use the following statement form:

> 要指定“in tree”`defconfig` 文件，请使用以下语句形式：

```
KBUILD_DEFCONFIG:<machine> ?= "defconfig_file"
```

Here is an example that assigns the `KBUILD_DEFCONFIG`{.interpreted-text role="term"} variable utilizing an override for the \"raspberrypi2\" `MACHINE`{.interpreted-text role="term"} and provides the path to the \"in-tree\" `defconfig` file to be used for a Raspberry Pi 2, which is based on the Broadcom 2708/2709 chipset:

> 以下是一个示例，该示例利用对\“raspberrypi2\”`MACHINE` 的重写来分配 `KBUILD_DEFCONFIG`｛.respered text role=“term”｝变量，并提供用于基于 Broadcom 2708/2709 芯片组的 Raspberry Pi 2 的\“in tree\”`DEFCONFIG` 文件的路径：

```
KBUILD_DEFCONFIG:raspberrypi2 ?= "bcm2709_defconfig"
```

Aside from modifying your kernel recipe and providing your own `defconfig` file, you need to be sure no files or statements set `SRC_URI`{.interpreted-text role="term"} to use a `defconfig` other than your \"in-tree\" file (e.g. a kernel\'s `linux-`[machine]{.title-ref}`.inc` file). In other words, if the build system detects a statement that identifies an \"out-of-tree\" `defconfig` file, that statement will override your `KBUILD_DEFCONFIG`{.interpreted-text role="term"} variable.

> 除了修改内核配方和提供自己的“defconfig”文件外，您还需要确保没有文件或语句集 `SRC_URI`{.tinterpreted-text role=“term”}来使用“树内”文件以外的“defconfig `”（例如，内核的` linux-`[machine]{.title-ref}`.inc `文件）。换言之，如果生成系统检测到一条标识“树外”` defconfig `文件的语句，该语句将覆盖` KBUILD_defconfig`｛.depredicted text role=“term”｝变量。

See the `KBUILD_DEFCONFIG`{.interpreted-text role="term"} variable description for more information.

> 有关详细信息，请参阅 `KBUILD_DEFCONFIG`｛.explored text role=“term”｝变量描述。

# Using `devtool` to Patch the Kernel

The steps in this procedure show you how you can patch the kernel using `devtool`.

> 此过程中的步骤向您展示了如何使用“devtool”修补内核。

::: note
::: title
Note
:::

Before attempting this procedure, be sure you have performed the steps to get ready for updating the kernel as described in the \"``kernel-dev/common:getting ready to develop using \`\`devtool\`\` ``{.interpreted-text role="ref"}\" section.

> 在尝试此过程之前，请确保您已经执行了“``kernel dev/common:geting ready to development using \```devtool\``”一节中所述的准备更新内核的步骤。
> :::

Patching the kernel involves changing or adding configurations to an existing kernel, changing or adding recipes to the kernel that are needed to support specific hardware features, or even altering the source code itself.

> 修补内核包括更改或添加现有内核的配置，更改或添加支持特定硬件功能所需的内核配方，甚至更改源代码本身。

This example creates a simple patch by adding some QEMU emulator console output at boot time through `printk` statements in the kernel\'s `calibrate.c` source code file. Applying the patch and booting the modified image causes the added messages to appear on the emulator\'s console. The example is a continuation of the setup procedure found in the \"``kernel-dev/common:getting ready to develop using \`\`devtool\`\` ``{.interpreted-text role="ref"}\" Section.

> 这个示例创建了一个简单的补丁，通过在内核的“calible.c”源代码文件中的“printk”语句在启动时添加一些 QEMU 模拟器控制台输出。应用修补程序并启动修改后的映像会导致添加的消息出现在模拟器的控制台上。该示例是\“``kernel dev/common:准备使用\`` devtool\````｛.depredicted text role=”ref“｝\”部分中设置过程的延续。

1. *Check Out the Kernel Source Files:* First you must use `devtool` to checkout the kernel source code in its workspace.

> 1.*检查内核源文件：*首先必须使用“devtool”在其工作区中检查内核源代码。

```
::: note
::: title
Note
:::

See this step in the \"`` kernel-dev/common:getting ready to develop using \`\`devtool\`\` ``{.interpreted-text role="ref"}\" section for more information.
:::

Use the following `devtool` command to check out the code:

    $ devtool modify linux-yocto

::: note
::: title
Note
:::

During the checkout operation, there is a bug that could cause errors such as the following:

``` none
ERROR: Taskhash mismatch 2c793438c2d9f8c3681fd5f7bc819efa versus
       be3a89ce7c47178880ba7bf6293d7404 for
       /path/to/esdk/layers/poky/meta/recipes-kernel/linux/linux-yocto_4.10.bb.do_unpack
```

You can safely ignore these messages. The source code is correctly checked out.
:::

```

2. *Edit the Source Files* Follow these steps to make some simple changes to the source files:

> 2.*编辑源文件*按照以下步骤对源文件进行一些简单的更改：

```

1. *Change the working directory*: In the previous step, the output noted where you can find the source files (e.g. `poky_sdk/workspace/sources/linux-yocto`). Change to where the kernel source code is before making your edits to the `calibrate.c` file:

   ```
   $ cd poky_sdk/workspace/sources/linux-yocto
   ```
2. *Edit the source file*: Edit the `init/calibrate.c` file to have the following changes:

   ```
   void calibrate_delay(void)
   {
       unsigned long lpj;
       static bool printed;
       int this_cpu = smp_processor_id();

       printk("*************************************\n");
       printk("*                                   *\n");
       printk("*        HELLO YOCTO KERNEL         *\n");
       printk("*                                   *\n");
       printk("*************************************\n");

       if (per_cpu(cpu_loops_per_jiffy, this_cpu)) {
             .
             .
             .
   ```

```

3. *Build the Updated Kernel Source:* To build the updated kernel source, use `devtool`:

> 3.*构建更新的内核源：*要构建更新的核心源，请使用“devtool”：

```

```
$ devtool build linux-yocto
```

```

4. *Create the Image With the New Kernel:* Use the `devtool build-image` command to create a new image that has the new kernel:

> 4.*使用新内核创建映像：*使用“devtool build Image”命令创建具有新内核的新映像：

```

```
$ cd ~
$ devtool build-image core-image-minimal
```

::: note
::: title
Note
:::

If the image you originally created resulted in a Wic file, you can use an alternate method to create the new image with the updated kernel. For an example, see the steps in the :yocto_wiki:[TipsAndTricks/KernelDevelopmentWithEsdk \</TipsAndTricks/KernelDevelopmentWithEsdk\>]{.title-ref} Wiki Page.
:::

```

5. *Test the New Image:* For this example, you can run the new image using QEMU to verify your changes:

> 5.*测试新映像：*对于本例，您可以使用 QEMU 运行新映像来验证您的更改：

```

1. *Boot the image*: Boot the modified image in the QEMU emulator using this command:

   ```
   $ runqemu qemux86
   ```
2. *Verify the changes*: Log into the machine using `root` with no password and then use the following shell command to scroll through the console\'s boot output.

   ```none
   # dmesg | less
   ```

   You should see the results of your `printk` statements as part of the output when you scroll down the console window.

```

6. *Stage and commit your changes*: Change your working directory to where you modified the `calibrate.c` file and use these Git commands to stage and commit your changes:

> 6.*暂存并提交您的更改*：将您的工作目录更改为您修改“calible.c”文件的位置，并使用以下 Git 命令暂存并提交更改：

```

```
$ cd poky_sdk/workspace/sources/linux-yocto
$ git status
$ git add init/calibrate.c
$ git commit -m "calibrate: Add printk example"
```

```

7. *Export the Patches and Create an Append File:* To export your commits as patches and create a `.bbappend` file, use the following command. This example uses the previously established layer named `meta-mylayer`:

> 7.*导出补丁并创建附加文件：*要将提交导出为补丁并创建 `.bappend` 文件，请使用以下命令。此示例使用了先前建立的名为“meta mylayer”的层：

```

```
$ devtool finish linux-yocto ~/meta-mylayer
```

::: note
::: title
Note
:::

See Step 3 of the \"``kernel-dev/common:getting ready to develop using \`\`devtool\`\` ``{.interpreted-text role="ref"}\" section for information on setting up this layer.
:::

Once the command finishes, the patches and the `.bbappend` file are located in the `~/meta-mylayer/recipes-kernel/linux` directory.

```

8. *Build the Image With Your Modified Kernel:* You can now build an image that includes your kernel patches. Execute the following command from your `Build Directory`{.interpreted-text role="term"} in the terminal set up to run BitBake:

> 8.*使用修改后的内核构建镜像：*您现在可以构建一个包含内核补丁的镜像。在设置为运行 BitBake 的终端中，从“构建目录”｛.explored text role=“term”｝执行以下命令：

```

```
$ cd poky/build
$ bitbake core-image-minimal
```

```

# Using Traditional Kernel Development to Patch the Kernel

The steps in this procedure show you how you can patch the kernel using traditional kernel development (i.e. not using `devtool` as described in the \"``kernel-dev/common:using \`\`devtool\`\` to patch the kernel``{.interpreted-text role="ref"}\" section).

> 本过程中的步骤向您展示了如何使用传统的内核开发来修补内核（即，不使用“devtool”，如\“`kernel dev/common:using\`\`devtool\`\`来修补内核 ``{.depredicted text role=“ref”}\”部分所述）。

::: note
::: title
Note
:::

Before attempting this procedure, be sure you have performed the steps to get ready for updating the kernel as described in the \"`kernel-dev/common:getting ready for traditional kernel development`{.interpreted-text role="ref"}\" section.

> 在尝试此过程之前，请确保您已经执行了“`kernel dev/common:geting ready for traditional kernel development`｛.depredicted text role=“ref”｝”一节中所述的步骤，为更新内核做好准备。
> :::

Patching the kernel involves changing or adding configurations to an existing kernel, changing or adding recipes to the kernel that are needed to support specific hardware features, or even altering the source code itself.

> 修补内核包括更改或添加现有内核的配置，更改或添加支持特定硬件功能所需的内核配方，甚至更改源代码本身。

The example in this section creates a simple patch by adding some QEMU emulator console output at boot time through `printk` statements in the kernel\'s `calibrate.c` source code file. Applying the patch and booting the modified image causes the added messages to appear on the emulator\'s console. The example is a continuation of the setup procedure found in the \"`kernel-dev/common:getting ready for traditional kernel development`{.interpreted-text role="ref"}\" Section.

> 本节中的示例通过在内核的“calible.c”源代码文件中的“printk”语句在启动时添加一些 QEMU 模拟器控制台输出，创建了一个简单的补丁。应用修补程序并启动修改后的映像会导致添加的消息出现在模拟器的控制台上。该示例是\“`kernel dev/common:为传统内核开发做好准备”｛.depreted text role=“ref”｝\“一节中设置过程的延续。

1. *Edit the Source Files* Prior to this step, you should have used Git to create a local copy of the repository for your kernel. Assuming you created the repository as directed in the \"`kernel-dev/common:getting ready for traditional kernel development`{.interpreted-text role="ref"}\" section, use the following commands to edit the `calibrate.c` file:

> 1.*编辑源文件*在此步骤之前，您应该使用 Git 为内核创建存储库的本地副本。假设您按照\“`kernel dev/common:准备进行传统内核开发`｛.depreted text role=“ref”｝\”部分中的指示创建了存储库，请使用以下命令编辑 `calible.c'文件：

```

1. *Change the working directory*: You need to locate the source files in the local copy of the kernel Git repository. Change to where the kernel source code is before making your edits to the `calibrate.c` file:

   ```
   $ cd ~/linux-yocto-4.12/init
   ```
2. *Edit the source file*: Edit the `calibrate.c` file to have the following changes:

   ```
   void calibrate_delay(void)
   {
       unsigned long lpj;
       static bool printed;
       int this_cpu = smp_processor_id();

       printk("*************************************\n");
       printk("*                                   *\n");
       printk("*        HELLO YOCTO KERNEL         *\n");
       printk("*                                   *\n");
       printk("*************************************\n");

       if (per_cpu(cpu_loops_per_jiffy, this_cpu)) {
             .
             .
             .
   ```

```

2. *Stage and Commit Your Changes:* Use standard Git commands to stage and commit the changes you just made:

> 2.*暂存并提交您的更改：*使用标准 Git 命令暂存并提交刚才所做的更改：

```

```
$ git add calibrate.c
$ git commit -m "calibrate.c - Added some printk statements"
```

If you do not stage and commit your changes, the OpenEmbedded Build System will not pick up the changes.

```

3. *Update Your local.conf File to Point to Your Source Files:* In addition to your `local.conf` file specifying to use \"kernel-modules\" and the \"qemux86\" machine, it must also point to the updated kernel source files. Add `SRC_URI`{.interpreted-text role="term"} and `SRCREV`{.interpreted-text role="term"} statements similar to the following to your `local.conf`:

> 3.*更新 local.conf 文件以指向源文件：*除了指定使用“内核模块”和“qemux86”机器的“local.conf”文件外，它还必须指向更新的内核源文件。将类似于以下语句的 `SRC_URI`｛.respered text role=“term”｝和 `SRCREV`｛.espered text role=“term“｝语句添加到 `local.conf` 中：

```

```
$ cd poky/build/conf
```

Add the following to the `local.conf`:

```
SRC_URI:pn-linux-yocto = "git:///path-to/linux-yocto-4.12;protocol=file;name=machine;branch=standard/base; \
                          git:///path-to/yocto-kernel-cache;protocol=file;type=kmeta;name=meta;branch=yocto-4.12;destsuffix=${KMETA}"
SRCREV_meta:qemux86 = "${AUTOREV}"
SRCREV_machine:qemux86 = "${AUTOREV}"
```

::: note
::: title
Note
:::

Be sure to replace [path-to]{.title-ref} with the pathname to your local Git repositories. Also, you must be sure to specify the correct branch and machine types. For this example, the branch is `standard/base` and the machine is `qemux86`.
:::

```

4. *Build the Image:* With the source modified, your changes staged and committed, and the `local.conf` file pointing to the kernel files, you can now use BitBake to build the image:

> 4.*构建镜像：*修改了源代码，提交了更改，并且“local.conf”文件指向内核文件，现在可以使用 BitBake 构建镜像：

```

```
$ cd poky/build
$ bitbake core-image-minimal
```

```

5. *Boot the image*: Boot the modified image in the QEMU emulator using this command. When prompted to login to the QEMU console, use \"root\" with no password:

> 5.*引导镜像*：使用此命令在 QEMU 模拟器中引导修改后的镜像。当提示登录 QEMU 控制台时，使用不带密码的\“root\”：

```

```
$ cd poky/build
$ runqemu qemux86
```

```

6. *Look for Your Changes:* As QEMU booted, you might have seen your changes rapidly scroll by. If not, use these commands to see your changes:

> 6.*查找您的更改：*当 QEMU 启动时，您可能已经看到您的更改快速滚动。如果没有，请使用以下命令查看您的更改

```

```none
# dmesg | less
```

You should see the results of your `printk` statements as part of the output when you scroll down the console window.

```

7. *Generate the Patch File:* Once you are sure that your patch works correctly, you can generate a `*.patch` file in the kernel source repository:

> 7.*生成修补程序文件：*一旦您确定您的修补程序工作正常，您就可以在内核源存储库中生成“*.Patch”文件：

```

```
$ cd ~/linux-yocto-4.12/init
$ git format-patch -1
0001-calibrate.c-Added-some-printk-statements.patch
```

```

8. *Move the Patch File to Your Layer:* In order for subsequent builds to pick up patches, you need to move the patch file you created in the previous step to your layer `meta-mylayer`. For this example, the layer created earlier is located in your home directory as `meta-mylayer`. When the layer was created using the `yocto-create` script, no additional hierarchy was created to support patches. Before moving the patch file, you need to add additional structure to your layer using the following commands:

> 8.*将修补程序文件移动到您的层：*为了后续构建能够获取修补程序，您需要将在上一步中创建的修补程序文件移到您的“meta-mylayer”层。在这个例子中，前面创建的层位于您的主目录中，名为“metamylayer”。当使用“yocto-create”脚本创建层时，没有创建额外的层次结构来支持修补程序。在移动修补程序文件之前，需要使用以下命令向图层添加其他结构：

```

```
$ cd ~/meta-mylayer
$ mkdir recipes-kernel
$ mkdir recipes-kernel/linux
$ mkdir recipes-kernel/linux/linux-yocto
```

Once you have created this hierarchy in your layer, you can move the patch file using the following command:

```
$ mv ~/linux-yocto-4.12/init/0001-calibrate.c-Added-some-printk-statements.patch ~/meta-mylayer/recipes-kernel/linux/linux-yocto
```

```

9. *Create the Append File:* Finally, you need to create the `linux-yocto_4.12.bbappend` file and insert statements that allow the OpenEmbedded build system to find the patch. The append file needs to be in your layer\'s `recipes-kernel/linux` directory and it must be named `linux-yocto_4.12.bbappend` and have the following contents:

> 9.*创建附加文件：*最后，您需要创建“linux-octo_4.12.bappend”文件并插入允许 OpenEmbedded 构建系统查找补丁的语句。附加文件需要位于层的“recipes kernel/linux”目录中，并且必须命名为“linux-yoct_4.12.bappend”，并且具有以下内容：

```

```
FILESEXTRAPATHS:prepend := "${THISDIR}/${PN}:"
SRC_URI += "file://0001-calibrate.c-Added-some-printk-statements.patch"
```

The `FILESEXTRAPATHS`{.interpreted-text role="term"} and `SRC_URI`{.interpreted-text role="term"} statements enable the OpenEmbedded build system to find the patch file.

For more information on append files and patches, see the \"`kernel-dev/common:creating the append file`{.interpreted-text role="ref"}\" and \"`kernel-dev/common:applying patches`{.interpreted-text role="ref"}\" sections. You can also see the \"`dev-manual/layers:appending other layers metadata with your layer`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

::: note
::: title
Note
:::

To build `core-image-minimal` again and see the effects of your patch, you can essentially eliminate the temporary source files saved in `poky/build/tmp/work/...` and residual effects of the build by entering the following sequence of commands:

```
$ cd poky/build
$ bitbake -c cleanall yocto-linux
$ bitbake core-image-minimal -c cleanall
$ bitbake core-image-minimal
$ runqemu qemux86
```

:::

```

# Configuring the Kernel

Configuring the Yocto Project kernel consists of making sure the `.config` file has all the right information in it for the image you are building. You can use the `menuconfig` tool and configuration fragments to make sure your `.config` file is just how you need it. You can also save known configurations in a `defconfig` file that the build system can use for kernel configuration.

> 配置 Yocto Project 内核包括确保“.config”文件中包含您正在构建的映像的所有正确信息。您可以使用“menuconfig”工具和配置片段来确保您的“.config”文件正是您所需要的。您还可以将已知配置保存在构建系统可用于内核配置的“defconfig”文件中。

This section describes how to use `menuconfig`, create and use configuration fragments, and how to interactively modify your `.config` file to create the leanest kernel configuration file possible.

> 本节介绍如何使用“menuconfig”、创建和使用配置片段，以及如何交互式修改“.config”文件以创建尽可能精简的内核配置文件。

For more information on kernel configuration, see the \"`kernel-dev/common:changing the configuration`{.interpreted-text role="ref"}\" section.

> 有关内核配置的更多信息，请参阅“`kernel dev/common:changing the configuration`｛.explored text role=“ref”｝\”一节。

## Using  `menuconfig`

The easiest way to define kernel configurations is to set them through the `menuconfig` tool. This tool provides an interactive method with which to set kernel configurations. For general information on `menuconfig`, see `Menuconfig`{.interpreted-text role="wikipedia"}.

> 定义内核配置的最简单方法是通过“menuconfig”工具进行设置。该工具提供了一种用于设置内核配置的交互式方法。有关 `menuconfig` 的一般信息，请参阅 `menuconfig`｛.explored text role=“wikipedia”｝。

To use the `menuconfig` tool in the Yocto Project development environment, you must do the following:

> 要在 Yocto Project 开发环境中使用“menuconfig”工具，必须执行以下操作：

- Because you launch `menuconfig` using BitBake, you must be sure to set up your environment by running the `structure-core-script`{.interpreted-text role="ref"} script found in the `Build Directory`{.interpreted-text role="term"}.

> -由于您使用 BitBake 启动“menuconfig”，因此必须确保通过运行“构建目录”中的“结构核心脚本”｛.depreted text role=“ref”｝脚本来设置环境。

- You must be sure of the state of your build\'s configuration in the `Source Directory`{.interpreted-text role="term"}.

> -您必须在“源目录”｛.explored text role=“term”｝中确定生成配置的状态。

- Your build host must have the following two packages installed:

```

libncurses5-dev
libtinfo-dev

```

The following commands initialize the BitBake environment, run the `ref-tasks-kernel_configme`{.interpreted-text role="ref"} task, and launch `menuconfig`. These commands assume the Source Directory\'s top-level folder is `poky`:

> 以下命令初始化 BitBake 环境，运行 `ref-tasks-kernel_configme`｛.depreted text role=“ref”｝任务，然后启动 `menuconfig`。这些命令假定源目录的顶级文件夹为“poky”：

```

$ cd poky
$ source oe-init-build-env
$ bitbake linux-yocto -c kernel_configme -f
$ bitbake linux-yocto -c menuconfig

```

Once `menuconfig` comes up, its standard interface allows you to interactively examine and configure all the kernel configuration parameters. After making your changes, simply exit the tool and save your changes to create an updated version of the `.config` configuration file.

> 一旦出现“menuconfig”，它的标准界面允许您以交互方式检查和配置所有内核配置参数。进行更改后，只需退出工具并保存更改即可创建“.config”配置文件的更新版本。

::: note
::: title
Note
:::

You can use the entire `.config` file as the `defconfig` file. For information on `defconfig` files, see the \"`kernel-dev/common:changing the configuration`{.interpreted-text role="ref"}\", \"``kernel-dev/common:using an "in-tree" \`\`defconfig\`\` file``{.interpreted-text role="ref"}\", and \"``kernel-dev/common:creating a \`\`defconfig\`\` file``{.interpreted-text role="ref"}\" sections.

> 您可以将整个“.config”文件用作“defconfig”文件。有关 `defconfig` 文件的信息，请参阅\“`kernel dev/common:更改配置`{.depredicted text role=“ref”}\”、\“`kernel dev-common:使用“in tree”\`\defconfig\`\`file ``{.depresected text rol=“ref”｝\”和\“` kernel dev/common:创建一个\`\deficonfig\`` file```{.epredicted textrole=”ref“}\”部分。
> :::

Consider an example that configures the \"CONFIG_SMP\" setting for the `linux-yocto-4.12` kernel.

> 考虑一个为 `linux-octo-4.12` 内核配置“CONFIG_SMP\”设置的示例。

::: note
::: title
Note
:::

The OpenEmbedded build system recognizes this kernel as `linux-yocto` through Metadata (e.g. `PREFERRED_VERSION`{.interpreted-text role="term"}`_linux-yocto ?= "4.12%"`).

> OpenEmbedded 构建系统通过元数据将该内核识别为“linux yocto”（例如，`PREFERRED_VERSION`｛.explored text role=“term”｝`_linux-yocto？=“4.12%”`）。
> :::

Once `menuconfig` launches, use the interface to navigate through the selections to find the configuration settings in which you are interested. For this example, you deselect \"CONFIG_SMP\" by clearing the \"Symmetric Multi-Processing Support\" option. Using the interface, you can find the option under \"Processor Type and Features\". To deselect \"CONFIG_SMP\", use the arrow keys to highlight \"Symmetric Multi-Processing Support\" and enter \"N\" to clear the asterisk. When you are finished, exit out and save the change.

> 启动“menuconfig”后，使用界面浏览选择，以找到您感兴趣的配置设置。对于本例，您可以通过清除“对称多处理支持”选项来取消选择“CONFIG_SMP”。使用该界面，您可以在“处理器类型和功能”下找到选项。要取消选择“CONFIG_SMP\”，请使用箭头键突出显示“Symmetric Multi-Processing Support”，然后输入“N\”清除星号。完成后，退出并保存更改。

Saving the selections updates the `.config` configuration file. This is the file that the OpenEmbedded build system uses to configure the kernel during the build. You can find and examine this file in the `Build Directory`{.interpreted-text role="term"} in `tmp/work/`. The actual `.config` is located in the area where the specific kernel is built. For example, if you were building a Linux Yocto kernel based on the `linux-yocto-4.12` kernel and you were building a QEMU image targeted for `x86` architecture, the `.config` file would be:

> 保存选择将更新 `.config` 配置文件。这是 OpenEmbedded 构建系统在构建过程中用于配置内核的文件。您可以在 `tmp/work/` 中的 `Build Directory`｛.explored text role=“term”｝中找到并检查此文件。实际的“.config”位于构建特定内核的区域中。例如，如果您正在构建基于“Linux-octo-4.12”内核的 Linux Yocto 内核，并且您正在构建针对“x86”体系结构的 QEMU 映像，那么“.config”文件将是：

```none
poky/build/tmp/work/qemux86-poky-linux/linux-yocto/4.12.12+gitAUTOINC+eda4d18...
...967-r0/linux-qemux86-standard-build/.config
```

::: note
::: title
Note
:::

The previous example directory is artificially split and many of the characters in the actual filename are omitted in order to make it more readable. Also, depending on the kernel you are using, the exact pathname might differ.

> 前面的示例目录被人为分割，实际文件名中的许多字符被省略，以使其更具可读性。此外，根据您使用的内核，确切的路径名可能会有所不同。
> :::

Within the `.config` file, you can see the kernel settings. For example, the following entry shows that symmetric multi-processor support is not set:

> 在“.config”文件中，您可以看到内核设置。例如，以下条目显示未设置对称多处理器支持：

```
# CONFIG_SMP is not set
```

A good method to isolate changed configurations is to use a combination of the `menuconfig` tool and simple shell commands. Before changing configurations with `menuconfig`, copy the existing `.config` and rename it to something else, use `menuconfig` to make as many changes as you want and save them, then compare the renamed configuration file against the newly created file. You can use the resulting differences as your base to create configuration fragments to permanently save in your kernel layer.

> 隔离已更改配置的一个好方法是结合使用“menuconfig”工具和简单的 shell 命令。在使用“menuconfig”更改配置之前，复制现有的“.config”并将其重命名为其他文件，使用“menuconfig”进行任意多的更改并保存，然后将重命名的配置文件与新创建的文件进行比较。您可以使用由此产生的差异作为基础来创建配置片段，以便永久保存在内核层中。

::: note
::: title
Note
:::

Be sure to make a copy of the `.config` file and do not just rename it. The build system needs an existing `.config` file from which to work.

> 请确保复制“.config”文件，而不仅仅是重命名它。生成系统需要一个现有的“/config”文件。
> :::

## Creating a  `defconfig` File

A `defconfig` file in the context of the Yocto Project is often a `.config` file that is copied from a build or a `defconfig` taken from the kernel tree and moved into recipe space. You can use a `defconfig` file to retain a known set of kernel configurations from which the OpenEmbedded build system can draw to create the final `.config` file.

> Yocto 项目上下文中的“defconfig”文件通常是从构建中复制的“.config”文件，或者是从内核树中提取并移动到配方空间中的“defconfig”文件。您可以使用“defconfig”文件来保留一组已知的内核配置，OpenEmbedded 构建系统可以从中提取这些配置来创建最终的“.config”文件。

::: note
::: title
Note
:::

Out-of-the-box, the Yocto Project never ships a `defconfig` or `.config` file. The OpenEmbedded build system creates the final `.config` file used to configure the kernel.

> 开箱即用，Yocto 项目从不提供“defconfig”或“.config”文件。OpenEmbedded 构建系统创建用于配置内核的最终“.config”文件。
> :::

To create a `defconfig`, start with a complete, working Linux kernel `.config` file. Copy that file to the appropriate `${``PN`{.interpreted-text role="term"}`}` directory in your layer\'s `recipes-kernel/linux` directory, and rename the copied file to \"defconfig\" (e.g. `~/meta-mylayer/recipes-kernel/linux/linux-yocto/defconfig`). Then, add the following lines to the linux-yocto `.bbappend` file in your layer:

> 要创建“defconfig”，请从一个完整的、可工作的 Linux 内核“.config”文件开始。将该文件复制到层的 `recipes kernel/linux` 目录中相应的 `${` PN `{.depredicted text role=“term”}`}`目录中，并将复制的文件重命名为` defconfig `（例如`~/meta-mylor/recipes kernel/linux/linux yocto/defconfig `）。然后，将以下行添加到层中的linux-yocto`.bappend` 文件中：

```
FILESEXTRAPATHS:prepend := "${THISDIR}/${PN}:"
SRC_URI += "file://defconfig"
```

The `SRC_URI`{.interpreted-text role="term"} tells the build system how to search for the file, while the `FILESEXTRAPATHS`{.interpreted-text role="term"} extends the `FILESPATH`{.interpreted-text role="term"} variable (search directories) to include the `${PN}` directory you created to hold the configuration changes.

> `SRC_URI`{.interplated-text role=“term”}告诉生成系统如何搜索该文件，而 `FILESEXTRAPATHS`{.enterplated_ted-text 角色=“tern”}扩展了 `FILESPATH`{.einterpreted-text role=”term“}变量（搜索目录），以包括为保存配置更改而创建的 `${PN}` 目录。

::: note
::: title
Note
:::

The build system applies the configurations from the `defconfig` file before applying any subsequent configuration fragments. The final kernel configuration is a combination of the configurations in the `defconfig` file and any configuration fragments you provide. You need to realize that if you have any configuration fragments, the build system applies these on top of and after applying the existing `defconfig` file configurations.

> 在应用任何后续配置片段之前，构建系统将应用“defconfig”文件中的配置。最终的内核配置是“defconfig”文件中的配置和您提供的任何配置片段的组合。您需要意识到，如果您有任何配置片段，构建系统会在应用现有的“defconfig”文件配置之前和之后应用这些片段。
> :::

For more information on configuring the kernel, see the \"`kernel-dev/common:changing the configuration`{.interpreted-text role="ref"}\" section.

> 有关配置内核的更多信息，请参阅“`kernel dev/common:更改配置`{.depreted text role=“ref”}\”一节。

## Creating Configuration Fragments

Configuration fragments are simply kernel options that appear in a file placed where the OpenEmbedded build system can find and apply them. The build system applies configuration fragments after applying configurations from a `defconfig` file. Thus, the final kernel configuration is a combination of the configurations in the `defconfig` file and then any configuration fragments you provide. The build system applies fragments on top of and after applying the existing defconfig file configurations.

> 配置片段只是出现在 OpenEmbedded 构建系统可以找到并应用它们的文件中的内核选项。构建系统在应用“defconfig”文件中的配置之后应用配置片段。因此，最终的内核配置是“defconfig”文件中的配置和您提供的任何配置片段的组合。构建系统在应用现有的 defconfig 文件配置之上和之后应用片段。

Syntactically, the configuration statement is identical to what would appear in the `.config` file, which is in the `Build Directory`{.interpreted-text role="term"}.

> 从语法上讲，配置语句与 `.config` 文件中出现的内容相同，该文件位于 `Build Directory`｛.depreted text role=“term”｝中。

::: note
::: title
Note
:::

For more information about where the `.config` file is located, see the example in the \"``kernel-dev/common:using \`\`menuconfig\`\` ``{.interpreted-text role="ref"}\" section.

> 有关“.config”文件所在位置的更多信息，请参阅\“``kernel dev/common:using\`\`menuconfig\`\` `”部分中的示例。
> :::

It is simple to create a configuration fragment. One method is to use shell commands. For example, issuing the following from the shell creates a configuration fragment file named `my_smp.cfg` that enables multi-processor support within the kernel:

> 创建一个配置片段很简单。一种方法是使用 shell 命令。例如，从 shell 发出以下命令会创建一个名为“my_smp.cfg”的配置片段文件，该文件支持内核中的多处理器：

```
$ echo "CONFIG_SMP=y" >> my_smp.cfg
```

::: note
::: title
Note
:::

All configuration fragment files must use the `.cfg` extension in order for the OpenEmbedded build system to recognize them as a configuration fragment.

> 所有配置片段文件都必须使用“.cfg”扩展名，以便 OpenEmbedded 构建系统将其识别为配置片段。
> :::

Another method is to create a configuration fragment using the differences between two configuration files: one previously created and saved, and one freshly created using the `menuconfig` tool.

> 另一种方法是使用两个配置文件之间的差异创建配置片段：一个是以前创建并保存的，另一个是使用“menuconfig”工具新创建的。

To create a configuration fragment using this method, follow these steps:

> 要使用此方法创建配置片段，请执行以下步骤：

1. *Complete a Build Through Kernel Configuration:* Complete a build at least through the kernel configuration task as follows:

> 1.*通过内核配置完成构建：*至少通过内核配置任务完成构建，如下所示：

```
    $ bitbake linux-yocto -c kernel_configme -f

This step ensures that you create a `.config` file from a known state. Because there are situations where your build state might become unknown, it is best to run this task prior to starting `menuconfig`.
```

2. *Launch menuconfig:* Run the `menuconfig` command:

> 2.*启动 menuconfig:*运行 `menuconfig` 命令：

```
    $ bitbake linux-yocto -c menuconfig
```

3. *Create the Configuration Fragment:* Run the `diffconfig` command to prepare a configuration fragment. The resulting file `fragment.cfg` is placed in the `${``WORKDIR`{.interpreted-text role="term"}`}` directory:

> 3.*创建配置片段：*运行“diffconfig”命令准备配置片段。生成的文件 `fragment.cfg` 被放置在 `${``WORKDIR`{.depreted text role=“term”}`}` 目录中：

```
    $ bitbake linux-yocto -c diffconfig
```

The `diffconfig` command creates a file that is a list of Linux kernel `CONFIG_` assignments. See the \"`kernel-dev/common:changing the configuration`{.interpreted-text role="ref"}\" section for additional information on how to use the output as a configuration fragment.

> “diffconfig”命令创建一个文件，该文件是 Linux 内核“CONFIG_”分配的列表。有关如何将输出用作配置片段的更多信息，请参阅“`kernel dev/common:changing the configuration`｛.explored text role=“ref”｝”一节。

::: note
::: title
Note
:::

You can also use this method to create configuration fragments for a BSP. See the \"`kernel-dev/advanced:bsp descriptions`{.interpreted-text role="ref"}\" section for more information.

> 您还可以使用此方法为 BSP 创建配置片段。有关详细信息，请参阅\“`kernel dev/advanced:bsp descriptions`｛.depreted text role=“ref”｝\”部分。
> :::

Where do you put your configuration fragment files? You can place these files in an area pointed to by `SRC_URI`{.interpreted-text role="term"} as directed by your `bblayers.conf` file, which is located in your layer. The OpenEmbedded build system picks up the configuration and adds it to the kernel\'s configuration. For example, suppose you had a set of configuration options in a file called `myconfig.cfg`. If you put that file inside a directory named `linux-yocto` that resides in the same directory as the kernel\'s append file within your layer and then add the following statements to the kernel\'s append file, those configuration options will be picked up and applied when the kernel is built:

> 您将配置片段文件放在哪里？您可以按照位于您所在层的“bblayers.conf”文件的指示，将这些文件放置在“SRC_URI”｛.explored text role=“term”｝指向的区域中。OpenEmbedded 构建系统获取配置并将其添加到内核的配置中。例如，假设您在一个名为“myconfig.cfg”的文件中有一组配置选项。如果您将该文件放在名为“linux-yocto”的目录中，该目录与您所在层中的内核附加文件位于同一目录中，然后将以下语句添加到内核附加文件中，则在构建内核时会拾取并应用这些配置选项：

```
FILESEXTRAPATHS:prepend := "${THISDIR}/${PN}:"
SRC_URI += "file://myconfig.cfg"
```

As mentioned earlier, you can group related configurations into multiple files and name them all in the `SRC_URI`{.interpreted-text role="term"} statement as well. For example, you could group separate configurations specifically for Ethernet and graphics into their own files and add those by using a `SRC_URI`{.interpreted-text role="term"} statement like the following in your append file:

> 如前所述，您可以将相关配置分组到多个文件中，并在 `SRC_URI`{.depreted text role=“term”}语句中对所有配置进行命名。例如，您可以将专门针对以太网和图形的单独配置分组到它们自己的文件中，并通过在附加文件中使用 `SRC_URI`｛.explored text role=“term”｝语句添加这些配置，如下所示：

```
SRC_URI += "file://myconfig.cfg \
            file://eth.cfg \
            file://gfx.cfg"
```

## Validating Configuration

You can use the `ref-tasks-kernel_configcheck`{.interpreted-text role="ref"} task to provide configuration validation:

> 您可以使用 `ref-tasks-kernel_configcheck`｛.explored text role=“ref”｝任务来提供配置验证：

```
$ bitbake linux-yocto -c kernel_configcheck -f
```

Running this task produces warnings for when a requested configuration does not appear in the final `.config` file or when you override a policy configuration in a hardware configuration fragment.

> 当请求的配置没有出现在最终的“.config”文件中时，或者当您覆盖硬件配置片段中的策略配置时，运行此任务会产生警告。

In order to run this task, you must have an existing `.config` file. See the \"``kernel-dev/common:using \`\`menuconfig\`\` ``{.interpreted-text role="ref"}\" section for information on how to create a configuration file.

> 为了运行此任务，您必须有一个现有的“.config”文件。有关如何创建配置文件的信息，请参阅“``kernel dev/common:using\`\`menuconfig\`\` ``｛.explored text role=“ref”｝\”一节。

Following is sample output from the `ref-tasks-kernel_configcheck`{.interpreted-text role="ref"} task:

> 以下是 `ref-tasks-kernel_configcheck`｛.explored text role=“ref”｝任务的输出示例：

```none
Loading cache: 100% |########################################################| Time: 0:00:00
Loaded 1275 entries from dependency cache.
NOTE: Resolving any missing task queue dependencies

Build Configuration:
    .
    .
    .

NOTE: Executing SetScene Tasks
NOTE: Executing RunQueue Tasks
WARNING: linux-yocto-4.12.12+gitAUTOINC+eda4d18ce4_16de014967-r0 do_kernel_configcheck:
    [kernel config]: specified values did not make it into the kernel's final configuration:

---------- CONFIG_X86_TSC -----------------
Config: CONFIG_X86_TSC
From: /home/scottrif/poky/build/tmp/work-shared/qemux86/kernel-source/.kernel-meta/configs/standard/bsp/common-pc/common-pc-cpu.cfg
Requested value:  CONFIG_X86_TSC=y
Actual value:


---------- CONFIG_X86_BIGSMP -----------------
Config: CONFIG_X86_BIGSMP
From: /home/scottrif/poky/build/tmp/work-shared/qemux86/kernel-source/.kernel-meta/configs/standard/cfg/smp.cfg
      /home/scottrif/poky/build/tmp/work-shared/qemux86/kernel-source/.kernel-meta/configs/standard/defconfig
Requested value:  # CONFIG_X86_BIGSMP is not set
Actual value:


---------- CONFIG_NR_CPUS -----------------
Config: CONFIG_NR_CPUS
From: /home/scottrif/poky/build/tmp/work-shared/qemux86/kernel-source/.kernel-meta/configs/standard/cfg/smp.cfg
      /home/scottrif/poky/build/tmp/work-shared/qemux86/kernel-source/.kernel-meta/configs/standard/bsp/common-pc/common-pc.cfg
      /home/scottrif/poky/build/tmp/work-shared/qemux86/kernel-source/.kernel-meta/configs/standard/defconfig
Requested value:  CONFIG_NR_CPUS=8
Actual value:     CONFIG_NR_CPUS=1


---------- CONFIG_SCHED_SMT -----------------
Config: CONFIG_SCHED_SMT
From: /home/scottrif/poky/build/tmp/work-shared/qemux86/kernel-source/.kernel-meta/configs/standard/cfg/smp.cfg
      /home/scottrif/poky/build/tmp/work-shared/qemux86/kernel-source/.kernel-meta/configs/standard/defconfig
Requested value:  CONFIG_SCHED_SMT=y
Actual value:



NOTE: Tasks Summary: Attempted 288 tasks of which 285 didn't need to be rerun and all succeeded.

Summary: There were 3 WARNING messages shown.
```

::: note
::: title
Note
:::

The previous output example has artificial line breaks to make it more readable.

> 前面的输出示例中有人为的换行符，使其可读性更强。
> :::

The output describes the various problems that you can encounter along with where to find the offending configuration items. You can use the information in the logs to adjust your configuration files and then repeat the `ref-tasks-kernel_configme`{.interpreted-text role="ref"} and `ref-tasks-kernel_configcheck`{.interpreted-text role="ref"} tasks until they produce no warnings.

> 输出描述了可能遇到的各种问题，以及在哪里可以找到有问题的配置项。您可以使用日志中的信息来调整配置文件，然后重复执行 `ref-tasks-kernel_configme`｛.depreted text role=“ref”｝和 `ref-ttasks-kernel-configcheck`｛.repreted text role=“ref”}任务，直到它们不产生警告为止。

For more information on how to use the `menuconfig` tool, see the ``kernel-dev/common:using \`\`menuconfig\`\` ``{.interpreted-text role="ref"} section.

> 有关如何使用 `menuconfig` 工具的更多信息，请参阅 ``kernel dev/common:using\`\`menuconfig\`\` `{.depreted text role=“ref”}部分。

## Fine-Tuning the Kernel Configuration File

You can make sure the `.config` file is as lean or efficient as possible by reading the output of the kernel configuration fragment audit, noting any issues, making changes to correct the issues, and then repeating.

> 您可以通过读取内核配置片段审核的输出，注意任何问题，进行更改以更正问题，然后重复，来确保“.config”文件尽可能精简或高效。

As part of the kernel build process, the `ref-tasks-kernel_configcheck`{.interpreted-text role="ref"} task runs. This task validates the kernel configuration by checking the final `.config` file against the input files. During the check, the task produces warning messages for the following issues:

> 作为内核构建过程的一部分，运行 `ref-tasks-kernel_configcheck`{.depreted text role=“ref”}任务。此任务通过对照输入文件检查最终的“.config”文件来验证内核配置。在检查过程中，任务会针对以下问题生成警告消息：

- Requested options that did not make it into the final `.config` file.
- Configuration items that appear twice in the same configuration fragment.
- Configuration items tagged as \"required\" that were overridden.
- A board overrides a non-board specific option.
- Listed options not valid for the kernel being processed. In other words, the option does not appear anywhere.

> -列出的选项对正在处理的内核无效。换句话说，该选项不会出现在任何位置。

::: note
::: title
Note
:::

The `ref-tasks-kernel_configcheck`{.interpreted-text role="ref"} task can also optionally report if an option is overridden during processing.

> 如果在处理过程中覆盖了某个选项，`ref-tasks-kernel_configcheck`｛.explored text role=“ref”｝任务也可以选择性地报告。
> :::

For each output warning, a message points to the file that contains a list of the options and a pointer to the configuration fragment that defines them. Collectively, the files are the key to streamlining the configuration.

> 对于每个输出警告，都会有一条消息指向包含选项列表和指向定义选项的配置片段的指针的文件。总的来说，这些文件是精简配置的关键。

To streamline the configuration, do the following:

> 要简化配置，请执行以下操作：

1. *Use a Working Configuration:* Start with a full configuration that you know works. Be sure the configuration builds and boots successfully. Use this configuration file as your baseline.

> 1.*使用工作配置：*从您知道有效的完整配置开始。确保配置构建和引导成功。使用此配置文件作为基线。

2. *Run Configure and Check Tasks:* Separately run the `ref-tasks-kernel_configme`{.interpreted-text role="ref"} and `ref-tasks-kernel_configcheck`{.interpreted-text role="ref"} tasks:

> 2.*运行配置和检查任务：*分别运行 `ref-Tasks-kernel_configme`｛.depreted text role=“ref”｝和 `ref-ttasks-kernel-configcheck`｛.epreted text role=“ref”}任务：

```
    $ bitbake linux-yocto -c kernel_configme -f
    $ bitbake linux-yocto -c kernel_configcheck -f
```

3. *Process the Results:* Take the resulting list of files from the `ref-tasks-kernel_configcheck`{.interpreted-text role="ref"} task warnings and do the following:

> 3.*处理结果：*从 `ref-tasks-kernel_configcheck`｛.explored text role=“ref”｝任务警告中获取结果文件列表，并执行以下操作：

```
-   Drop values that are redefined in the fragment but do not change the final `.config` file.
-   Analyze and potentially drop values from the `.config` file that override required configurations.
-   Analyze and potentially remove non-board specific options.
-   Remove repeated and invalid options.
```

4. *Re-Run Configure and Check Tasks:* After you have worked through the output of the kernel configuration audit, you can re-run the `ref-tasks-kernel_configme`{.interpreted-text role="ref"} and `ref-tasks-kernel_configcheck`{.interpreted-text role="ref"} tasks to see the results of your changes. If you have more issues, you can deal with them as described in the previous step.

> 4.*重新运行配置和检查任务：*完成内核配置审核的输出后，您可以重新运行 `ref-Tasks-kernel_configme`｛.depreted text role=“ref”｝和 `ref-ttasks-kernel-configcheck`｛.repreted text role=“ref”}任务，以查看更改的结果。如果您有更多问题，可以按照上一步中的说明进行处理。

Iteratively working through steps two through four eventually yields a minimal, streamlined configuration file. Once you have the best `.config`, you can build the Linux Yocto kernel.

> 反复执行第二步到第四步，最终生成一个最小的、精简的配置文件。一旦你有了最好的“.config”，你就可以构建 Linux Yocto 内核了。

# Expanding Variables

Sometimes it is helpful to determine what a variable expands to during a build. You can examine the values of variables by examining the output of the `bitbake -e` command. The output is long and is more easily managed in a text file, which allows for easy searches:

> 有时，在构建过程中确定变量扩展到什么是有帮助的。您可以通过检查“bitbake-e”命令的输出来检查变量的值。输出很长，更容易在文本文件中进行管理，从而可以轻松搜索：

```
$ bitbake -e virtual/kernel > some_text_file
```

Within the text file, you can see exactly how each variable is expanded and used by the OpenEmbedded build system.

> 在文本文件中，您可以确切地看到 OpenEmbedded 构建系统是如何展开和使用每个变量的。

# Working with a \"Dirty\" Kernel Version String

If you build a kernel image and the version string has a \"+\" or a \"-dirty\" at the end, it means there are uncommitted modifications in the kernel\'s source directory. Follow these steps to clean up the version string:

> 如果您构建了一个内核映像，并且版本字符串的末尾有一个\“+\”或\“-dirty\”，则意味着在内核的源目录中存在未提交的修改。按照以下步骤清理版本字符串：

1. *Discover the Uncommitted Changes:* Go to the kernel\'s locally cloned Git repository (source directory) and use the following Git command to list the files that have been changed, added, or removed:

> 1.*发现未提交的更改：*转到内核的本地克隆 Git 存储库（源目录），使用以下 Git 命令列出已更改、添加或删除的文件：

```
    $ git status
```

2. *Commit the Changes:* You should commit those changes to the kernel source tree regardless of whether or not you will save, export, or use the changes:

> 2.*提交更改：*无论是否保存、导出或使用更改，都应该将这些更改提交到内核源代码树：

```
    $ git add
    $ git commit -s -a -m "getting rid of -dirty"
```

3. *Rebuild the Kernel Image:* Once you commit the changes, rebuild the kernel.

> 3.*重建内核映像：*提交更改后，重建内核。

```
Depending on your particular kernel development workflow, the commands you use to rebuild the kernel might differ. For information on building the kernel image when using `devtool`, see the \"`` kernel-dev/common:using \`\`devtool\`\` to patch the kernel ``{.interpreted-text role="ref"}\" section. For information on building the kernel image when using BitBake, see the \"`kernel-dev/common:using traditional kernel development to patch the kernel`{.interpreted-text role="ref"}\" section.
```

# Working With Your Own Sources

If you cannot work with one of the Linux kernel versions supported by existing linux-yocto recipes, you can still make use of the Yocto Project Linux kernel tooling by working with your own sources. When you use your own sources, you will not be able to leverage the existing kernel `Metadata`{.interpreted-text role="term"} and stabilization work of the linux-yocto sources. However, you will be able to manage your own Metadata in the same format as the linux-yocto sources. Maintaining format compatibility facilitates converging with linux-yocto on a future, mutually-supported kernel version.

> 如果您不能使用现有 Linux yocto 配方支持的某个 Linux 内核版本，您仍然可以通过使用自己的源代码来使用 yocto Project Linux 内核工具。当您使用自己的源代码时，您将无法利用现有的内核 `Metadata`｛.explored text role=“term”｝和 linux yocto 源代码的稳定工作。但是，您将能够以与 linux yocto 源代码相同的格式管理自己的元数据。保持格式兼容性有助于在未来相互支持的内核版本上与 linux-yocto 融合。

To help you use your own sources, the Yocto Project provides a linux-yocto custom recipe that uses `kernel.org` sources and the Yocto Project Linux kernel tools for managing kernel Metadata. You can find this recipe in the `poky` Git repository: :yocto\_[git:%60meta-skeleton/recipes-kernel/linux/linux-yocto-custom.bb](git:%60meta-skeleton/recipes-kernel/linux/linux-yocto-custom.bb) \</poky/tree/meta-skeleton/recipes-kernel/linux/linux-yocto-custom.bb\>\`.

> 为了帮助您使用自己的源代码，Yocto 项目提供了一个 linux Yocto 自定义配方，该配方使用“kernel.org”源代码和 Yocto Project linux 内核工具来管理内核元数据。您可以在“poky”Git 存储库中找到此配方：：yocto\_<Git:%60meta skelene/precipes kernel/linux/linux yocto custom.bb>\</poky/tree/meta-stelene/pecipes kernel/linux/linux yocto-custom.bb\>\`。

Here are some basic steps you can use to work with your own sources:

> 以下是一些基本步骤，您可以使用这些步骤来处理自己的资源：

1. *Create a Copy of the Kernel Recipe:* Copy the `linux-yocto-custom.bb` recipe to your layer and give it a meaningful name. The name should include the version of the Yocto Linux kernel you are using (e.g. `linux-yocto-myproject_4.12.bb`, where \"4.12\" is the base version of the Linux kernel with which you would be working).

> 1.*创建内核配方的副本：*将“linux yocto custom.bb”配方复制到您的层，并为其命名一个有意义的名称。名称应包括您正在使用的 Yocto Linux 内核的版本（例如“Linux-Yocto-myproject_4.12.bb”，其中\“4.12\”是您将要使用的 Linux 内核的基本版本）。

2. *Create a Directory for Your Patches:* In the same directory inside your layer, create a matching directory to store your patches and configuration files (e.g. `linux-yocto-myproject`).

> 2.*为您的补丁程序创建目录：*在层内的同一目录中，创建一个匹配的目录来存储补丁程序和配置文件（例如“linux yocto myproject”）。

3. *Ensure You Have Configurations:* Make sure you have either a `defconfig` file or configuration fragment files in your layer. When you use the `linux-yocto-custom.bb` recipe, you must specify a configuration. If you do not have a `defconfig` file, you can run the following:

> 3.*确保您有配置：*确保您的层中有“defconfig”文件或配置片段文件。当您使用“linux-yocto-custom.bb”配方时，您必须指定一个配置。如果没有“defconfig”文件，则可以运行以下操作：

```
    $ make defconfig

After running the command, copy the resulting `.config` file to the `files` directory in your layer as \"defconfig\" and then add it to the `SRC_URI`{.interpreted-text role="term"} variable in the recipe.

Running the `make defconfig` command results in the default configuration for your architecture as defined by your kernel. However, there is no guarantee that this configuration is valid for your use case, or that your board will even boot. This is particularly true for non-x86 architectures.

To use non-x86 `defconfig` files, you need to be more specific and find one that matches your board (i.e. for arm, you look in `arch/arm/configs` and use the one that is the best starting point for your board).
```

4. *Edit the Recipe:* Edit the following variables in your recipe as appropriate for your project:

> 4.*编辑配方：*根据您的项目，编辑配方中的以下变量：

```
-   `SRC_URI`{.interpreted-text role="term"}: The `SRC_URI`{.interpreted-text role="term"} should specify a Git repository that uses one of the supported Git fetcher protocols (i.e. `file`, `git`, `http`, and so forth). The `SRC_URI`{.interpreted-text role="term"} variable should also specify either a `defconfig` file or some configuration fragment files. The skeleton recipe provides an example `SRC_URI`{.interpreted-text role="term"} as a syntax reference.

-   `LINUX_VERSION`{.interpreted-text role="term"}: The Linux kernel version you are using (e.g. \"4.12\").

-   `LINUX_VERSION_EXTENSION`{.interpreted-text role="term"}: The Linux kernel `CONFIG_LOCALVERSION` that is compiled into the resulting kernel and visible through the `uname` command.

-   `SRCREV`{.interpreted-text role="term"}: The commit ID from which you want to build.

-   `PR`{.interpreted-text role="term"}: Treat this variable the same as you would in any other recipe. Increment the variable to indicate to the OpenEmbedded build system that the recipe has changed.

-   `PV`{.interpreted-text role="term"}: The default `PV`{.interpreted-text role="term"} assignment is typically adequate. It combines the `LINUX_VERSION`{.interpreted-text role="term"} with the Source Control Manager (SCM) revision as derived from the `SRCPV`{.interpreted-text role="term"} variable. The combined results are a string with the following form:

        3.19.11+git1+68a635bf8dfb64b02263c1ac80c948647cc76d5f_1+218bd8d2022b9852c60d32f0d770931e3cf343e2

    While lengthy, the extra verbosity in `PV`{.interpreted-text role="term"} helps ensure you are using the exact sources from which you intend to build.

-   `COMPATIBLE_MACHINE`{.interpreted-text role="term"}: A list of the machines supported by your new recipe. This variable in the example recipe is set by default to a regular expression that matches only the empty string, \"(\^\$)\". This default setting triggers an explicit build failure. You must change it to match a list of the machines that your new recipe supports. For example, to support the `qemux86` and `qemux86-64` machines, use the following form:

        COMPATIBLE_MACHINE = "qemux86|qemux86-64"
```

5. *Customize Your Recipe as Needed:* Provide further customizations to your recipe as needed just as you would customize an existing linux-yocto recipe. See the \"`ref-manual/devtool-reference:modifying an existing recipe`{.interpreted-text role="ref"}\" section for information.

> 5.*根据需要自定义您的配方：*根据需要对您的配方进行进一步的自定义，就像您自定义现有的 linux yocto 配方一样。有关信息，请参阅\“`ref manual/devtool reference:modifying an existing recipe`｛.depredicted text role=“ref”｝\”一节。

# Working with Out-of-Tree Modules

This section describes steps to build out-of-tree modules on your target and describes how to incorporate out-of-tree modules in the build.

> 本节介绍在目标上构建树外模块的步骤，并介绍如何在构建中合并树外模块。

## Building Out-of-Tree Modules on the Target

While the traditional Yocto Project development model would be to include kernel modules as part of the normal build process, you might find it useful to build modules on the target. This could be the case if your target system is capable and powerful enough to handle the necessary compilation. Before deciding to build on your target, however, you should consider the benefits of using a proper cross-development environment from your build host.

> 虽然传统的 Yocto 项目开发模型会将内核模块作为正常构建过程的一部分，但您可能会发现在目标上构建模块很有用。如果您的目标系统有足够的能力和功能来处理必要的编译，则可能会出现这种情况。但是，在决定基于目标进行构建之前，您应该考虑从构建主机使用适当的交叉开发环境的好处。

If you want to be able to build out-of-tree modules on the target, there are some steps you need to take on the target that is running your SDK image. Briefly, the `kernel-dev` package is installed by default on all `*.sdk` images and the `kernel-devsrc` package is installed on many of the `*.sdk` images. However, you need to create some scripts prior to attempting to build the out-of-tree modules on the target that is running that image.

> 如果您希望能够在目标上构建树外模块，则需要在运行 SDK 映像的目标上采取一些步骤。简言之，“kernel dev”包默认安装在所有“*.sdk”映像上，“kernel-devsrc”包安装在许多“*.sdk”映像上。但是，在尝试在运行该映像的目标上构建树外模块之前，您需要创建一些脚本。

Prior to attempting to build the out-of-tree modules, you need to be on the target as root and you need to change to the `/usr/src/kernel` directory. Next, `make` the scripts:

> 在尝试构建树外模块之前，您需要以 root 身份在目标上，并且需要更改到“/usr/src/kernel”目录。接下来，“制作”脚本：

```none
# cd /usr/src/kernel
# make scripts
```

Because all SDK image recipes include `dev-pkgs`, the `kernel-dev` packages will be installed as part of the SDK image and the `kernel-devsrc` packages will be installed as part of applicable SDK images. The SDK uses the scripts when building out-of-tree modules. Once you have switched to that directory and created the scripts, you should be able to build your out-of-tree modules on the target.

> 由于所有 SDK 映像配方都包含“dev-pkgs”，因此“kernel dev”包将作为 SDK 映像的一部分安装，“kernel devsrc”包将用作适用的 SDK 映像的部分安装。SDK 在构建树外模块时使用脚本。一旦切换到该目录并创建了脚本，就应该能够在目标上构建树外模块。

## Incorporating Out-of-Tree Modules

While it is always preferable to work with sources integrated into the Linux kernel sources, if you need an external kernel module, the `hello-mod.bb` recipe is available as a template from which you can create your own out-of-tree Linux kernel module recipe.

> 虽然最好使用集成到 Linux 内核源中的源，但如果您需要外部内核模块，“hello mod.bb”配方可以作为模板使用，您可以从中创建自己的树外 Linux 内核模块配方。

This template recipe is located in the `poky` Git repository of the Yocto Project: :yocto\_[git:%60meta-skeleton/recipes-kernel/hello-mod/hello-mod_0.1.bb](git:%60meta-skeleton/recipes-kernel/hello-mod/hello-mod_0.1.bb) \</poky/tree/meta-skeleton/recipes-kernel/hello-mod/hello-mod_0.1.bb\>\`.

> 此模板配方位于 Yocto 项目的“poky”Git 存储库中：：Yocto\_<Git:%60meta sklete/recipes kernel/hello mod/hello-mod_0.1.bb>\</poky/tree/meta-stlete/recips kernel/hello-mod/hello-mod_0.1.bb \>\`。

To get started, copy this recipe to your layer and give it a meaningful name (e.g. `mymodule_1.0.bb`). In the same directory, create a new directory named `files` where you can store any source files, patches, or other files necessary for building the module that do not come with the sources. Finally, update the recipe as needed for the module. Typically, you will need to set the following variables:

> 要开始，请将此配方复制到您的层中，并为其指定一个有意义的名称（例如“mymodule_1.0.bb”）。在同一目录中，创建一个名为“files”的新目录，您可以在其中存储任何源文件、修补程序或构建模块所需的其他文件，但这些文件不是源文件。最后，根据模块的需要更新配方。通常，您需要设置以下变量：

- `DESCRIPTION`{.interpreted-text role="term"}
- `LICENSE* <LICENSE>`{.interpreted-text role="term"}
- `SRC_URI`{.interpreted-text role="term"}
- `PV`{.interpreted-text role="term"}

Depending on the build system used by the module sources, you might need to make some adjustments. For example, a typical module `Makefile` looks much like the one provided with the `hello-mod` template:

> 根据模块源使用的构建系统，您可能需要进行一些调整。例如，一个典型的模块“Makefile”看起来很像“hello-mod”模板提供的模块：

```
obj-m := hello.o

SRC := $(shell pwd)

all:
 $(MAKE) -C $(KERNEL_SRC) M=$(SRC)

modules_install:
 $(MAKE) -C $(KERNEL_SRC) M=$(SRC) modules_install
...
```

The important point to note here is the `KERNEL_SRC`{.interpreted-text role="term"} variable. The `ref-classes-module`{.interpreted-text role="ref"} class sets this variable and the `KERNEL_PATH`{.interpreted-text role="term"} variable to `${STAGING_KERNEL_DIR}` with the necessary Linux kernel build information to build modules. If your module `Makefile` uses a different variable, you might want to override the `ref-tasks-compile`{.interpreted-text role="ref"} step, or create a patch to the `Makefile` to work with the more typical `KERNEL_SRC`{.interpreted-text role="term"} or `KERNEL_PATH`{.interpreted-text role="term"} variables.

> 这里需要注意的重要一点是 `KERNEL_SRC`｛.explored text role=“term”｝变量。`ref classes module`｛.expreted text role=“ref”｝类将此变量和 `KERNEL_PATH`｛.repreted text role=“term”}变量设置为 `${STAGING_KERNEL_DIR｝，并提供构建模块所需的 Linux 内核构建信息。如果您的模块“Makefile”使用不同的变量，您可能希望覆盖“ref tasks compile”｛.depredicted text role=“ref”｝步骤，或创建“Makefile’的补丁，以使用更典型的“KERNEL_SRC”｛.epredicted textrolE=“term”｝或“KENNEL_PATH”｛.repredicted extrole=”term｝变量。

After you have prepared your recipe, you will likely want to include the module in your images. To do this, see the documentation for the following variables in the Yocto Project Reference Manual and set one of them appropriately for your machine configuration file:

> 在你准备好食谱后，你可能会想在你的图片中包括这个模块。为此，请参阅 Yocto 项目参考手册中关于以下变量的文档，并为您的机器配置文件适当设置其中一个变量：

- `MACHINE_ESSENTIAL_EXTRA_RDEPENDS`{.interpreted-text role="term"}
- `MACHINE_ESSENTIAL_EXTRA_RRECOMMENDS`{.interpreted-text role="term"}
- `MACHINE_EXTRA_RDEPENDS`{.interpreted-text role="term"}
- `MACHINE_EXTRA_RRECOMMENDS`{.interpreted-text role="term"}

Modules are often not required for boot and can be excluded from certain build configurations. The following allows for the most flexibility:

> 模块通常不需要启动，并且可以从某些构建配置中排除。以下内容提供了最大的灵活性：

```
MACHINE_EXTRA_RRECOMMENDS += "kernel-module-mymodule"
```

The value is derived by appending the module filename without the `.ko` extension to the string \"kernel-module-\".

> 该值是通过将不带“.ko”扩展名的模块文件名附加到字符串\“kernel module-\”而派生的。

Because the variable is `RRECOMMENDS`{.interpreted-text role="term"} and not a `RDEPENDS`{.interpreted-text role="term"} variable, the build will not fail if this module is not available to include in the image.

> 由于变量是 `RRECOMMENDS`｛.explored text role=“term”｝，而不是 `RDEPENDS`{.explered text rol=“term“｝变量，因此如果此模块不可包含在图像中，则构建不会失败。

# Inspecting Changes and Commits

A common question when working with a kernel is: \"What changes have been applied to this tree?\" Rather than using \"grep\" across directories to see what has changed, you can use Git to inspect or search the kernel tree. Using Git is an efficient way to see what has changed in the tree.

> 使用内核时，一个常见的问题是：“对此树应用了哪些更改？”您可以使用 Git 来检查或搜索内核树，而不是在目录之间使用“grep”来查看更改了什么。使用 Git 是查看树中发生了什么变化的有效方法。

## What Changed in a Kernel?

Following are a few examples that show how to use Git commands to examine changes. These examples are by no means the only way to see changes.

> 以下是一些示例，展示了如何使用 Git 命令来检查更改。这些例子绝不是看到变化的唯一途径。

::: note
::: title
Note
:::

In the following examples, unless you provide a commit range, `kernel.org` history is blended with Yocto Project kernel changes. You can form ranges by using branch names from the kernel tree as the upper and lower commit markers with the Git commands. You can see the branch names through the web interface to the Yocto Project source repositories at :yocto\_[git:%60/](git:%60/)\`.

> 在以下示例中，除非您提供提交范围，否则“kernel.org”历史记录将与 Yocto Project 内核更改混合在一起。您可以使用 Git 命令使用内核树中的分支名称作为上下提交标记来形成范围。您可以通过 Yocto Project 源代码存储库的 web 界面查看分支名称，网址为：Yocto\_[git:%60/](git:%60/)\`。
> :::

To see a full range of the changes, use the `git whatchanged` command and specify a commit range for the branch ([commit]{.title-ref}`..`[commit]{.title-ref}).

> 要查看完整的更改范围，请使用“git whatchanged”命令并指定分支的提交范围（[commit]｛.title-ref｝`..`[commit]{.title-ref｝）。

Here is an example that looks at what has changed in the `emenlow` branch of the `linux-yocto-3.19` kernel. The lower commit range is the commit associated with the `standard/base` branch, while the upper commit range is the commit associated with the `standard/emenlow` branch:

> 下面是一个示例，它着眼于“linux-yoct-3.19”内核的“emenlow”分支中发生了什么变化。较低的提交范围是与“标准/基本”分支相关联的提交，而较高的提交范围则是与“standard/emenlow”分支相关的提交：

```
$ git whatchanged origin/standard/base..origin/standard/emenlow
```

To see short, one line summaries of changes use the `git log` command:

> 要查看更改的简短摘要，请使用“git log”命令：

```
$ git log --oneline origin/standard/base..origin/standard/emenlow
```

Use this command to see code differences for the changes:

> 使用此命令可以查看更改的代码差异：

```
$ git diff origin/standard/base..origin/standard/emenlow
```

Use this command to see the commit log messages and the text differences:

> 使用此命令可以查看提交日志消息和文本差异：

```
$ git show origin/standard/base..origin/standard/emenlow
```

Use this command to create individual patches for each change. Here is an example that creates patch files for each commit and places them in your `Documents` directory:

> 使用此命令可以为每个更改创建单独的修补程序。下面是一个为每次提交创建补丁文件并将其放置在“Documents”目录中的示例：

```
$ git format-patch -o $HOME/Documents origin/standard/base..origin/standard/emenlow
```

## Showing a Particular Feature or Branch Change

Tags in the Yocto Project kernel tree divide changes for significant features or branches. The `git show` tag command shows changes based on a tag. Here is an example that shows `systemtap` changes:

> Yocto 项目内核树中的标记将更改划分为重要功能或分支。“git show”标记命令显示基于标记的更改。以下是一个显示“systemtap”更改的示例：

```
$ git show systemtap
```

You can use the `git branch --contains` tag command to show the branches that contain a particular feature. This command shows the branches that contain the `systemtap` feature:

> 您可以使用“gitbranch--contains”标记命令来显示包含特定功能的分支。此命令显示包含“systemtap”功能的分支：

```
$ git branch --contains systemtap
```

# Adding Recipe-Space Kernel Features

You can add kernel features in the `recipe-space <kernel-dev/advanced:recipe-space metadata>`{.interpreted-text role="ref"} by using the `KERNEL_FEATURES`{.interpreted-text role="term"} variable and by specifying the feature\'s `.scc` file path in the `SRC_URI`{.interpreted-text role="term"} statement. When you add features using this method, the OpenEmbedded build system checks to be sure the features are present. If the features are not present, the build stops. Kernel features are the last elements processed for configuring and patching the kernel. Therefore, adding features in this manner is a way to enforce specific features are present and enabled without needing to do a full audit of any other layer\'s additions to the `SRC_URI`{.interpreted-text role="term"} statement.

> 您可以通过使用 `kernel_feature`｛.expected text role=“term”｝变量并在 `SRC_URI`｛.dexpected text role=”term“｝语句中指定功能的 `.scc` 文件路径，在 `recipe space<kernel dev/advanced:recipe pace metadata>`｛.respered text role=“ref”｝中添加内核功能。使用此方法添加功能时，OpenEmbedded 构建系统会进行检查以确保这些功能存在。如果这些功能不存在，则生成将停止。内核特性是为配置和修补内核而处理的最后元素。因此，以这种方式添加功能是一种强制特定功能存在和启用的方式，而无需对任何其他层添加到 `SRC_URI`{.depreted text role=“term”}语句中的内容进行全面审核。

You add a kernel feature by providing the feature as part of the `KERNEL_FEATURES`{.interpreted-text role="term"} variable and by providing the path to the feature\'s `.scc` file, which is relative to the root of the kernel Metadata. The OpenEmbedded build system searches all forms of kernel Metadata on the `SRC_URI`{.interpreted-text role="term"} statement regardless of whether the Metadata is in the \"kernel-cache\", system kernel Metadata, or a recipe-space Metadata (i.e. part of the kernel recipe). See the \"`kernel-dev/advanced:kernel metadata location`{.interpreted-text role="ref"}\" section for additional information.

> 您可以添加内核功能，方法是将该功能作为 `kernel_feature`｛.explored text role=“term”｝变量的一部分提供，并提供该功能的 `.scc'文件的路径，该路径相对于内核元数据的根。OpenEmbedded构建系统在` SRC_URI `｛.explored text role=“term”｝语句上搜索所有形式的内核元数据，而不管元数据是在\“内核缓存\”、系统内核元数据还是配方空间元数据（即内核配方的一部分）中。有关更多信息，请参阅\“` kernel dev/advanced:kernel 元数据位置 `｛.depreted text role=“ref”｝\”部分。

When you specify the feature\'s `.scc` file on the `SRC_URI`{.interpreted-text role="term"} statement, the OpenEmbedded build system adds the directory of that `.scc` file along with all its subdirectories to the kernel feature search path. Because subdirectories are searched, you can reference a single `.scc` file in the `SRC_URI`{.interpreted-text role="term"} statement to reference multiple kernel features.

> 当您在“SRC_URI”｛.explored text role=“term”｝语句中指定功能的“.scc”文件时，OpenEmbedded 生成系统会将该“.scc’文件的目录及其所有子目录添加到内核功能搜索路径中。由于会搜索子目录，因此可以在 `SRC_URI`｛.depreted text role=“term”｝语句中引用单个 `.scc` 文件来引用多个内核功能。

Consider the following example that adds the \"test.scc\" feature to the build.

> 请考虑以下将“test.scc\”功能添加到生成中的示例。

1. *Create the Feature File:* Create a `.scc` file and locate it just as you would any other patch file, `.cfg` file, or fetcher item you specify in the `SRC_URI`{.interpreted-text role="term"} statement.

> 1.*创建功能文件：*创建一个 `.sccc` 文件并定位它，就像您在 `SRC_URI`｛.depreted text role=“term”｝语句中指定的任何其他修补程序文件、`.cfg` 文件或 fetcher 项一样。

```
::: note
::: title
Note
:::

-   You must add the directory of the `.scc` file to the fetcher\'s search path in the same manner as you would add a `.patch` file.
-   You can create additional `.scc` files beneath the directory that contains the file you are adding. All subdirectories are searched during the build as potential feature directories.
:::

Continuing with the example, suppose the \"test.scc\" feature you are adding has a `test.scc` file in the following directory:

    my_recipe
    |
    +-linux-yocto
       |
       +-test.cfg
       +-test.scc

In this example, the `linux-yocto` directory has both the feature `test.scc` file and a similarly named configuration fragment file `test.cfg`.
```

2. *Add the Feature File to SRC_URI:* Add the `.scc` file to the recipe\'s `SRC_URI`{.interpreted-text role="term"} statement:

> 2.*将功能文件添加到 SRC_URI:*将 `.scc` 文件添加到配方的 `SRC_URI`｛.depreted text role=“term”｝语句：

```
    SRC_URI += "file://test.scc"

The leading space before the path is important as the path is appended to the existing path.
```

3. *Specify the Feature as a Kernel Feature:* Use the `KERNEL_FEATURES`{.interpreted-text role="term"} statement to specify the feature as a kernel feature:

> 3.*将该功能指定为内核功能：*使用 `Kernel_Feature`｛.depreted text role=“term”｝语句将该功能定义为内核功能

```
    KERNEL_FEATURES += "test.scc"

The OpenEmbedded build system processes the kernel feature when it builds the kernel.

::: note
::: title
Note
:::

If other features are contained below \"test.scc\", then their directories are relative to the directory containing the `test.scc` file.
:::
```
