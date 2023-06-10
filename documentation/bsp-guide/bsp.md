---
tip: translate by openai@2023-06-10 09:57:02
...
---
title: Board Support Packages (BSP) \-\-- Developer\'s Guide
------------------------------------------------------

A Board Support Package (BSP) is a collection of information that defines how to support a particular hardware device, set of devices, or hardware platform. The BSP includes information about the hardware features present on the device and kernel configuration information along with any additional hardware drivers required. The BSP also lists any additional software components required in addition to a generic Linux software stack for both essential and optional platform features.

> 一个板支持包（BSP）是一组信息的集合，用于定义如何支持特定的硬件设备、设备组或硬件平台。BSP 包括有关设备上存在的硬件特征以及内核配置信息以及所需的任何其他硬件驱动程序的信息。BSP 还列出了除通用 Linux 软件堆栈之外所需的任何其他软件组件，用于必要的和可选的平台功能。

This guide presents information about BSP layers, defines a structure for components so that BSPs follow a commonly understood layout, discusses how to customize a recipe for a BSP, addresses BSP licensing, and provides information that shows you how to create a BSP Layer using the `bitbake-layers <bsp-guide/bsp:Creating a new BSP Layer Using the` bitbake-layers `Script>`{.interpreted-text role="ref"} tool.

> 这份指南提供有关 BSP 层的信息，定义组件的结构，以便 BSP 遵循一种通常理解的布局，讨论如何定制 BSP 的配方，解决 BSP 许可证，并提供信息，向您展示如何使用 `bitbake-layers` 工具创建 BSP 层。

# BSP Layers

A BSP consists of a file structure inside a base directory. Collectively, you can think of the base directory, its file structure, and the contents as a BSP layer. Although not a strict requirement, BSP layers in the Yocto Project use the following well-established naming convention:

> 一个 BSP 包括基础目录中的文件结构。总的来说，您可以把基础目录、其文件结构和内容看作一个 BSP 层。尽管不是严格的要求，Yocto 项目中的 BSP 层使用以下传统的命名约定：

```
meta-bsp_root_name
```

The string \"meta-\" is prepended to the machine or platform name, which is \"bsp_root_name\" in the above form.

::: note
::: title
Note
:::

Because the BSP layer naming convention is well-established, it is advisable to follow it when creating layers. Technically speaking, a BSP layer name does not need to start with `meta-`. However, various scripts and tools in the Yocto Project development environment assume this convention.

> 由于 BSP 层命名约定已经建立，因此在创建层时最好遵循它。从技术上讲，BSP 层名称不需要以 `meta-` 开头。但是，Yocto Project 开发环境中的各种脚本和工具都假定这种约定。
> :::

To help understand the BSP layer concept, consider the BSPs that the Yocto Project supports and provides with each release. You can see the layers in the `overview-manual/development-environment:yocto project source repositories`{.interpreted-text role="ref"} through a web interface at :yocto\_[git:%60/](git:%60/)`. If you go to that interface, you will find a list of repositories under \"Yocto Metadata Layers\".

> 为了帮助理解 BSP 层概念，请考虑 Yocto Project 支持并在每次发布中提供的 BSP。您可以通过网页界面在 overview-manual/development-environment:yocto 项目源存储库中看到这些层：yocto_[git:％60 /]（git：％60 /）。如果您访问该界面，您将在“Yocto 元数据层”下找到一个存储库列表。

::: note
::: title
Note
:::

Layers that are no longer actively supported as part of the Yocto Project appear under the heading \"Yocto Metadata Layer Archive.\"
:::

Each repository is a BSP layer supported by the Yocto Project (e.g. `meta-raspberrypi` and `meta-intel`). Each of these layers is a repository unto itself and clicking on the layer name displays two URLs from which you can clone the layer\'s repository to your local system. Here is an example that clones the Raspberry Pi BSP layer:

> 每个存储库都是 Yocto 项目支持的 BSP 层（例如 `meta-raspberrypi` 和 `meta-intel`）。每个层都是一个存储库，点击层名称将显示两个 URL，您可以将该层的存储库克隆到本地系统。这是克隆 Raspberry Pi BSP 层的示例：

```
$ git clone git://git.yoctoproject.org/meta-raspberrypi
```

In addition to BSP layers, the `meta-yocto-bsp` layer is part of the shipped `poky` repository. The `meta-yocto-bsp` layer maintains several \"reference\" BSPs including the ARM-based Beaglebone, MIPS-based EdgeRouter, and generic versions of both 32-bit and 64-bit IA machines.

> 除了 BSP 层之外，`meta-yocto-bsp` 层也是提供的 `poky` 仓库的一部分。 `meta-yocto-bsp` 层维护了多个“参考”BSP，包括基于 ARM 的 Beaglebone，基于 MIPS 的 EdgeRouter 以及 32 位和 64 位 IA 机器的通用版本。

For information on typical BSP development workflow, see the `bsp-guide/bsp:developing a board support package (bsp)`{.interpreted-text role="ref"} section. For more information on how to set up a local copy of source files from a Git repository, see the `dev-manual/start:locating yocto project source files`{.interpreted-text role="ref"} section in the Yocto Project Development Tasks Manual.

> 要了解典型的 BSP 开发工作流程，请参阅 bsp-guide / bsp：开发板支持包（BSP）部分。要了解有关如何从 Git 存储库设置本地源文件副本的更多信息，请参阅 Yocto 项目开发任务手册中的 dev-manual / start：定位 Yocto 项目源文件部分。

The BSP layer\'s base directory (`meta-bsp_root_name`) is the root directory of that Layer. This directory is what you add to the `BBLAYERS`{.interpreted-text role="term"} variable in the `conf/bblayers.conf` file found in your `Build Directory`{.interpreted-text role="term"}, which is established after you run the OpenEmbedded build environment setup script (i.e. ` ref-manual/structure:` oe-init-build-env `  `{.interpreted-text role="ref"}). Adding the root directory allows the `OpenEmbedded Build System`{.interpreted-text role="term"} to recognize the BSP layer and from it build an image. Here is an example:

> BSP 层的基础目录（`meta-bsp_root_name`）是该层的根目录。您需要将此目录添加到您的构建目录（即运行 OpenEmbedded 构建环境设置脚本（即 `ref-manual/structure:` oe-init-build-env `）后建立的目录中的` conf/bblayers.conf `文件中的` BBLAYERS `变量中。添加根目录允许` OpenEmbedded 构建系统 ` 识别 BSP 层，并从中构建镜像。以下是示例：

```
BBLAYERS ?= " \
   /usr/local/src/yocto/meta \
   /usr/local/src/yocto/meta-poky \
   /usr/local/src/yocto/meta-yocto-bsp \
   /usr/local/src/yocto/meta-mylayer \
   "
```

::: note
::: title
Note
:::

Ordering and `BBFILE_PRIORITY`{.interpreted-text role="term"} for the layers listed in `BBLAYERS`{.interpreted-text role="term"} matter. For example, if multiple layers define a machine configuration, the OpenEmbedded build system uses the last layer searched given similar layer priorities. The build system works from the top-down through the layers listed in `BBLAYERS`{.interpreted-text role="term"}.

> 订购和 `BBFILE_PRIORITY`{.interpreted-text role="term"}对于列在 `BBLAYERS`{.interpreted-text role="term"}中的层非常重要。例如，如果多个层定义了机器配置，OpenEmbedded 构建系统将使用给定相似层优先级的最后一层搜索。构建系统从 `BBLAYERS`{.interpreted-text role="term"}中列出的层顶部开始向下工作。
> :::

Some BSPs require or depend on additional layers beyond the BSP\'s root layer in order to be functional. In this case, you need to specify these layers in the `README` \"Dependencies\" section of the BSP\'s root layer. Additionally, if any build instructions exist for the BSP, you must add them to the \"Dependencies\" section.

> 有些 BSP 需要或依赖于 BSP 根层之外的其他层才能够正常工作。在这种情况下，您需要在 BSP 根层的“README”“依赖关系”部分中指定这些层。此外，如果存在任何 BSP 的构建说明，您必须将它们添加到“依赖关系”部分中。

Some layers function as a layer to hold other BSP layers. These layers are known as \"`container layers <Container Layer>`{.interpreted-text role="term"}\". An example of this type of layer is OpenEmbedded\'s :oe\_[git:%60meta-openbedded](git:%60meta-openbedded) \</meta-openembedded\>[ layer. The ]{.title-ref}[meta-openembedded]{.title-ref}[ layer contains many ]{.title-ref}[meta-\*]{.title-ref}` layers. In cases like this, you need to include the names of the actual layers you want to work with, such as:

> 一些层可以用作容纳其他 BSP 层的层。这些层被称为“容器层（Container Layer）”。这种类型的层的一个例子是 OpenEmbedded 的：oe_[git:`meta-openbedded`](git:%60meta-openbedded%60) \</meta-openembedded\>[层。]{.title-ref}[meta-openembedded]{.title-ref}[层包含许多]{.title-ref}[meta-\*]{.title-ref}层。在这种情况下，您需要包括实际要使用的层的名称，例如：

```
BBLAYERS ?= " \
  /usr/local/src/yocto/meta \
  /usr/local/src/yocto/meta-poky \
  /usr/local/src/yocto/meta-yocto-bsp \
  /usr/local/src/yocto/meta-mylayer \
  .../meta-openembedded/meta-oe \
  .../meta-openembedded/meta-perl \
  .../meta-openembedded/meta-networking \
  "
```

and so on.

For more information on layers, see the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section of the Yocto Project Development Tasks Manual.

> 要了解更多有关层的信息，请参阅 Yocto Project 开发任务手册中的“dev-manual / layers：理解和创建层”部分。

# Preparing Your Build Host to Work With BSP Layers

This section describes how to get your build host ready to work with BSP layers. Once you have the host set up, you can create the layer as described in the \"`bsp-guide/bsp:creating a new bsp layer using the` bitbake-layers `script`{.interpreted-text role="ref"}\" section.

> 这一节描述如何让构建主机准备好与 BSP 层面一起工作。一旦你设置好主机，就可以按照"bsp-guide/bsp:使用 bitbake-layers 脚本创建一个新的 bsp 层"部分描述的方式创建层。

::: note
::: title
Note
:::

For structural information on BSPs, see the `bsp-guide/bsp:example filesystem layout`{.interpreted-text role="ref"} section.
:::

1. _Set Up the Build Environment:_ Be sure you are set up to use BitBake in a shell. See the \"`dev-manual/start:preparing the build host`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual for information on how to get a build host ready that is either a native Linux machine or a machine that uses CROPS.

> 1. _设置构建环境:_ 确保您已经准备好在 shell 中使用 BitBake。有关如何准备构建主机（本地 Linux 机器或使用 CROPS 的机器）的信息，请参阅 Yocto 项目开发任务手册中的“dev-manual/start：准备构建主机”部分。

2. _Clone the poky Repository:_ You need to have a local copy of the Yocto Project `Source Directory`{.interpreted-text role="term"} (i.e. a local `poky` repository). See the \"`dev-manual/start:cloning the` poky `repository`{.interpreted-text role="ref"}\" and possibly the \"`dev-manual/start:checking out by branch in poky`{.interpreted-text role="ref"}\" or \"`dev-manual/start:checking out by tag in poky`{.interpreted-text role="ref"}\" sections all in the Yocto Project Development Tasks Manual for information on how to clone the `poky` repository and check out the appropriate branch for your work.

> 2. 克隆 poky 仓库：您需要本地拥有 Yocto Project 源代码目录（即本地 poky 仓库）。请参阅 Yocto Project 开发任务手册中的 "dev-manual/start：克隆 poky 仓库"、"dev-manual/start：在 poky 中按分支检出" 或 "dev-manual/start：在 poky 中按标签检出" 章节，了解如何克隆 poky 仓库并检出适合您工作的适当分支。

3. _Determine the BSP Layer You Want:_ The Yocto Project supports many BSPs, which are maintained in their own layers or in layers designed to contain several BSPs. To get an idea of machine support through BSP layers, you can look at the :yocto_dl:[index of machines \</releases/yocto/yocto-&DISTRO;/machines\>]{.title-ref} for the release.

> 3. 确定您想要的 BSP 层：Yocto 项目支持许多 BSP，它们存储在自己的层中或用于包含多个 BSP 的层中。要了解通过 BSP 层支持的机器，您可以查看发行版的：yocto_dl:[机器索引 \</releases/yocto/yocto-&DISTRO;/machines\>]{.title-ref}。

4. _Optionally Clone the meta-intel BSP Layer:_ If your hardware is based on current Intel CPUs and devices, you can leverage this BSP layer. For details on the `meta-intel` BSP layer, see the layer\'s :yocto\_[git:%60README](git:%60README) \</meta-intel/tree/README\>` file.

> 如果您的硬件基于当前的 Intel CPU 和设备，您可以利用此 BSP 层。有关 `meta-intel` BSP 层的详细信息，请参阅层的:yocto\_[git:%60README](git:%60README) \</meta-intel/tree/README\>` 文件。

1. _Navigate to Your Source Directory:_ Typically, you set up the `meta-intel` Git repository inside the `Source Directory`{.interpreted-text role="term"} (e.g. `poky`). :

> 请帮助我翻译，“1. _导航到您的源目录：_ 通常，您将 `meta-intel` Git 存储库设置在 `源目录`（例如 `poky`）中。”

```
  ```
  $ cd /home/you/poky
  ```
```

2. _Clone the Layer:_ :

   ```
   $ git clone git://git.yoctoproject.org/meta-intel.git
   Cloning into 'meta-intel'...
   remote: Counting objects: 15585, done.
   remote: Compressing objects: 100% (5056/5056), done.
   remote: Total 15585 (delta 9123), reused 15329 (delta 8867)
   Receiving objects: 100% (15585/15585), 4.51 MiB | 3.19 MiB/s, done.
   Resolving deltas: 100% (9123/9123), done.
   Checking connectivity... done.
   ```
3. _Check Out the Proper Branch:_ The branch you check out for `meta-intel` must match the same branch you are using for the Yocto Project release (e.g. `&DISTRO_NAME_NO_CAP;`):

> 3. 检出正确的分支：您检出的 `meta-intel` 分支必须与您正在使用的 Yocto Project 发行版（例如 `&DISTRO_NAME_NO_CAP;`）相匹配。

```
  ```
  $ cd meta-intel
  $ git checkout -b &DISTRO_NAME_NO_CAP; remotes/origin/&DISTRO_NAME_NO_CAP;
  Branch &DISTRO_NAME_NO_CAP; set up to track remote branch
  &DISTRO_NAME_NO_CAP; from origin.
  Switched to a new branch '&DISTRO_NAME_NO_CAP;'
  ```

  ::: note
  ::: title
  Note
  :::

  To see the available branch names in a cloned repository, use the `git branch -al` command. See the \"`dev-manual/start:checking out by branch in poky`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual for more information.
  :::
```

5. _Optionally Set Up an Alternative BSP Layer:_ If your hardware can be more closely leveraged to an existing BSP not within the `meta-intel` BSP layer, you can clone that BSP layer.

> 5. 可选地设置另一个 BSP 层：如果您的硬件可以更加紧密地利用 `meta-intel` BSP 层中不存在的现有 BSP，则可以克隆该 BSP 层。

The process is identical to the process used for the `meta-intel` layer except for the layer\'s name. For example, if you determine that your hardware most closely matches the `meta-raspberrypi`, clone that layer:

> 这个过程与为 `meta-intel` 层使用的过程完全相同，只是层的名称不同。例如，如果你确定你的硬件最接近 `meta-raspberrypi`，克隆该层：

```
$ git clone git://git.yoctoproject.org/meta-raspberrypi
Cloning into 'meta-raspberrypi'...
remote: Counting objects: 4743, done.
remote: Compressing objects: 100% (2185/2185), done.
remote: Total 4743 (delta 2447), reused 4496 (delta 2258)
Receiving objects: 100% (4743/4743), 1.18 MiB | 0 bytes/s, done.
Resolving deltas: 100% (2447/2447), done.
Checking connectivity... done.
```

6. _Initialize the Build Environment:_ While in the root directory of the Source Directory (i.e. `poky`), run the ` ref-manual/structure:` oe-init-build-env `  `{.interpreted-text role="ref"} environment setup script to define the OpenEmbedded build environment on your build host. :

> 在源代码目录（即 `poky`）的根目录中，运行 `ref-manual/structure:oe-init-build-env` 环境设置脚本，在您的构建主机上定义 OpenEmbedded 构建环境。

```
$ source oe-init-build-env
```

Among other things, the script creates the `Build Directory`{.interpreted-text role="term"}, which is `build` in this case and is located in the `Source Directory`{.interpreted-text role="term"}. After the script runs, your current working directory is set to the `build` directory.

> 脚本除了其他事情之外，还会创建“构建目录”，在本例中为“build”，位于“源目录”中。脚本运行后，当前工作目录将设置为“build”目录。

# Example Filesystem Layout

Defining a common BSP directory structure allows end-users to understand and become familiar with that standard. A common format also encourages standardization of software support for hardware.

> 定义一个共同的 BSP 目录结构可以让最终用户理解并熟悉该标准。一个通用的格式也鼓励对硬件的软件支持进行标准化。

The proposed form described in this section does have elements that are specific to the OpenEmbedded build system. It is intended that developers can use this structure with other build systems besides the OpenEmbedded build system. It is also intended that it will be simple to extract information and convert it to other formats if required. The OpenEmbedded build system, through its standard `layers mechanism <overview-manual/yp-intro:the yocto project layer model>`{.interpreted-text role="ref"}, can directly accept the format described as a layer. The BSP layer captures all the hardware-specific details in one place using a standard format, which is useful for any person wishing to use the hardware platform regardless of the build system they are using.

> 本节中描述的建议形式确实具有与 OpenEmbedded 构建系统特定的元素。预期开发人员可以使用此结构与除 OpenEmbedded 构建系统之外的其他构建系统。同时，也预期它将简单提取信息并将其转换为其他格式（如果需要）。通过其标准“层次结构”，OpenEmbedded 构建系统可以直接接受所描述的格式作为一个层次结构。 BSP 层次结构使用标准格式在一个位置捕获所有硬件特定的细节，这对于任何希望使用硬件平台的人来说都是有用的，而不管他们使用的构建系统是什么。

The BSP specification does not include a build system or other tools -the specification is concerned with the hardware-specific components only. At the end-distribution point, you can ship the BSP layer combined with a build system and other tools. Realize that it is important to maintain the distinction that the BSP layer, a build system, and tools are separate components that could be combined in certain end products.

> BSP 规范不包括构建系统或其他工具 - 该规范仅关注与硬件相关的组件。在最终发行点，您可以将 BSP 层与构建系统和其他工具结合在一起发货。要记住，BSP 层、构建系统和工具是独立的组件，可以结合在某些最终产品中。

Before looking at the recommended form for the directory structure inside a BSP layer, you should be aware that there are some requirements in order for a BSP layer to be considered compliant with the Yocto Project. For that list of requirements, see the \"`bsp-guide/bsp:released bsp requirements`{.interpreted-text role="ref"}\" section.

> 在查看 BSP 层内部目录结构的推荐形式之前，您应该知道，为了使 BSP 层符合 Yocto 项目的要求，必须满足一些要求。有关该要求列表，请参见“ bsp-guide/bsp：发布的 bsp 要求”部分。

Below is the typical directory structure for a BSP layer. While this basic form represents the standard, realize that the actual layout for individual BSPs could differ. :

> 以下是 BSP 层的典型目录结构。尽管这种基本形式代表了标准，但要意识到对于各个 BSP 来说，实际布局可能会有所不同。

```
meta-bsp_root_name/
meta-bsp_root_name/bsp_license_file
meta-bsp_root_name/README
meta-bsp_root_name/README.sources
meta-bsp_root_name/binary/bootable_images
meta-bsp_root_name/conf/layer.conf
meta-bsp_root_name/conf/machine/*.conf
meta-bsp_root_name/recipes-bsp/*
meta-bsp_root_name/recipes-core/*
meta-bsp_root_name/recipes-graphics/*
meta-bsp_root_name/recipes-kernel/linux/linux-yocto_kernel_rev.bbappend
```

Below is an example of the Raspberry Pi BSP layer that is available from the :yocto\_[git:%60Source](git:%60Source) Repositories \<\>`:

```none
meta-raspberrypi/COPYING.MIT
meta-raspberrypi/README.md
meta-raspberrypi/classes
meta-raspberrypi/classes/sdcard_image-rpi.bbclass
meta-raspberrypi/conf/
meta-raspberrypi/conf/layer.conf
meta-raspberrypi/conf/machine/
meta-raspberrypi/conf/machine/raspberrypi-cm.conf
meta-raspberrypi/conf/machine/raspberrypi-cm3.conf
meta-raspberrypi/conf/machine/raspberrypi.conf
meta-raspberrypi/conf/machine/raspberrypi0-wifi.conf
meta-raspberrypi/conf/machine/raspberrypi0.conf
meta-raspberrypi/conf/machine/raspberrypi2.conf
meta-raspberrypi/conf/machine/raspberrypi3-64.conf
meta-raspberrypi/conf/machine/raspberrypi3.conf
meta-raspberrypi/conf/machine/include
meta-raspberrypi/conf/machine/include/rpi-base.inc
meta-raspberrypi/conf/machine/include/rpi-default-providers.inc
meta-raspberrypi/conf/machine/include/rpi-default-settings.inc
meta-raspberrypi/conf/machine/include/rpi-default-versions.inc
meta-raspberrypi/conf/machine/include/tune-arm1176jzf-s.inc
meta-raspberrypi/docs
meta-raspberrypi/docs/Makefile
meta-raspberrypi/docs/conf.py
meta-raspberrypi/docs/contributing.md
meta-raspberrypi/docs/extra-apps.md
meta-raspberrypi/docs/extra-build-config.md
meta-raspberrypi/docs/index.rst
meta-raspberrypi/docs/layer-contents.md
meta-raspberrypi/docs/readme.md
meta-raspberrypi/files
meta-raspberrypi/files/custom-licenses
meta-raspberrypi/files/custom-licenses/Broadcom
meta-raspberrypi/recipes-bsp
meta-raspberrypi/recipes-bsp/bootfiles
meta-raspberrypi/recipes-bsp/bootfiles/bcm2835-bootfiles.bb
meta-raspberrypi/recipes-bsp/bootfiles/rpi-config_git.bb
meta-raspberrypi/recipes-bsp/common
meta-raspberrypi/recipes-bsp/common/firmware.inc
meta-raspberrypi/recipes-bsp/formfactor
meta-raspberrypi/recipes-bsp/formfactor/formfactor
meta-raspberrypi/recipes-bsp/formfactor/formfactor/raspberrypi
meta-raspberrypi/recipes-bsp/formfactor/formfactor/raspberrypi/machconfig
meta-raspberrypi/recipes-bsp/formfactor/formfactor_0.0.bbappend
meta-raspberrypi/recipes-bsp/rpi-u-boot-src
meta-raspberrypi/recipes-bsp/rpi-u-boot-src/files
meta-raspberrypi/recipes-bsp/rpi-u-boot-src/files/boot.cmd.in
meta-raspberrypi/recipes-bsp/rpi-u-boot-src/rpi-u-boot-scr.bb
meta-raspberrypi/recipes-bsp/u-boot
meta-raspberrypi/recipes-bsp/u-boot/u-boot
meta-raspberrypi/recipes-bsp/u-boot/u-boot/*.patch
meta-raspberrypi/recipes-bsp/u-boot/u-boot_%.bbappend
meta-raspberrypi/recipes-connectivity
meta-raspberrypi/recipes-connectivity/bluez5
meta-raspberrypi/recipes-connectivity/bluez5/bluez5
meta-raspberrypi/recipes-connectivity/bluez5/bluez5/*.patch
meta-raspberrypi/recipes-connectivity/bluez5/bluez5/BCM43430A1.hcd
meta-raspberrypi/recipes-connectivity/bluez5/bluez5brcm43438.service
meta-raspberrypi/recipes-connectivity/bluez5/bluez5_%.bbappend
meta-raspberrypi/recipes-core
meta-raspberrypi/recipes-core/images
meta-raspberrypi/recipes-core/images/rpi-basic-image.bb
meta-raspberrypi/recipes-core/images/rpi-hwup-image.bb
meta-raspberrypi/recipes-core/images/rpi-test-image.bb
meta-raspberrypi/recipes-core/packagegroups
meta-raspberrypi/recipes-core/packagegroups/packagegroup-rpi-test.bb
meta-raspberrypi/recipes-core/psplash
meta-raspberrypi/recipes-core/psplash/files
meta-raspberrypi/recipes-core/psplash/files/psplash-raspberrypi-img.h
meta-raspberrypi/recipes-core/psplash/psplash_git.bbappend
meta-raspberrypi/recipes-core/udev
meta-raspberrypi/recipes-core/udev/udev-rules-rpi
meta-raspberrypi/recipes-core/udev/udev-rules-rpi/99-com.rules
meta-raspberrypi/recipes-core/udev/udev-rules-rpi.bb
meta-raspberrypi/recipes-devtools
meta-raspberrypi/recipes-devtools/bcm2835
meta-raspberrypi/recipes-devtools/bcm2835/bcm2835_1.52.bb
meta-raspberrypi/recipes-devtools/pi-blaster
meta-raspberrypi/recipes-devtools/pi-blaster/files
meta-raspberrypi/recipes-devtools/pi-blaster/files/*.patch
meta-raspberrypi/recipes-devtools/pi-blaster/pi-blaster_git.bb
meta-raspberrypi/recipes-devtools/python
meta-raspberrypi/recipes-devtools/python/python-rtimu
meta-raspberrypi/recipes-devtools/python/python-rtimu/*.patch
meta-raspberrypi/recipes-devtools/python/python-rtimu_git.bb
meta-raspberrypi/recipes-devtools/python/python-sense-hat_2.2.0.bb
meta-raspberrypi/recipes-devtools/python/rpi-gpio
meta-raspberrypi/recipes-devtools/python/rpi-gpio/*.patch
meta-raspberrypi/recipes-devtools/python/rpi-gpio_0.6.3.bb
meta-raspberrypi/recipes-devtools/python/rpio
meta-raspberrypi/recipes-devtools/python/rpio/*.patch
meta-raspberrypi/recipes-devtools/python/rpio_0.10.0.bb
meta-raspberrypi/recipes-devtools/wiringPi
meta-raspberrypi/recipes-devtools/wiringPi/files
meta-raspberrypi/recipes-devtools/wiringPi/files/*.patch
meta-raspberrypi/recipes-devtools/wiringPi/wiringpi_git.bb
meta-raspberrypi/recipes-graphics
meta-raspberrypi/recipes-graphics/eglinfo
meta-raspberrypi/recipes-graphics/eglinfo/eglinfo-fb_%.bbappend
meta-raspberrypi/recipes-graphics/eglinfo/eglinfo-x11_%.bbappend
meta-raspberrypi/recipes-graphics/mesa
meta-raspberrypi/recipes-graphics/mesa/mesa-gl_%.bbappend
meta-raspberrypi/recipes-graphics/mesa/mesa_%.bbappend
meta-raspberrypi/recipes-graphics/userland
meta-raspberrypi/recipes-graphics/userland/userland
meta-raspberrypi/recipes-graphics/userland/userland/*.patch
meta-raspberrypi/recipes-graphics/userland/userland_git.bb
meta-raspberrypi/recipes-graphics/vc-graphics
meta-raspberrypi/recipes-graphics/vc-graphics/files
meta-raspberrypi/recipes-graphics/vc-graphics/files/egl.pc
meta-raspberrypi/recipes-graphics/vc-graphics/files/vchiq.sh
meta-raspberrypi/recipes-graphics/vc-graphics/vc-graphics-hardfp.bb
meta-raspberrypi/recipes-graphics/vc-graphics/vc-graphics.bb
meta-raspberrypi/recipes-graphics/vc-graphics/vc-graphics.inc
meta-raspberrypi/recipes-graphics/wayland
meta-raspberrypi/recipes-graphics/wayland/weston_%.bbappend
meta-raspberrypi/recipes-graphics/xorg-xserver
meta-raspberrypi/recipes-graphics/xorg-xserver/xserver-xf86-config
meta-raspberrypi/recipes-graphics/xorg-xserver/xserver-xf86-config/rpi
meta-raspberrypi/recipes-graphics/xorg-xserver/xserver-xf86-config/rpi/xorg.conf
meta-raspberrypi/recipes-graphics/xorg-xserver/xserver-xf86-config/rpi/xorg.conf.d
meta-raspberrypi/recipes-graphics/xorg-xserver/xserver-xf86-config/rpi/xorg.conf.d/10-evdev.conf
meta-raspberrypi/recipes-graphics/xorg-xserver/xserver-xf86-config/rpi/xorg.conf.d/98-pitft.conf
meta-raspberrypi/recipes-graphics/xorg-xserver/xserver-xf86-config/rpi/xorg.conf.d/99-calibration.conf
meta-raspberrypi/recipes-graphics/xorg-xserver/xserver-xf86-config_0.1.bbappend
meta-raspberrypi/recipes-graphics/xorg-xserver/xserver-xorg_%.bbappend
meta-raspberrypi/recipes-kernel
meta-raspberrypi/recipes-kernel/linux-firmware
meta-raspberrypi/recipes-kernel/linux-firmware/files
meta-raspberrypi/recipes-kernel/linux-firmware/files/brcmfmac43430-sdio.bin
meta-raspberrypi/recipes-kernel/linux-firmware/files/brcfmac43430-sdio.txt
meta-raspberrypi/recipes-kernel/linux-firmware/linux-firmware_%.bbappend
meta-raspberrypi/recipes-kernel/linux
meta-raspberrypi/recipes-kernel/linux/linux-raspberrypi-dev.bb
meta-raspberrypi/recipes-kernel/linux/linux-raspberrypi.inc
meta-raspberrypi/recipes-kernel/linux/linux-raspberrypi_4.14.bb
meta-raspberrypi/recipes-kernel/linux/linux-raspberrypi_4.9.bb
meta-raspberrypi/recipes-multimedia
meta-raspberrypi/recipes-multimedia/gstreamer
meta-raspberrypi/recipes-multimedia/gstreamer/gstreamer1.0-omx
meta-raspberrypi/recipes-multimedia/gstreamer/gstreamer1.0-omx/*.patch
meta-raspberrypi/recipes-multimedia/gstreamer/gstreamer1.0-omx_%.bbappend
meta-raspberrypi/recipes-multimedia/gstreamer/gstreamer1.0-plugins-bad_%.bbappend
meta-raspberrypi/recipes-multimedia/gstreamer/gstreamer1.0-omx-1.12
meta-raspberrypi/recipes-multimedia/gstreamer/gstreamer1.0-omx-1.12/*.patch
meta-raspberrypi/recipes-multimedia/omxplayer
meta-raspberrypi/recipes-multimedia/omxplayer/omxplayer
meta-raspberrypi/recipes-multimedia/omxplayer/omxplayer/*.patch
meta-raspberrypi/recipes-multimedia/omxplayer/omxplayer_git.bb
meta-raspberrypi/recipes-multimedia/x264
meta-raspberrypi/recipes-multimedia/x264/x264_git.bbappend
meta-raspberrypi/wic meta-raspberrypi/wic/sdimage-raspberrypi.wks
```

The following sections describe each part of the proposed BSP format.

## License Files

You can find these files in the BSP Layer at:

```
meta-bsp_root_name/bsp_license_file
```

These optional files satisfy licensing requirements for the BSP. The type or types of files here can vary depending on the licensing requirements. For example, in the Raspberry Pi BSP, all licensing requirements are handled with the `COPYING.MIT` file.

> 这些可选文件满足 BSP 的许可要求。这里的文件类型可能因许可要求而有所不同。例如，在 Raspberry Pi BSP 中，所有许可要求都通过 `COPYING.MIT` 文件处理。

Licensing files can be MIT, BSD, GPLv\*, and so forth. These files are recommended for the BSP but are optional and totally up to the BSP developer. For information on how to maintain license compliance, see the \"`dev-manual/licenses:maintaining open source license compliance during your product's lifecycle`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 许可文件可以是 MIT、BSD、GPLv*等。这些文件是针对 BSP 推荐的，但是可选的，完全取决于 BSP 开发者。有关如何维护许可证合规性的信息，请参见 Yocto 项目开发任务手册中的“dev-manual/licenses：在产品生命周期中维护开源许可证合规性”部分。

## README File

You can find this file in the BSP Layer at:

```
meta-bsp_root_name/README
```

This file provides information on how to boot the live images that are optionally included in the `binary/` directory. The `README` file also provides information needed for building the image.

> 这个文件提供有关如何启动可选包含在“binary/”目录中的实时镜像的信息。 `README` 文件还提供构建镜像所需的信息。

At a minimum, the `README` file must contain a list of dependencies, such as the names of any other layers on which the BSP depends and the name of the BSP maintainer with his or her contact information.

> 至少，`README` 文件必须包含一个依赖项列表，比如 BSP 所依赖的其他层的名称以及 BSP 维护者的姓名及联系方式。

## README.sources File

You can find this file in the BSP Layer at:

```
meta-bsp_root_name/README.sources
```

This file provides information on where to locate the BSP source files used to build the images (if any) that reside in `meta-bsp_root_name/binary`. Images in the `binary` would be images released with the BSP. The information in the `README.sources` file also helps you find the `Metadata`{.interpreted-text role="term"} used to generate the images that ship with the BSP.

> 这个文件提供关于如何找到用于构建位于'meta-bsp_root_name/binary'中的图像（如果有的话）的 BSP 源文件的信息。 `binary` 中的图像将是与 BSP 一起发布的图像。 `README.sources` 文件中的信息也可以帮助您找到用于生成与 BSP 一起发布的图像的元数据。

::: note
::: title
Note
:::

If the BSP\'s `binary` directory is missing or the directory has no images, an existing `README.sources` file is meaningless and usually does not exist.

> 如果 BSP 的“二进制”目录丢失或者该目录没有图像，那么现有的“README.sources”文件毫无意义，通常也不存在。
> :::

## Pre-built User Binaries

You can find these files in the BSP Layer at:

```
meta-bsp_root_name/binary/bootable_images
```

This optional area contains useful pre-built kernels and user-space filesystem images released with the BSP that are appropriate to the target system. This directory typically contains graphical (e.g. Sato) and minimal live images when the BSP tarball has been created and made available in the :yocto_home:[Yocto Project \<\>]{.title-ref} website. You can use these kernels and images to get a system running and quickly get started on development tasks.

> 这个可选区域包含了随 BSP 发布的有用的预构建内核和用户空间文件系统映像，适用于目标系统。当 BSP tarball 已经创建并在 :yocto_home:[Yocto Project \<\>]{.title-ref} 网站上可用时，该目录通常包含图形（例如 Sato）和最小的实时映像。您可以使用这些内核和映像来启动系统并快速开始开发任务。

The exact types of binaries present are highly hardware-dependent. The `README <bsp-guide/bsp:readme file>`{.interpreted-text role="ref"} file should be present in the BSP Layer and it explains how to use the images with the target hardware. Additionally, the `README.sources <bsp-guide/bsp:readme.sources file>`{.interpreted-text role="ref"} file should be present to locate the sources used to build the images and provide information on the Metadata.

> 确切的二进制类型取决于硬件。BSP 层应该包含一个 `README <bsp-guide/bsp:readme file>`{.interpreted-text role="ref"}文件，用于解释如何在目标硬件上使用这些图像。此外，应该存在 `README.sources <bsp-guide/bsp:readme.sources file>`{.interpreted-text role="ref"}文件，用于定位用于构建图像的源，并提供元数据信息。

## Layer Configuration File

You can find this file in the BSP Layer at:

```
meta-bsp_root_name/conf/layer.conf
```

The `conf/layer.conf` file identifies the file structure as a layer, identifies the contents of the layer, and contains information about how the build system should use it. Generally, a standard boilerplate file such as the following works. In the following example, you would replace \"bsp\" with the actual name of the BSP (i.e. \"bsp_root_name\" from the example template). :

> `conf/layer.conf` 文件将文件结构标识为一个层，识别层的内容，并包含有关构建系统如何使用它的信息。通常，标准样板文件如下所示可以正常工作。在下面的示例中，您将用实际的 BSP 名称（即示例模板中的“bsp_root_name”）替换“bsp”。

```
# We have a conf and classes directory, add to BBPATH
BBPATH .= ":${LAYERDIR}"

# We have a recipes directory containing .bb and .bbappend files, add to BBFILES
BBFILES += "${LAYERDIR}/recipes-*/*/*.bb \
            ${LAYERDIR}/recipes-*/*/*.bbappend"

BBFILE_COLLECTIONS += "bsp"
BBFILE_PATTERN_bsp = "^${LAYERDIR}/"
BBFILE_PRIORITY_bsp = "6"
LAYERDEPENDS_bsp = "intel"
```

To illustrate the string substitutions, here are the corresponding statements from the Raspberry Pi `conf/layer.conf` file:

```
# We have a conf and classes directory, append to BBPATH
BBPATH .= ":${LAYERDIR}"

# We have a recipes directory containing .bb and .bbappend files, add to BBFILES
BBFILES += "${LAYERDIR}/recipes*/*/*.bb \
            ${LAYERDIR}/recipes*/*/*.bbappend"

BBFILE_COLLECTIONS += "raspberrypi"
BBFILE_PATTERN_raspberrypi := "^${LAYERDIR}/"
BBFILE_PRIORITY_raspberrypi = "9"

# Additional license directories.
LICENSE_PATH += "${LAYERDIR}/files/custom-licenses"
.
.
.
```

This file simply makes `BitBake`{.interpreted-text role="term"} aware of the recipes and configuration directories. The file must exist so that the OpenEmbedded build system can recognize the BSP.

> 这个文件只是让 BitBake 知道食谱和配置目录。该文件必须存在，以便 OpenEmbedded 构建系统能够识别 BSP。

## Hardware Configuration Options

You can find these files in the BSP Layer at:

```
meta-bsp_root_name/conf/machine/*.conf
```

The machine files bind together all the information contained elsewhere in the BSP into a format that the build system can understand. Each BSP Layer requires at least one machine file. If the BSP supports multiple machines, multiple machine configuration files can exist. These filenames correspond to the values to which users have set the `MACHINE`{.interpreted-text role="term"} variable.

> 机器文件将 BSP 中的所有信息绑定在一起，以便构建系统能够理解。每个 BSP 层至少需要一个机器文件。如果 BSP 支持多台机器，则可以存在多个机器配置文件。这些文件名对应于用户设置的 `MACHINE`{.interpreted-text role="term"}变量的值。

These files define things such as the kernel package to use (`PREFERRED_PROVIDER`{.interpreted-text role="term"} of `virtual/kernel <dev-manual/new-recipe:using virtual providers>`{.interpreted-text role="ref"}), the hardware drivers to include in different types of images, any special software components that are needed, any bootloader information, and also any special image format requirements.

> 这些文件定义了诸如要使用的内核包（`virtual/kernel <dev-manual/new-recipe:using virtual providers>`{.interpreted-text role="ref"}的 `PREFERRED_PROVIDER`{.interpreted-text role="term"}）、不同类型图像中要包含的硬件驱动程序、所需的任何特殊软件组件、任何引导加载程序信息以及任何特殊图像格式要求的内容。

This configuration file could also include a hardware \"tuning\" file that is commonly used to define the package architecture and specify optimization flags, which are carefully chosen to give best performance on a given processor.

> 这个配置文件也可以包括一个常用的硬件“调整”文件，用于定义包架构并指定优化标志，这些标志经过精心挑选，可以在给定处理器上获得最佳性能。

Tuning files are found in the `meta/conf/machine/include` directory within the `Source Directory`{.interpreted-text role="term"}. For example, many `tune-*` files (e.g. `tune-arm1136jf-s.inc`, `tune-1586-nlp.inc`, and so forth) reside in the `poky/meta/conf/machine/include` directory.

> 文件调整可以在源目录的 `meta/conf/machine/include` 目录中找到。例如，许多 `tune-*` 文件（例如 `tune-arm1136jf-s.inc`，`tune-1586-nlp.inc` 等）都位于 `poky/meta/conf/machine/include` 目录中。

To use an include file, you simply include them in the machine configuration file. For example, the Raspberry Pi BSP `raspberrypi3.conf` contains the following statement:

> 使用包含文件，只需将它们包含在机器配置文件中即可。例如，Raspberry Pi BSP “raspberrypi3.conf”包含以下语句：

```
include conf/machine/include/rpi-base.inc
```

## Miscellaneous BSP-Specific Recipe Files

You can find these files in the BSP Layer at:

```
meta-bsp_root_name/recipes-bsp/*
```

This optional directory contains miscellaneous recipe files for the BSP. Most notably would be the formfactor files. For example, in the Raspberry Pi BSP, there is the `formfactor_0.0.bbappend` file, which is an append file used to augment the recipe that starts the build. Furthermore, there are machine-specific settings used during the build that are defined by the `machconfig` file further down in the directory. Here is the `machconfig` file for the Raspberry Pi BSP:

> 这个可选的目录包含 BSP 的各种配方文件。最值得注意的是 formfactor 文件。例如，在 Raspberry Pi BSP 中，有一个 `formfactor_0.0.bbappend` 文件，它是一个用于增强开始构建的配方的追加文件。此外，还有在构建期间定义的特定于机器的设置，由更深处的目录中的 `machconfig` 文件定义。这里是 Raspberry Pi BSP 的 `machconfig` 文件：

```
HAVE_TOUCHSCREEN=0
HAVE_KEYBOARD=1

DISPLAY_CAN_ROTATE=0
DISPLAY_ORIENTATION=0
DISPLAY_DPI=133
```

::: note
::: title
Note
:::

If a BSP does not have a formfactor entry, defaults are established according to the formfactor configuration file that is installed by the main formfactor recipe `meta/recipes-bsp/formfactor/formfactor_0.0.bb`, which is found in the `Source Directory`{.interpreted-text role="term"}.

> 如果 BSP 没有 formfactor 条目，则根据由主 formfactor 配方 `meta/recipes-bsp/formfactor/formfactor_0.0.bb` 安装的 formfactor 配置文件来建立默认值，该文件位于“源目录”中。
> :::

## Display Support Files

You can find these files in the BSP Layer at:

```
meta-bsp_root_name/recipes-graphics/*
```

This optional directory contains recipes for the BSP if it has special requirements for graphics support. All files that are needed for the BSP to support a display are kept here.

> 这个可选目录包含了 BSP 的配方，如果它对图形支持有特殊要求的话。所有 BSP 支持显示所需要的文件都保存在这里。

## Linux Kernel Configuration

You can find these files in the BSP Layer at:

```
meta-bsp_root_name/recipes-kernel/linux/linux*.bbappend
meta-bsp_root_name/recipes-kernel/linux/*.bb
```

Append files (`*.bbappend`) modify the main kernel recipe being used to build the image. The `*.bb` files would be a developer-supplied kernel recipe. This area of the BSP hierarchy can contain both these types of files although, in practice, it is likely that you would have one or the other.

> 追加文件（`*.bbappend`）修改用于构建镜像的主内核配方。 `*.bb` 文件将是开发人员提供的内核配方。 该 BSP 层次结构可以包含这两种类型的文件，尽管实际上您可能只有一种或另一种。

For your BSP, you typically want to use an existing Yocto Project kernel recipe found in the `Source Directory`{.interpreted-text role="term"} at `meta/recipes-kernel/linux`. You can append machine-specific changes to the kernel recipe by using a similarly named append file, which is located in the BSP Layer for your target device (e.g. the `meta-bsp_root_name/recipes-kernel/linux` directory).

> 对于您的 BSP，通常您希望使用 Yocto Project 内核配方，该配方可在“源目录”中的 `meta/recipes-kernel/linux` 中找到。您可以通过使用类似命名的追加文件（位于您的目标设备的 BSP 层中，例如 `meta-bsp_root_name/recipes-kernel/linux` 目录），将机器特定的更改附加到内核配方。

Suppose you are using the `linux-yocto_4.4.bb` recipe to build the kernel. In other words, you have selected the kernel in your `"bsp_root_name".conf` file by adding `PREFERRED_PROVIDER`{.interpreted-text role="term"} and `PREFERRED_VERSION`{.interpreted-text role="term"} statements as follows:

> 假设您正在使用 `linux-yocto_4.4.bb` 配方来构建内核。换句话说，您已经通过添加 `PREFERRED_PROVIDER` 和 `PREFERRED_VERSION` 语句来在您的 `"bsp_root_name" .conf` 文件中选择内核：

```
PREFERRED_PROVIDER_virtual/kernel ?= "linux-yocto"
PREFERRED_VERSION_linux-yocto ?= "4.4%"
```

::: note
::: title
Note
:::

When the preferred provider is assumed by default, the `PREFERRED_PROVIDER`{.interpreted-text role="term"} statement does not appear in the `"bsp_root_name".conf` file.

> 当默认假定首选提供者时，`PREFERRED_PROVIDER`{.interpreted-text role="term"}声明不会出现在 `"bsp_root_name".conf` 文件中。
> :::

You would use the `linux-yocto_4.4.bbappend` file to append specific BSP settings to the kernel, thus configuring the kernel for your particular BSP.

> 你可以使用 `linux-yocto_4.4.bbappend` 文件来附加特定的 BSP 设置到内核，从而为你的特定 BSP 配置内核。

You can find more information on what your append file should contain in the \"`kernel-dev/common:creating the append file`{.interpreted-text role="ref"}\" section in the Yocto Project Linux Kernel Development Manual.

> 您可以在 Yocto 项目 Linux 内核开发手册中的“kernel-dev/common：创建附加文件”部分找到有关您的附加文件应包含的更多信息。

An alternate scenario is when you create your own kernel recipe for the BSP. A good example of this is the Raspberry Pi BSP. If you examine the `recipes-kernel/linux` directory you see the following:

> 另一种情况是，您可以为 BSP 创建自己的内核配方。树莓派 BSP 就是一个很好的例子。如果您检查 `recipes-kernel/linux` 目录，您会看到以下内容：

```
linux-raspberrypi-dev.bb
linux-raspberrypi.inc
linux-raspberrypi_4.14.bb
linux-raspberrypi_4.9.bb
```

The directory contains three kernel recipes and a common include file.

# Developing a Board Support Package (BSP)

This section describes the high-level procedure you can follow to create a BSP. Although not required for BSP creation, the `meta-intel` repository, which contains many BSPs supported by the Yocto Project, is part of the example.

> 这一节描述了您可以遵循的创建 BSP 的高级过程。尽管不是 BSP 创建所必需的，但包含许多由 Yocto 项目支持的 BSP 的 `meta-intel` 存储库是本例的一部分。

For an example that shows how to create a new layer using the tools, see the \"`bsp-guide/bsp:creating a new bsp layer using the` bitbake-layers `script`{.interpreted-text role="ref"}\" section.

> 为了查看如何使用工具创建新层的示例，请参阅“bsp-guide / bsp：使用 bitbake-layers 脚本创建新的 bsp 层”部分。

The following illustration and list summarize the BSP creation general workflow.

![image](figures/bsp-dev-flow.png){.align-center width="70.0%"}

1. _Set up Your Host Development System to Support Development Using the Yocto Project_: See the \"`dev-manual/start:preparing the build host`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual for options on how to get a system ready to use the Yocto Project.

> 查看 Yocto Project 开发任务手册中的“dev-manual/start：准备构建主机”部分，了解如何使用 Yocto Project 准备系统。

2. _Establish the meta-intel Repository on Your System:_ Having local copies of these supported BSP layers on your system gives you access to layers you might be able to leverage when creating your BSP. For information on how to get these files, see the \"`bsp-guide/bsp:preparing your build host to work with bsp layers`{.interpreted-text role="ref"}\" section.

> 2. 在您的系统上建立 meta-intel 存储库：在您的系统上拥有这些受支持的 BSP 层的本地副本可以让您访问创建 BSP 时可能可以利用的层。有关如何获取这些文件的信息，请参阅“bsp-guide / bsp：准备您的构建主机以使用 bsp 层”部分。

3. _Create Your Own BSP Layer Using the bitbake-layers Script:_ Layers are ideal for isolating and storing work for a given piece of hardware. A layer is really just a location or area in which you place the recipes and configurations for your BSP. In fact, a BSP is, in itself, a special type of layer. The simplest way to create a new BSP layer that is compliant with the Yocto Project is to use the `bitbake-layers` script. For information about that script, see the \"`bsp-guide/bsp:creating a new bsp layer using the` bitbake-layers `script`{.interpreted-text role="ref"}\" section.

> 使用 bitbake-layers 脚本创建自己的 BSP 层：层对于隔离和存储给定硬件的工作是理想的。层实际上只是您放置 BSP 食谱和配置的位置或区域。实际上，BSP 本身就是一种特殊类型的层。创建符合 Yocto 项目的新 BSP 层的最简单方法是使用 `bitbake-layers` 脚本。有关该脚本的信息，请参阅“bsp-guide / bsp：使用”bitbake-layers“脚本创建新的 bsp 层”部分。

Another example that illustrates a layer is an application. Suppose you are creating an application that has library or other dependencies in order for it to compile and run. The layer, in this case, would be where all the recipes that define those dependencies are kept. The key point for a layer is that it is an isolated area that contains all the relevant information for the project that the OpenEmbedded build system knows about. For more information on layers, see the \"`overview-manual/yp-intro:the yocto project layer model`{.interpreted-text role="ref"}\" section in the Yocto Project Overview and Concepts Manual. You can also reference the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual. For more information on BSP layers, see the \"`bsp-guide/bsp:bsp layers`{.interpreted-text role="ref"}\" section.

> 另一个示例来说明图层是一个应用程序。假设您正在创建一个应用程序，该应用程序需要库或其他依赖项才能编译和运行。在这种情况下，图层将是定义这些依赖项的所有配方所保留的地方。图层的关键点是，它是一个隔离的区域，其中包含 OpenEmbedded 构建系统所知道的有关该项目的所有相关信息。有关图层的更多信息，请参阅 Yocto 项目概述和概念手册中的“Yocto 项目图层模型”部分。您还可以参考 Yocto 项目开发任务手册中的“理解和创建图层”部分。有关 BSP 图层的更多信息，请参阅“BSP 图层”部分。

::: note
::: title
Note
:::

- There are four hardware reference BSPs in the Yocto Project release, located in the `poky/meta-yocto-bsp` BSP layer:
  - Texas Instruments Beaglebone (`beaglebone-yocto`)
  - Ubiquiti Networks EdgeRouter Lite (`edgerouter`)
  - Two general IA platforms (`genericx86` and `genericx86-64`)
- There are three core Intel BSPs in the Yocto Project release, in the `meta-intel` layer:
  - `intel-core2-32`, which is a BSP optimized for the Core2 family of CPUs as well as all CPUs prior to the Silvermont core.
  - `intel-corei7-64`, which is a BSP optimized for Nehalem and later Core and Xeon CPUs as well as Silvermont and later Atom CPUs, such as the Baytrail SoCs.
  - `intel-quark`, which is a BSP optimized for the Intel Galileo gen1 & gen2 development boards.
    :::

When you set up a layer for a new BSP, you should follow a standard layout. This layout is described in the \"`bsp-guide/bsp:example filesystem layout`{.interpreted-text role="ref"}\" section. In the standard layout, notice the suggested structure for recipes and configuration information. You can see the standard layout for a BSP by examining any supported BSP found in the `meta-intel` layer inside the Source Directory.

> 当你为新的 BSP 设置一个层时，你应该遵循标准布局。这个布局被描述在"bsp-guide/bsp:example 文件系统布局"部分中。在标准布局中，注意针对食谱和配置信息的建议结构。你可以通过检查源目录中 meta-intel 层内的任何受支持的 BSP 来查看 BSP 的标准布局。

4. _Make Configuration Changes to Your New BSP Layer:_ The standard BSP layer structure organizes the files you need to edit in `conf` and several `recipes-*` directories within the BSP layer. Configuration changes identify where your new layer is on the local system and identifies the kernel you are going to use. When you run the `bitbake-layers` script, you are able to interactively configure many things for the BSP (e.g. keyboard, touchscreen, and so forth).

> 4. 修改新 BSP 层的配置：标准 BSP 层结构将您需要编辑的文件组织在 `conf` 和 BSP 层内的几个 `recipes-*` 目录中。配置更改将您的新层放置在本地系统上的位置标识出来，并标识出要使用的内核。当您运行 `bitbake-layers` 脚本时，您可以为 BSP 交互式配置许多内容（例如键盘，触摸屏等）。

5. _Make Recipe Changes to Your New BSP Layer:_ Recipe changes include altering recipes (`*.bb` files), removing recipes you do not use, and adding new recipes or append files (`.bbappend`) that support your hardware.

> 5. 对新的 BSP 层做出菜谱更改：更改菜谱（`*.bb` 文件）、删除不用的菜谱，以及添加新的菜谱或者添加支持你的硬件的 `.bbappend` 文件。

6. _Prepare for the Build:_ Once you have made all the changes to your BSP layer, there remains a few things you need to do for the OpenEmbedded build system in order for it to create your image. You need to get the build environment ready by sourcing an environment setup script (i.e. `oe-init-build-env`) and you need to be sure two key configuration files are configured appropriately: the `conf/local.conf` and the `conf/bblayers.conf` file. You must make the OpenEmbedded build system aware of your new layer. See the \"`dev-manual/layers:enabling your layer`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual for information on how to let the build system know about your new layer.

> 准备构建：在您对 BSP 层做出所有更改之后，您还需要为 OpenEmbedded 构建系统做一些事情，以便创建您的映像。您需要通过源环境设置脚本（即 `oe-init-build-env`）准备构建环境，并且确保两个关键配置文件配置正确：`conf/local.conf` 和 `conf/bblayers.conf` 文件。您必须让 OpenEmbedded 构建系统意识到您的新层。有关如何让构建系统知道您的新层的信息，请参见 Yocto 项目开发任务手册中的“dev-manual/layers：启用您的层”部分。

7. _Build the Image:_ The OpenEmbedded build system uses the BitBake tool to build images based on the type of image you want to create. You can find more information about BitBake in the `BitBake User Manual <bitbake:index>`{.interpreted-text role="doc"}.

> _建立映像：_OpenEmbedded 构建系统使用 BitBake 工具根据您要创建的映像类型来构建映像。您可以在“BitBake 用户手册 <bitbake：index>”中找到更多有关 BitBake 的信息。

The build process supports several types of images to satisfy different needs. See the \"`ref-manual/images:Images`{.interpreted-text role="ref"}\" chapter in the Yocto Project Reference Manual for information on supported images.

> 构建过程支持几种类型的镜像，以满足不同的需求。有关支持的镜像信息，请参阅 Yocto 项目参考手册中的“ref-manual/images：Images”章节。

# Requirements and Recommendations for Released BSPs

This section describes requirements and recommendations for a released BSP to be considered compliant with the Yocto Project.

## Released BSP Requirements

Before looking at BSP requirements, you should consider the following:

- The requirements here assume the BSP layer is a well-formed, \"legal\" layer that can be added to the Yocto Project. For guidelines on creating a layer that meets these base requirements, see the \"`bsp-guide/bsp:bsp layers`{.interpreted-text role="ref"}\" section in this manual and the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 要求这里假定 BSP 层是一个良好形成的、“合法”的层，可以添加到 Yocto 项目中。有关如何创建符合这些基本要求的层的指南，请参阅本手册中的“bsp-guide/bsp:bsp layers”部分，以及 Yocto 项目开发任务手册中的“dev-manual/layers:understanding and creating layers”部分。

- The requirements in this section apply regardless of how you package a BSP. You should consult the packaging and distribution guidelines for your specific release process. For an example of packaging and distribution requirements, see the \":yocto_wiki:[Third Party BSP Release Process \</Third_Party_BSP_Release_Process\>]{.title-ref}\" wiki page.

> 在本节中，无论您如何打包 BSP，这些要求均适用。您应该查阅您特定发布过程的打包和分发指南。有关打包和分发要求的示例，请参阅“yocto_wiki：[第三方 BSP 发布流程]{.title-ref}”维基页面。

- The requirements for the BSP as it is made available to a developer are completely independent of the released form of the BSP. For example, the BSP Metadata can be contained within a Git repository and could have a directory structure completely different from what appears in the officially released BSP layer.

> 要求对开发者提供的 BSP 完全独立于 BSP 发布形式。例如，BSP 元数据可以包含在 Git 存储库中，其目录结构可能与正式发布的 BSP 层完全不同。

- It is not required that specific packages or package modifications exist in the BSP layer, beyond the requirements for general compliance with the Yocto Project. For example, there is no requirement dictating that a specific kernel or kernel version be used in a given BSP.

> 不需要在 BSP 层面上存在特定的包或包修改，除了符合 Yocto 项目的一般要求。例如，不需要在给定的 BSP 中使用特定的内核或内核版本。

Following are the requirements for a released BSP that conform to the Yocto Project:

- _Layer Name:_ The BSP must have a layer name that follows the Yocto Project standards. For information on BSP layer names, see the \"`bsp-guide/bsp:bsp layers`{.interpreted-text role="ref"}\" section.

> BSP 必须有一个符合 Yocto 项目标准的层名称。有关 BSP 层名称的信息，请参见“bsp-guide/bsp：bsp 层”部分。

- _File System Layout:_ When possible, use the same directory names in your BSP layer as listed in the `recipes.txt` file, which is found in `poky/meta` directory of the `Source Directory`{.interpreted-text role="term"} or in the OpenEmbedded-Core Layer (`openembedded-core`) at :oe\_[git:%60/openembedded-core/tree/meta](git:%60/openembedded-core/tree/meta)`.

> _文件系统布局：_ 如果可能，请在 BSP 层中使用在 `recipes.txt` 文件中列出的相同目录名称，该文件位于源目录的 `poky/meta` 目录中，或者在 OpenEmbedded-Core 层（`openembedded-core`）中，位置为：oe_[git:%60/openembedded-core/tree/meta](git:%60/openembedded-core/tree/meta)。

You should place recipes (`*.bb` files) and recipe modifications (`*.bbappend` files) into `recipes-*` subdirectories by functional area as outlined in `recipes.txt`. If you cannot find a category in `recipes.txt` to fit a particular recipe, you can make up your own `recipes-*` subdirectory.

> 你应该按照 `recipes.txt` 中的描述，将食谱（`*.bb` 文件）和食谱修改（`*.bbappend` 文件）放进 `recipes-*` 子目录中，按照功能区分。如果你找不到 `recipes.txt` 中适合特定食谱的类别，你可以自己创建 `recipes-*` 子目录。

Within any particular `recipes-*` category, the layout should match what is found in the OpenEmbedded-Core Git repository (`openembedded-core`) or the Source Directory (`poky`). In other words, make sure you place related files in appropriately-related `recipes-*` subdirectories specific to the recipe\'s function, or within a subdirectory containing a set of closely-related recipes. The recipes themselves should follow the general guidelines for recipes used in the Yocto Project found in the \":oe_wiki:[OpenEmbedded Style Guide \</Styleguide\>]{.title-ref}\".

> 在任何特定的 `recipes-*` 类别中，布局应与 OpenEmbedded-Core Git 存储库（`openembedded-core`）或源目录（`poky`）中找到的内容相匹配。换句话说，请确保您将相关文件放置在与食谱功能相关的适当的 `recipes-*` 子目录中，或者放置在包含一组密切相关食谱的子目录中。食谱本身应遵循 Yocto 项目中使用的食谱的一般准则，可在“oe_wiki：[OpenEmbedded Style Guide \</Styleguide\>] {.title-ref}”中找到。

- _License File:_ You must include a license file in the `meta-bsp_root_name` directory. This license covers the BSP Metadata as a whole. You must specify which license to use since no default license exists. See the :yocto\_[git:%60COPYING.MIT](git:%60COPYING.MIT) \</meta-raspberrypi/tree/COPYING.MIT\>[ file for the Raspberry Pi BSP in the ]{.title-ref}[meta-raspberrypi]{.title-ref}` BSP layer as an example.

> 你必须在 `meta-bsp_root_name` 目录中包含一个许可文件。此许可证涵盖了 BSP 元数据作为一个整体。由于没有默认许可证，你必须指定要使用的许可证。参见 meta-raspberrypi BSP 层中的 Raspberry Pi BSP 的 COPYING.MIT 文件作为示例。

- _README File:_ You must include a `README` file in the `meta-bsp_root_name` directory. See the :yocto\_[git:%60README.md](git:%60README.md) \</meta-raspberrypi/tree/README.md\>[ file for the Raspberry Pi BSP in the ]{.title-ref}[meta-raspberrypi]{.title-ref}` BSP layer as an example.

> 你必须在 `meta-bsp_root_name` 目录中包含一个 `README` 文件。参考 `meta-raspberrypi` BSP 层中的 `yocto_README.md` 文件（Raspberry Pi BSP 的示例）。

At a minimum, the `README` file should contain the following:

- A brief description of the target hardware.
- A list of all the dependencies of the BSP. These dependencies are typically a list of required layers needed to build the BSP. However, the dependencies should also contain information regarding any other dependencies the BSP might have.

> 这个 BSP 的所有依赖项的列表。这些依赖项通常是构建 BSP 所需的所有层的列表。但是，这些依赖项还应该包含 BSP 可能拥有的其他依赖项的信息。

- Any required special licensing information. For example, this information includes information on special variables needed to satisfy a EULA, or instructions on information needed to build or distribute binaries built from the BSP Metadata.

> 任何所需的特殊许可信息。例如，此信息包括满足 EULA 所需的特殊变量的信息，或有关构建或分发基于 BSP 元数据构建的二进制文件所需信息的说明。

- The name and contact information for the BSP layer maintainer. This is the person to whom patches and questions should be sent. For information on how to find the right person, see the \"`dev-manual/changes:submitting a change to the yocto project`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> - BSP 层维护者的姓名和联系方式。这是应将补丁和问题发送的人。有关如何找到正确的人的信息，请参见 Yocto 项目开发任务手册中的“dev-manual/changes：提交 Yocto 项目的变更”部分。

- Instructions on how to build the BSP using the BSP layer.
- Instructions on how to boot the BSP build from the BSP layer.
- Instructions on how to boot the binary images contained in the `binary` directory, if present.
- Information on any known bugs or issues that users should know about when either building or booting the BSP binaries.
- _README.sources File:_ If your BSP contains binary images in the `binary` directory, you must include a `README.sources` file in the `meta-bsp_root_name` directory. This file specifies exactly where you can find the sources used to generate the binary images.

> 如果您的 BSP 包含 binary 目录中的二进制图像，您必须在'meta-bsp_root_name'目录中包含一个'README.sources'文件。此文件指定您可以找到用于生成二进制图像的源位置。

- _Layer Configuration File:_ You must include a `conf/layer.conf` file in the `meta-bsp_root_name` directory. This file identifies the `meta-bsp_root_name` BSP layer as a layer to the build system.

> 必须在 `meta-bsp_root_name` 目录中包含一个 `conf/layer.conf` 文件。该文件将 `meta-bsp_root_name` BSP 层标识为构建系统的一个层。

- _Machine Configuration File:_ You must include one or more `conf/machine/bsp_root_name.conf` files in the `meta-bsp_root_name` directory. These configuration files define machine targets that can be built using the BSP layer. Multiple machine configuration files define variations of machine configurations that the BSP supports. If a BSP supports multiple machine variations, you need to adequately describe each variation in the BSP `README` file. Do not use multiple machine configuration files to describe disparate hardware. If you do have very different targets, you should create separate BSP layers for each target.

> 必须在 `meta-bsp_root_name` 目录中包含一个或多个 `conf/machine/bsp_root_name.conf` 文件。这些配置文件定义了可以使用 BSP 层构建的机器目标。多个机器配置文件定义了 BSP 支持的机器变体。如果 BSP 支持多个机器变体，则需要在 BSP `README` 文件中充分描述每个变体。不要使用多个机器配置文件来描述不同的硬件。如果您有非常不同的目标，应该为每个目标创建单独的 BSP 层。

::: note
::: title
Note
:::

It is completely possible for a developer to structure the working repository as a conglomeration of unrelated BSP files, and to possibly generate BSPs targeted for release from that directory using scripts or some other mechanism (e.g. `meta-yocto-bsp` layer). Such considerations are outside the scope of this document.

> 发展者完全可以将工作存储库结构化为一系列不相关的 BSP 文件，并且可以使用脚本或其他机制（例如 `meta-yocto-bsp` 层）从该目录中生成针对发布的 BSP。这些考虑不在本文档范围之内。
> :::

## Released BSP Recommendations

Following are recommendations for released BSPs that conform to the Yocto Project:

- _Bootable Images:_ Released BSPs can contain one or more bootable images. Including bootable images allows users to easily try out the BSP using their own hardware.

> 可引导映像：发布的 BSP 可以包含一个或多个可引导映像。包含可引导映像可以让用户轻松地使用自己的硬件来尝试 BSP。

In some cases, it might not be convenient to include a bootable image. If so, you might want to make two versions of the BSP available: one that contains binary images, and one that does not. The version that does not contain bootable images avoids unnecessary download times for users not interested in the images.

> 在某些情况下，可能不方便包含可引导映像。 如果是这样，您可能希望提供两个 BSP 版本：一个包含二进制映像，另一个不包含可引导映像。 不包含可引导映像的版本可以避免对不感兴趣的图像的用户不必要的下载时间。

If you need to distribute a BSP and include bootable images or build kernel and filesystems meant to allow users to boot the BSP for evaluation purposes, you should put the images and artifacts within a `binary/` subdirectory located in the `meta-bsp_root_name` directory.

> 如果您需要分发 BSP，并包含可引导映像或构建内核和文件系统，以便用户可以引导 BSP 进行评估，您应该将映像和工件放在 `meta-bsp_root_name` 目录下的 `binary/` 子目录中。

::: note
::: title
Note
:::

If you do include a bootable image as part of the BSP and the image was built by software covered by the GPL or other open source licenses, it is your responsibility to understand and meet all licensing requirements, which could include distribution of source files.

> 如果您在 BSP 中包含可引导映像，并且该映像是由 GPL 或其他开源许可证所覆盖的软件构建的，您有责任理解并满足所有许可要求，其中可能包括分发源文件。
> :::

- _Use a Yocto Linux Kernel:_ Kernel recipes in the BSP should be based on a Yocto Linux kernel. Basing your recipes on these kernels reduces the costs for maintaining the BSP and increases its scalability. See the `Yocto Linux Kernel` category in the :yocto\_[git:%60Source](git:%60Source) Repositories \<\>` for these kernels.

> 使用 Yocto Linux 内核：BSP 中的内核配方应基于 Yocto Linux 内核。将您的配方基于这些内核可以降低维护 BSP 的成本，并提高其可扩展性。有关这些内核，请参阅:yocto_[git:`Source`](git:%60Source%60)存储库中的“Yocto Linux 内核”类别。

# Customizing a Recipe for a BSP

If you plan on customizing a recipe for a particular BSP, you need to do the following:

- Create a `*.bbappend` file for the modified recipe. For information on using append files, see the \"`dev-manual/layers:appending other layers metadata with your layer`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 创建一个用于修改配方的 `*.bbappend` 文件。有关使用附加文件的信息，请参阅 Yocto 项目开发任务手册中的"dev-manual / layers：使用您的层附加其他层元数据"部分。

- Ensure your directory structure in the BSP layer that supports your machine is such that the OpenEmbedded build system can find it. See the example later in this section for more information.

> 确保您在支持您的机器的 BSP 层中的目录结构，以便 OpenEmbedded 构建系统可以找到它。有关更多信息，请参阅本节后面的示例。

- Put the append file in a directory whose name matches the machine\'s name and is located in an appropriate sub-directory inside the BSP layer (i.e. `recipes-bsp`, `recipes-graphics`, `recipes-core`, and so forth).

> 将附加文件放置在与机器名称匹配的目录中，该目录位于 BSP 层的合适子目录中（即 `recipes-bsp`、`recipes-graphics`、`recipes-core` 等）。

- Place the BSP-specific files in the proper directory inside the BSP layer. How expansive the layer is affects where you must place these files. For example, if your layer supports several different machine types, you need to be sure your layer\'s directory structure includes hierarchy that separates the files according to machine. If your layer does not support multiple machines, the layer would not have that additional hierarchy and the files would obviously not be able to reside in a machine-specific directory.

> 将 BSP 特定文件放置在 BSP 层内的适当目录中。层的规模会影响您必须将这些文件放置的位置。例如，如果您的层支持多种不同的机器类型，则需要确保您的层的目录结构包括将文件根据机器分离的层次结构。如果您的层不支持多台机器，则该层将没有额外的层次结构，因此文件显然无法位于机器特定的目录中。

Following is a specific example to help you better understand the process. This example customizes a recipe by adding a BSP-specific configuration file named `interfaces` to the `init-ifupdown_1.0.bb` recipe for machine \"xyz\" where the BSP layer also supports several other machines:

> 以下是一个具体的例子，可以帮助您更好地理解这个过程。此示例通过向名为“interfaces”的 BSP 特定配置文件中添加“init-ifupdown_1.0.bb”配方，为机器“xyz”定制配方，其中 BSP 层也支持其他几台机器：

1. Edit the `init-ifupdown_1.0.bbappend` file so that it contains the following:

   ```
   FILESEXTRAPATHS:prepend := "${THISDIR}/files:"
   ```

   The append file needs to be in the `meta-xyz/recipes-core/init-ifupdown` directory.
2. Create and place the new `interfaces` configuration file in the BSP\'s layer here:

   ```
   meta-xyz/recipes-core/init-ifupdown/files/xyz-machine-one/interfaces
   ```

   ::: note
   ::: title
   Note
   :::

   If the `meta-xyz` layer did not support multiple machines, you would place the interfaces configuration file in the layer here:

   ```
   meta-xyz/recipes-core/init-ifupdown/files/interfaces
   ```

   :::

   The `FILESEXTRAPATHS`{.interpreted-text role="term"} variable in the append files extends the search path the build system uses to find files during the build. Consequently, for this example you need to have the `files` directory in the same location as your append file.

> `FILESEXTRAPATHS` 变量在附加文件中扩展了构建系统在构建期间查找文件的搜索路径。 因此，对于此示例，您需要将 `files` 目录与附加文件位于相同位置。

# BSP Licensing Considerations

In some cases, a BSP contains separately-licensed Intellectual Property (IP) for a component or components. For these cases, you are required to accept the terms of a commercial or other type of license that requires some kind of explicit End User License Agreement (EULA). Once you accept the license, the OpenEmbedded build system can then build and include the corresponding component in the final BSP image. If the BSP is available as a pre-built image, you can download the image after agreeing to the license or EULA.

> 在某些情况下，BSP 包含单独授权的知识产权（IP）组件或组件。对于这些情况，您需要接受商业或其他类型许可证的条款，该许可证要求某种明确的最终用户许可协议（EULA）。一旦您接受许可证，OpenEmbedded 构建系统就可以构建并将相应组件包含在最终的 BSP 图像中。如果 BSP 可以作为预构建的图像提供，您可以在同意许可证或 EULA 后下载该图像。

You could find that some separately-licensed components that are essential for normal operation of the system might not have an unencumbered (or free) substitute. Without these essential components, the system would be non-functional. Then again, you might find that other licensed components that are simply \'good-to-have\' or purely elective do have an unencumbered, free replacement component that you can use rather than agreeing to the separately-licensed component. Even for components essential to the system, you might find an unencumbered component that is not identical but will work as a less-capable version of the licensed version in the BSP recipe.

> 你可能会发现，一些对系统正常运行至关重要的单独许可的组件没有不受限制的（或免费的）替代品。没有这些必要的组件，系统将无法正常工作。而且，你可能会发现，其他许可的组件只是“有益的”或纯粹的选择性组件，可以提供一个不受限制的、免费的替代组件，而不是同意单独许可的组件。即使是对系统至关重要的组件，你也可能会发现一个不受限制的组件，它与许可版本在 BSP 配方中不完全相同，但可以作为功能较弱的版本来使用。

For cases where you can substitute a free component and still maintain the system\'s functionality, the \"DOWNLOADS\" selection from the \"SOFTWARE\" tab on the :yocto_home:[Yocto Project Website \<\>]{.title-ref} makes available de-featured BSPs that are completely free of any IP encumbrances. For these cases, you can use the substitution directly and without any further licensing requirements. If present, these fully de-featured BSPs are named appropriately different as compared to the names of their respective encumbered BSPs. If available, these substitutions are your simplest and most preferred options. Obviously, use of these substitutions assumes the resulting functionality meets system requirements.

> 对于可以替换免费组件而仍然保持系统功能的情况，从 Yocto 项目网站上的“软件”标签中的“下载”选项可以提供完全免于任何知识产权限制的缩减版 BSP。对于这些情况，您可以直接使用替代品，而无需任何进一步的许可要求。如果存在，这些完全缩减版的 BSP 会以与其相应有限制的 BSP 的名称不同的方式命名。如果可用，这些替代品是您最简单和最优选的选择。显然，使用这些替代品假定产生的功能符合系统要求。

::: note
::: title
Note
:::

If however, a non-encumbered version is unavailable or it provides unsuitable functionality or quality, you can use an encumbered version.
:::

There are two different methods within the OpenEmbedded build system to satisfy the licensing requirements for an encumbered BSP. The following list describes them in order of preference:

> 在 OpenEmbedded 构建系统中有两种不同的方法可以满足紧缩 BSP 的许可要求。 以下列表按优先顺序描述它们:

1. _Use the LICENSE_FLAGS Variable to Define the Recipes that Have Commercial or Other Types of Specially-Licensed Packages:_ For each of those recipes, you can specify a matching license string in a `local.conf` variable named `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"}. Specifying the matching license string signifies that you agree to the license. Thus, the build system can build the corresponding recipe and include the component in the image. See the \"`dev-manual/licenses:enabling commercially licensed recipes`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual for details on how to use these variables.

> 使用 LICENSE_FLAGS 变量来定义具有商业或其他特殊许可证的食谱：对于每个食谱，您可以在名为“LICENSE_FLAGS_ACCEPTED”的 `local.conf` 变量中指定匹配的许可证字符串。指定匹配的许可证字符串表示您同意该许可证。因此，构建系统可以构建相应的食谱，并将组件包括在图像中。有关如何使用这些变量的详细信息，请参见 Yocto 项目开发任务手册中的“dev-manual / licenses：enabling commercially licensed recipes”部分。

If you build as you normally would, without specifying any recipes in the `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"} variable, the build stops and provides you with the list of recipes that you have tried to include in the image that need entries in the `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"} variable. Once you enter the appropriate license flags into it, restart the build to continue where it left off. During the build, the prompt will not appear again since you have satisfied the requirement.

> 如果您按照正常方式构建，而不在 `LICENSE_FLAGS_ACCEPTED` 变量中指定任何配方，则构建将停止并向您提供尝试包含在映像中的配方列表，这些配方需要在 `LICENSE_FLAGS_ACCEPTED` 变量中进行输入。输入相应的许可标志后，重新启动构建以继续其中断的位置。在构建期间，提示不会再次出现，因为您已经满足了要求。

Once the appropriate license flags are on the white list in the `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"} variable, you can build the encumbered image with no change at all to the normal build process.

> 一旦适当的许可标志被添加到 `LICENSE_FLAGS_ACCEPTED` 变量的白名单中，您就可以在没有更改正常构建流程的情况下构建受限图像。

2. _Get a Pre-Built Version of the BSP:_ You can get this type of BSP by selecting the \"DOWNLOADS\" item from the \"SOFTWARE\" tab on the :yocto_home:[Yocto Project website \<\>]{.title-ref}. You can download BSP tarballs that contain proprietary components after agreeing to the licensing requirements of each of the individually encumbered packages as part of the download process. Obtaining the BSP this way allows you to access an encumbered image immediately after agreeing to the click-through license agreements presented by the website. If you want to build the image yourself using the recipes contained within the BSP tarball, you will still need to create an appropriate `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"} to match the encumbered recipes in the BSP.

> 2.获取预构建的 BSP：您可以从 Yocto 项目网站上的“软件”选项卡中选择“下载”项获取此类 BSP。您可以在同意每个单独受限包的许可要求后下载包含专有组件的 BSP tarball。通过这种方式获取 BSP 可以在同意网站提供的点击协议后立即访问受限图像。如果您想使用 BSP tarball 中包含的食谱自行构建图像，您仍然需要创建一个适当的 `LICENSE_FLAGS_ACCEPTED`{.interpreted-text role="term"}来匹配 BSP 中受限的食谱。

::: note
::: title
Note
:::

Pre-compiled images are bundled with a time-limited kernel that runs for a predetermined amount of time (10 days) before it forces the system to reboot. This limitation is meant to discourage direct redistribution of the image. You must eventually rebuild the image if you want to remove this restriction.

> 预编译的镜像附带有一个时间限制的内核，可以运行预定义的时间（10 天），然后强制系统重新启动。这种限制旨在阻止直接重新分发图像。如果您想要删除此限制，最终必须重新构建图像。
> :::

# Creating a new BSP Layer Using the `bitbake-layers` Script

The `bitbake-layers create-layer` script automates creating a BSP layer. What makes a layer a \"BSP layer\" is the presence of at least one machine configuration file. Additionally, a BSP layer usually has a kernel recipe or an append file that leverages off an existing kernel recipe. The primary requirement, however, is the machine configuration.

> 脚本 `bitbake-layers create-layer` 可以自动创建 BSP 层。至少有一个机器配置文件使得一个层成为“BSP 层”。此外，BSP 层通常具有内核配方或者是一个利用现有内核配方的附加文件。然而，主要要求是机器配置。

Use these steps to create a BSP layer:

- _Create a General Layer:_ Use the `bitbake-layers` script with the `create-layer` subcommand to create a new general layer. For instructions on how to create a general layer using the `bitbake-layers` script, see the \"`dev-manual/layers:creating a general layer using the` bitbake-layers `script`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 使用 `bitbake-layers` 脚本的 `create-layer` 子命令创建一个新的通用层。有关如何使用 `bitbake-layers` 脚本创建通用层的说明，请参阅 Yocto Project 开发任务手册中的“使用 `bitbake-layers` 脚本创建通用层 `{.interpreted-text role="ref"}”部分。

- _Create a Layer Configuration File:_ Every layer needs a layer configuration file. This configuration file establishes locations for the layer\'s recipes, priorities for the layer, and so forth. You can find examples of `layer.conf` files in the Yocto Project :yocto\_[git:%60Source](git:%60Source) Repositories \<\>[. To get examples of what you need in your configuration file, locate a layer (e.g. \"meta-ti\") and examine the :yocto_git:`local.conf \</meta-ti/tree/meta-ti-bsp/conf/layer.conf\>]{.title-ref} file.

> 创建一个层配置文件：每个层都需要一个层配置文件。此配置文件建立了层的菜谱位置，优先级等。您可以在 Yocto 项目: yocto_[git:’Source'（git:’Source'）存储库中找到“layer.conf”文件的示例。要获得配置文件所需的示例，请定位一个层（例如“meta-ti”），并检查:yocto_git:“local.conf \</ meta-ti / tree / meta-ti-bsp / conf / layer.conf\>”] {.title-ref}文件。

- _Create a Machine Configuration File:_ Create a `conf/machine/bsp_root_name.conf` file. See :yocto\_[git:%60meta-yocto-bsp/conf/machine](git:%60meta-yocto-bsp/conf/machine) \</poky/tree/meta-yocto-bsp/conf/machine\>[ for sample ]{.title-ref}[bsp_root_name.conf]{.title-ref}[ files. There are other samples such as :yocto_git:`meta-ti \</meta-ti/tree/meta-ti-bsp/conf/machine\>]{.title-ref} and :yocto\_[git:%60meta-freescale](git:%60meta-freescale) \</meta-freescale/tree/conf/machine\>` from other vendors that have more specific machine and tuning examples.

> 创建机器配置文件：创建一个 `conf/machine/bsp_root_name.conf` 文件。参考：yocto [git:`meta-yocto-bsp/conf/machine`](git:%60meta-yocto-bsp/conf/machine%60) </poky/tree/meta-yocto-bsp/conf/machine> 中的[bsp_root_name.conf]{.title-ref}文件。还有其他的样本，例如 yocto [git:`meta-ti`](git:%60meta-ti%60) </meta-ti/tree/meta-ti-bsp/conf/machine> 和 yocto [git:`meta-freescale`](git:%60meta-freescale%60) </meta-freescale/tree/conf/machine>，它们拥有更详细的机器和调优示例。

- _Create a Kernel Recipe:_ Create a kernel recipe in `recipes-kernel/linux` by either using a kernel append file or a new custom kernel recipe file (e.g. `yocto-linux_4.12.bb`). The BSP layers mentioned in the previous step also contain different kernel examples. See the \"`kernel-dev/common:modifying an existing recipe`{.interpreted-text role="ref"}\" section in the Yocto Project Linux Kernel Development Manual for information on how to create a custom kernel.

> 创建内核配方：在“recipes-kernel/linux”中使用内核附加文件或新的自定义内核配方文件（例如“yocto-linux_4.12.bb”）创建内核配方。前面步骤中提到的 BSP 层也包含不同的内核示例。有关如何创建自定义内核的信息，请参阅 Yocto Project Linux 内核开发手册中的“kernel-dev/common：修改现有配方”部分。

The remainder of this section provides a description of the Yocto Project reference BSP for Beaglebone, which resides in the :yocto\_[git:%60meta-yocto-bsp](git:%60meta-yocto-bsp) \</poky/tree/meta-yocto-bsp\>` layer.

> 本节的其余部分提供了 Beaglebone 的 Yocto 项目参考 BSP 的描述，它位于:yocto\_[git:%60meta-yocto-bsp](git:%60meta-yocto-bsp) \</poky/tree/meta-yocto-bsp\>层中。

## BSP Layer Configuration Example

The layer\'s `conf` directory contains the `layer.conf` configuration file. In this example, the `conf/layer.conf` file is the following:

```
# We have a conf and classes directory, add to BBPATH
BBPATH .= ":${LAYERDIR}"

# We have a recipes directory containing .bb and .bbappend files, add to BBFILES
BBFILES += "${LAYERDIR}/recipes-*/*/*.bb \
            ${LAYERDIR}/recipes-*/*/*.bbappend"

BBFILE_COLLECTIONS += "yoctobsp"
BBFILE_PATTERN_yoctobsp = "^${LAYERDIR}/"
BBFILE_PRIORITY_yoctobsp = "5"
LAYERVERSION_yoctobsp = "4"
LAYERSERIES_COMPAT_yoctobsp = "&DISTRO_NAME_NO_CAP;"
```

The variables used in this file configure the layer. A good way to learn about layer configuration files is to examine various files for BSP from the :yocto\_[git:%60Source](git:%60Source) Repositories \<\>`.

> 这个文件中使用的变量用于配置层。一个学习层配置文件的好方法是检查来自:yocto_[git:`Source](git:` Source)存储库的各种 BSP 文件。

For a detailed description of this particular layer configuration file, see \"`step 3 <dev-manual/layers:creating your own layer>`{.interpreted-text role="ref"}\" in the discussion that describes how to create layers in the Yocto Project Development Tasks Manual.

> 对于此特定层配置文件的详细描述，请参见 Yocto 项目开发任务手册中描述如何创建层的讨论中的“第 3 步 <dev-manual/layers：创建自己的层 >”。

## BSP Machine Configuration Example

As mentioned earlier in this section, the existence of a machine configuration file is what makes a layer a BSP layer as compared to a general or kernel layer.

> 正如本节早先提到的，机器配置文件的存在使得 BSP 层与通用或内核层有所不同。

There are one or more machine configuration files in the `bsp_layer/conf/machine/` directory of the layer:

```
bsp_layer/conf/machine/machine1\.conf
bsp_layer/conf/machine/machine2\.conf
bsp_layer/conf/machine/machine3\.conf
... more ...
```

For example, the machine configuration file for the [BeagleBone and BeagleBone Black development boards](https://beagleboard.org/bone) is located in the layer `poky/meta-yocto-bsp/conf/machine` and is named `beaglebone-yocto.conf`:

> 例如，[BeagleBone 和 BeagleBone Black 开发板](https://beagleboard.org/bone)的机器配置文件位于层 `poky/meta-yocto-bsp/conf/machine` 中，名为 `beaglebone-yocto.conf`：

```
#@TYPE: Machine
#@NAME: Beaglebone-yocto machine
#@DESCRIPTION: Reference machine configuration for http://beagleboard.org/bone and http://beagleboard.org/black boards

PREFERRED_PROVIDER_virtual/xserver ?= "xserver-xorg"
XSERVER ?= "xserver-xorg \
            xf86-video-modesetting \
           "

MACHINE_EXTRA_RRECOMMENDS = "kernel-modules kernel-devicetree"

EXTRA_IMAGEDEPENDS += "u-boot"

DEFAULTTUNE ?= "cortexa8hf-neon"
include conf/machine/include/arm/armv7a/tune-cortexa8.inc

IMAGE_FSTYPES += "tar.bz2 jffs2 wic wic.bmap"
EXTRA_IMAGECMD:jffs2 = "-lnp "
WKS_FILE ?= "beaglebone-yocto.wks"
IMAGE_INSTALL:append = " kernel-devicetree kernel-image-zimage"
do_image_wic[depends] += "mtools-native:do_populate_sysroot dosfstools-native:do_populate_sysroot"

SERIAL_CONSOLES ?= "115200;ttyS0 115200;ttyO0"
SERIAL_CONSOLES_CHECK = "${SERIAL_CONSOLES}"

PREFERRED_PROVIDER_virtual/kernel ?= "linux-yocto"
PREFERRED_VERSION_linux-yocto ?= "5.0%"

KERNEL_IMAGETYPE = "zImage"
KERNEL_DEVICETREE = "am335x-bone.dtb am335x-boneblack.dtb am335x-bonegreen.dtb"
KERNEL_EXTRA_ARGS += "LOADADDR=${UBOOT_ENTRYPOINT}"

SPL_BINARY = "MLO"
UBOOT_SUFFIX = "img"
UBOOT_MACHINE = "am335x_evm_defconfig"
UBOOT_ENTRYPOINT = "0x80008000"
UBOOT_LOADADDRESS = "0x80008000"

MACHINE_FEATURES = "usbgadget usbhost vfat alsa"

IMAGE_BOOT_FILES ?= "u-boot.${UBOOT_SUFFIX} MLO zImage am335x-bone.dtb am335x-boneblack.dtb am335x-bonegreen.dtb"
```

The variables used to configure the machine define machine-specific properties; for example, machine-dependent packages, machine tunings, the type of kernel to build, and U-Boot configurations.

> 变量用于配置机器定义机器特定的属性；例如，机器相关的软件包、机器调优、要构建的内核类型以及 U-Boot 配置。

The following list provides some explanation for the statements found in the example reference machine configuration file for the BeagleBone development boards. Realize that much more can be defined as part of a machine\'s configuration file. In general, you can learn about related variables that this example does not have by locating the variables in the \"`ref-manual/variables:variables glossary`{.interpreted-text role="ref"}\" in the Yocto Project Reference Manual.

> 以下列表为 BeagleBone 开发板的示例参考机器配置文件提供了一些解释。请记住，机器配置文件可以定义更多内容。一般而言，您可以通过在 Yocto 项目参考手册中定位“ref-manual/variables:variables glossary”中的变量来了解本示例中没有的相关变量。

- `PREFERRED_PROVIDER_virtual/xserver <PREFERRED_PROVIDER>`{.interpreted-text role="term"}: The recipe that provides \"virtual/xserver\" when more than one provider is found. In this case, the recipe that provides \"virtual/xserver\" is \"xserver-xorg\", available in `poky/meta/recipes-graphics/xorg-xserver`.

> 首选提供者 virtual/xserver：当发现多个提供者时，提供“virtual/xserver”的食谱。在这种情况下，提供“virtual/xserver”的食谱是“xserver-xorg”，可在 poky/meta/recipes-graphics/xorg-xserver 中找到。

- `XSERVER`{.interpreted-text role="term"}: The packages that should be installed to provide an X server and drivers for the machine. In this example, the \"xserver-xorg\" and \"xf86-video-modesetting\" are installed.

> `XSERVER`：为机器提供 X 服务器和驱动程序所需要安装的软件包。在本例中，已安装了“xserver-xorg”和“xf86-video-modesetting”。

- `MACHINE_EXTRA_RRECOMMENDS`{.interpreted-text role="term"}: A list of machine-dependent packages not essential for booting the image. Thus, the build does not fail if the packages do not exist. However, the packages are required for a fully-featured image.

> `- ` MACHINE_EXTRA_RRECOMMENDS`：一个不必要的机器依赖的软件包列表，不会影响镜像的启动。但是，这些软件包是完整镜像所必须的。

::: tip
::: title
Tip
:::

There are many `MACHINE*` variables that help you configure a particular piece of hardware.
:::

- `EXTRA_IMAGEDEPENDS`{.interpreted-text role="term"}: Recipes to build that do not provide packages for installing into the root filesystem but building the image depends on the recipes. Sometimes a recipe is required to build the final image but is not needed in the root filesystem. In this case, the U-Boot recipe must be built for the image.

> - `EXTRA_IMAGEDEPENDS`：用于构建但不提供安装到根文件系统的软件包的配方。有时需要构建最终映像文件的配方，但不需要在根文件系统中使用。在这种情况下，必须为映像文件构建 U-Boot 配方。

- `DEFAULTTUNE`{.interpreted-text role="term"}: Machines use tunings to optimize machine, CPU, and application performance. These features, which are collectively known as \"tuning features\", are set in the `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"} layer (e.g. `poky/meta/conf/machine/include`). In this example, the default tuning file is `cortexa8hf-neon`.

> 默认调整：机器使用调整来优化机器、CPU 和应用程序性能。这些特性，统称为“调整特性”，设置在 OpenEmbedded-Core（OE-Core）层（例如 poky/meta/conf/machine/include）中。在本例中，默认调整文件是 cortexa8hf-neon。

::: note
::: title
Note
:::

The include statement that pulls in the `conf/machine/include/arm/tune-cortexa8.inc` file provides many tuning possibilities.
:::

- `IMAGE_FSTYPES`{.interpreted-text role="term"}: The formats the OpenEmbedded build system uses during the build when creating the root filesystem. In this example, four types of images are supported.

> `- ` IMAGE_FSTYPES`：OpenEmbedded 构建系统在构建根文件系统时使用的格式。在本例中，支持四种类型的图像。

- `EXTRA_IMAGECMD`{.interpreted-text role="term"}: Specifies additional options for image creation commands. In this example, the \"-lnp \" option is used when creating the `JFFS2 <JFFS2>`{.interpreted-text role="wikipedia"} image.

> `EXTRA_IMAGECMD`：用于指定图像创建命令的附加选项。在本例中，在创建 `JFFS2` 图像时使用“-lnp”选项。

- `WKS_FILE`{.interpreted-text role="term"}: The location of the ` Wic kickstart <ref-manual/kickstart:openembedded kickstart (`.wks `) reference> `{.interpreted-text role="ref"} file used by the OpenEmbedded build system to create a partitioned image (image.wic).

> - `WKS_FILE`：OpenEmbedded 构建系统用来创建分区镜像（image.wic）的 Wic Kickstart（`.wks`）参考文件的位置。

- `IMAGE_INSTALL`{.interpreted-text role="term"}: Specifies packages to install into an image through the `ref-classes-image`{.interpreted-text role="ref"} class. Recipes use the `IMAGE_INSTALL`{.interpreted-text role="term"} variable.

> `IMAGE_INSTALL`：通过 `ref-classes-image` 类安装到图像中的指定包。食谱使用 `IMAGE_INSTALL` 变量。

- `do_image_wic[depends]`: A task that is constructed during the build. In this example, the task depends on specific tools in order to create the sysroot when building a Wic image.

> `- ` do_image_wic[依赖]`：构建期间构建的任务。在这个示例中，任务依赖于特定的工具，以便在构建 Wic 映像时创建 sysroot。

- `SERIAL_CONSOLES`{.interpreted-text role="term"}: Defines a serial console (TTY) to enable using getty. In this case, the baud rate is \"115200\" and the device name is \"ttyO0\".

> `SERIAL_CONSOLES`：定义一个串行控制台（TTY）以启用使用 getty。在这种情况下，波特率为“115200”，设备名称为“ttyO0”。

- `PREFERRED_PROVIDER_virtual/kernel <PREFERRED_PROVIDER>`{.interpreted-text role="term"}: Specifies the recipe that provides \"virtual/kernel\" when more than one provider is found. In this case, the recipe that provides \"virtual/kernel\" is \"linux-yocto\", which exists in the layer\'s `recipes-kernel/linux` directory.

> 指定当发现多个提供者时，提供“virtual/kernel”的配方。在这种情况下，提供“virtual/kernel”的配方是“linux-yocto”，它存在于层的“recipes-kernel / linux”目录中。

- `PREFERRED_VERSION_linux-yocto <PREFERRED_VERSION>`{.interpreted-text role="term"}: Defines the version of the recipe used to build the kernel, which is \"5.0\" in this case.

> 定义用于构建内核的配方版本，在这种情况下为“5.0”。

- `KERNEL_IMAGETYPE`{.interpreted-text role="term"}: The type of kernel to build for the device. In this case, the OpenEmbedded build system creates a \"zImage\" image type.

> `- KERNEL_IMAGETYPE：要为设备构建的内核类型。在这种情况下，OpenEmbedded构建系统会创建“zImage”图像类型。`

- `KERNEL_DEVICETREE`{.interpreted-text role="term"}: The names of the generated Linux kernel device trees (i.e. the `*.dtb`) files. All the device trees for the various BeagleBone devices are included.

> `KERNEL_DEVICETREE`：生成的 Linux 内核设备树（即 `*.dtb`）文件的名称。 包括各种 BeagleBone 设备的所有设备树。

- `KERNEL_EXTRA_ARGS`{.interpreted-text role="term"}: Additional `make` command-line arguments the OpenEmbedded build system passes on when compiling the kernel. In this example, `LOADADDR=${UBOOT_ENTRYPOINT}` is passed as a command-line argument.

> `-` KERNEL_EXTRA_ARGS `：OpenEmbedded构建系统编译内核时传递的额外` make `命令行参数。在这个例子中，` LOADADDR=${UBOOT_ENTRYPOINT}` 被作为一个命令行参数传递。

- `SPL_BINARY`{.interpreted-text role="term"}: Defines the Secondary Program Loader (SPL) binary type. In this case, the SPL binary is set to \"MLO\", which stands for Multimedia card LOader.

> - `SPL_BINARY`：定义了次程序加载器（SPL）的二进制类型。在这种情况下，SPL 二进制被设置为“MLO”，代表多媒体卡加载器。

The BeagleBone development board requires an SPL to boot and that SPL file type must be MLO. Consequently, the machine configuration needs to define `SPL_BINARY`{.interpreted-text role="term"} as `MLO`.

> BeagleBone 开发板需要一个 SPL 来引导启动，并且 SPL 文件类型必须是 MLO。因此，机器配置需要将 `SPL_BINARY` 作为 `MLO` 定义。

::: note
::: title
Note
:::

For more information on how the SPL variables are used, see the :yocto\_[git:%60u-boot.inc](git:%60u-boot.inc) \</poky/tree/meta/recipes-bsp/u-boot/u-boot.inc\>` include file.

> 若要了解更多关于 SPL 变量的使用情况，请参阅:yocto\_[git:%60u-boot.inc](git:%60u-boot.inc) \</poky/tree/meta/recipes-bsp/u-boot/u-boot.inc\> 包含文件。
> :::

- `UBOOT_* <UBOOT_ENTRYPOINT>`{.interpreted-text role="term"}: Defines various U-Boot configurations needed to build a U-Boot image. In this example, a U-Boot image is required to boot the BeagleBone device. See the following variables for more information:

> - `UBOOT_* <UBOOT_ENTRYPOINT>`：定义构建 U-Boot 镜像所需的各种 U-Boot 配置。在本例中，需要 U-Boot 镜像来启动 BeagleBone 设备。有关更多信息，请参阅以下变量：

- `UBOOT_SUFFIX`{.interpreted-text role="term"}: Points to the generated U-Boot extension.
- `UBOOT_MACHINE`{.interpreted-text role="term"}: Specifies the value passed on the make command line when building a U-Boot image.
- `UBOOT_ENTRYPOINT`{.interpreted-text role="term"}: Specifies the entry point for the U-Boot image.
- `UBOOT_LOADADDRESS`{.interpreted-text role="term"}: Specifies the load address for the U-Boot image.
- `MACHINE_FEATURES`{.interpreted-text role="term"}: Specifies the list of hardware features the BeagleBone device is capable of supporting. In this case, the device supports \"usbgadget usbhost vfat alsa\".

> `- MACHINE_FEATURES：指定BeagleBone设备能够支持的硬件功能列表。在这种情况下，设备支持“usbgadget usbhost vfat alsa”。`

- `IMAGE_BOOT_FILES`{.interpreted-text role="term"}: Files installed into the device\'s boot partition when preparing the image using the Wic tool with the `bootimg-partition` or `bootimg-efi` source plugin.

> IMAGE_BOOT_FILES：使用 Wic 工具，并使用 bootimg-partition 或 bootimg-efi 源插件准备映像时，安装到设备引导分区的文件。

## BSP Kernel Recipe Example

The kernel recipe used to build the kernel image for the BeagleBone device was established in the machine configuration:

```
PREFERRED_PROVIDER_virtual/kernel ?= "linux-yocto"
PREFERRED_VERSION_linux-yocto ?= "5.0%"
```

The `meta-yocto-bsp/recipes-kernel/linux` directory in the layer contains metadata used to build the kernel. In this case, a kernel append file (i.e. `linux-yocto_5.0.bbappend`) is used to override an established kernel recipe (i.e. `linux-yocto_5.0.bb`), which is located in :yocto\_[git:%60/poky/tree/meta/recipes-kernel/linux](git:%60/poky/tree/meta/recipes-kernel/linux)`.

> 该层中的 `meta-yocto-bsp/recipes-kernel/linux` 目录包含用于构建内核的元数据。在这种情况下，使用内核附加文件（即 `linux-yocto_5.0.bbappend`）覆盖已建立的内核配方（即 `linux-yocto_5.0.bb`），该配方位于：yocto_[git:%60/poky/tree/meta/recipes-kernel/linux](git:%60/poky/tree/meta/recipes-kernel/linux)。

Following is the contents of the append file:

```
KBRANCH:genericx86 = "v5.0/standard/base"
KBRANCH:genericx86-64 = "v5.0/standard/base"
KBRANCH:edgerouter = "v5.0/standard/edgerouter"
KBRANCH:beaglebone-yocto = "v5.0/standard/beaglebone"

KMACHINE:genericx86 ?= "common-pc"
KMACHINE:genericx86-64 ?= "common-pc-64"
KMACHINE:beaglebone-yocto ?= "beaglebone"

SRCREV_machine:genericx86 ?= "3df4aae6074e94e794e27fe7f17451d9353cdf3d"
SRCREV_machine:genericx86-64 ?= "3df4aae6074e94e794e27fe7f17451d9353cdf3d"
SRCREV_machine:edgerouter ?= "3df4aae6074e94e794e27fe7f17451d9353cdf3d"
SRCREV_machine:beaglebone-yocto ?= "3df4aae6074e94e794e27fe7f17451d9353cdf3d"

COMPATIBLE_MACHINE:genericx86 = "genericx86"
COMPATIBLE_MACHINE:genericx86-64 = "genericx86-64"
COMPATIBLE_MACHINE:edgerouter = "edgerouter"
COMPATIBLE_MACHINE:beaglebone-yocto = "beaglebone-yocto"

LINUX_VERSION:genericx86 = "5.0.3"
LINUX_VERSION:genericx86-64 = "5.0.3"
LINUX_VERSION:edgerouter = "5.0.3"
LINUX_VERSION:beaglebone-yocto = "5.0.3"
```

This particular append file works for all the machines that are part of the `meta-yocto-bsp` layer. The relevant statements are appended with the \"beaglebone-yocto\" string. The OpenEmbedded build system uses these statements to override similar statements in the kernel recipe:

> 这个特定的附加文件适用于所有属于 `meta-yocto-bsp` 层的机器。相关语句使用“beaglebone-yocto”字符串附加。OpenEmbedded 构建系统使用这些语句来覆盖内核配方中的类似语句：

- `KBRANCH`{.interpreted-text role="term"}: Identifies the kernel branch that is validated, patched, and configured during the build.
- `KMACHINE`{.interpreted-text role="term"}: Identifies the machine name as known by the kernel, which is sometimes a different name than what is known by the OpenEmbedded build system.

> KMACHINE：识别内核所知的机器名称，有时与 OpenEmbedded 构建系统所知的名称不同。

- `SRCREV`{.interpreted-text role="term"}: Identifies the revision of the source code used to build the image.
- `COMPATIBLE_MACHINE`{.interpreted-text role="term"}: A regular expression that resolves to one or more target machines with which the recipe is compatible.

> `- ` COMPATIBLE_MACHINE`{.interpreted-text role="term"}: 一个正则表达式，用于解析一个或多个与配方兼容的目标机器。

- `LINUX_VERSION`{.interpreted-text role="term"}: The Linux version from kernel.org used by the OpenEmbedded build system to build the kernel image.
