---
tip: translate by baidu@2023-06-07 10:44:22
...
---
title: Yocto Project Concepts
-----------------------------

This chapter provides explanations for Yocto Project concepts that go beyond the surface of \"how-to\" information and reference (or look-up) material. Concepts such as components, the `OpenEmbedded Build System`{.interpreted-text role="term"} workflow, cross-development toolchains, shared state cache, and so forth are explained.

> 本章对 Yocto 项目的概念进行了解释，这些概念超出了“如何操作”信息和参考（或查找）材料的表面。解释了组件、`OpenEmbedded Build System`｛.explored text role=“term”｝工作流、跨开发工具链、共享状态缓存等概念。

# Yocto Project Components

The `BitBake`{.interpreted-text role="term"} task executor together with various types of configuration files form the `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"}. This section overviews these components by describing their use and how they interact.

> `BitBake`｛.depredicted text role=“term”｝任务执行器与各种类型的配置文件一起形成 `OpenEmbedded Core（OE Core）`｛.epredicted text role=”term“｝。本节通过描述这些组件的使用及其交互方式概述了这些组件。

BitBake handles the parsing and execution of the data files. The data itself is of various types:

> BitBake 处理数据文件的解析和执行。数据本身有多种类型：

- *Recipes:* Provides details about particular pieces of software.
- *Class Data:* Abstracts common build information (e.g. how to build a Linux kernel).
- *Configuration Data:* Defines machine-specific settings, policy decisions, and so forth. Configuration data acts as the glue to bind everything together.

> -*配置数据：*定义特定于机器的设置、策略决策等。配置数据充当了将所有内容绑定在一起的粘合剂。

BitBake knows how to combine multiple data sources together and refers to each data source as a layer. For information on layers, see the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section of the Yocto Project Development Tasks Manual.

> BitBake 知道如何将多个数据源组合在一起，并将每个数据源称为一个层。有关层的信息，请参阅 Yocto 项目开发任务手册的\“`devmanual/layers:understanding and createing layers`｛.depreted text role=“ref”｝\”一节。

Following are some brief details on these core components. For additional information on how these components interact during a build, see the \"`overview-manual/concepts:openembedded build system concepts`{.interpreted-text role="ref"}\" section.

> 以下是关于这些核心组件的一些简要细节。有关这些组件在构建过程中如何交互的更多信息，请参阅“概述手册/概念：开放式构建系统概念”一节。

## BitBake

BitBake is the tool at the heart of the `OpenEmbedded Build System`{.interpreted-text role="term"} and is responsible for parsing the `Metadata`{.interpreted-text role="term"}, generating a list of tasks from it, and then executing those tasks.

> BitBake 是“OpenEmbedded Build System”的核心工具，负责解析“Metadata”｛.depreted text role=“term”｝，从中生成任务列表，然后执行这些任务。

This section briefly introduces BitBake. If you want more information on BitBake, see the `BitBake User Manual <bitbake:index>`{.interpreted-text role="doc"}.

> 本节简要介绍 BitBake。如果您想了解有关 BitBake 的更多信息，请参阅 `BitBake用户手册<BitBake:index>`{.depredicted text role=“doc”}。

To see a list of the options BitBake supports, use either of the following commands:

> 若要查看 BitBake 支持的选项列表，请使用以下任一命令：

```
$ bitbake -h
$ bitbake --help
```

The most common usage for BitBake is `bitbake recipename`, where `recipename` is the name of the recipe you want to build (referred to as the \"target\"). The target often equates to the first part of a recipe\'s filename (e.g. \"foo\" for a recipe named `foo_1.3.0-r0.bb`). So, to process the `matchbox-desktop_1.2.3.bb` recipe file, you might type the following:

> BitBake 最常见的用法是“BitBake-recipename”，其中“recipename”是要构建的配方的名称（称为“目标”）。目标通常相当于配方文件名的第一部分（例如，名为“foo_1.3.0-r0.bb”的配方的“foo”）。因此，要处理“matchbox-desktop_1.2.3.bb”配方文件，您可以键入以下内容：

```
$ bitbake matchbox-desktop
```

Several different versions of `matchbox-desktop` might exist. BitBake chooses the one selected by the distribution configuration. You can get more details about how BitBake chooses between different target versions and providers in the \"`bitbake-user-manual/bitbake-user-manual-execution:preferences`{.interpreted-text role="ref"}\" section of the BitBake User Manual.

> 可能存在几种不同版本的“火柴盒桌面”。BitBake 选择由分布配置选择的一个。有关 BitBake 如何在不同的目标版本和提供程序之间进行选择的更多详细信息，请参阅《BitBake 用户手册》的\“`BitBake用户手册/BitBake用户手动执行：首选项`{.depreted text role=”ref“}\”部分。

BitBake also tries to execute any dependent tasks first. So for example, before building `matchbox-desktop`, BitBake would build a cross compiler and `glibc` if they had not already been built.

> BitBake 还尝试首先执行任何相关任务。例如，在构建“火柴盒桌面”之前，BitBake 会构建一个交叉编译器和“glibc”，如果它们还没有构建的话。

A useful BitBake option to consider is the `-k` or `--continue` option. This option instructs BitBake to try and continue processing the job as long as possible even after encountering an error. When an error occurs, the target that failed and those that depend on it cannot be remade. However, when you use this option other dependencies can still be processed.

> 需要考虑的一个有用的 BitBake 选项是“-k”或“--continue”选项。此选项指示 BitBake 尝试并尽可能长时间地继续处理作业，即使在遇到错误之后也是如此。发生错误时，无法重新生成失败的目标和依赖它的目标。但是，当您使用此选项时，仍然可以处理其他依赖项。

## Recipes

Files that have the `.bb` suffix are \"recipes\" files. In general, a recipe contains information about a single piece of software. This information includes the location from which to download the unaltered source, any source patches to be applied to that source (if needed), which special configuration options to apply, how to compile the source files, and how to package the compiled output.

> 后缀为“.bb”的文件是“配方”文件。一般来说，配方包含关于单个软件的信息。这些信息包括下载未更改源的位置、要应用于该源的任何源修补程序（如果需要）、要应用的特殊配置选项、如何编译源文件以及如何打包编译后的输出。

The term \"package\" is sometimes used to refer to recipes. However, since the word \"package\" is used for the packaged output from the OpenEmbedded build system (i.e. `.ipk` or `.deb` files), this document avoids using the term \"package\" when referring to recipes.

> “包装”一词有时用来指代菜谱。但是，由于“package”一词用于 OpenEmbedded 构建系统的打包输出（即 `.ipk` 或 `.deb` 文件），因此本文档在提及配方时避免使用“package \”一词。

## Classes

Class files (`.bbclass`) contain information that is useful to share between recipes files. An example is the `ref-classes-autotools`{.interpreted-text role="ref"} class, which contains common settings for any application that is built with the `GNU Autotools <GNU_Autotools>`{.interpreted-text role="wikipedia"}. The \"`ref-manual/classes:Classes`{.interpreted-text role="ref"}\" chapter in the Yocto Project Reference Manual provides details about classes and how to use them.

> 类文件（`.bbclass`）包含有助于在配方文件之间共享的信息。一个例子是 `ref classes autotools`｛.explored text role=“ref”｝类，该类包含使用 `GNU autotools＜GNU_autotools＞`{.explered text rol=“wikipedia”}构建的任何应用程序的通用设置。Yocto 项目参考手册中的“`ref manual/classes:classes`｛.explored text role=”ref“｝\”一章提供了有关类以及如何使用它们的详细信息。

## Configurations

The configuration files (`.conf`) define various configuration variables that govern the OpenEmbedded build process. These files fall into several areas that define machine configuration options, distribution configuration options, compiler tuning options, general common configuration options, and user configuration options in `conf/local.conf`, which is found in the `Build Directory`{.interpreted-text role="term"}.

> 配置文件（`.conf`）定义了控制 OpenEmbedded 构建过程的各种配置变量。这些文件分为几个区域，定义了“conf/local.conf”中的机器配置选项、分发配置选项、编译器调整选项、通用配置选项和用户配置选项，这些选项位于“构建目录”｛.depreted text role=“term”｝中。

# Layers

Layers are repositories that contain related metadata (i.e. sets of instructions) that tell the OpenEmbedded build system how to build a target. `overview-manual/yp-intro:the yocto project layer model`{.interpreted-text role="ref"} facilitates collaboration, sharing, customization, and reuse within the Yocto Project development environment. Layers logically separate information for your project. For example, you can use a layer to hold all the configurations for a particular piece of hardware. Isolating hardware-specific configurations allows you to share other metadata by using a different layer where that metadata might be common across several pieces of hardware.

> 层是包含相关元数据（即指令集）的存储库，这些元数据告诉 OpenEmbedded 构建系统如何构建目标 `概述手册/yp简介：yocto项目层模型`{.depredicted text role=“ref”}促进了 yocto 项目开发环境中的协作、共享、定制和重用。图层在逻辑上分离项目的信息。例如，您可以使用一个层来保存特定硬件的所有配置。隔离特定于硬件的配置允许您通过使用不同的层来共享其他元数据，其中该元数据可能在多个硬件中是通用的。

There are many layers working in the Yocto Project development environment. The :yocto_home:[Yocto Project Compatible Layer Index \</software-overview/layers/\>]{.title-ref} and :oe_layerindex:[OpenEmbedded Layer Index \<\>]{.title-ref} both contain layers from which you can use or leverage.

> Yocto 项目开发环境中有许多层在工作。：yocto_home:[yocto Project Compatible Layer Index\</software overview/layers/\>]｛.title-ref｝和：oe_layerindex:[OpenEmbedded Layer Index\<\>]{.title-ref｝都包含可以使用或利用的层。

By convention, layers in the Yocto Project follow a specific form. Conforming to a known structure allows BitBake to make assumptions during builds on where to find types of metadata. You can find procedures and learn about tools (i.e. `bitbake-layers`) for creating layers suitable for the Yocto Project in the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section of the Yocto Project Development Tasks Manual.

> 按照惯例，Yocto 项目中的层遵循特定的形式。符合已知的结构允许 BitBake 在构建过程中根据元数据类型做出假设。您可以在 Yocto 项目开发任务手册的\“`dev manual/layers:understanding and createing layers`｛.depreted text role=“ref”｝\”部分中找到创建适合 Yocto 工程的层的程序并了解有关工具（即“bitbake layers”）。

# OpenEmbedded Build System Concepts

This section takes a more detailed look inside the build process used by the `OpenEmbedded Build System`{.interpreted-text role="term"}, which is the build system specific to the Yocto Project. At the heart of the build system is BitBake, the task executor.

> 本节更详细地介绍了“OpenEmbedded 构建系统”｛.explored text role=“term”｝使用的构建过程，该构建系统是 Yocto 项目特有的构建系统。构建系统的核心是任务执行器 BitBake。

The following diagram represents the high-level workflow of a build. The remainder of this section expands on the fundamental input, output, process, and metadata logical blocks that make up the workflow.

> 下图表示生成的高级工作流。本节的其余部分将扩展构成工作流的基本输入、输出、过程和元数据逻辑块。

![image](figures/YP-flow-diagram.png){width="100.0%"}

In general, the build\'s workflow consists of several functional areas:

> 通常，构建的工作流由几个功能区域组成：

- *User Configuration:* metadata you can use to control the build process.
- *Metadata Layers:* Various layers that provide software, machine, and distro metadata.
- *Source Files:* Upstream releases, local projects, and SCMs.
- *Build System:* Processes under the control of `BitBake`{.interpreted-text role="term"}. This block expands on how BitBake fetches source, applies patches, completes compilation, analyzes output for package generation, creates and tests packages, generates images, and generates cross-development tools.

> -*构建系统：*在 `BitBake` 的控制下处理｛.explored text role=“term”｝。该块扩展了 BitBake 如何获取源代码、应用补丁、完成编译、分析包生成的输出、创建和测试包、生成图像以及生成交叉开发工具。

- *Package Feeds:* Directories containing output packages (RPM, DEB or IPK), which are subsequently used in the construction of an image or Software Development Kit (SDK), produced by the build system. These feeds can also be copied and shared using a web server or other means to facilitate extending or updating existing images on devices at runtime if runtime package management is enabled.

> -*包提要：*包含输出包（RPM、DEB 或 IPK）的目录，这些输出包随后用于构建由构建系统生成的映像或软件开发工具包（SDK）。如果启用了运行时包管理，还可以使用 web 服务器或其他方式复制和共享这些提要，以便于在运行时扩展或更新设备上的现有图像。

- *Images:* Images produced by the workflow.
- *Application Development SDK:* Cross-development tools that are produced along with an image or separately with BitBake.

> -*应用程序开发 SDK：*与图像一起生成或与 BitBake 单独生成的交叉开发工具。

## User Configuration

User configuration helps define the build. Through user configuration, you can tell BitBake the target architecture for which you are building the image, where to store downloaded source, and other build properties.

> 用户配置有助于定义生成。通过用户配置，您可以告诉 BitBake 为其构建图像的目标体系结构、将下载的源存储在何处以及其他构建属性。

The following figure shows an expanded representation of the \"User Configuration\" box of the `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"}:

> 下图显示了 `general workflow figure<overview manual/concepts:openembedded build system concepts>`{.depredicted text role=“ref”}的“用户配置”框的扩展表示：

![image](figures/user-configuration.png){width="100.0%"}

BitBake needs some basic configuration files in order to complete a build. These files are `*.conf` files. The minimally necessary ones reside as example files in the `build/conf` directory of the `Source Directory`{.interpreted-text role="term"}. For simplicity, this section refers to the Source Directory as the \"Poky Directory.\"

> BitBake 需要一些基本的配置文件才能完成构建。这些文件是“*.conf”文件。最低限度需要的文件作为示例文件存在于“源目录”的“build/conf”目录中｛.depreted text role=“term”｝。为了简单起见，本节将源目录称为“Poky 目录”

When you clone the `Poky`{.interpreted-text role="term"} Git repository or you download and unpack a Yocto Project release, you can set up the Source Directory to be named anything you want. For this discussion, the cloned repository uses the default name `poky`.

> 当您克隆 `Poky`｛.depredicted text role=“term”｝Git 存储库或下载并解压 Yocto Project 版本时，您可以将源目录设置为任何您想要的名称。在本讨论中，克隆的存储库使用默认名称“poky”。

::: note
::: title
Note
:::

The Poky repository is primarily an aggregation of existing repositories. It is not a canonical upstream source.

> Poky 存储库主要是现有存储库的集合。它不是一个规范的上游来源。
> :::

The `meta-poky` layer inside Poky contains a `conf` directory that has example configuration files. These example files are used as a basis for creating actual configuration files when you source `structure-core-script`{.interpreted-text role="ref"}, which is the build environment script.

> poky 内部的“meta poky”层包含一个“conf”目录，该目录具有示例配置文件。这些示例文件用作创建实际配置文件的基础，当您源代码为“structure core script”｛.explored text role=“ref”｝（即构建环境脚本）时。

Sourcing the build environment script creates a `Build Directory`{.interpreted-text role="term"} if one does not already exist. BitBake uses the `Build Directory`{.interpreted-text role="term"} for all its work during builds. The Build Directory has a `conf` directory that contains default versions of your `local.conf` and `bblayers.conf` configuration files. These default configuration files are created only if versions do not already exist in the `Build Directory`{.interpreted-text role="term"} at the time you source the build environment setup script.

> 如果生成环境脚本的来源不存在，则会创建一个“生成目录”｛.depreted text role=“term”｝。BitBake 在构建期间的所有工作都使用“构建目录”｛.explored text role=“term”｝。构建目录有一个“conf”目录，其中包含“local.conf”和“bblayers.conf”配置文件的默认版本。只有当在获取生成环境设置脚本时，“生成目录”｛.depredicted text role=“term”｝中还不存在版本时，才会创建这些默认配置文件。

Because the Poky repository is fundamentally an aggregation of existing repositories, some users might be familiar with running the `structure-core-script`{.interpreted-text role="ref"} script in the context of separate `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"} and BitBake repositories rather than a single Poky repository. This discussion assumes the script is executed from within a cloned or unpacked version of Poky.

> 由于 Poky 存储库基本上是现有存储库的集合，因此一些用户可能熟悉在单独的 `OpenEmbedded core（OE core）`{.depredicted text role=“term”}和 BitBake 存储库的上下文中运行 `structure core script`{.depreced text rol=“ref”}脚本，而不是在单个 Poky 存储库内运行。本讨论假设脚本是在 Poky 的克隆或解压缩版本中执行的。

Depending on where the script is sourced, different sub-scripts are called to set up the `Build Directory`{.interpreted-text role="term"} (Yocto or OpenEmbedded). Specifically, the script `scripts/oe-setup-builddir` inside the poky directory sets up the `Build Directory`{.interpreted-text role="term"} and seeds the directory (if necessary) with configuration files appropriate for the Yocto Project development environment.

> 根据脚本的来源，会调用不同的子脚本来设置“构建目录”｛.explored text role=“term”｝（Yocto 或 OpenEmbedded）。具体而言，poky 目录中的脚本“scripts/oe-setup-builddir”设置“构建目录”{.depredicted text role=“term”}，并在目录中（如有必要）植入适用于 Yocto 项目开发环境的配置文件。

::: note
::: title
Note
:::

The scripts/oe-setup-builddir script uses the `$TEMPLATECONF` variable to determine which sample configuration files to locate.

> scripts/oe-setup-builddir 脚本使用“$TEMPLATECOFF”变量来确定要查找的示例配置文件。
> :::

The `local.conf` file provides many basic variables that define a build environment. Here is a list of a few. To see the default configurations in a `local.conf` file created by the build environment script, see the :yocto\_[git:%60local.conf.sample](git:%60local.conf.sample) \</poky/tree/meta-poky/conf/templates/default/local.conf.sample\>[ in the ]{.title-ref}[meta-poky]{.title-ref}\` layer:

> “local.conf”文件提供了许多定义构建环境的基本变量。以下是一些清单。要查看由构建环境脚本创建的“local.conf”文件中的默认配置，请参阅：yocto\_[git:%60local.conf.sample]（git:%60local.conf.ssample）\</poky/tree/meta-poky/conf/templates/default/local.conf.sSample\>[在]｛.title-ref｝[meta-poky]｛.title-ref}\`层中：

- *Target Machine Selection:* Controlled by the `MACHINE`{.interpreted-text role="term"} variable.
- *Download Directory:* Controlled by the `DL_DIR`{.interpreted-text role="term"} variable.
- *Shared State Directory:* Controlled by the `SSTATE_DIR`{.interpreted-text role="term"} variable.

> -*共享状态目录：*由 `SSTATE_DIR`｛.explored text role=“term”｝变量控制。

- *Build Output:* Controlled by the `TMPDIR`{.interpreted-text role="term"} variable.
- *Distribution Policy:* Controlled by the `DISTRO`{.interpreted-text role="term"} variable.
- *Packaging Format:* Controlled by the `PACKAGE_CLASSES`{.interpreted-text role="term"} variable.
- *SDK Target Architecture:* Controlled by the `SDKMACHINE`{.interpreted-text role="term"} variable.

> -*SDK 目标体系结构：*由 `SDKMACHINE`｛.explored text role=“term”｝变量控制。

- *Extra Image Packages:* Controlled by the `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} variable.

> -*额外的图像包：*由 `Extra_Image_FEATURE`｛.explored text role=“term”｝变量控制。

::: note
::: title
Note
:::

Configurations set in the `conf/local.conf` file can also be set in the `conf/site.conf` and `conf/auto.conf` configuration files.

> 在“conf/local.conf”文件中设置的配置也可以在“conf/site.conf”和“conf/auto.conf”配置文件中设置。
> :::

The `bblayers.conf` file tells BitBake what layers you want considered during the build. By default, the layers listed in this file include layers minimally needed by the build system. However, you must manually add any custom layers you have created. You can find more information on working with the `bblayers.conf` file in the \"`dev-manual/layers:enabling your layer`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> “bblayers.conf”文件告诉 BitBake 在构建过程中需要考虑哪些层。默认情况下，此文件中列出的图层包括生成系统最低限度需要的图层。但是，必须手动添加已创建的任何自定义图层。您可以在 Yocto 项目开发任务手册的\“`dev manual/layers:enableing your layer`｛.depredicted text role=“ref”｝\”部分找到有关使用 `bblayers.conf` 文件的更多信息。

The files `site.conf` and `auto.conf` are not created by the environment initialization script. If you want the `site.conf` file, you need to create it yourself. The `auto.conf` file is typically created by an autobuilder:

> 文件“site.conf”和“auto.conf”不是由环境初始化脚本创建的。如果您想要“site.conf”文件，您需要自己创建它。“auto.conf”文件通常由 autobuilder 创建：

- *site.conf:* You can use the `conf/site.conf` configuration file to configure multiple build directories. For example, suppose you had several build environments and they shared some common features. You can set these default build properties here. A good example is perhaps the packaging format to use through the `PACKAGE_CLASSES`{.interpreted-text role="term"} variable.

> -*site.conf:*您可以使用“conf/site.conf”配置文件来配置多个构建目录。例如，假设您有几个构建环境，它们共享一些共同的功能。您可以在此处设置这些默认生成属性。一个很好的例子可能是通过 `PACKAGE_CLASES`{.depreted text role=“term”}变量使用的打包格式。

- *auto.conf:* The file is usually created and written to by an autobuilder. The settings put into the file are typically the same as you would find in the `conf/local.conf` or the `conf/site.conf` files.

> -*auto.conf:*该文件通常由 autobuilder 创建和写入。文件中的设置通常与“conf/local.conf”或“conf/site.conf”文件中的相同。

You can edit all configuration files to further define any particular build environment. This process is represented by the \"User Configuration Edits\" box in the figure.

> 您可以编辑所有配置文件以进一步定义任何特定的生成环境。该过程由图中的“用户配置编辑”框表示。

When you launch your build with the `bitbake target` command, BitBake sorts out the configurations to ultimately define your build environment. It is important to understand that the `OpenEmbedded Build System`{.interpreted-text role="term"} reads the configuration files in a specific order: `site.conf`, `auto.conf`, and `local.conf`. And, the build system applies the normal assignment statement rules as described in the \"`bitbake:bitbake-user-manual/bitbake-user-manual-metadata`{.interpreted-text role="doc"}\" chapter of the BitBake User Manual. Because the files are parsed in a specific order, variable assignments for the same variable could be affected. For example, if the `auto.conf` file and the `local.conf` set variable1 to different values, because the build system parses `local.conf` after `auto.conf`, variable1 is assigned the value from the `local.conf` file.

> 当您使用“bitbake target”命令启动构建时，bitbake 会对配置进行排序，以最终定义构建环境。重要的是要理解，`OpenEmbedded Build System`｛.explored text role=“term”｝按特定顺序读取配置文件：`site.conf`、`auto.conf` 和 `local.conf`，构建系统应用正常赋值语句规则，如《bitbake 用户手册》的“`bitbake:bitbake-user manual/bitbake-user manual metadata`｛.explored text role=“doc”｝”一章中所述。因为文件是按特定顺序解析的，所以同一变量的变量分配可能会受到影响。例如，如果“auto.conf”文件和“local.conf”将 variable1 设置为不同的值，因为构建系统在“auto.conf”之后解析“local.conf”，所以 variable1 被分配来自“local.coonf”文件的值。

## Metadata, Machine Configuration, and Policy Configuration

The previous section described the user configurations that define BitBake\'s global behavior. This section takes a closer look at the layers the build system uses to further control the build. These layers provide Metadata for the software, machine, and policies.

> 上一节介绍了定义 BitBake 全局行为的用户配置。本节将详细介绍构建系统用于进一步控制构建的层。这些层为软件、机器和策略提供元数据。

In general, there are three types of layer input. You can see them below the \"User Configuration\" box in the \`general workflow figure \<overview-manual/concepts:openembedded build system concepts\>\`:

> 通常，有三种类型的图层输入。您可以在“常规工作流程图”的“用户配置”框下方看到它们：

- *Metadata (.bb + Patches):* Software layers containing user-supplied recipe files, patches, and append files. A good example of a software layer might be the :oe_layer:[meta-qt5 layer \</meta-qt5\>]{.title-ref} from the :oe_layerindex:[OpenEmbedded Layer Index \<\>]{.title-ref}. This layer is for version 5.0 of the popular [Qt](https://wiki.qt.io/About_Qt) cross-platform application development framework for desktop, embedded and mobile.

> -*元数据（.bb+ 修补程序）：*包含用户提供的配方文件、修补程序和附加文件的软件层。软件层的一个很好的例子可能是：oe_layer:[meta-qt5 layer\</meta-qt5\>]｛.title-ref｝来自：oe_llayerindex:[OpenEmbedded layer Index\<\>]{.title-ref｝。该层适用于流行的[Qt]的 5.0 版本([https://wiki.qt.io/About_Qt](https://wiki.qt.io/About_Qt))用于桌面、嵌入式和移动的跨平台应用程序开发框架。

- *Machine BSP Configuration:* Board Support Package (BSP) layers (i.e. \"BSP Layer\" in the following figure) providing machine-specific configurations. This type of information is specific to a particular target architecture. A good example of a BSP layer from the `overview-manual/yp-intro:reference distribution (poky)`{.interpreted-text role="ref"} is the :yocto\_[git:%60meta-yocto-bsp](git:%60meta-yocto-bsp) \</poky/tree/meta-yocto-bsp\>\` layer.

> -*机器 BSP 配置：*提供机器特定配置的板支持包（BSP）层（即下图中的“BSP 层”）。这种类型的信息是特定于特定目标体系结构的。“概述手册/yp intro:reference distribution（poky）”｛.depreted text role=“ref”｝中 BSP 层的一个很好的例子是：yocto\_[git:%60meta yocto BSP]（git:%60meta yoctobsp）\</poky/tree/meta yocto BSP\>\`层。

- *Policy Configuration:* Distribution Layers (i.e. \"Distro Layer\" in the following figure) providing top-level or general policies for the images or SDKs being built for a particular distribution. For example, in the Poky Reference Distribution the distro layer is the :yocto\_[git:%60meta-poky](git:%60meta-poky) \</poky/tree/meta-poky\>[ layer. Within the distro layer is a ]{.title-ref}[conf/distro]{.title-ref}[ directory that contains distro configuration files (e.g. :yocto_git:\`poky.conf \</poky/tree/meta-poky/conf/distro/poky.conf\>]{.title-ref} that contain many policy configurations for the Poky distribution.

> -*策略配置：*分发层（即下图中的“Distro Layer”）为为为特定分发构建的映像或 SDK 提供顶级或通用策略。例如在 Poky 参考分发中，发行版层是：yocto\_[git:%60meta Poky]（git:%60meta Poky）\</Poky/tree/meta Poky \>[层。发行版层内是一个]｛.title-ref｝[conf/distrio]｛.title-ref}[包含发行版配置文件的目录（例如：yocto_git:\`Poky.conf \</Poky/tree/meta-Poky/conf/distro/Poky.conf\>]其包含用于 Poky 分布的许多策略配置。

The following figure shows an expanded representation of these three layers from the `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"}:

> 下图显示了这三个层的扩展表示，它们来自 `general workflow figure＜overview manual/concepts:openembedded build system concepts>`{.depredicted text role=“ref”}：

![image](figures/layer-input.png){.align-center width="70.0%"}

In general, all layers have a similar structure. They all contain a licensing file (e.g. `COPYING.MIT`) if the layer is to be distributed, a `README` file as good practice and especially if the layer is to be distributed, a configuration directory, and recipe directories. You can learn about the general structure for layers used with the Yocto Project in the \"`dev-manual/layers:creating your own layer`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual. For a general discussion on layers and the many layers from which you can draw, see the \"`overview-manual/concepts:layers`{.interpreted-text role="ref"}\" and \"`overview-manual/yp-intro:the yocto project layer model`{.interpreted-text role="ref"}\" sections both earlier in this manual.

> 一般来说，所有层都具有相似的结构。如果要分发层，它们都包含一个许可文件（例如“COPYING.MIT”），作为良好做法的“README”文件，尤其是如果要分发该层，还包含一个配置目录和配方目录。您可以在 Yocto 项目开发任务手册的\“`dev manual/layers:createing your own layer`｛.depredicted text role=”ref“｝\”一节中了解 Yocto Project 使用的层的一般结构。有关图层和可以绘制的许多图层的一般性讨论，请参阅本手册前面的\“`overview manual/concepts:layers`｛.depreted text role=“ref”｝\”和\“`overview manual/yp intro:yocto project图层模型`｛-depreted text role=“ref”}\”部分。

If you explored the previous links, you discovered some areas where many layers that work with the Yocto Project exist. The :yocto\_[git:%60Source](git:%60Source) Repositories \<\>\` also shows layers categorized under \"Yocto Metadata Layers.\"

> 如果您浏览了前面的链接，您会发现一些区域存在许多与 Yocto 项目相关的层。：yocto\_[git:%60Source]（git:%60Source）存储库\<\>\`还显示了分类在“yocto 元数据层”下的层

::: note
::: title
Note
:::

There are layers in the Yocto Project Source Repositories that cannot be found in the OpenEmbedded Layer Index. Such layers are either deprecated or experimental in nature.

> Yocto 项目源存储库中的某些层在 OpenEmbedded 层索引中找不到。这种层在本质上要么是不赞成的，要么是实验性的。
> :::

BitBake uses the `conf/bblayers.conf` file, which is part of the user configuration, to find what layers it should be using as part of the build.

> BitBake 使用“conf/bbblayers.conf”文件（它是用户配置的一部分）来查找它应该使用哪些层作为构建的一部分。

### Distro Layer

The distribution layer provides policy configurations for your distribution. Best practices dictate that you isolate these types of configurations into their own layer. Settings you provide in `conf/distro/distro.conf` override similar settings that BitBake finds in your `conf/local.conf` file in the `Build Directory`{.interpreted-text role="term"}.

> 分发层为您的分发提供策略配置。最佳实践要求将这些类型的配置隔离到它们自己的层中。您在“conf/disro/distro.conf”中提供的设置覆盖了 BitBake 在“构建目录”中的“conf/local.conf”文件中找到的类似设置。

The following list provides some explanation and references for what you typically find in the distribution layer:

> 以下列表为您通常在分布层中找到的内容提供了一些解释和参考：

- *classes:* Class files (`.bbclass`) hold common functionality that can be shared among recipes in the distribution. When your recipes inherit a class, they take on the settings and functions for that class. You can read more about class files in the \"`ref-manual/classes:Classes`{.interpreted-text role="ref"}\" chapter of the Yocto Reference Manual.

> -*classes:*类文件（`.bbclass`）包含可以在分发中的配方之间共享的通用功能。当您的配方继承一个类时，它们将承担该类的设置和函数。您可以在《Yocto 参考手册》的\“`ref manual/classes:classes`｛.depreted text role=”ref“｝\”一章中阅读更多关于类文件的信息。

- *conf:* This area holds configuration files for the layer (`conf/layer.conf`), the distribution (`conf/distro/distro.conf`), and any distribution-wide include files.

> -*conf:*此区域保存层（“conf/layer.conf”）、分发（“conf/distrio/distro.conf”）的配置文件，以及任何分发范围内的 include 文件。

- *recipes-*:\* Recipes and append files that affect common functionality across the distribution. This area could include recipes and append files to add distribution-specific configuration, initialization scripts, custom image recipes, and so forth. Examples of `recipes-*` directories are `recipes-core` and `recipes-extra`. Hierarchy and contents within a `recipes-*` directory can vary. Generally, these directories contain recipe files (`*.bb`), recipe append files (`*.bbappend`), directories that are distro-specific for configuration files, and so forth.

> -*配方-*：\*配方和附加文件会影响整个发行版的通用功能。这个区域可以包括配方和附加文件，以添加特定于分发的配置、初始化脚本、自定义图像配方等等。“recipes-*”目录的例子有“recipes-core”和“recipes extra”。“recipes-*”目录中的层次结构和内容可能会有所不同。通常，这些目录包含配方文件（`*.bb`）、配方附加文件（`*bbappend`）、特定于发行版的配置文件目录等等。

### BSP Layer

The BSP Layer provides machine configurations that target specific hardware. Everything in this layer is specific to the machine for which you are building the image or the SDK. A common structure or form is defined for BSP layers. You can learn more about this structure in the `/bsp-guide/index`{.interpreted-text role="doc"}.

> BSP 层提供针对特定硬件的机器配置。该层中的所有内容都特定于您正在为其构建映像或 SDK 的机器。为 BSP 层定义了一种通用的结构或形式。有关此结构的详细信息，请参阅 `nbsp guide/index`｛.depreted text role=“doc”｝。

::: note
::: title
Note
:::

In order for a BSP layer to be considered compliant with the Yocto Project, it must meet some structural requirements.

> 为了使 BSP 层被认为符合 Yocto 项目，它必须满足一些结构要求。
> :::

The BSP Layer\'s configuration directory contains configuration files for the machine (`conf/machine/machine.conf`) and, of course, the layer (`conf/layer.conf`).

> BSP 层的配置目录包含机器的配置文件（“conf/machine/machine.conf”），当然还有层的配置文件。

The remainder of the layer is dedicated to specific recipes by function: `recipes-bsp`, `recipes-core`, `recipes-graphics`, `recipes-kernel`, and so forth. There can be metadata for multiple formfactors, graphics support systems, and so forth.

> 该层的其余部分按功能专门用于特定的食谱：“食谱 nbsp”、“食谱核心”、“配方图形”、“菜谱内核”等等。可以有用于多种形式因素、图形支持系统等的元数据。

::: note
::: title
Note
:::

While the figure shows several recipes-\* directories, not all these directories appear in all BSP layers.

> 虽然图中显示了几个配方目录，但并非所有这些目录都出现在所有 BSP 层中。
> :::

### Software Layer

The software layer provides the Metadata for additional software packages used during the build. This layer does not include Metadata that is specific to the distribution or the machine, which are found in their respective layers.

> 软件层为构建过程中使用的其他软件包提供元数据。此层不包括特定于分发或机器的元数据，这些元数据位于各自的层中。

This layer contains any recipes, append files, and patches, that your project needs.

> 该层包含项目所需的任何配方、附加文件和修补程序。

## Sources

In order for the OpenEmbedded build system to create an image or any target, it must be able to access source files. The `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"} represents source files using the \"Upstream Project Releases\", \"Local Projects\", and \"SCMs (optional)\" boxes. The figure represents mirrors, which also play a role in locating source files, with the \"Source Materials\" box.

> 为了使 OpenEmbedded 构建系统能够创建映像或任何目标，它必须能够访问源文件。`general workflow figure＜overview manual/concepts:openembedded build system concepts>`{.depreced text role=“ref”}表示使用“上游项目发布”、“本地项目”和“SCM（可选）”框的源文件。该图表示镜像，镜像也在查找源文件中发挥作用，带有\“source Materials”框。

The method by which source files are ultimately organized is a function of the project. For example, for released software, projects tend to use tarballs or other archived files that can capture the state of a release guaranteeing that it is statically represented. On the other hand, for a project that is more dynamic or experimental in nature, a project might keep source files in a repository controlled by a Source Control Manager (SCM) such as Git. Pulling source from a repository allows you to control the point in the repository (the revision) from which you want to build software. A combination of the two is also possible.

> 最终组织源文件的方法是项目的一个功能。例如，对于已发布的软件，项目倾向于使用 tarball 或其他归档文件，这些文件可以捕获发布的状态，从而保证它是静态表示的。另一方面，对于一个更具动态性或实验性的项目，项目可能会将源文件保存在由源代码管理器（SCM）（如 Git）控制的存储库中。从存储库中提取源代码可以控制存储库中要用来构建软件的点（修订版）。两者的结合也是可能的。

BitBake uses the `SRC_URI`{.interpreted-text role="term"} variable to point to source files regardless of their location. Each recipe must have a `SRC_URI`{.interpreted-text role="term"} variable that points to the source.

> BitBake 使用 `SRC_URI`｛.explored text role=“term”｝变量来指向源文件，而不管它们的位置如何。每个配方都必须有一个指向源的 `SRC_URI`｛.explored text role=“term”｝变量。

Another area that plays a significant role in where source files come from is pointed to by the `DL_DIR`{.interpreted-text role="term"} variable. This area is a cache that can hold previously downloaded source. You can also instruct the OpenEmbedded build system to create tarballs from Git repositories, which is not the default behavior, and store them in the `DL_DIR`{.interpreted-text role="term"} by using the `BB_GENERATE_MIRROR_TARBALLS`{.interpreted-text role="term"} variable.

> 另一个在源文件来源方面发挥重要作用的领域是 `DL_DIR`{.depredicted text role=“term”}变量。此区域是一个缓存，可以保存以前下载的源。您还可以指示 OpenEmbedded 构建系统从 Git 存储库创建 tarball（这不是默认行为），并通过使用 `BB_GENERATE_MIRROR_tarballs`{.depreted text role=“term”}变量将其存储在 `DL_DIR`{.deploted text role=“term”}中。

Judicious use of a `DL_DIR`{.interpreted-text role="term"} directory can save the build system a trip across the Internet when looking for files. A good method for using a download directory is to have `DL_DIR`{.interpreted-text role="term"} point to an area outside of your `Build Directory`{.interpreted-text role="term"}. Doing so allows you to safely delete the `Build Directory`{.interpreted-text role="term"} if needed without fear of removing any downloaded source file.

> 明智地使用 `DL_DIR`｛.explored text role=“term”｝目录可以使构建系统在查找文件时省去一次穿越 Internet 的旅行。使用下载目录的一个好方法是让 `DL_DIR`｛.depredicted text role=“term”｝指向 `Build directory`｛.epredicted textrole=”term“｝之外的区域。这样做可以在需要时安全地删除“构建目录”｛.depreted text role=“term”｝，而不用担心删除任何下载的源文件。

The remainder of this section provides a deeper look into the source files and the mirrors. Here is a more detailed look at the source file area of the `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"}:

> 本节的其余部分提供了对源文件和镜像的更深入了解。以下是 `general workflow figure<overview manual/concepts:openembedded build system concepts>`{.depredicted text role=“ref”}的源文件区域的更详细信息：

![image](figures/source-input.png){.align-center width="70.0%"}

### Upstream Project Releases

Upstream project releases exist anywhere in the form of an archived file (e.g. tarball or zip file). These files correspond to individual recipes. For example, the figure uses specific releases each for BusyBox, Qt, and Dbus. An archive file can be for any released product that can be built using a recipe.

> 上游项目发布以存档文件（例如 tarball 或 zip 文件）的形式存在于任何地方。这些文件对应于各个配方。例如，该图为 BusyBox、Qt 和 Dbus 分别使用了特定的版本。档案文件可以用于任何可以使用配方构建的已发布产品。

### Local Projects

Local projects are custom bits of software the user provides. These bits reside somewhere local to a project \-\-- perhaps a directory into which the user checks in items (e.g. a local directory containing a development source tree used by the group).

> 本地项目是用户提供的自定义软件。这些位位于项目的本地某个地方\-可能是用户在其中签入项目的目录（例如，包含该组使用的开发源树的本地目录）。

The canonical method through which to include a local project is to use the `ref-classes-externalsrc`{.interpreted-text role="ref"} class to include that local project. You use either the `local.conf` or a recipe\'s append file to override or set the recipe to point to the local directory on your disk to pull in the whole source tree.

> 包含本地项目的规范方法是使用 `ref classes externalsrc`｛.depreted text role=“ref”｝类来包含该本地项目。您可以使用“local.conf”或配方的附加文件来覆盖或设置配方指向磁盘上的本地目录，以获取整个源树。

### Source Control Managers (Optional)

Another place from which the build system can get source files is with `bitbake-user-manual/bitbake-user-manual-fetching:fetchers`{.interpreted-text role="ref"} employing various Source Control Managers (SCMs) such as Git or Subversion. In such cases, a repository is cloned or checked out. The `ref-tasks-fetch`{.interpreted-text role="ref"} task inside BitBake uses the `SRC_URI`{.interpreted-text role="term"} variable and the argument\'s prefix to determine the correct fetcher module.

> 构建系统可以从中获取源文件的另一个地方是使用诸如 Git 或 Subversion 之类的各种源代码管理器（SCMs）的 `bitbake user manual/bitbake user-manual fetching:fetchers'｛.explored text role=“ref”｝。在这种情况下，存储库会被克隆或检出。BitBake内部的` ref tasks fetch `｛.depreted text role=“ref”｝任务使用` SRC_URI`｛.repreted text role=“term”｝变量和参数的前缀来确定正确的 fetcher 模块。

::: note
::: title
Note
:::

For information on how to have the OpenEmbedded build system generate tarballs for Git repositories and place them in the `DL_DIR`{.interpreted-text role="term"} directory, see the `BB_GENERATE_MIRROR_TARBALLS`{.interpreted-text role="term"} variable in the Yocto Project Reference Manual.

> 有关如何让 OpenEmbedded 构建系统为 Git 存储库生成 tarball 并将其放置在 `DL_DIR`｛.depredicted text role=“term”｝目录中的信息，请参阅 Yocto 项目参考手册中的 `BB_generate_MIRROR_TARBALL`｛.epredicted textrole=”term“｝变量。
> :::

When fetching a repository, BitBake uses the `SRCREV`{.interpreted-text role="term"} variable to determine the specific revision from which to build.

> 在获取存储库时，BitBake 使用 `SRCREV`｛.explored text role=“term”｝变量来确定构建的特定修订。

### Source Mirror(s)

There are two kinds of mirrors: pre-mirrors and regular mirrors. The `PREMIRRORS`{.interpreted-text role="term"} and `MIRRORS`{.interpreted-text role="term"} variables point to these, respectively. BitBake checks pre-mirrors before looking upstream for any source files. Pre-mirrors are appropriate when you have a shared directory that is not a directory defined by the `DL_DIR`{.interpreted-text role="term"} variable. A Pre-mirror typically points to a shared directory that is local to your organization.

> 有两种反射镜：预反射镜和常规反射镜。`PREMIRRORS`｛.depreced text role=“term”｝和 `MIRRORS`｛.epreced textrole=”term“｝变量分别指向这些变量。BitBake 在向上游查找任何源文件之前检查预镜像。如果共享目录不是由 `DL_DIR`｛.depreced text role=“term”｝变量定义的目录，则预镜像是合适的。预镜像通常指向组织本地的共享目录。

Regular mirrors can be any site across the Internet that is used as an alternative location for source code should the primary site not be functioning for some reason or another.

> 常规镜像可以是 Internet 上的任何站点，如果主站点由于某种原因无法运行，则该站点可以用作源代码的替代位置。

## Package Feeds

When the OpenEmbedded build system generates an image or an SDK, it gets the packages from a package feed area located in the `Build Directory`{.interpreted-text role="term"}. The `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"} shows this package feeds area in the upper-right corner.

> 当 OpenEmbedded 构建系统生成映像或 SDK 时，它会从位于“构建目录”｛.depreced text role=“term”｝中的包馈送区域获取包。`general workflow figure<overview manual/concepts:openembedded build system concepts>`{.depreted text role=“ref”}在右上角显示了此包提要区域。

This section looks a little closer into the package feeds area used by the build system. Here is a more detailed look at the area:

> 本节将更深入地介绍构建系统所使用的包提要区域。以下是该地区的详细情况：

![image](figures/package-feeds.png){width="100.0%"}

Package feeds are an intermediary step in the build process. The OpenEmbedded build system provides classes to generate different package types, and you specify which classes to enable through the `PACKAGE_CLASSES`{.interpreted-text role="term"} variable. Before placing the packages into package feeds, the build process validates them with generated output quality assurance checks through the `ref-classes-insane`{.interpreted-text role="ref"} class.

> 包提要是构建过程中的中间步骤。OpenEmbedded 构建系统提供了用于生成不同包类型的类，您可以通过 `package_CLASES`｛.explored text role=“term”｝变量指定要启用的类。在将包放入包提要之前，构建过程通过 `ref classes neuro`{.depreted text role=“ref”}类生成输出质量保证检查来验证它们。

The package feed area resides in the `Build Directory`{.interpreted-text role="term"}. The directory the build system uses to temporarily store packages is determined by a combination of variables and the particular package manager in use. See the \"Package Feeds\" box in the illustration and note the information to the right of that area. In particular, the following defines where package files are kept:

> 包馈送区域位于“生成目录”｛.explored text role=“term”｝中。构建系统用于临时存储包的目录由变量和使用中的特定包管理器的组合决定。请参阅图中的“Package Feeds”框，并注意该区域右侧的信息。特别是，以下定义了包文件的保存位置：

- `DEPLOY_DIR`{.interpreted-text role="term"}: Defined as `tmp/deploy` in the `Build Directory`{.interpreted-text role="term"}.

> -`DEPLOY_DIR`｛.depreted text role=“term”｝：在 `Build Directory` 中定义为 `tmp/DEPLOY`｛.repreted text role=“term“｝。

- `DEPLOY_DIR_*`: Depending on the package manager used, the package type sub-folder. Given RPM, IPK, or DEB packaging and tarball creation, the `DEPLOY_DIR_RPM`{.interpreted-text role="term"}, `DEPLOY_DIR_IPK`{.interpreted-text role="term"}, or `DEPLOY_DIR_DEB`{.interpreted-text role="term"} variables are used, respectively.

> -`DEPLOY_DIR_*`：根据使用的包管理器，包类型的子文件夹。给定 RPM、IPK 或 DEB 封装和 tarball 创建，将分别使用 `DEPLOY_DIR_RPM`｛.depreced text role=“term”｝、`DEPLOY_DIR_IPK`｛-depreced textrole=”term“｝或 `DEPLOY.DIR_DEB`｛.epreced extrole=。

- `PACKAGE_ARCH`{.interpreted-text role="term"}: Defines architecture-specific sub-folders. For example, packages could be available for the i586 or qemux86 architectures.

> -`PACKAGE_ARCH`｛.explored text role=“term”｝：定义体系结构特定的子文件夹。例如，包可以用于 i586 或 qemux86 体系结构。

BitBake uses the `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"} tasks to generate packages and place them into the package holding area (e.g. `do_package_write_ipk` for IPK packages). See the \"`ref-tasks-package_write_deb`{.interpreted-text role="ref"}\", \"`ref-tasks-package_write_ipk`{.interpreted-text role="ref"}\", and \"`ref-tasks-package_write_rpm`{.interpreted-text role="ref"}\" sections in the Yocto Project Reference Manual for additional information. As an example, consider a scenario where an IPK packaging manager is being used and there is package architecture support for both i586 and qemux86. Packages for the i586 architecture are placed in `build/tmp/deploy/ipk/i586`, while packages for the qemux86 architecture are placed in `build/tmp/deploy/ipk/qemux86`.

> BitBake 使用 `do_package_write_*<ref-tasks-package_write_deb>`{.depredicted text role=“ref”}任务来生成包并将其放入包容纳区（例如，对于 ipk 包，`do_papackage_write_ipk`）。有关更多信息，请参阅 Yocto 项目参考手册中的\“`ref-tasks-package_write_deb`｛.depredicted text role=“ref”｝\”、\“`ref-tasks-package_write_ipk`｛.epredicted text-role=“ref”}\”和\“`ref-tasks-package_write_rm`｛.repredicted ext-role=”ref“}\”章节。例如，考虑一个场景，其中使用了 IPK 封装管理器，并且同时支持 i586 和 qemux86 的封装体系结构。i586 体系结构的软件包位于“build/tmp/deploy/ipk/i586”中，而 qemux86 体系结构的程序包位于“uild/tmp/deloy/ipk/qemux86”中。

## BitBake Tool

The OpenEmbedded build system uses `BitBake`{.interpreted-text role="term"} to produce images and Software Development Kits (SDKs). You can see from the `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"}, the BitBake area consists of several functional areas. This section takes a closer look at each of those areas.

> OpenEmbedded 构建系统使用 `BitBake`｛.explored text role=“term”｝来生成图像和软件开发工具包（SDK）。您可以从 `general workflow figure<overview manual/concepts:openembedded build system concepts>`{.depreted text role=“ref”}中看到，BitBake 区域由几个功能区域组成。本节将详细介绍这些领域中的每一个。

::: note
::: title
Note
:::

Documentation for the BitBake tool is available separately. See the `BitBake User Manual <bitbake:index>`{.interpreted-text role="doc"} for reference material on BitBake.

> BitBake 工具的文档可单独提供。有关 BitBake 的参考资料，请参阅 `BitBake用户手册<BitBake:index>`{.depreced text role=“doc”}。
> :::

### Source Fetching

The first stages of building a recipe are to fetch and unpack the source code:

> 构建配方的第一阶段是获取和解压源代码：

![image](figures/source-fetching.png){width="100.0%"}

The `ref-tasks-fetch`{.interpreted-text role="ref"} and `ref-tasks-unpack`{.interpreted-text role="ref"} tasks fetch the source files and unpack them into the `Build Directory`{.interpreted-text role="term"}.

> `ref tasks fetch`｛.depreted text role=“ref”｝和 `ref task unpack`｛.repreted text role=“ref”}任务提取源文件并将其解包到 `Build Directory`｛.epreted text 角色=“term”｝中。

::: note
::: title
Note
:::

For every local file (e.g. `file://`) that is part of a recipe\'s `SRC_URI`{.interpreted-text role="term"} statement, the OpenEmbedded build system takes a checksum of the file for the recipe and inserts the checksum into the signature for the `ref-tasks-fetch`{.interpreted-text role="ref"} task. If any local file has been modified, the `ref-tasks-fetch`{.interpreted-text role="ref"} task and all tasks that depend on it are re-executed.

> 对于配方的“SRC_URI”｛.explored text role=“term”｝语句中的每个本地文件（例如，“file://”），OpenEmbedded 构建系统获取配方文件的校验和，并将校验和插入到“ref tasks fetch”｛.sexplored textrole=”ref“｝任务的签名中。如果修改了任何本地文件，则会重新执行 `ref tasks fetch`｛.explored text role=“ref”｝任务以及依赖于它的所有任务。
> :::

By default, everything is accomplished in the `Build Directory`{.interpreted-text role="term"}, which has a defined structure. For additional general information on the `Build Directory`{.interpreted-text role="term"}, see the \"`structure-core-build`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual.

> 默认情况下，所有内容都在“构建目录”｛.explored text role=“term”｝中完成，该目录具有定义的结构。有关“构建目录”的其他一般信息，请参阅 Yocto 项目参考手册中的“结构核心构建”部分。

Each recipe has an area in the `Build Directory`{.interpreted-text role="term"} where the unpacked source code resides. The `S`{.interpreted-text role="term"} variable points to this area for a recipe\'s unpacked source code. The name of that directory for any given recipe is defined from several different variables. The preceding figure and the following list describe the `Build Directory`{.interpreted-text role="term"}\'s hierarchy:

> 每个配方在“构建目录”中都有一个区域｛.explored text role=“term”｝，解压缩的源代码位于该区域。`S`｛.explored text role=“term”｝变量指向配方的解包源代码的这一区域。任何给定配方的目录名称都是由几个不同的变量定义的。上图和下表描述了“构建目录”的层次结构：

- `TMPDIR`{.interpreted-text role="term"}: The base directory where the OpenEmbedded build system performs all its work during the build. The default base directory is the `tmp` directory.

> -`TMPDIR`｛.explored text role=“term”｝：OpenEmbedded 构建系统在构建过程中执行所有工作的基本目录。默认的基本目录是“tmp”目录。

- `PACKAGE_ARCH`{.interpreted-text role="term"}: The architecture of the built package or packages. Depending on the eventual destination of the package or packages (i.e. machine architecture, `Build Host`{.interpreted-text role="term"}, SDK, or specific machine), `PACKAGE_ARCH`{.interpreted-text role="term"} varies. See the variable\'s description for details.

> -`PACKAGE_ARCH`｛.explored text role=“term”｝：构建的一个或多个包的体系结构。根据一个或多个包的最终目的地（即机器体系结构、`Build Host`｛.depreted text role=“term”｝、SDK 或特定机器），`package_ARCH`｛.repreted text role=“term“｝各不相同。有关详细信息，请参阅变量的描述。

- `TARGET_OS`{.interpreted-text role="term"}: The operating system of the target device. A typical value would be \"linux\" (e.g. \"qemux86-poky-linux\").

> -`TARGET_OS`｛.explored text role=“term”｝：目标设备的操作系统。一个典型的值是“linux”（例如“qemux86 poky linux”）。

- `PN`{.interpreted-text role="term"}: The name of the recipe used to build the package. This variable can have multiple meanings. However, when used in the context of input files, `PN`{.interpreted-text role="term"} represents the name of the recipe.

> -`PN`｛.explored text role=“term”｝：用于构建包的配方的名称。这个变量可以有多种含义。然而，当在输入文件的上下文中使用时，`PN`｛.explored text role=“term”｝表示配方的名称。

- `WORKDIR`{.interpreted-text role="term"}: The location where the OpenEmbedded build system builds a recipe (i.e. does the work to create the package).

> -`WORKDIR`｛.explored text role=“term”｝：OpenEmbedded 构建系统构建配方（即完成创建包的工作）的位置。

- `PV`{.interpreted-text role="term"}: The version of the recipe used to build the package.
- `PR`{.interpreted-text role="term"}: The revision of the recipe used to build the package.
- `S`{.interpreted-text role="term"}: Contains the unpacked source files for a given recipe.

  - `BPN`{.interpreted-text role="term"}: The name of the recipe used to build the package. The `BPN`{.interpreted-text role="term"} variable is a version of the `PN`{.interpreted-text role="term"} variable but with common prefixes and suffixes removed.

> -`BPN`｛.explored text role=“term”｝：用于构建包的配方的名称。`BPN`｛.depredicted text role=“term”｝变量是 `PN`｛.epredicted textrole=”term“｝变量的一个版本，但去掉了常见的前缀和后缀。

- `PV`{.interpreted-text role="term"}: The version of the recipe used to build the package.

::: note
::: title
Note
:::

In the previous figure, notice that there are two sample hierarchies: one based on package architecture (i.e. `PACKAGE_ARCH`{.interpreted-text role="term"}) and one based on a machine (i.e. `MACHINE`{.interpreted-text role="term"}). The underlying structures are identical. The differentiator being what the OpenEmbedded build system is using as a build target (e.g. general architecture, a build host, an SDK, or a specific machine).

> 在上图中，请注意有两个示例层次结构：一个基于包体系结构（即 `package_ARCH`｛.depreted text role=“term”｝），另一个基于机器（即 `machine`｛.repreted text role=“term“｝）。底层结构是相同的。区别在于 OpenEmbedded 构建系统正在使用什么作为构建目标（例如，通用架构、构建主机、SDK 或特定机器）。
> :::

### Patching

Once source code is fetched and unpacked, BitBake locates patch files and applies them to the source files:

> 一旦提取并解包了源代码，BitBake 就会定位补丁文件并将其应用于源文件：

![image](figures/patching.png){width="100.0%"}

The `ref-tasks-patch`{.interpreted-text role="ref"} task uses a recipe\'s `SRC_URI`{.interpreted-text role="term"} statements and the `FILESPATH`{.interpreted-text role="term"} variable to locate applicable patch files.

> `ref tasks patch`｛.expreted text role=“ref”｝任务使用配方的 `SRC_URI`｛.repreted text role=“term”｝语句和 `FILESPATH`｛.depreted text 角色=“term”｝变量来查找适用的修补程序文件。

Default processing for patch files assumes the files have either `*.patch` or `*.diff` file types. You can use `SRC_URI`{.interpreted-text role="term"} parameters to change the way the build system recognizes patch files. See the `ref-tasks-patch`{.interpreted-text role="ref"} task for more information.

> 修补程序文件的默认处理假定文件具有“*.patch”或“*.diff”文件类型。您可以使用 `SRC_URI`｛.explored text role=“term”｝参数来更改生成系统识别修补程序文件的方式。有关详细信息，请参阅 `ref tasks patch`｛.explored text role=“ref”｝任务。

BitBake finds and applies multiple patches for a single recipe in the order in which it locates the patches. The `FILESPATH`{.interpreted-text role="term"} variable defines the default set of directories that the build system uses to search for patch files. Once found, patches are applied to the recipe\'s source files, which are located in the `S`{.interpreted-text role="term"} directory.

> BitBake 按照查找补丁的顺序为单个配方查找并应用多个补丁。`FILESPATH`｛.respered text role=“term”｝变量定义生成系统用于搜索修补程序文件的默认目录集。一旦找到补丁，就会将其应用于配方的源文件，这些文件位于 `s`｛.explored text role=“term”｝目录中。

For more information on how the source directories are created, see the \"`overview-manual/concepts:source fetching`{.interpreted-text role="ref"}\" section. For more information on how to create patches and how the build system processes patches, see the \"`dev-manual/new-recipe:patching code`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual. You can also see the \"``sdk-manual/extensible:use \`\`devtool modify\`\` to modify the source of an existing component``{.interpreted-text role="ref"}\" section in the Yocto Project Application Development and the Extensible Software Development Kit (SDK) manual and the \"`kernel-dev/common:using traditional kernel development to patch the kernel`{.interpreted-text role="ref"}\" section in the Yocto Project Linux Kernel Development Manual.

> 有关如何创建源目录的更多信息，请参阅\“`overview manual/concepts:source fetching`｛.depredicted text role=”ref“｝\”一节。有关如何创建修补程序以及构建系统如何处理修补程序的更多信息，请参阅 Yocto 项目开发任务手册中的“`dev manual/new recipe:patching code`｛.depreted text role=“ref”｝”一节。您还可以看到 Yocto Project Application Development and the extensible Software Development Kit（sdk）manual 中的\“`sdk manual/extensible:use \`\devtool modify\`\` to modify the source of a existing component”“｛.explored text role=”ref“｝”部分，以及 Yocto Project Linux 内核开发手册。

### Configuration, Compilation, and Staging

After source code is patched, BitBake executes tasks that configure and compile the source code. Once compilation occurs, the files are copied to a holding area (staged) in preparation for packaging:

> 修补源代码后，BitBake 将执行配置和编译源代码的任务。编译完成后，文件将被复制到保存区（暂存），为打包做准备：

![image](figures/configuration-compile-autoreconf.png){width="100.0%"}

This step in the build process consists of the following tasks:

> 构建过程中的此步骤包括以下任务：

- `ref-tasks-prepare_recipe_sysroot`{.interpreted-text role="ref"}: This task sets up the two sysroots in `${``WORKDIR`{.interpreted-text role="term"}`}` (i.e. `recipe-sysroot` and `recipe-sysroot-native`) so that during the packaging phase the sysroots can contain the contents of the `ref-tasks-populate_sysroot`{.interpreted-text role="ref"} tasks of the recipes on which the recipe containing the tasks depends. A sysroot exists for both the target and for the native binaries, which run on the host system.

> -`ref-tasks-prepare_recipe_sysroot`｛.explored text role=“ref”｝：此任务在 `$｛` WORKDIR `｛.Expered text rol=“term”｝` 中设置两个系统根（即 `recipe sysroot` 和 `recipe-sysroot native` 配方），以便在打包阶段，系统根可以包含配方的 `ref-ttasks-populate_sysroot'｛任务取决于情况。对于在主机系统上运行的目标二进制文件和本机二进制文件，都存在系统根。

- *do_configure*: This task configures the source by enabling and disabling any build-time and configuration options for the software being built. Configurations can come from the recipe itself as well as from an inherited class. Additionally, the software itself might configure itself depending on the target for which it is being built.

> -*do_configure*：此任务通过启用和禁用正在构建的软件的任何构建时间和配置选项来配置源。配置既可以来自配方本身，也可以来自继承的类。此外，软件本身可能会根据构建它的目标来配置自己。

The configurations handled by the `ref-tasks-configure`{.interpreted-text role="ref"} task are specific to configurations for the source code being built by the recipe.

> “ref tasks configure”｛.explored text role=“ref”｝任务所处理的配置特定于配方所构建的源代码的配置。

If you are using the `ref-classes-autotools`{.interpreted-text role="ref"} class, you can add additional configuration options by using the `EXTRA_OECONF`{.interpreted-text role="term"} or `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"} variables. For information on how this variable works within that class, see the `ref-classes-autotools`{.interpreted-text role="ref"} class :yocto\_[git:%60here](git:%60here) \</poky/tree/meta/classes-recipe/autotools.bbclass\>\`.

> 如果您使用的是 `ref classes autotools`｛.respered text role=“ref”｝类，则可以使用 `EXTRA_OECOCONF`｛.espered text role=“term”｝或 `PACKACECONFIG_CONFARGS`｛.despered text 角色=“term”｝变量添加其他配置选项。有关此变量如何在该类中工作的信息，请参阅 `ref classes autotools`｛.explored text role=“ref”｝class:yocto\_[git:%60here]（git:%60where）\</poky/tree/meta/classes recipe/autotools.bbclass\>\`。

- *do_compile*: Once a configuration task has been satisfied, BitBake compiles the source using the `ref-tasks-compile`{.interpreted-text role="ref"} task. Compilation occurs in the directory pointed to by the `B`{.interpreted-text role="term"} variable. Realize that the `B`{.interpreted-text role="term"} directory is, by default, the same as the `S`{.interpreted-text role="term"} directory.

> -*do_compile*：一旦满足了配置任务，BitBake 就会使用 `ref tasks compile`｛.explored text role=“ref”｝任务编译源代码。编译发生在 `B`｛.explored text role=“term”｝变量指向的目录中。请注意，默认情况下，`B`｛.depreced text role=“term”｝目录与 `S`｛.epreced textrole=”term“｝目录相同。

- *do_install*: After compilation completes, BitBake executes the `ref-tasks-install`{.interpreted-text role="ref"} task. This task copies files from the `B`{.interpreted-text role="term"} directory and places them in a holding area pointed to by the `D`{.interpreted-text role="term"} variable. Packaging occurs later using files from this holding directory.

> -*do_install*：编译完成后，BitBake 执行 `ref tasks install`｛.explored text role=“ref”｝任务。此任务从 `B`｛.depredicted text role=“term”｝目录复制文件，并将它们放置在 `D`｛.epredicted textrole=”term“｝变量指向的保留区域中。稍后将使用此保存目录中的文件进行打包。

### Package Splitting

After source code is configured, compiled, and staged, the build system analyzes the results and splits the output into packages:

> 在配置、编译和暂存源代码后，构建系统会分析结果并将输出拆分为多个包：

![image](figures/analysis-for-package-splitting.png){width="100.0%"}

The `ref-tasks-package`{.interpreted-text role="ref"} and `ref-tasks-packagedata`{.interpreted-text role="ref"} tasks combine to analyze the files found in the `D`{.interpreted-text role="term"} directory and split them into subsets based on available packages and files. Analysis involves the following as well as other items: splitting out debugging symbols, looking at shared library dependencies between packages, and looking at package relationships.

> `ref tasks package`｛.depreted text role=“ref”｝和 `ref tasks-packagedata`｛.epreted text role=“ref”}任务结合起来分析在 `D`｛.repreted text 角色=“term”｝目录中找到的文件，并根据可用的包和文件将它们拆分为子集。分析涉及以下以及其他项目：拆分调试符号，查看包之间的共享库依赖关系，以及查看包关系。

The `ref-tasks-packagedata`{.interpreted-text role="ref"} task creates package metadata based on the analysis such that the build system can generate the final packages. The `ref-tasks-populate_sysroot`{.interpreted-text role="ref"} task stages (copies) a subset of the files installed by the `ref-tasks-install`{.interpreted-text role="ref"} task into the appropriate sysroot. Working, staged, and intermediate results of the analysis and package splitting process use several areas:

> `ref tasks packagedata`｛.explored text role=“ref”｝任务基于分析创建包元数据，使得构建系统可以生成最终包。`ref-tasks-populate_sysroot`｛.depreted text role=“ref”｝任务将 `ref tasks install`｛.repreted text role=“ref”}任务安装的文件的子集暂存（复制）到相应的 sysroot 中。分析和包拆分过程的工作、阶段和中间结果使用了几个领域：

- `PKGD`{.interpreted-text role="term"}: The destination directory (i.e. `package`) for packages before they are split into individual packages.

> -`PKGD`｛.explored text role=“term”｝：包在拆分为单个包之前的目标目录（即“包”）。

- `PKGDESTWORK`{.interpreted-text role="term"}: A temporary work area (i.e. `pkgdata`) used by the `ref-tasks-package`{.interpreted-text role="ref"} task to save package metadata.

> -`PKGDESTWORK`｛.depreted text role=“term”｝：`ref tasks package`｛.epreted text role=“ref”｝任务用于保存包元数据的临时工作区（即 `pkgdata`）。

- `PKGDEST`{.interpreted-text role="term"}: The parent directory (i.e. `packages-split`) for packages after they have been split.

> -`PKGDEST`｛.explored text role=“term”｝：拆分后的包的父目录（即“packages split”）。

- `PKGDATA_DIR`{.interpreted-text role="term"}: A shared, global-state directory that holds packaging metadata generated during the packaging process. The packaging process copies metadata from `PKGDESTWORK`{.interpreted-text role="term"} to the `PKGDATA_DIR`{.interpreted-text role="term"} area where it becomes globally available.

> -`PKGDATA_DIR`｛.explored text role=“term”｝：一个共享的全局状态目录，用于保存打包过程中生成的打包元数据。打包过程将元数据从 `PKGDESTWORK`｛.expected text role=“term”｝复制到 `PKGDATA_DIR`｛.dexpected textrole=”term“｝区域，在该区域中元数据变得全局可用。

- `STAGING_DIR_HOST`{.interpreted-text role="term"}: The path for the sysroot for the system on which a component is built to run (i.e. `recipe-sysroot`).

> -`STAGING_DIR_HOST`｛.explored text role=“term”｝：组件要在其上运行的系统的系统根路径（即 `recipe sysroot`）。

- `STAGING_DIR_NATIVE`{.interpreted-text role="term"}: The path for the sysroot used when building components for the build host (i.e. `recipe-sysroot-native`).

> -`STAGING_DIR_NAMETIVE`｛.explored text role=“term”｝：为生成主机生成组件时使用的系统根路径（即 `recipe sysroot NATIVE`）。

- `STAGING_DIR_TARGET`{.interpreted-text role="term"}: The path for the sysroot used when a component that is built to execute on a system and it generates code for yet another machine (e.g. `ref-classes-cross-canadian`{.interpreted-text role="ref"} recipes).

> -`STAGING_DIR_TARGET`｛.explored text role=“term”｝：当一个组件被构建为在系统上执行，并且它为另一台机器生成代码时使用的系统根的路径（例如，`ref classes cross-candand`｛.Expered text role=“ref”}recipes）。

The `FILES`{.interpreted-text role="term"} variable defines the files that go into each package in `PACKAGES`{.interpreted-text role="term"}. If you want details on how this is accomplished, you can look at :yocto\_[git:%60package.bbclass](git:%60package.bbclass) \</poky/tree/meta/classes-global/package.bbclass\>\`.

> `FILES`｛.depreted text role=“term”｝变量定义了进入 `PACKAGES`｛.epreted text role=“term“｝中每个包的文件。如果您想了解如何实现这一目标的详细信息，可以查看：yocto\_[git:%60package.bbclass]（git:%60package.bbcclass）\</poky/tree/meta/classes global/package.bbcclass\>\`。

Depending on the type of packages being created (RPM, DEB, or IPK), the `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"} task creates the actual packages and places them in the Package Feed area, which is `${TMPDIR}/deploy`. You can see the \"`overview-manual/concepts:package feeds`{.interpreted-text role="ref"}\" section for more detail on that part of the build process.

> 根据正在创建的包的类型（RPM、DEB 或 IPK），`do_package_write_*<ref-tasks-package_write_DEB>`{.expreted text role=“ref”}任务会创建实际的包，并将它们放置在 PackageFeed 区域，即 `${TMPDIR}/deploy`。您可以参阅“`overview manual/concepts:package feed`｛.explored text role=“ref”｝”一节，了解有关构建过程这一部分的更多详细信息。

::: note
::: title
Note
:::

Support for creating feeds directly from the `deploy/*` directories does not exist. Creating such feeds usually requires some kind of feed maintenance mechanism that would upload the new packages into an official package feed (e.g. the Ångström distribution). This functionality is highly distribution-specific and thus is not provided out of the box.

> 不支持直接从“deploy/*”目录创建提要。创建这样的提要通常需要某种提要维护机制，将新包上传到官方包提要（例如 Ångström 分发）。该功能是高度特定于分发的，因此不是开箱即用的。
> :::

### Image Generation

Once packages are split and stored in the Package Feeds area, the build system uses BitBake to generate the root filesystem image:

> 一旦包被拆分并存储在 Package Feeds 区域中，构建系统就会使用 BitBake 生成根文件系统映像：

![image](figures/image-generation.png){width="100.0%"}

The image generation process consists of several stages and depends on several tasks and variables. The `ref-tasks-rootfs`{.interpreted-text role="ref"} task creates the root filesystem (file and directory structure) for an image. This task uses several key variables to help create the list of packages to actually install:

> 图像生成过程由几个阶段组成，并取决于几个任务和变量。`ref tasks rootfs`{.depreted text role=“ref”}任务为映像创建根文件系统（文件和目录结构）。此任务使用几个关键变量来帮助创建要实际安装的软件包列表：

- `IMAGE_INSTALL`{.interpreted-text role="term"}: Lists out the base set of packages from which to install from the Package Feeds area.

> -`IMAGE_INSTALL`｛.explored text role=“term”｝：从“程序包源”区域列出要安装的基本程序包集。

- `PACKAGE_EXCLUDE`{.interpreted-text role="term"}: Specifies packages that should not be installed into the image.

> -`PACKAGE_EXCLUDE`｛.explored text role=“term”｝：指定不应安装到映像中的程序包。

- `IMAGE_FEATURES`{.interpreted-text role="term"}: Specifies features to include in the image. Most of these features map to additional packages for installation.

> -`IMAGE_FATURE`｛.explored text role=“term”｝：指定要包含在图像中的功能。这些功能中的大多数都映射到要安装的附加软件包中。

- `PACKAGE_CLASSES`{.interpreted-text role="term"}: Specifies the package backend (e.g. RPM, DEB, or IPK) to use and consequently helps determine where to locate packages within the Package Feeds area.

> -`PACKAGE_CLASES`｛.explored text role=“term”｝：指定要使用的包后端（例如 RPM、DEB 或 IPK），从而有助于确定在包提要区域内定位包的位置。

- `IMAGE_LINGUAS`{.interpreted-text role="term"}: Determines the language(s) for which additional language support packages are installed.

> -`IMAGE_LINGUAS`｛.explored text role=“term”｝：确定为其安装其他语言支持包的语言。

- `PACKAGE_INSTALL`{.interpreted-text role="term"}: The final list of packages passed to the package manager for installation into the image.

> -`PACKAGE_INSTALL`｛.explored text role=“term”｝：传递给包管理器以安装到映像中的包的最终列表。

With `IMAGE_ROOTFS`{.interpreted-text role="term"} pointing to the location of the filesystem under construction and the `PACKAGE_INSTALL`{.interpreted-text role="term"} variable providing the final list of packages to install, the root file system is created.

> 当 `IMAGE_ROOTFS`｛.expected text role=“term”｝指向正在构建的文件系统的位置，并且 `PACKAGE_INSTALL`｛.dexpected textrole=”term“｝变量提供要安装的包的最终列表时，根文件系统就创建了。

Package installation is under control of the package manager (e.g. dnf/rpm, opkg, or apt/dpkg) regardless of whether or not package management is enabled for the target. At the end of the process, if package management is not enabled for the target, the package manager\'s data files are deleted from the root filesystem. As part of the final stage of package installation, post installation scripts that are part of the packages are run. Any scripts that fail to run on the build host are run on the target when the target system is first booted. If you are using a `read-only root filesystem <dev-manual/read-only-rootfs:creating a read-only root filesystem>`{.interpreted-text role="ref"}, all the post installation scripts must succeed on the build host during the package installation phase since the root filesystem on the target is read-only.

> 无论是否为目标启用包管理，包安装都在包管理器（例如 dnf/rpm、opkg 或 apt/dpkg）的控制之下。在该过程结束时，如果未为目标启用包管理，则会从根文件系统中删除包管理器的数据文件。作为包安装的最后阶段的一部分，运行作为包一部分的安装后脚本。第一次启动目标系统时，任何未能在生成主机上运行的脚本都将在目标上运行。如果您使用的是“只读根文件系统 <devmanual/read-only-rootfs:create a read-only-root filesystem>`{.depredicted text role=“ref”}，则在包安装阶段，所有安装后脚本都必须在构建主机上成功，因为目标上的根文件系统是只读的。

The final stages of the `ref-tasks-rootfs`{.interpreted-text role="ref"} task handle post processing. Post processing includes creation of a manifest file and optimizations.

> ref tasks rootfs 任务的最后阶段处理后期处理。后处理包括创建清单文件和优化。

The manifest file (`.manifest`) resides in the same directory as the root filesystem image. This file lists out, line-by-line, the installed packages. The manifest file is useful for the `ref-classes-testimage`{.interpreted-text role="ref"} class, for example, to determine whether or not to run specific tests. See the `IMAGE_MANIFEST`{.interpreted-text role="term"} variable for additional information.

> 清单文件（`.manifest`）与根文件系统映像位于同一目录中。该文件逐行列出已安装的程序包。例如，清单文件对于“ref classes testimage”｛.explored text role=“ref”｝类非常有用，可以确定是否运行特定的测试。有关更多信息，请参阅 `IMAGE_MMANIFEST`｛.explored text role=“term”｝变量。

Optimizing processes that are run across the image include `mklibs` and any other post-processing commands as defined by the `ROOTFS_POSTPROCESS_COMMAND`{.interpreted-text role="term"} variable. The `mklibs` process optimizes the size of the libraries.

> 在映像上运行的优化进程包括“mklibs”和由“ROOTFS_POSTPROCESS_COMMAND`”｛.depredicted text role=“term”｝变量定义的任何其他后处理命令。“mklibs”过程优化了库的大小。

After the root filesystem is built, processing begins on the image through the `ref-tasks-image`{.interpreted-text role="ref"} task. The build system runs any pre-processing commands as defined by the `IMAGE_PREPROCESS_COMMAND`{.interpreted-text role="term"} variable. This variable specifies a list of functions to call before the build system creates the final image output files.

> 建立根文件系统后，通过 `ref tasks image`｛.depreted text role=“ref”｝任务开始对映像进行处理。构建系统运行由 `IMAGE_PREPROCESS_COMMAND`｛.explored text role=“term”｝变量定义的任何预处理命令。此变量指定在生成系统创建最终图像输出文件之前要调用的函数列表。

The build system dynamically creates `do_image_* <ref-tasks-image>`{.interpreted-text role="ref"} tasks as needed, based on the image types specified in the `IMAGE_FSTYPES`{.interpreted-text role="term"} variable. The process turns everything into an image file or a set of image files and can compress the root filesystem image to reduce the overall size of the image. The formats used for the root filesystem depend on the `IMAGE_FSTYPES`{.interpreted-text role="term"} variable. Compression depends on whether the formats support compression.

> 构建系统根据 `image_FSTYPE`｛.depreted text role=“term”｝变量中指定的图像类型，根据需要动态创建 `do_image_*<ref tasks image>`｛.repreted text role=“ref”｝任务。该过程将所有内容转换为一个映像文件或一组映像文件，并可以压缩根文件系统映像以减小映像的总体大小。根文件系统使用的格式取决于 `IMAGE_FSTYPES`｛.depreted text role=“term”｝变量。压缩取决于格式是否支持压缩。

As an example, a dynamically created task when creating a particular image type would take the following form:

> 例如，创建特定图像类型时动态创建的任务将采用以下形式：

```
do_image_type
```

So, if the type as specified by the `IMAGE_FSTYPES`{.interpreted-text role="term"} were `ext4`, the dynamically generated task would be as follows:

> 因此，如果 `IMAGE_FSTTYPE`｛.explored text role=“term”｝指定的类型为 `ext4`，则动态生成的任务将如下所示：

```
do_image_ext4
```

The final task involved in image creation is the `do_image_complete <ref-tasks-image-complete>`{.interpreted-text role="ref"} task. This task completes the image by applying any image post processing as defined through the `IMAGE_POSTPROCESS_COMMAND`{.interpreted-text role="term"} variable. The variable specifies a list of functions to call once the build system has created the final image output files.

> 图像创建中涉及的最后一项任务是 `do_image_complete<ref tasks image complete>`{.depredicted text role=“ref”}任务。此任务通过应用通过 `image_POSTPPROCESS_COMMAND`{.depreted text role=“term”}变量定义的任何图像后处理来完成图像。该变量指定生成系统创建最终图像输出文件后要调用的函数列表。

::: note
::: title
Note
:::

The entire image generation process is run under Pseudo. Running under Pseudo ensures that the files in the root filesystem have correct ownership.

> 整个图像生成过程都在“伪”下运行。在 Pseudo 下运行可确保根文件系统中的文件具有正确的所有权。
> :::

### SDK Generation

The OpenEmbedded build system uses BitBake to generate the Software Development Kit (SDK) installer scripts for both the standard SDK and the extensible SDK (eSDK):

> OpenEmbedded 构建系统使用 BitBake 为标准 SDK 和可扩展 SDK（eSDK）生成软件开发工具包（SDK）安装程序脚本：

![image](figures/sdk-generation.png){width="100.0%"}

::: note
::: title
Note
:::

For more information on the cross-development toolchain generation, see the \"`overview-manual/concepts:cross-development toolchain generation`{.interpreted-text role="ref"}\" section. For information on advantages gained when building a cross-development toolchain using the `ref-tasks-populate_sdk`{.interpreted-text role="ref"} task, see the \"`sdk-manual/appendix-obtain:building an sdk installer`{.interpreted-text role="ref"}\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.

> 有关跨开发工具链生成的更多信息，请参阅\“`overview manual/concepts:cross development toolchain generation`｛.explored text role=”ref“｝\”一节。有关使用 `ref-tasks-populate_sdk`｛.depredicted text role=“ref”｝任务构建跨开发工具链所获得优势的信息，请参阅 Yocto 项目应用程序开发和可扩展软件开发工具包（eSDK）手册中的\“`sdk manual/a附录获取：构建sdk安装程序`｛.epredicted textrole=”ref“｝\”一节。
> :::

Like image generation, the SDK script process consists of several stages and depends on many variables. The `ref-tasks-populate_sdk`{.interpreted-text role="ref"} and `ref-tasks-populate_sdk_ext`{.interpreted-text role="ref"} tasks use these key variables to help create the list of packages to actually install. For information on the variables listed in the figure, see the \"`overview-manual/concepts:application development sdk`{.interpreted-text role="ref"}\" section.

> 与图像生成一样，SDK 脚本过程由几个阶段组成，并依赖于许多变量。`ref-tasks-populate_sdk`｛.depreced text role=“ref”｝和 `ref-ttasks-populate_sdk_ext`｛.epreced text-role=“ref”}任务使用这些关键变量来帮助创建要实际安装的包的列表。有关图中列出的变量的信息，请参阅\“`overview manual/concepts:application development sdk`｛.depreted text role=”ref“｝\”一节。

The `ref-tasks-populate_sdk`{.interpreted-text role="ref"} task helps create the standard SDK and handles two parts: a target part and a host part. The target part is the part built for the target hardware and includes libraries and headers. The host part is the part of the SDK that runs on the `SDKMACHINE`{.interpreted-text role="term"}.

> `ref-tasks-populate_sdk`{.depredicted text role=“ref”}任务有助于创建标准 sdk 并处理两个部分：目标部分和主机部分。目标部件是为目标硬件构建的部件，包括库和头。主机部分是运行在 `SDKMACHINE`｛.explored text role=“term”｝上的 SDK 部分。

The `ref-tasks-populate_sdk_ext`{.interpreted-text role="ref"} task helps create the extensible SDK and handles host and target parts differently than its counter part does for the standard SDK. For the extensible SDK, the task encapsulates the build system, which includes everything needed (host and target) for the SDK.

> `ref-tasks-populate_sdk_ext`{.depredicted text role=“ref”}任务有助于创建可扩展 sdk，并与标准 sdk 的计数器部分不同地处理主机和目标部分。对于可扩展 SDK，任务封装了构建系统，其中包括 SDK 所需的一切（主机和目标）。

Regardless of the type of SDK being constructed, the tasks perform some cleanup after which a cross-development environment setup script and any needed configuration files are created. The final output is the Cross-development toolchain installation script (`.sh` file), which includes the environment setup script.

> 无论构建的 SDK 类型如何，任务都会执行一些清理，然后创建跨开发环境设置脚本和任何所需的配置文件。最终输出是跨开发工具链安装脚本（`.sh` 文件），其中包括环境设置脚本。

### Stamp Files and the Rerunning of Tasks

For each task that completes successfully, BitBake writes a stamp file into the `STAMPS_DIR`{.interpreted-text role="term"} directory. The beginning of the stamp file\'s filename is determined by the `STAMP`{.interpreted-text role="term"} variable, and the end of the name consists of the task\'s name and current `input checksum <overview-manual/concepts:checksums (signatures)>`{.interpreted-text role="ref"}.

> 对于每一个成功完成的任务，BitBake 都会将一个 stamp 文件写入 `STAMPS_DIR`｛.explored text role=“term”｝目录。戳文件文件名的开头由 `stamp`｛.depreced text role=“term”｝变量决定，名称的末尾由任务名称和当前 `input checksum＜overview manual/concepts:checksums（signatures）>`｛.epreded text role=“ref”｝组成。

::: note
::: title
Note
:::

This naming scheme assumes that `BB_SIGNATURE_HANDLER`{.interpreted-text role="term"} is \"OEBasicHash\", which is almost always the case in current OpenEmbedded.

> 这个命名方案假设 `BB_SIGNATURE_HANDLER`｛.explored text role=“term”｝是\“OEBasicHash\”，这在当前的 OpenEmbedded 中几乎总是这样。
> :::

To determine if a task needs to be rerun, BitBake checks if a stamp file with a matching input checksum exists for the task. In this case, the task\'s output is assumed to exist and still be valid. Otherwise, the task is rerun.

> 为了确定是否需要重新运行任务，BitBake 会检查该任务是否存在具有匹配输入校验和的戳文件。在这种情况下，假设任务的输出存在并且仍然有效。否则，将重新运行任务。

::: note
::: title
Note
:::

The stamp mechanism is more general than the shared state (sstate) cache mechanism described in the \"`overview-manual/concepts:setscene tasks and shared state`{.interpreted-text role="ref"}\" section. BitBake avoids rerunning any task that has a valid stamp file, not just tasks that can be accelerated through the sstate cache.

> stamp 机制比“概述手册/概念：setscene 任务和共享状态”一节中描述的共享状态（sstate）缓存机制更通用。BitBake 避免重新运行任何具有有效戳文件的任务，而不仅仅是可以通过 sstate 缓存加速的任务。

However, you should realize that stamp files only serve as a marker that some work has been done and that these files do not record task output. The actual task output would usually be somewhere in `TMPDIR`{.interpreted-text role="term"} (e.g. in some recipe\'s `WORKDIR`{.interpreted-text role="term"}.) What the sstate cache mechanism adds is a way to cache task output that can then be shared between build machines.

> 但是，您应该意识到，stamp 文件只是一个标记，表明已经完成了一些工作，并且这些文件不会记录任务输出。实际任务输出通常位于 `TMPDIR`｛.explored text role=“term”｝中的某个位置（例如，在某些配方的 `WORKDIR`｛..explored textrole=”term“｝中）。sstate 缓存机制添加的是一种缓存任务输出的方法，然后可以在构建机器之间共享。
> :::

Since `STAMPS_DIR`{.interpreted-text role="term"} is usually a subdirectory of `TMPDIR`{.interpreted-text role="term"}, removing `TMPDIR`{.interpreted-text role="term"} will also remove `STAMPS_DIR`{.interpreted-text role="term"}, which means tasks will properly be rerun to repopulate `TMPDIR`{.interpreted-text role="term"}.

> 由于 `STAMPS_DIR`｛.explored text role=“term”｝通常是 `TMPDIR` 的子目录｛.expered text role=“term”｝，删除 `TMPDIR`{.explered text role=“term“｝也将删除 `STAMPS_DIR`｛。

If you want some task to always be considered \"out of date\", you can mark it with the `nostamp <bitbake-user-manual/bitbake-user-manual-metadata:variable flags>`{.interpreted-text role="ref"} varflag. If some other task depends on such a task, then that task will also always be considered out of date, which might not be what you want.

> 如果您希望某项任务始终被视为“过时”，则可以使用 `nostamp<bitbake user manual/bitbake user-manual metadata:variable flags>`{.depreced text role=“ref”}varflag 对其进行标记。如果其他任务依赖于这样的任务，那么该任务也将始终被视为过时，这可能不是您想要的。

For details on how to view information about a task\'s signature, see the \"`dev-manual/debugging:viewing task variable dependencies`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 有关如何查看任务签名信息的详细信息，请参阅 Yocto 项目开发任务手册中的“`dev manual/debugging:viewing task variable dependencies`｛.depreted text role=“ref”｝”一节。

### Setscene Tasks and Shared State

The description of tasks so far assumes that BitBake needs to build everything and no available prebuilt objects exist. BitBake does support skipping tasks if prebuilt objects are available. These objects are usually made available in the form of a shared state (sstate) cache.

> 到目前为止，任务的描述假设 BitBake 需要构建所有内容，并且不存在可用的预构建对象。如果预构建对象可用，BitBake 确实支持跳过任务。这些对象通常以共享状态（sstate）缓存的形式提供。

::: note
::: title
Note
:::

For information on variables affecting sstate, see the `SSTATE_DIR`{.interpreted-text role="term"} and `SSTATE_MIRRORS`{.interpreted-text role="term"} variables.

> 有关影响 sstate_DIR 的变量的信息，请参阅 `sstate_DIR`｛.depreted text role=“term”｝和 `sstate_MIRRORS`｛.epreted text role=“term“｝变量。
> :::

The idea of a setscene task (i.e `do_taskname_setscene`) is a version of the task where instead of building something, BitBake can skip to the end result and simply place a set of files into specific locations as needed. In some cases, it makes sense to have a setscene task variant (e.g. generating package files in the `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"} task). In other cases, it does not make sense (e.g. a `ref-tasks-patch`{.interpreted-text role="ref"} task or a `ref-tasks-unpack`{.interpreted-text role="ref"} task) since the work involved would be equal to or greater than the underlying task.

> setscene 任务（即“do_taskname_setscene”）是该任务的一个版本，BitBake 可以跳到最终结果，并根据需要将一组文件放置到特定位置，而不是构建某些东西。在某些情况下，有一个 setscene 任务变体是有意义的（例如，在 `do_package_write_*<ref-tasks-package_write_deb>`{.expreted text role=“ref”}任务中生成包文件）。在其他情况下，这是没有意义的（例如，`ref tasks patch`｛.depreted text role=“ref”}任务或 `ref tasks-unpack`｛.repreted text role=“ref”｝任务），因为所涉及的工作量等于或大于基础任务。

In the build system, the common tasks that have setscene variants are `ref-tasks-package`{.interpreted-text role="ref"}, `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"}, `ref-tasks-deploy`{.interpreted-text role="ref"}, `ref-tasks-packagedata`{.interpreted-text role="ref"}, and `ref-tasks-populate_sysroot`{.interpreted-text role="ref"}. Notice that these tasks represent most of the tasks whose output is an end result.

> 在构建系统中，具有 setscene 变体的常见任务是 `ref tasks package`｛.depredicted text role=“ref”｝、`do_package_write_*<ref-tasks-package_write_deb>`｛.epredicted text-role=“ref”}、`ref tasks-deploy`｛.Epredicted ext-role=”ref“｝、` ref tasks-package-data`｛.repredicted text role=”ref“}和 `ref-tasks-cpopulate_sysroot`。请注意，这些任务代表了输出为最终结果的大多数任务。

The build system has knowledge of the relationship between these tasks and other preceding tasks. For example, if BitBake runs `do_populate_sysroot_setscene` for something, it does not make sense to run any of the `ref-tasks-fetch`{.interpreted-text role="ref"}, `ref-tasks-unpack`{.interpreted-text role="ref"}, `ref-tasks-patch`{.interpreted-text role="ref"}, `ref-tasks-configure`{.interpreted-text role="ref"}, `ref-tasks-compile`{.interpreted-text role="ref"}, and `ref-tasks-install`{.interpreted-text role="ref"} tasks. However, if `ref-tasks-package`{.interpreted-text role="ref"} needs to be run, BitBake needs to run those other tasks.

> 构建系统具有这些任务与其他先前任务之间的关系的知识。例如，如果 BitBake 为某件事运行 `do_populate_sysroot_setscene`，则运行 `ref tasks fetch`｛.depredicted text role=“ref”｝、`ref tasks-unpack`｛.epredicted text-role=“ref”}、`ref任务patch`｛.Epredicted ext-role=”ref“｝、`ref tasks configure`｛.repredicted text role=，和 `ref tasks install`｛.explored text role=“ref”｝任务。但是，如果需要运行 `ref tasks package`｛.explored text role=“ref”｝，则 BitBake 需要运行其他任务。

It becomes more complicated if everything can come from an sstate cache because some objects are simply not required at all. For example, you do not need a compiler or native tools, such as quilt, if there isn\'t anything to compile or patch. If the `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"} packages are available from sstate, BitBake does not need the `ref-tasks-package`{.interpreted-text role="ref"} task data.

> 如果所有内容都可以来自 sstate 缓存，则会变得更加复杂，因为有些对象根本不需要。例如，如果没有任何要编译或修补的内容，则不需要编译器或本地工具（如被子）。如果从 sstate 可以获得 `do_package_write_*<ref-tasks-package_write_deb>`{.depredicted text role=“ref”}包，则 BitBake 不需要 `ref tasks package`{.depreted text rol=“ref”｝任务数据。

To handle all these complexities, BitBake runs in two phases. The first is the \"setscene\" stage. During this stage, BitBake first checks the sstate cache for any targets it is planning to build. BitBake does a fast check to see if the object exists rather than doing a complete download. If nothing exists, the second phase, which is the setscene stage, completes and the main build proceeds.

> 为了处理所有这些复杂性，BitBake 分两个阶段运行。第一个阶段是“setscene”阶段。在此阶段，BitBake 首先检查 sstate 缓存中计划构建的任何目标。BitBake 会快速检查对象是否存在，而不是进行完整的下载。如果什么都不存在，则第二阶段，即 setscene 阶段，完成并继续进行主要构建。

If objects are found in the sstate cache, the build system works backwards from the end targets specified by the user. For example, if an image is being built, the build system first looks for the packages needed for that image and the tools needed to construct an image. If those are available, the compiler is not needed. Thus, the compiler is not even downloaded. If something was found to be unavailable, or the download or setscene task fails, the build system then tries to install dependencies, such as the compiler, from the cache.

> 如果在 sstate 缓存中找到对象，则构建系统将从用户指定的最终目标向后工作。例如，如果正在构建一个映像，那么构建系统首先会查找该映像所需的包以及构建映像所需要的工具。如果这些可用，则不需要编译器。因此，编译器甚至没有被下载。如果发现某些内容不可用，或者下载或设置场景任务失败，则构建系统会尝试从缓存中安装依赖项，例如编译器。

The availability of objects in the sstate cache is handled by the function specified by the `BB_HASHCHECK_FUNCTION`{.interpreted-text role="term"} variable and returns a list of available objects. The function specified by the `BB_SETSCENE_DEPVALID`{.interpreted-text role="term"} variable is the function that determines whether a given dependency needs to be followed, and whether for any given relationship the function needs to be passed. The function returns a True or False value.

> sstate 缓存中对象的可用性由 `BB_HASHCHECK_UNCTION`｛.explored text role=“term”｝变量指定的函数处理，并返回可用对象的列表。由 `BB_SETSCENE_DEPVALID`｛.explored text role=“term”｝变量指定的函数是一个确定是否需要遵循给定依赖项以及是否需要为任何给定关系传递该函数的函数。函数返回 True 或 False 值。

## Images

The images produced by the build system are compressed forms of the root filesystem and are ready to boot on a target device. You can see from the `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"} that BitBake output, in part, consists of images. This section takes a closer look at this output:

> 构建系统生成的映像是根文件系统的压缩形式，可以在目标设备上启动。您可以从 `general workflow figure<overview manual/concepts:openembedded build system concepts>`{.expreted text role=“ref”}中看到，BitBake 输出部分由图像组成。本节将详细介绍此输出：

![image](figures/images.png){.align-center width="75.0%"}

::: note
::: title
Note
:::

For a list of example images that the Yocto Project provides, see the \"`/ref-manual/images`{.interpreted-text role="doc"}\" chapter in the Yocto Project Reference Manual.

> 有关 Yocto 项目提供的示例图像列表，请参阅《Yocto Project Reference manual》中的“`/ref manual/images`{.depreted text role=“doc”}\”一章。
> :::

The build process writes images out to the `Build Directory`{.interpreted-text role="term"} inside the `tmp/deploy/images/machine/` folder as shown in the figure. This folder contains any files expected to be loaded on the target device. The `DEPLOY_DIR`{.interpreted-text role="term"} variable points to the `deploy` directory, while the `DEPLOY_DIR_IMAGE`{.interpreted-text role="term"} variable points to the appropriate directory containing images for the current configuration.

> 构建过程将图像写入“tmp/deploy/images/machine/”文件夹内的“构建目录”｛.depredicted text role=“term”｝中，如图所示。该文件夹包含预期加载到目标设备上的任何文件。`DEPLOY_DIR`｛.expected text role=“term”｝变量指向 `DEPLOY` 目录，而 `DEPLOY_DIR_IMAGE`｛.dexpected textrole=”term“｝变量则指向包含当前配置图像的适当目录。

- kernel-image: A kernel binary file. The `KERNEL_IMAGETYPE`{.interpreted-text role="term"} variable determines the naming scheme for the kernel image file. Depending on this variable, the file could begin with a variety of naming strings. The `deploy/images/` machine directory can contain multiple image files for the machine.

> -内核映像：一个内核二进制文件。`KERNEL_IMAGETYPE`｛.explored text role=“term”｝变量确定内核映像文件的命名方案。根据这个变量，文件可以以各种命名字符串开头。“deploy/images/”机器目录可以包含该机器的多个映像文件。

- root-filesystem-image: Root filesystems for the target device (e.g. `*.ext3` or `*.bz2` files). The `IMAGE_FSTYPES`{.interpreted-text role="term"} variable determines the root filesystem image type. The `deploy/images/` machine directory can contain multiple root filesystems for the machine.

> -根文件系统映像：目标设备的根文件系统（例如“*.ext3”或“*.bz2”文件）。`IMAGE_FSTTYPE`｛.explored text role=“term”｝变量确定根文件系统映像类型。“deploy/images/”机器目录可以包含该机器的多个根文件系统。

- kernel-modules: Tarballs that contain all the modules built for the kernel. Kernel module tarballs exist for legacy purposes and can be suppressed by setting the `MODULE_TARBALL_DEPLOY`{.interpreted-text role="term"} variable to \"0\". The `deploy/images/` machine directory can contain multiple kernel module tarballs for the machine.

> -内核模块：包含为内核构建的所有模块的 Tarball。内核模块 TARBALL 是为遗留目的而存在的，可以通过将 `module_TARBALL_DEPLOY`｛.depredicted text role=“term”｝变量设置为\“0\”来抑制它。“deploy/images/”机器目录可以包含该机器的多个内核模块 tarball。

- bootloaders: If applicable to the target machine, bootloaders supporting the image. The `deploy/images/` machine directory can contain multiple bootloaders for the machine.

> -bootloaders：如果适用于目标机器，则为支持映像的 bootloaders。“deploy/images/”机器目录可以包含该机器的多个引导加载程序。

- symlinks: The `deploy/images/` machine folder contains a symbolic link that points to the most recently built file for each machine. These links might be useful for external scripts that need to obtain the latest version of each file.

> -symlinks:`deploy/images/` 机器文件夹包含一个符号链接，指向每台机器最近生成的文件。这些链接对于需要获取每个文件的最新版本的外部脚本可能很有用。

## Application Development SDK

In the `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"}, the output labeled \"Application Development SDK\" represents an SDK. The SDK generation process differs depending on whether you build an extensible SDK (e.g. `bitbake -c populate_sdk_ext` imagename) or a standard SDK (e.g. `bitbake -c populate_sdk` imagename). This section takes a closer look at this output:

> 在 `general workflow figure＜overview manual/concepts:openembedded build system concepts>`{.depreted text role=“ref”}中，标记为“应用程序开发 SDK”的输出表示 SDK。SDK 生成过程的不同取决于您是构建可扩展的 SDK（例如“bitbake-c populate_SDK_ext”imagename）还是构建标准 SDK（例如，“bitbake-c populate_SDK”image name）。本节将详细介绍此输出：

![image](figures/sdk.png){width="100.0%"}

The specific form of this output is a set of files that includes a self-extracting SDK installer (`*.sh`), host and target manifest files, and files used for SDK testing. When the SDK installer file is run, it installs the SDK. The SDK consists of a cross-development toolchain, a set of libraries and headers, and an SDK environment setup script. Running this installer essentially sets up your cross-development environment. You can think of the cross-toolchain as the \"host\" part because it runs on the SDK machine. You can think of the libraries and headers as the \"target\" part because they are built for the target hardware. The environment setup script is added so that you can initialize the environment before using the tools.

> 此输出的具体形式是一组文件，其中包括自解压 SDK 安装程序（`*.sh`）、主机和目标清单文件以及用于 SDK 测试的文件。运行 SDK 安装程序文件时，它将安装 SDK。SDK 由一个跨开发工具链、一组库和头文件以及一个 SDK 环境设置脚本组成。运行这个安装程序基本上设置了交叉开发环境。您可以将交叉工具链视为“主机”部分，因为它运行在 SDK 机器上。您可以将库和标头视为“目标”部分，因为它们是为目标硬件构建的。添加了环境设置脚本，以便您可以在使用工具之前初始化环境。

::: note
::: title
Note
:::

- The Yocto Project supports several methods by which you can set up this cross-development environment. These methods include downloading pre-built SDK installers or building and installing your own SDK installer.

> -Yocto 项目支持几种方法，您可以通过这些方法来设置这种跨开发环境。这些方法包括下载预构建的 SDK 安装程序或构建并安装您自己的 SDK 安装器。

- For background information on cross-development toolchains in the Yocto Project development environment, see the \"`overview-manual/concepts:cross-development toolchain generation`{.interpreted-text role="ref"}\" section.

> -有关 Yocto 项目开发环境中跨开发工具链的背景信息，请参阅“概述手册/概念：跨开发工具链条生成”一节。

- For information on setting up a cross-development environment, see the `/sdk-manual/index`{.interpreted-text role="doc"} manual.

> -有关设置交叉开发环境的信息，请参阅 `/sdk manual/index`{.depreted text role=“doc”}手册。

:::

> ：：：

All the output files for an SDK are written to the `deploy/sdk` folder inside the `Build Directory`{.interpreted-text role="term"} as shown in the previous figure. Depending on the type of SDK, there are several variables to configure these files. Here are the variables associated with an extensible SDK:

> 如上图所示，SDK 的所有输出文件都写入“构建目录”内的“deploy/SDK”文件夹。根据 SDK 的类型，有几个变量可以配置这些文件。以下是与可扩展 SDK 相关联的变量：

- `DEPLOY_DIR`{.interpreted-text role="term"}: Points to the `deploy` directory.
- `SDK_EXT_TYPE`{.interpreted-text role="term"}: Controls whether or not shared state artifacts are copied into the extensible SDK. By default, all required shared state artifacts are copied into the SDK.

> -`SDK_EXT_TYPE`｛.explored text role=“term”｝：控制是否将共享状态工件复制到可扩展 SDK 中。默认情况下，所有必需的共享状态工件都会复制到 SDK 中。

- `SDK_INCLUDE_PKGDATA`{.interpreted-text role="term"}: Specifies whether or not packagedata is included in the extensible SDK for all recipes in the \"world\" target.

> -`SDK_INCLUDE_PKGDATA`｛.explored text role=“term”｝：指定是否在“world\”目标中的所有配方的可扩展 SDK 中包含 packagedata。

- `SDK_INCLUDE_TOOLCHAIN`{.interpreted-text role="term"}: Specifies whether or not the toolchain is included when building the extensible SDK.

> -`SDK_INCLUDE_TOOLCHAIN`｛.explored text role=“term”｝：指定在构建可扩展 SDK 时是否包括工具链。

- `ESDK_LOCALCONF_ALLOW`{.interpreted-text role="term"}: A list of variables allowed through from the build system configuration into the extensible SDK configuration.

> -`ESDK_LOCALCONF_ALLOW`｛.explored text role=“term”｝：允许从生成系统配置进入可扩展 SDK 配置的变量列表。

- `ESDK_LOCALCONF_REMOVE`{.interpreted-text role="term"}: A list of variables not allowed through from the build system configuration into the extensible SDK configuration.

> -`ESDK_LOCALCONF_REMOVE`｛.explored text role=“term”｝：不允许从生成系统配置进入可扩展 SDK 配置的变量列表。

- `ESDK_CLASS_INHERIT_DISABLE`{.interpreted-text role="term"}: A list of classes to remove from the `INHERIT`{.interpreted-text role="term"} value globally within the extensible SDK configuration.

> -`ESDK_CLASS_INHERIT_DISABLE`｛.respered text role=“term”｝：可扩展 SDK 配置中要从 `INHERIT`｛.espered text role=“term“｝值全局删除的类列表。

This next list, shows the variables associated with a standard SDK:

> 下一个列表显示了与标准 SDK 相关联的变量：

- `DEPLOY_DIR`{.interpreted-text role="term"}: Points to the `deploy` directory.
- `SDKMACHINE`{.interpreted-text role="term"}: Specifies the architecture of the machine on which the cross-development tools are run to create packages for the target hardware.

> -`SDKMACHINE`｛.explored text role=“term”｝：指定运行交叉开发工具为目标硬件创建包的机器的体系结构。

- `SDKIMAGE_FEATURES`{.interpreted-text role="term"}: Lists the features to include in the \"target\" part of the SDK.

> -`SDKIMAGE_FEATURE`｛.explored text role=“term”｝：列出要包含在 SDK 的“target”部分中的功能。

- `TOOLCHAIN_HOST_TASK`{.interpreted-text role="term"}: Lists packages that make up the host part of the SDK (i.e. the part that runs on the `SDKMACHINE`{.interpreted-text role="term"}). When you use `bitbake -c populate_sdk imagename` to create the SDK, a set of default packages apply. This variable allows you to add more packages.

> -`TOOLCHAIN_HOST_TAK`｛.explored text role=“term”｝：列出组成 SDK 主机部分的包（即在 `SDKMACHINE` 上运行的部分）｛。当您使用“bitbake-c populate_sdk imagename”创建 sdk 时，将应用一组默认包。此变量允许您添加更多的包。

- `TOOLCHAIN_TARGET_TASK`{.interpreted-text role="term"}: Lists packages that make up the target part of the SDK (i.e. the part built for the target hardware).

> -`TOOLCHAIN_TARGET_TASK`｛.explored text role=“term”｝：列出组成 SDK 目标部分（即为目标硬件构建的部分）的包。

- `SDKPATH`{.interpreted-text role="term"}: Defines the default SDK installation path offered by the installation script.

> -`SDKPATH`｛.explored text role=“term”｝：定义安装脚本提供的默认 SDK 安装路径。

- `SDK_HOST_MANIFEST`{.interpreted-text role="term"}: Lists all the installed packages that make up the host part of the SDK. This variable also plays a minor role for extensible SDK development as well. However, it is mainly used for the standard SDK.

> -`SDK_HOST_MANIFEST`｛.explored text role=“term”｝：列出构成 SDK 主机部分的所有已安装包。这个变量在可扩展 SDK 开发中也起着次要作用。但是，它主要用于标准 SDK。

- `SDK_TARGET_MANIFEST`{.interpreted-text role="term"}: Lists all the installed packages that make up the target part of the SDK. This variable also plays a minor role for extensible SDK development as well. However, it is mainly used for the standard SDK.

> -`SDK_TARGET_MANIFEST`｛.explored text role=“term”｝：列出构成 SDK 目标部分的所有已安装包。这个变量在可扩展 SDK 开发中也起着次要作用。但是，它主要用于标准 SDK。

# Cross-Development Toolchain Generation

The Yocto Project does most of the work for you when it comes to creating `sdk-manual/intro:the cross-development toolchain`{.interpreted-text role="ref"}. This section provides some technical background on how cross-development toolchains are created and used. For more information on toolchains, you can also see the `/sdk-manual/index`{.interpreted-text role="doc"} manual.

> Yocto 项目在创建“sdk manual/intro:交叉开发工具链”时为您完成了大部分工作。本节提供了一些关于如何创建和使用跨开发工具链的技术背景。有关工具链的更多信息，您还可以参阅 `/sdk manual/index`｛.depreted text role=“doc”｝手册。

In the Yocto Project development environment, cross-development toolchains are used to build images and applications that run on the target hardware. With just a few commands, the OpenEmbedded build system creates these necessary toolchains for you.

> 在 Yocto 项目开发环境中，跨开发工具链用于构建在目标硬件上运行的映像和应用程序。只需几个命令，OpenEmbedded 构建系统就可以为您创建这些必要的工具链。

The following figure shows a high-level build environment regarding toolchain construction and use.

> 下图显示了一个关于工具链构建和使用的高级构建环境。

![image](figures/cross-development-toolchains.png){width="100.0%"}

Most of the work occurs on the Build Host. This is the machine used to build images and generally work within the Yocto Project environment. When you run `BitBake`{.interpreted-text role="term"} to create an image, the OpenEmbedded build system uses the host `gcc` compiler to bootstrap a cross-compiler named `gcc-cross`. The `gcc-cross` compiler is what BitBake uses to compile source files when creating the target image. You can think of `gcc-cross` simply as an automatically generated cross-compiler that is used internally within BitBake only.

> 大部分工作都发生在生成主机上。这是一台用于构建图像的机器，通常在 Yocto 项目环境中工作。当您运行 `BitBake`｛.explored text role=“term”｝来创建图像时，OpenEmbedded 构建系统会使用主机 `gcc` 编译器来引导名为 `gcc-cross` 的交叉编译器。“gcc cross”编译器是 BitBake 在创建目标图像时用来编译源文件的编译器。您可以简单地将“gcc-cross”看作是一个仅在 BitBake 内部使用的自动生成的交叉编译器。

::: note
::: title
Note
:::

The extensible SDK does not use `gcc-cross-canadian` since this SDK ships a copy of the OpenEmbedded build system and the sysroot within it contains `gcc-cross`.

> 可扩展 SDK 不使用“gcc-cross-canadian”，因为此 SDK 提供了 OpenEmbedded 构建系统的副本，其中的 sysroot 包含“gcc-cross”。
> :::

The chain of events that occurs when the standard toolchain is bootstrapped:

> 启动标准工具链时发生的事件链：

```
binutils-cross -> linux-libc-headers -> gcc-cross -> libgcc-initial -> glibc -> libgcc -> gcc-runtime
```

- `gcc`: The compiler, GNU Compiler Collection (GCC).
- `binutils-cross`: The binary utilities needed in order to run the `gcc-cross` phase of the bootstrap operation and build the headers for the C library.

> -“binutils cross”：运行引导操作的“gcc cross”阶段并为 C 库构建标头所需的二进制实用程序。

- `linux-libc-headers`: Headers needed for the cross-compiler and C library build.
- `libgcc-initial`: An initial version of the gcc support library needed to bootstrap `glibc`.
- `libgcc`: The final version of the gcc support library which can only be built once there is a C library to link against.

> -“libgcc”：gcc 支持库的最终版本，只有在有 C 库链接时才能构建。

- `glibc`: The GNU C Library.
- `gcc-cross`: The final stage of the bootstrap process for the cross-compiler. This stage results in the actual cross-compiler that BitBake uses when it builds an image for a targeted device.

> -“gcc cross”：交叉编译器引导过程的最后阶段。这个阶段产生了 BitBake 在为目标设备构建图像时使用的实际交叉编译器。

This tool is a \"native\" tool (i.e. it is designed to run on the build host).

> 此工具是“本机”工具（即设计用于在生成主机上运行）。

- `gcc-runtime`: Runtime libraries resulting from the toolchain bootstrapping process. This tool produces a binary that consists of the runtime libraries need for the targeted device.

> -`gcc runtime`：由工具链引导过程产生的运行库。此工具生成一个二进制文件，其中包含目标设备所需的运行库。

You can use the OpenEmbedded build system to build an installer for the relocatable SDK used to develop applications. When you run the installer, it installs the toolchain, which contains the development tools (e.g., `gcc-cross-canadian`, `binutils-cross-canadian`, and other `nativesdk-*` tools), which are tools native to the SDK (i.e. native to `SDK_ARCH`{.interpreted-text role="term"}), you need to cross-compile and test your software. The figure shows the commands you use to easily build out this toolchain. This cross-development toolchain is built to execute on the `SDKMACHINE`{.interpreted-text role="term"}, which might or might not be the same machine as the Build Host.

> 您可以使用 OpenEmbedded 构建系统为用于开发应用程序的可重定位 SDK 构建安装程序。当您运行安装程序时，它会安装工具链，其中包含开发工具（例如，`gcc-cross-canadian`、`binutils-cross-canadian'和其他` nativesdk-*`工具），这些工具是SDK的本地工具（即，` SDK_ARCH `的本地工具），您需要交叉编译和测试您的软件。该图显示了用于轻松构建此工具链的命令。这个跨开发工具链是为在` SDKMACHINE`｛.depreted text role=“term”｝上执行而构建的，该机器可能与构建主机是同一台机器，也可能不是同一台计算机。

::: note
::: title
Note
:::

If your target architecture is supported by the Yocto Project, you can take advantage of pre-built images that ship with the Yocto Project and already contain cross-development toolchain installers.

> 如果 Yocto 项目支持您的目标体系结构，您可以利用 Yocto Project 附带的预构建映像，这些映像已经包含跨开发工具链安装程序。
> :::

Here is the bootstrap process for the relocatable toolchain:

> 以下是可重定位工具链的引导过程：

```
gcc -> binutils-crosssdk -> gcc-crosssdk-initial -> linux-libc-headers -> glibc-initial -> nativesdk-glibc -> gcc-crosssdk -> gcc-cross-canadian
```

- `gcc`: The build host\'s GNU Compiler Collection (GCC).
- `binutils-crosssdk`: The bare minimum binary utilities needed in order to run the `gcc-crosssdk-initial` phase of the bootstrap operation.

> -“binutils crossdk”：运行引导操作的“gcc crossdk initial”阶段所需的最低限度的二进制实用程序。

- `gcc-crosssdk-initial`: An early stage of the bootstrap process for creating the cross-compiler. This stage builds enough of the `gcc-crosssdk` and supporting pieces so that the final stage of the bootstrap process can produce the finished cross-compiler. This tool is a \"native\" binary that runs on the build host.

> -`gcc-crosssdkinitial`：创建交叉编译器的引导过程的早期阶段。这个阶段构建了足够多的“gcc-crossdk”和支持部件，以便引导过程的最后阶段可以生成完成的交叉编译器。此工具是在生成主机上运行的“本机”二进制文件。

- `linux-libc-headers`: Headers needed for the cross-compiler.
- `glibc-initial`: An initial version of the Embedded GLIBC needed to bootstrap `nativesdk-glibc`.
- `nativesdk-glibc`: The Embedded GLIBC needed to bootstrap the `gcc-crosssdk`.
- `gcc-crosssdk`: The final stage of the bootstrap process for the relocatable cross-compiler. The `gcc-crosssdk` is a transitory compiler and never leaves the build host. Its purpose is to help in the bootstrap process to create the eventual `gcc-cross-canadian` compiler, which is relocatable. This tool is also a \"native\" package (i.e. it is designed to run on the build host).

> -`gcc-crosssdk`：可重定位交叉编译器引导过程的最后阶段。“gcc-crosssdk”是一个临时编译器，从不离开构建主机。它的目的是在引导过程中帮助创建最终的“gcc 跨加拿大”编译器，该编译器是可重定位的。该工具也是一个“本机”软件包（即设计用于在生成主机上运行）。

- `gcc-cross-canadian`: The final relocatable cross-compiler. When run on the `SDKMACHINE`{.interpreted-text role="term"}, this tool produces executable code that runs on the target device. Only one cross-canadian compiler is produced per architecture since they can be targeted at different processor optimizations using configurations passed to the compiler through the compile commands. This circumvents the need for multiple compilers and thus reduces the size of the toolchains.

> -`gcc-cross-canadian`：最后一个可重定位的交叉编译器。当在 `SDKMACHINE`｛.explored text role=“term”｝上运行时，此工具会生成在目标设备上运行的可执行代码。每个体系结构只生成一个跨加拿大编译器，因为它们可以使用通过编译命令传递给编译器的配置针对不同的处理器优化。这就避免了对多个编译器的需要，从而减少了工具链的大小。

::: note
::: title
Note
:::

For information on advantages gained when building a cross-development toolchain installer, see the \"`sdk-manual/appendix-obtain:building an sdk installer`{.interpreted-text role="ref"}\" appendix in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.

> 有关构建跨开发工具链安装程序所获得优势的信息，请参阅 Yocto 项目应用程序开发和可扩展软件开发工具包（eSDK）手册中的\“`sdk manual/appendment get:building an sdk installer`｛.explored text role=“ref”｝\”附录。
> :::

# Shared State Cache

By design, the OpenEmbedded build system builds everything from scratch unless `BitBake`{.interpreted-text role="term"} can determine that parts do not need to be rebuilt. Fundamentally, building from scratch is attractive as it means all parts are built fresh and there is no possibility of stale data that can cause problems. When developers hit problems, they typically default back to building from scratch so they have a known state from the start.

> 根据设计，OpenEmbedded 构建系统从头开始构建所有内容，除非“BitBake”｛.explored text role=“term”｝可以确定不需要重建部件。从根本上说，从头开始构建很有吸引力，因为这意味着所有部件都是全新构建的，不存在可能导致问题的陈旧数据。当开发人员遇到问题时，他们通常会默认从头开始构建，这样他们从一开始就有一个已知的状态。

Building an image from scratch is both an advantage and a disadvantage to the process. As mentioned in the previous paragraph, building from scratch ensures that everything is current and starts from a known state. However, building from scratch also takes much longer as it generally means rebuilding things that do not necessarily need to be rebuilt.

> 从头开始构建图像对这个过程来说既是优点也是缺点。如前一段所述，从头开始构建确保一切都是最新的，并从已知状态开始。然而，从头开始建造也需要更长的时间，因为这通常意味着重建不一定需要重建的东西。

The Yocto Project implements shared state code that supports incremental builds. The implementation of the shared state code answers the following questions that were fundamental roadblocks within the OpenEmbedded incremental build support system:

> Yocto 项目实现了支持增量构建的共享状态代码。共享状态代码的实现回答了以下问题，这些问题是 OpenEmbedded 增量构建支持系统中的基本障碍：

- What pieces of the system have changed and what pieces have not changed?
- How are changed pieces of software removed and replaced?
- How are pre-built components that do not need to be rebuilt from scratch used when they are available?

> -不需要从头开始重建的预构建组件在可用时如何使用？

For the first question, the build system detects changes in the \"inputs\" to a given task by creating a checksum (or signature) of the task\'s inputs. If the checksum changes, the system assumes the inputs have changed and the task needs to be rerun. For the second question, the shared state (sstate) code tracks which tasks add which output to the build process. This means the output from a given task can be removed, upgraded or otherwise manipulated. The third question is partly addressed by the solution for the second question assuming the build system can fetch the sstate objects from remote locations and install them if they are deemed to be valid.

> 对于第一个问题，构建系统通过创建任务输入的校验和（或签名）来检测给定任务的“输入”中的更改。如果校验和发生变化，则系统假定输入已发生变化，需要重新运行任务。对于第二个问题，共享状态（sstate）代码跟踪哪些任务将哪些输出添加到构建过程中。这意味着可以删除、升级或以其他方式操纵给定任务的输出。第三个问题部分由第二个问题的解决方案解决，假设构建系统可以从远程位置获取 sstate 对象，并在它们被认为有效的情况下安装它们。

::: note
::: title
Note
:::

- The build system does not maintain `PR`{.interpreted-text role="term"} information as part of the shared state packages. Consequently, there are considerations that affect maintaining shared state feeds. For information on how the build system works with packages and can track incrementing `PR`{.interpreted-text role="term"} information, see the \"`dev-manual/packages:automatically incrementing a package version number`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> -生成系统不将 `PR`｛.explored text role=“term”｝信息作为共享状态包的一部分进行维护。因此，存在影响维护共享状态提要的注意事项。有关构建系统如何使用包以及如何跟踪递增的 `PR`｛.explored text role=“term”｝信息的信息，请参阅 Yocto 项目开发任务手册中的\“`dev manual/packages:automatically incremental a package version number`｛..explored textrole=”ref“｝\”一节。

- The code in the build system that supports incremental builds is complex. For techniques that help you work around issues related to shared state code, see the \"`dev-manual/debugging:viewing metadata used to create the input signature of a shared state task`{.interpreted-text role="ref"}\" and \"`dev-manual/debugging:invalidating shared state to force a task to run`{.interpreted-text role="ref"}\" sections both in the Yocto Project Development Tasks Manual.

> -构建系统中支持增量构建的代码非常复杂。有关帮助您解决与共享状态代码相关问题的技术，请参阅 Yocto 项目开发任务手册中的\“`dev manual/debugging:查看用于创建共享状态任务的输入签名的元数据`｛.depreted text role=“ref”｝\”和\“`dev manual/debugging:使共享状态无效以强制任务运行`｛.repreted text role=“ref”}\”部分。

:::

> ：：：

The rest of this section goes into detail about the overall incremental build architecture, the checksums (signatures), and shared state.

> 本节的其余部分将详细介绍整个增量构建体系结构、校验和（签名）和共享状态。

## Overall Architecture

When determining what parts of the system need to be built, BitBake works on a per-task basis rather than a per-recipe basis. You might wonder why using a per-task basis is preferred over a per-recipe basis. To help explain, consider having the IPK packaging backend enabled and then switching to DEB. In this case, the `ref-tasks-install`{.interpreted-text role="ref"} and `ref-tasks-package`{.interpreted-text role="ref"} task outputs are still valid. However, with a per-recipe approach, the build would not include the `.deb` files. Consequently, you would have to invalidate the whole build and rerun it. Rerunning everything is not the best solution. Also, in this case, the core must be \"taught\" much about specific tasks. This methodology does not scale well and does not allow users to easily add new tasks in layers or as external recipes without touching the packaged-staging core.

> 在确定需要构建系统的哪些部分时，BitBake 是基于每个任务而不是基于每个配方来工作的。你可能会想，为什么使用按任务的基础比使用按配方的基础更可取。为了帮助解释，可以考虑启用 IPK 打包后端，然后切换到 DEB。在这种情况下，`ref tasks install`{.depreced text role=“ref”}和 `ref task package`{.depreded text rol=“ref”｝任务输出仍然有效。但是，使用按配方的方法，构建将不包括“.deb”文件。因此，您将不得不使整个生成无效并重新运行它。重新运行所有内容并不是最好的解决方案。此外，在这种情况下，核心必须“教会”很多关于特定任务的知识。这种方法不能很好地扩展，并且不允许用户在不接触打包的暂存核心的情况下轻松地在层中或作为外部配方添加新任务。

## Checksums (Signatures)

The shared state code uses a checksum, which is a unique signature of a task\'s inputs, to determine if a task needs to be run again. Because it is a change in a task\'s inputs that triggers a rerun, the process needs to detect all the inputs to a given task. For shell tasks, this turns out to be fairly easy because the build process generates a \"run\" shell script for each task and it is possible to create a checksum that gives you a good idea of when the task\'s data changes.

> 共享状态代码使用校验和来确定是否需要再次运行任务，校验和是任务输入的唯一签名。因为是任务输入的变化触发了重新运行，所以进程需要检测到给定任务的所有输入。对于 shell 任务，这相当容易，因为构建过程会为每个任务生成一个“运行”shell 脚本，并且可以创建一个校验和，让您很好地了解任务的数据何时更改。

To complicate the problem, there are things that should not be included in the checksum. First, there is the actual specific build path of a given task \-\-- the `WORKDIR`{.interpreted-text role="term"}. It does not matter if the work directory changes because it should not affect the output for target packages. Also, the build process has the objective of making native or cross packages relocatable.

> 使问题复杂化的是，有些东西不应该包含在校验和中。首先，有一个给定任务的实际特定构建路径\-`WORKDIR`｛.explored text role=“term”｝。工作目录是否更改并不重要，因为它不应该影响目标包的输出。此外，构建过程的目标是使本机或跨包可重新定位。

::: note
::: title
Note
:::

Both native and cross packages run on the build host. However, cross packages generate output for the target architecture.

> 本机包和交叉包都在生成主机上运行。但是，交叉包为目标体系结构生成输出。
> :::

The checksum therefore needs to exclude `WORKDIR`{.interpreted-text role="term"}. The simplistic approach for excluding the work directory is to set `WORKDIR`{.interpreted-text role="term"} to some fixed value and create the checksum for the \"run\" script.

> 因此，校验和需要排除 `WORKDIR`｛.explored text role=“term”｝。排除工作目录的简单方法是将 `WORKDIR`｛.explored text role=“term”｝设置为某个固定值，并为“run”脚本创建校验和。

Another problem results from the \"run\" scripts containing functions that might or might not get called. The incremental build solution contains code that figures out dependencies between shell functions. This code is used to prune the \"run\" scripts down to the minimum set, thereby alleviating this problem and making the \"run\" scripts much more readable as a bonus.

> 另一个问题是“运行”脚本包含可能被调用或可能未被调用的函数。增量构建解决方案包含计算 shell 函数之间依赖关系的代码。这段代码用于将“运行”脚本精简到最小集，从而缓解了这个问题，并使“运行”剧本更具可读性。

So far, there are solutions for shell scripts. What about Python tasks? The same approach applies even though these tasks are more difficult. The process needs to figure out what variables a Python function accesses and what functions it calls. Again, the incremental build solution contains code that first figures out the variable and function dependencies, and then creates a checksum for the data used as the input to the task.

> 到目前为止，已有针对 shell 脚本的解决方案。Python 任务呢？即使这些任务更加困难，同样的方法也适用。这个过程需要弄清楚 Python 函数访问哪些变量以及调用哪些函数。同样，增量构建解决方案包含的代码首先计算变量和函数的依赖关系，然后为用作任务输入的数据创建校验和。

Like the `WORKDIR`{.interpreted-text role="term"} case, there can be situations where dependencies should be ignored. For these situations, you can instruct the build process to ignore a dependency by using a line like the following:

> 与 `WORKDIR`｛.explored text role=“term”｝的情况一样，也可能存在应忽略依赖关系的情况。对于这些情况，您可以使用以下行指示构建过程忽略依赖项：

```
PACKAGE_ARCHS[vardepsexclude] = "MACHINE"
```

This example ensures that the `PACKAGE_ARCHS`{.interpreted-text role="term"} variable does not depend on the value of `MACHINE`{.interpreted-text role="term"}, even if it does reference it.

> 此示例确保 `PACKAGE_ARCHS`｛.depredicted text role=“term”｝变量不依赖于 `MACHINE`｛.epredicted textrole=”term“｝的值，即使它确实引用了它。

Equally, there are cases where you need to add dependencies BitBake is not able to find. You can accomplish this by using a line like the following:

> 同样，在某些情况下，您需要添加 BitBake 找不到的依赖项。您可以使用如下所示的行来完成此操作：

```
PACKAGE_ARCHS[vardeps] = "MACHINE"
```

This example explicitly adds the `MACHINE`{.interpreted-text role="term"} variable as a dependency for `PACKAGE_ARCHS`{.interpreted-text role="term"}.

> 此示例显式添加了 `MACHINE`｛.explored text role=“term”｝变量作为 `PACKAGE_ARCHS`｛..explored text-role=“term“｝的依赖项。

As an example, consider a case with in-line Python where BitBake is not able to figure out dependencies. When running in debug mode (i.e. using `-DDD`), BitBake produces output when it discovers something for which it cannot figure out dependencies. The Yocto Project team has currently not managed to cover those dependencies in detail and is aware of the need to fix this situation.

> 例如，考虑一个内嵌 Python 的情况，其中 BitBake 无法计算依赖项。在调试模式下运行时（即使用“-DDD”），BitBake 在发现无法确定依赖关系的内容时会产生输出。Yocto 项目团队目前尚未详细介绍这些依赖关系，并意识到有必要解决这种情况。

Thus far, this section has limited discussion to the direct inputs into a task. Information based on direct inputs is referred to as the \"basehash\" in the code. However, the question of a task\'s indirect inputs still exits \-\-- items already built and present in the `Build Directory`{.interpreted-text role="term"}. The checksum (or signature) for a particular task needs to add the hashes of all the tasks on which the particular task depends. Choosing which dependencies to add is a policy decision. However, the effect is to generate a checksum that combines the basehash and the hashes of the task\'s dependencies.

> 到目前为止，本节的讨论仅限于对任务的直接投入。基于直接输入的信息在代码中被称为“basehash”。然而，任务的间接输入问题仍然存在\-已经构建并存在于“构建目录”中的项目｛.explored text role=“term”｝。特定任务的校验和（或签名）需要添加特定任务所依赖的所有任务的哈希。选择要添加的依赖项是一个策略决定。然而，其效果是生成一个校验和，该校验和结合了基本哈希和任务依赖项的哈希。

At the code level, there are multiple ways by which both the basehash and the dependent task hashes can be influenced. Within the BitBake configuration file, you can give BitBake some extra information to help it construct the basehash. The following statement effectively results in a list of global variable dependency excludes (i.e. variables never included in any checksum):

> 在代码级别，有多种方式可以影响基本哈希和依赖任务哈希。在 BitBake 配置文件中，您可以为 BitBake 提供一些额外的信息，以帮助它构建 basehash。以下语句有效地生成了全局变量依赖项排除列表（即任何校验和中从未包含的变量）：

```
BB_BASEHASH_IGNORE_VARS ?= "TMPDIR FILE PATH PWD BB_TASKHASH BBPATH DL_DIR \\
    SSTATE_DIR THISDIR FILESEXTRAPATHS FILE_DIRNAME HOME LOGNAME SHELL TERM \\
    USER FILESPATH STAGING_DIR_HOST STAGING_DIR_TARGET COREBASE PRSERV_HOST \\
    PRSERV_DUMPDIR PRSERV_DUMPFILE PRSERV_LOCKDOWN PARALLEL_MAKE \\
    CCACHE_DIR EXTERNAL_TOOLCHAIN CCACHE CCACHE_DISABLE LICENSE_PATH SDKPKGSUFFIX"
```

The previous example does not include `WORKDIR`{.interpreted-text role="term"} since that variable is actually constructed as a path within `TMPDIR`{.interpreted-text role="term"}, which is included above.

> 上一个示例不包括 `WORKDIR`｛.explored text role=“term”｝，因为该变量实际上被构造为 `TMPDIR`｛..explored text role=”term“｝内的路径，该路径包含在上面。

The rules for deciding which hashes of dependent tasks to include through dependency chains are more complex and are generally accomplished with a Python function. The code in `meta/lib/oe/sstatesig.py` shows two examples of this and also illustrates how you can insert your own policy into the system if so desired. This file defines the two basic signature generators `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"} uses: \"OEBasic\" and \"OEBasicHash\". By default, a dummy \"noop\" signature handler is enabled in BitBake. This means that behavior is unchanged from previous versions. OE-Core uses the \"OEBasicHash\" signature handler by default through this setting in the `bitbake.conf` file:

> 决定通过依赖链包含哪些依赖任务哈希的规则更为复杂，通常使用 Python 函数来完成。“meta/lib/oe/statesig.py”中的代码显示了这方面的两个示例，并说明了如果需要，如何将自己的策略插入到系统中。该文件定义了两个基本签名生成器 `OpenEmbedded Core（OE Core）`｛.explored text role=“term”｝使用：\“OEBasic\”和\“OEBasecHash\”。默认情况下，BitBake 中会启用一个伪“noop”签名处理程序。这意味着行为与以前的版本相比没有变化。OE Core 通过“bitbake.conf”文件中的此设置默认使用“OEBasicHash”签名处理程序：

```
BB_SIGNATURE_HANDLER ?= "OEBasicHash"
```

The \"OEBasicHash\" `BB_SIGNATURE_HANDLER`{.interpreted-text role="term"} is the same as the \"OEBasic\" version but adds the task hash to the `stamp files <overview-manual/concepts:stamp files and the rerunning of tasks>`{.interpreted-text role="ref"}. This results in any metadata change that changes the task hash, automatically causing the task to be run again. This removes the need to bump `PR`{.interpreted-text role="term"} values, and changes to metadata automatically ripple across the build.

> \“OEBasicHash\”`BB_SIGNATURE_HANDLER`｛.explored text role=“term”｝与\“OEBasic”版本相同，但将任务哈希添加到 `stamp files<概述手册/概念：stamp files and The rerunning of tasks>`{.explered text rol=“ref”}中。这会导致更改任务哈希的任何元数据，从而自动导致任务再次运行。这消除了碰撞 `PR`｛.explored text role=“term”｝值的需要，并且对元数据的更改会自动波及整个构建。

It is also worth noting that the end result of these signature generators is to make some dependency and hash information available to the build. This information includes:

> 同样值得注意的是，这些签名生成器的最终结果是使一些依赖项和哈希信息可用于构建。这些信息包括：

- `BB_BASEHASH:task-` taskname: The base hashes for each task in the recipe.
- `BB_BASEHASH_` filename `:` taskname: The base hashes for each dependent task.
- `BB_TASKHASH`{.interpreted-text role="term"}: The hash of the currently running task.

## Shared State

Checksums and dependencies, as discussed in the previous section, solve half the problem of supporting a shared state. The other half of the problem is being able to use checksum information during the build and being able to reuse or rebuild specific components.

> 如前一节所述，校验和和和依赖关系解决了支持共享状态的一半问题。问题的另一半是能够在构建过程中使用校验和信息，以及能够重用或重建特定组件。

The `ref-classes-sstate`{.interpreted-text role="ref"} class is a relatively generic implementation of how to \"capture\" a snapshot of a given task. The idea is that the build process does not care about the source of a task\'s output. Output could be freshly built or it could be downloaded and unpacked from somewhere. In other words, the build process does not need to worry about its origin.

> `ref classes sstate`｛.explored text role=“ref”｝类是如何“捕获”给定任务的快照的一个相对通用的实现。其思想是，构建过程并不关心任务输出的来源。输出可以是新构建的，也可以从某个地方下载和解压缩。换句话说，构建过程不需要担心其来源。

Two types of output exist. One type is just about creating a directory in `WORKDIR`{.interpreted-text role="term"}. A good example is the output of either `ref-tasks-install`{.interpreted-text role="ref"} or `ref-tasks-package`{.interpreted-text role="ref"}. The other type of output occurs when a set of data is merged into a shared directory tree such as the sysroot.

> 存在两种类型的输出。一种类型只是在 `WORKDIR` 中创建一个目录{.expreted text role=“term”}。一个很好的例子是“ref tasks install”｛.depreted text role=“ref”｝或“ref task package”｛.repreted text role=“ref”}的输出。另一种类型的输出发生在将一组数据合并到共享目录树（如 sysroot）中时。

The Yocto Project team has tried to keep the details of the implementation hidden in the `ref-classes-sstate`{.interpreted-text role="ref"} class. From a user\'s perspective, adding shared state wrapping to a task is as simple as this `ref-tasks-deploy`{.interpreted-text role="ref"} example taken from the `ref-classes-deploy`{.interpreted-text role="ref"} class:

> Yocto 项目团队试图将实现的细节隐藏在 `ref classes sstate`｛.depreted text role=“ref”｝类中。从用户的角度来看，将共享状态包装添加到任务中就像以下 `ref tasks deploy`｛.depreted text role=“ref”｝类中的示例一样简单：

```
DEPLOYDIR = "${WORKDIR}/deploy-${PN}"
SSTATETASKS += "do_deploy"
do_deploy[sstate-inputdirs] = "${DEPLOYDIR}"
do_deploy[sstate-outputdirs] = "${DEPLOY_DIR_IMAGE}"

python do_deploy_setscene () {
    sstate_setscene(d)
}
addtask do_deploy_setscene
do_deploy[dirs] = "${DEPLOYDIR} ${B}"
do_deploy[stamp-extra-info] = "${MACHINE_ARCH}"
```

The following list explains the previous example:

> 以下列表解释了前面的示例：

- Adding `do_deploy` to `SSTATETASKS` adds some required sstate-related processing, which is implemented in the `ref-classes-sstate`{.interpreted-text role="ref"} class, to before and after the `ref-tasks-deploy`{.interpreted-text role="ref"} task.

> -将“do_deploy”添加到“SSATETASKS”中，会在“ref tasks deploy”｛.depredicted text role=“ref”｝任务之前和之后添加一些所需的与 sstate 相关的处理，这些处理在“ref classes sstate”｛.epredicted text-role=“ref”}类中实现。

- The `do_deploy[sstate-inputdirs] = "${DEPLOYDIR}"` declares that `ref-tasks-deploy`{.interpreted-text role="ref"} places its output in `${DEPLOYDIR}` when run normally (i.e. when not using the sstate cache). This output becomes the input to the shared state cache.

> -`do_deploy[state inputdirs]=“$｛DEPLOYDIR｝”` 声明 `ref tasks deploy`｛.explored text role=“ref”｝在正常运行时（即不使用 sstate 缓存时）将其输出置于 `$｛DEPLOYDIR｝'中。该输出成为共享状态缓存的输入。

- The `do_deploy[sstate-outputdirs] = "${DEPLOY_DIR_IMAGE}"` line causes the contents of the shared state cache to be copied to `${DEPLOY_DIR_IMAGE}`.

> -`do_deploy[state outputdirs]=“$｛deploy_DIR_IMAGE｝”` 行会将共享状态缓存的内容复制到 `$｛deploy_DIR_IMAGE｝'。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

If `ref-tasks-deploy`{.interpreted-text role="ref"} is not already in the shared state cache or if its input checksum (signature) has changed from when the output was cached, the task runs to populate the shared state cache, after which the contents of the shared state cache is copied to \${`DEPLOY_DIR_IMAGE`{.interpreted-text role="term"}}. If `ref-tasks-deploy`{.interpreted-text role="ref"} is in the shared state cache and its signature indicates that the cached output is still valid (i.e. if no relevant task inputs have changed), then the contents of the shared state cache copies directly to \${`DEPLOY_DIR_IMAGE`{.interpreted-text role="term"}} by the `do_deploy_setscene` task instead, skipping the `ref-tasks-deploy`{.interpreted-text role="ref"} task.

> 如果 `ref tasks deploy`｛.depredicted text role=“ref”｝尚未在共享状态缓存中，或者其输入校验和（签名）与缓存输出时相比发生了更改，则运行该任务以填充共享状态缓存，然后共享状态缓存的内容被复制到\$｛`deploy_DIR_IMAGE`｛.epredicted textrole=”term“｝｝｝。如果 `ref tasks deploy`｛.explored text role=“ref”｝在共享状态缓存中，并且其签名指示缓存的输出仍然有效（即，如果没有相关的任务输入发生变化），则共享状态缓存的内容由 `do_deploy_setscene` 任务直接复制到\$｛`deploy_DIR_IMAGE`｛.sexplored textrole=”term“｝｝，跳过 `ref tasks deploy`｛.depreted text role=“ref”｝任务。

:::

> ：：：

- The following task definition is glue logic needed to make the previous settings effective:

  ```

  ```

> ```
> ```

python do_deploy_setscene () {

> python do_deploy_setscene（）{
> sstate_setscene(d)

}

> }

addtask do_deploy_setscene

> 添加任务 do_deploy_setscene

```

> ```


`sstate_setscene()` takes the flags above as input and accelerates the `ref-tasks-deploy`{.interpreted-text role="ref"} task through the shared state cache if possible. If the task was accelerated, `sstate_setscene()` returns True. Otherwise, it returns False, and the normal `ref-tasks-deploy`{.interpreted-text role="ref"} task runs. For more information, see the \"`bitbake-user-manual/bitbake-user-manual-execution:setscene`{.interpreted-text role="ref"}\" section in the BitBake User Manual.

> `sstate_setscene（）`将上面的标志作为输入，并在可能的情况下通过共享状态缓存加速`ref tasks deploy`{.depreted text role=“ref”}任务。如果任务被加速，“sstate_setscene（）”将返回True。否则，它将返回False，并运行正常的`ref tasks deploy`｛.explored text role=“ref”｝任务。有关详细信息，请参阅《bitbake用户手册》中的\“`bitbake用户手册/bitbake用户手动执行：setscene`{.depreted text role=”ref“}\”一节。

- The `do_deploy[dirs] = "${DEPLOYDIR} ${B}"` line creates `${DEPLOYDIR}` and `${B}` before the `ref-tasks-deploy`{.interpreted-text role="ref"} task runs, and also sets the current working directory of `ref-tasks-deploy`{.interpreted-text role="ref"} to `${B}`. For more information, see the \"`bitbake-user-manual/bitbake-user-manual-metadata:variable flags`{.interpreted-text role="ref"}\" section in the BitBake User Manual.

> -`do_deploy[dirs]=“$｛DEPLOYDIR｝$｛B｝”`行在`ref tasks deploy`｛.explored text role=“ref”｝任务运行之前创建`$｛DEPLOYDIR｝`和`$｛。有关详细信息，请参阅《bitbake用户手册》中的“`bitbake user manual/bitbake user-manual metadata:variable flags`｛.explored text role=“ref”｝\”一节。


::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：


In cases where `sstate-inputdirs` and `sstate-outputdirs` would be the same, you can use `sstate-plaindirs`. For example, to preserve the \${`PKGD`{.interpreted-text role="term"}} and \${`PKGDEST`{.interpreted-text role="term"}} output from the `ref-tasks-package`{.interpreted-text role="ref"} task, use the following:

> 如果“sstateinputdirs”和“sstateoutputdirs”相同，则可以使用“sstateplandirs”。例如，要保留`ref tasks package`{.depredicted text role=“ref”}任务输出的\${`PKGD`{.epredicted text-role=“term”}｝和\${` PKGDEST`{.deverdicted text-lole=“erm”}，请使用以下命令：


```

> ```
> ```

do_package[sstate-plaindirs] = "${PKGD} ${PKGDEST}"

> do_package[state plandirs]=“$｛PKGD｝$｛PKGDEST｝”

```

> ```


:::

> ：：：

- The `do_deploy[stamp-extra-info] = "${MACHINE_ARCH}"` line appends extra metadata to the `stamp file <overview-manual/concepts:stamp files and the rerunning of tasks>`{.interpreted-text role="ref"}. In this case, the metadata makes the task specific to a machine\'s architecture. See the \"`bitbake-user-manual/bitbake-user-manual-execution:the task list`{.interpreted-text role="ref"}\" section in the BitBake User Manual for more information on the `stamp-extra-info` flag.

> -`do_deploy[stamp extra info]=“$｛MACHINE_ARCH｝”`行将额外的元数据附加到`stamp file<overview manual/concepts:stamp files and The rerunning of tasks>`{.depredicted text role=“ref”}。在这种情况下，元数据使任务特定于机器的体系结构。有关“标记额外信息”标志的更多信息，请参阅《bitbake用户手册》中的“`bitbake用户手册/bitbake用户手动执行：任务列表`{.expreted text role=“ref”}\”部分。

- `sstate-inputdirs` and `sstate-outputdirs` can also be used with multiple directories. For example, the following declares `PKGDESTWORK`{.interpreted-text role="term"} and `SHLIBWORK` as shared state input directories, which populates the shared state cache, and `PKGDATA_DIR`{.interpreted-text role="term"} and `SHLIBSDIR` as the corresponding shared state output directories:

> -“sstateinputdirs”和“sstateoutputdirs”也可以与多个目录一起使用。例如，以下声明`PKGDESTWORK`｛.explored text role=“term”｝和`SHLIBWORK`为填充共享状态缓存的共享状态输入目录，`PKGDATA_DIR`｛.explored textrole=”term“｝和` SHLIBSDIR`为相应的共享状态输出目录：


```

> ```
> ```

do_package[sstate-inputdirs] = "${PKGDESTWORK} ${SHLIBSWORKDIR}"

> do_package[state inputdirs]=“$｛PKGDESTWORK｝$｛SHLIBSWORKDIR｝”

do_package[sstate-outputdirs] = "${PKGDATA_DIR} ${SHLIBSDIR}"

> do_package[state outputdirs]=“$｛PKGDATA_DIR｝$｛SHLIBSDIR｝”

```

> ```

- These methods also include the ability to take a lockfile when manipulating shared state directory structures, for cases where file additions or removals are sensitive:

> -这些方法还包括在操作共享状态目录结构时获取锁定文件的能力，适用于文件添加或删除敏感的情况：


```

> ```
> ```

do_package[sstate-lockfile] = "${PACKAGELOCK}"

> do_package[state-lockfile]=“$｛PACKAGELOCK｝”

```

> ```


Behind the scenes, the shared state code works by looking in `SSTATE_DIR`{.interpreted-text role="term"} and `SSTATE_MIRRORS`{.interpreted-text role="term"} for shared state files. Here is an example:

> 在后台，共享状态代码的工作方式是在共享状态文件的`SSTATE_DIR`｛.depreted text role=“term”｝和`SSTATE_MIRRORS`｛.repreted text role=“term”｝中查找。以下是一个示例：

```

SSTATE_MIRRORS ?= "
file://.* https://someserver.tld/share/sstate/PATH;downloadfilename=PATH
file://.* file:///some/local/dir/sstate/PATH"

```

::: note
::: title
Note
:::


The shared state directory (`SSTATE_DIR`{.interpreted-text role="term"}) is organized into two-character subdirectories, where the subdirectory names are based on the first two characters of the hash. If the shared state directory structure for a mirror has the same structure as `SSTATE_DIR`{.interpreted-text role="term"}, you must specify \"PATH\" as part of the URI to enable the build system to map to the appropriate subdirectory.

> 共享状态目录（`SSTATE_DIR`｛.explored text role=“term”｝）被组织为两个字符的子目录，其中子目录名称基于哈希的前两个字符。如果镜像的共享状态目录结构与`SSTATE_DIR`｛.explored text role=“term”｝具有相同的结构，则必须指定\“PATH\”作为URI的一部分，以使生成系统能够映射到相应的子目录。
:::


The shared state package validity can be detected just by looking at the filename since the filename contains the task checksum (or signature) as described earlier in this section. If a valid shared state package is found, the build process downloads it and uses it to accelerate the task.

> 共享状态包的有效性可以通过查看文件名来检测，因为文件名包含本节前面所述的任务校验和（或签名）。如果找到有效的共享状态包，则构建过程会下载该包并使用它来加速任务。


The build processes use the `*_setscene` tasks for the task acceleration phase. BitBake goes through this phase before the main execution code and tries to accelerate any tasks for which it can find shared state packages. If a shared state package for a task is available, the shared state package is used. This means the task and any tasks on which it is dependent are not executed.

> 构建进程在任务加速阶段使用“*_setscene”任务。BitBake在主执行代码之前经历这个阶段，并试图加速它可以找到共享状态包的任何任务。如果任务的共享状态包可用，则使用该共享状态包。这意味着该任务及其所依赖的任何任务都不会执行。


As a real world example, the aim is when building an IPK-based image, only the `ref-tasks-package_write_ipk`{.interpreted-text role="ref"} tasks would have their shared state packages fetched and extracted. Since the sysroot is not used, it would never get extracted. This is another reason why a task-based approach is preferred over a recipe-based approach, which would have to install the output from every task.

> 作为一个现实世界的例子，其目的是在构建基于IPK的映像时，只有`ref-tasks-package_write_IPK`{.depredicted text role=“ref”}任务才会获取和提取其共享状态包。由于没有使用sysroot，因此永远不会提取它。这也是为什么基于任务的方法比基于配方的方法更受欢迎的另一个原因，后者必须安装每个任务的输出。

## Hash Equivalence


The above section explained how BitBake skips the execution of tasks whose output can already be found in the Shared State cache.

> 上面的部分解释了BitBake如何跳过其输出已经在共享状态缓存中找到的任务的执行。


During a build, it may often be the case that the output / result of a task might be unchanged despite changes in the task\'s input values. An example might be whitespace changes in some input C code. In project terms, this is what we define as \"equivalence\".

> 在构建过程中，通常情况下，尽管任务的输入值发生了变化，但任务的输出/结果可能保持不变。一个例子可能是一些输入C代码中的空白更改。在项目术语中，这就是我们所定义的“等价”。


To keep track of such equivalence, BitBake has to manage three hashes for each task:

> 为了跟踪这种等价性，BitBake必须为每个任务管理三个哈希：


- The *task hash* explained earlier: computed from the recipe metadata, the task code and the task hash values from its dependencies. When changes are made, these task hashes are therefore modified, causing the task to re-execute. The task hashes of tasks depending on this task are therefore modified too, causing the whole dependency chain to re-execute.

> -前面解释的*taskhash*：根据配方元数据、任务代码及其依赖项中的任务哈希值计算得出。因此，当进行更改时，这些任务哈希会被修改，从而导致任务重新执行。因此，依赖于此任务的任务的任务哈希也被修改，导致整个依赖链重新执行。

- The *output hash*, a new hash computed from the output of Shared State tasks, tasks that save their resulting output to a Shared State tarball. The mapping between the task hash and its output hash is reported to a new *Hash Equivalence* server. This mapping is stored in a database by the server for future reference.

> -*输出散列*，一个根据共享状态任务的输出计算的新散列，这些任务将其结果输出保存到共享状态tarball。任务哈希及其输出哈希之间的映射被报告给新的*hash等效*服务器。该映射由服务器存储在数据库中以供将来参考。

- The *unihash*, a new hash, initially set to the task hash for the task. This is used to track the *unicity* of task output, and we will explain how its value is maintained.

> -*unihash*是一个新的散列，最初设置为该任务的任务散列。这用于跟踪任务输出的*unicity*，我们将解释如何维护其值。


When Hash Equivalence is enabled, BitBake computes the task hash for each task by using the unihash of its dependencies, instead of their task hash.

> 启用“哈希等效”后，BitBake将使用其依赖项的unihash而不是其任务哈希来计算每个任务的任务哈希。


Now, imagine that a Shared State task is modified because of a change in its code or metadata, or because of a change in its dependencies. Since this modifies its task hash, this task will need re-executing. Its output hash will therefore be computed again.

> 现在，假设一个共享状态任务由于其代码或元数据的更改，或者由于其依赖项的更改而被修改。由于这会修改其任务哈希，因此需要重新执行此任务。因此，将再次计算其输出哈希。


Then, the new mapping between the new task hash and its output hash will be reported to the Hash Equivalence server. The server will let BitBake know whether this output hash is the same as a previously reported output hash, for a different task hash.

> 然后，新任务哈希与其输出哈希之间的新映射将被报告给哈希等价服务器。对于不同的任务哈希，服务器会让BitBake知道这个输出哈希是否与之前报告的输出哈希相同。


If the output hash is already known, BitBake will update the task\'s unihash to match the original task hash that generated that output. Thanks to this, the depending tasks will keep a previously recorded task hash, and BitBake will be able to retrieve their output from the Shared State cache, instead of re-executing them. Similarly, the output of further downstream tasks can also be retrieved from Shared State.

> 如果输出哈希已知，BitBake将更新任务的unihash，以匹配生成该输出的原始任务哈希。正因为如此，依赖的任务将保留以前记录的任务哈希，BitBake将能够从共享状态缓存中检索它们的输出，而不是重新执行它们。类似地，还可以从共享状态中检索进一步下游任务的输出。


If the output hash is unknown, a new entry will be created on the Hash Equivalence server, matching the task hash to that output. The depending tasks, still having a new task hash because of the change, will need to re-execute as expected. The change propagates to the depending tasks.

> 如果输出哈希是未知的，则将在哈希等效服务器上创建一个新条目，将任务哈希与该输出相匹配。由于更改，依赖的任务仍有新的任务哈希，需要按预期重新执行。更改传播到相关任务。


To summarize, when Hash Equivalence is enabled, a change in one of the tasks in BitBake\'s run queue doesn\'t have to propagate to all the downstream tasks that depend on the output of this task, causing a full rebuild of such tasks, and so on with the next depending tasks. Instead, when the output of this task remains identical to previously recorded output, BitBake can safely retrieve all the downstream task output from the Shared State cache.

> 总之，当启用哈希等效时，BitBake运行队列中某个任务的更改不必传播到依赖于该任务输出的所有下游任务，从而导致这些任务的完全重建，依此类推。相反，当该任务的输出与之前记录的输出保持一致时，BitBake可以安全地从共享状态缓存中检索所有下游任务输出。

::: note
::: title
Note
:::


Having `/test-manual/reproducible-builds`{.interpreted-text role="doc"} is a key ingredient for the stability of the task\'s output hash. Therefore, the effectiveness of Hash Equivalence strongly depends on it.

> 拥有`/test manual/reparable builds`{.depredicted text role=“doc”}是任务输出哈希稳定性的关键因素。因此，哈希等价的有效性在很大程度上取决于它。
:::


This applies to multiple scenarios:

> 这适用于多种情况：


- A \"trivial\" change to a recipe that doesn\'t impact its generated output, such as whitespace changes, modifications to unused code paths or in the ordering of variables.

> -对配方的“琐碎”更改不会影响其生成的输出，例如空格更改、对未使用的代码路径的修改或变量排序。

- Shared library updates, for example to fix a security vulnerability. For sure, the programs using such a library should be rebuilt, but their new binaries should remain identical. The corresponding tasks should have a different output hash because of the change in the hash of their library dependency, but thanks to their output being identical, Hash Equivalence will stop the propagation down the dependency chain.

> -共享库更新，例如修复安全漏洞。当然，使用这样一个库的程序应该重新构建，但它们的新二进制文件应该保持相同。相应的任务应该有一个不同的输出哈希，因为它们的库依赖项的哈希发生了变化，但由于它们的输出是相同的，哈希等效将停止依赖链上的传播。

- Native tool updates. Though the depending tasks should be rebuilt, it\'s likely that they will generate the same output and be marked as equivalent.

> -本机工具更新。尽管应该重新构建依赖的任务，但它们很可能会生成相同的输出并标记为等效的。


This mechanism is enabled by default in Poky, and is controlled by three variables:

> 该机制在Poky中默认启用，由三个变量控制：


- `bitbake:BB_HASHSERVE`{.interpreted-text role="term"}, specifying a local or remote Hash Equivalence server to use.

> -`bitbake:BB_ASHSERVE`｛.explored text role=“term”｝，指定要使用的本地或远程哈希等效服务器。

- `BB_HASHSERVE_UPSTREAM`{.interpreted-text role="term"}, when `BB_HASHSERVE = "auto"`, allowing to connect the local server to an upstream one.

> -`BB_HASHSERVE_UPSTREAM`｛.explored text role=“term”｝，当`BB_HASHSERVE=“auto”`时，允许将本地服务器连接到上游服务器。

- `bitbake:BB_SIGNATURE_HANDLER`{.interpreted-text role="term"}, which must be set to `OEEquivHash`.

> -`bitbake:BB_SIGNATURE_HANDLER`｛.解释的文本角色=“术语”｝，必须设置为`OEEquivHash`。


Therefore, the default configuration in Poky corresponds to the below settings:

> 因此，Poky中的默认配置对应于以下设置：

```

BB_HASHSERVE = "auto"
BB_SIGNATURE_HANDLER = "OEEquivHash"

```


Rather than starting a local server, another possibility is to rely on a Hash Equivalence server on a network, by setting:

> 与启动本地服务器不同，另一种可能性是依靠网络上的哈希等效服务器，通过设置：

```

BB_HASHSERVE = "<HOSTNAME>:<PORT>"

```

::: note
::: title
Note
:::


The shared Hash Equivalence server needs to be maintained together with the Shared State cache. Otherwise, the server could report Shared State hashes that only exist on specific clients.

> 共享哈希等效服务器需要与共享状态缓存一起维护。否则，服务器可能会报告仅存在于特定客户端上的共享状态哈希。


We therefore recommend that one Hash Equivalence server be set up to correspond with a given Shared State cache, and to start this server in *read-only mode*, so that it doesn\'t store equivalences for Shared State caches that are local to clients.

> 因此，我们建议设置一个哈希等效服务器，以对应给定的共享状态缓存，并以*只读模式*启动该服务器，这样它就不会存储客户端本地共享状态缓存的等效值。


See the `BB_HASHSERVE`{.interpreted-text role="term"} reference for details about starting a Hash Equivalence server.

> 有关启动哈希等效服务器的详细信息，请参阅`BB_HASHSERVE`｛.explored text role=“term”｝参考。
:::


See the [video](https://www.youtube.com/watch?v=zXEdqGS62Wc) of Joshua Watt\'s [Hash Equivalence and Reproducible Builds](https://elinux.org/images/3/37/Hash_Equivalence_and_Reproducible_Builds.pdf) presentation at ELC 2020 for a very synthetic introduction to the Hash Equivalence implementation in the Yocto Project.

> 请参阅[视频](https://www.youtube.com/watch?v=zXEdqGS62Wc)Joshua Watt的[哈希等价和可复制构建](https://elinux.org/images/3/37/Hash_Equivalence_and_Reproducible_Builds.pdf)在ELC 2020上发表演讲，对Yocto项目中的哈希等效实现进行了非常全面的介绍。

# Automatically Added Runtime Dependencies


The OpenEmbedded build system automatically adds common types of runtime dependencies between packages, which means that you do not need to explicitly declare the packages using `RDEPENDS`{.interpreted-text role="term"}. There are three automatic mechanisms (`shlibdeps`, `pcdeps`, and `depchains`) that handle shared libraries, package configuration (pkg-config) modules, and `-dev` and `-dbg` packages, respectively. For other types of runtime dependencies, you must manually declare the dependencies.

> OpenEmbedded构建系统自动在包之间添加常见类型的运行时依赖项，这意味着您不需要使用`RDEPENDS`｛.depredicted text role=“term”｝显式声明包。有三种自动机制（“shlibdeps”、“pcdeps”和“depchains”）分别处理共享库、包配置（pkg config）模块以及“-dev”和“-dbg”包。对于其他类型的运行时依赖项，必须手动声明依赖项。


- `shlibdeps`: During the `ref-tasks-package`{.interpreted-text role="ref"} task of each recipe, all shared libraries installed by the recipe are located. For each shared library, the package that contains the shared library is registered as providing the shared library. More specifically, the package is registered as providing the `soname <Soname>`{.interpreted-text role="wikipedia"} of the library. The resulting shared-library-to-package mapping is saved globally in `PKGDATA_DIR`{.interpreted-text role="term"} by the `ref-tasks-packagedata`{.interpreted-text role="ref"} task.

> -`shlibdeps`：在每个配方的`ref tasks package`｛.explored text role=“ref”｝任务期间，将找到该配方安装的所有共享库。对于每个共享库，包含共享库的包都注册为提供共享库。更具体地说，该包被注册为提供库的“soname＜soname＞”{.depreted text role=“wikipedia”}。由此产生的共享库到包的映射由`ref tasks packagedata`｛.depreted text role=“ref”}任务全局保存在`PKGDATA_DIR`｛.repreted text role=“term”｝中。


  Simultaneously, all executables and shared libraries installed by the recipe are inspected to see what shared libraries they link against. For each shared library dependency that is found, `PKGDATA_DIR`{.interpreted-text role="term"} is queried to see if some package (likely from a different recipe) contains the shared library. If such a package is found, a runtime dependency is added from the package that depends on the shared library to the package that contains the library.

> 同时，将检查配方安装的所有可执行文件和共享库，以查看它们所链接的共享库。对于找到的每个共享库依赖项，都会查询`PKGDATA_DIR`｛.explored text role=“term”｝，以查看某个包（可能来自不同的配方）是否包含共享库。如果找到这样的包，则会将运行时依赖项从依赖于共享库的包添加到包含该库的包。


  The automatically added runtime dependency also includes a version restriction. This version restriction specifies that at least the current version of the package that provides the shared library must be used, as if \"package (\>= version)\" had been added to `RDEPENDS`{.interpreted-text role="term"}. This forces an upgrade of the package containing the shared library when installing the package that depends on the library, if needed.

> 自动添加的运行时依赖项还包括版本限制。此版本限制指定必须至少使用提供共享库的程序包的当前版本，就好像“程序包（\>=版本）”已添加到`RDEPENDS`｛.explored text role=“term”｝中一样。如果需要，在安装依赖于共享库的包时，这将强制升级包含共享库的程序包。


  If you want to avoid a package being registered as providing a particular shared library (e.g. because the library is for internal use only), then add the library to `PRIVATE_LIBS`{.interpreted-text role="term"} inside the package\'s recipe.

> 如果您想避免一个包被注册为提供特定的共享库（例如，因为该库仅用于内部使用），请将该库添加到包的配方内的`PRIVATE_LIBRS`｛.depreted text role=“term”｝中。

- `pcdeps`: During the `ref-tasks-package`{.interpreted-text role="ref"} task of each recipe, all pkg-config modules (`*.pc` files) installed by the recipe are located. For each module, the package that contains the module is registered as providing the module. The resulting module-to-package mapping is saved globally in `PKGDATA_DIR`{.interpreted-text role="term"} by the `ref-tasks-packagedata`{.interpreted-text role="ref"} task.

> -`pcdeps`：在每个配方的`ref tasks package`{.depreted text role=“ref”}任务期间，将找到该配方安装的所有pkg配置模块（`*.pc`文件）。对于每个模块，包含该模块的包被注册为提供该模块。由此产生的模块到包的映射由`ref tasks packagedata`｛.depreted text role=“ref”}任务全局保存在`PKGDATA_DIR`｛.repreted text role=“term”｝中。


  Simultaneously, all pkg-config modules installed by the recipe are inspected to see what other pkg-config modules they depend on. A module is seen as depending on another module if it contains a \"Requires:\" line that specifies the other module. For each module dependency, `PKGDATA_DIR`{.interpreted-text role="term"} is queried to see if some package contains the module. If such a package is found, a runtime dependency is added from the package that depends on the module to the package that contains the module.

> 同时，将检查该配方安装的所有pkg配置模块，以了解它们所依赖的其他pkg配置模块。如果一个模块包含指定另一个模块的\“Requires:\”行，则该模块将被视为依赖于另一个模块。对于每个模块依赖项，都会查询`PKGDATA_DIR`｛.explored text role=“term”｝，以查看某个包是否包含该模块。如果找到这样的包，则会将运行时依赖项从依赖于模块的包添加到包含模块的包。


  ::: note

> ：：：注释

  ::: title

> ：：标题

  Note

> 笔记

  :::

> ：：：


  The pcdeps mechanism most often infers dependencies between -dev packages.

> pcdeps机制通常推断-dev包之间的依赖关系。

  :::

> ：：：

- `depchains`: If a package `foo` depends on a package `bar`, then `foo-dev` and `foo-dbg` are also made to depend on `bar-dev` and `bar-dbg`, respectively. Taking the `-dev` packages as an example, the `bar-dev` package might provide headers and shared library symlinks needed by `foo-dev`, which shows the need for a dependency between the packages.

> -“depchains”：如果包“foo”依赖于包“bar”，则“foo-dev”和“foo-dbg”也分别依赖于“bar dev”和“bar dbg”。以“-dev”包为例，“bar dev”包可能提供“foo dev”所需的标头和共享库符号链接，这表明了包之间需要依赖关系。


  The dependencies added by `depchains` are in the form of `RRECOMMENDS`{.interpreted-text role="term"}.

> “depchains”添加的依赖项的形式为“RRECOMMENDS”｛.explored text role=“term”｝。


  ::: note

> ：：：注释

  ::: title

> ：：标题

  Note

> 笔记

  :::

> ：：：


  By default, `foo-dev` also has an `RDEPENDS`{.interpreted-text role="term"}-style dependency on `foo`, because the default value of `RDEPENDS:${PN}-dev` (set in `bitbake.conf`) includes \"\${PN}\".

> 默认情况下，`foo-dev`对`foo`也有`RDEPENDS`｛.explored text role=“term”｝样式的依赖关系，因为`RDEPENDS:$｛PN｝-dev`（在`bitbake.conf`中设置）的默认值包括\“\$｛PN}\”。

  :::

> ：：：


  To ensure that the dependency chain is never broken, `-dev` and `-dbg` packages are always generated by default, even if the packages turn out to be empty. See the `ALLOW_EMPTY`{.interpreted-text role="term"} variable for more information.

> 为了确保依赖链永远不会断开，默认情况下始终生成“-dev”和“-dbg”包，即使这些包是空的。有关详细信息，请参阅`ALLOW_EMPTY`｛.explored text role=“term”｝变量。


The `ref-tasks-package`{.interpreted-text role="ref"} task depends on the `ref-tasks-packagedata`{.interpreted-text role="ref"} task of each recipe in `DEPENDS`{.interpreted-text role="term"} through use of a `[``deptask <bitbake-user-manual/bitbake-user-manual-metadata:variable flags>`{.interpreted-text role="ref"}`]` declaration, which guarantees that the required shared-library / module-to-package mapping information will be available when needed as long as `DEPENDS`{.interpreted-text role="term"} has been correctly set.

> `ref tasks package`｛.depredicted text role=“ref”｝任务依赖于`depends`中每个配方的`ref tasks-packagedata`｛.epredicted text-role=“ref”}任务，通过使用`[`deptask<bitbake user manual/bitbake user-manual metadata:variable flags>`{.depredictedtext-role=“ref”}`]`声明，这保证了所需的共享库/模块到包的映射信息将在需要时可用，只要正确设置了`DEPENDS`{.depreted text role=“term”}即可。

# Fakeroot and Pseudo


Some tasks are easier to implement when allowed to perform certain operations that are normally reserved for the root user (e.g. `ref-tasks-install`{.interpreted-text role="ref"}, `do_package_write* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"}, `ref-tasks-rootfs`{.interpreted-text role="ref"}, and `do_image_* <ref-tasks-image>`{.interpreted-text role="ref"}). For example, the `ref-tasks-install`{.interpreted-text role="ref"} task benefits from being able to set the UID and GID of installed files to arbitrary values.

> 当允许执行通常为根用户保留的某些操作时，某些任务更容易实现（例如，`ref tasks install`{.depredicted text role=“ref”}、`do_package_write*<ref-tasks-package_write_deb>`{.debredicted text role=“ref”}`、`ref tasks-rootfs`{.depreced text role=“ref”｝和`do_image_*<ref tasks image>`{.epredicted text role=”ref“}）。例如，“ref tasks install”｛.explored text role=“ref”｝任务可以将已安装文件的UID和GID设置为任意值，这使任务受益匪浅。


One approach to allowing tasks to perform root-only operations would be to require `BitBake`{.interpreted-text role="term"} to run as root. However, this method is cumbersome and has security issues. The approach that is actually used is to run tasks that benefit from root privileges in a \"fake\" root environment. Within this environment, the task and its child processes believe that they are running as the root user, and see an internally consistent view of the filesystem. As long as generating the final output (e.g. a package or an image) does not require root privileges, the fact that some earlier steps ran in a fake root environment does not cause problems.

> 允许任务只执行root操作的一种方法是要求“BitBake”｛.depreted text role=“term”｝以root身份运行。然而，这种方法很麻烦并且存在安全问题。实际使用的方法是在“fake\”根环境中运行受益于根权限的任务。在这个环境中，任务及其子进程认为它们是作为根用户运行的，并看到文件系统的内部一致视图。只要生成最终输出（例如，包或图像）不需要root权限，早期的一些步骤在伪root环境中运行就不会造成问题。


The capability to run tasks in a fake root environment is known as \"[fakeroot](http://man.he.net/man1/fakeroot)\", which is derived from the BitBake keyword/variable flag that requests a fake root environment for a task.

> 在伪根环境中运行任务的功能被称为\“[fakeroot](http://man.he.net/man1/fakeroot)\”，它派生自BitBake关键字/变量标志，该标志请求任务的伪根环境。


In the `OpenEmbedded Build System`{.interpreted-text role="term"}, the program that implements fakeroot is known as :yocto_home:[Pseudo \</software-item/pseudo/\>]{.title-ref}. Pseudo overrides system calls by using the environment variable `LD_PRELOAD`, which results in the illusion of running as root. To keep track of \"fake\" file ownership and permissions resulting from operations that require root permissions, Pseudo uses an SQLite 3 database. This database is stored in `${``WORKDIR`{.interpreted-text role="term"}`}/pseudo/files.db` for individual recipes. Storing the database in a file as opposed to in memory gives persistence between tasks and builds, which is not accomplished using fakeroot.

> 在`OpenEmbedded Build System`｛.explored text role=“term”｝中，实现fakeroot的程序被称为：yocto_home:[Pudo\</software item/pudo/\>]｛.title ref｝。伪通过使用环境变量`LD_PRELOAD'覆盖系统调用，这会导致以root身份运行的错觉。为了跟踪需要根权限的操作所产生的“伪”文件所有权和权限，Pseudo使用了SQLite 3数据库。该数据库存储在单个配方的`${``WORKDIR`{.depreced text role=“term”}`}/pudo-files.db`中。将数据库存储在文件中，而不是存储在内存中，可以实现任务和构建之间的持久性，这是使用fakeroot无法实现的。

::: note
::: title
Note
:::


If you add your own task that manipulates the same files or directories as a fakeroot task, then that task also needs to run under fakeroot. Otherwise, the task cannot run root-only operations, and cannot see the fake file ownership and permissions set by the other task. You need to also add a dependency on `virtual/fakeroot-native:do_populate_sysroot`, giving the following:

> 如果您添加了自己的任务，该任务操作与fakeroot任务相同的文件或目录，那么该任务也需要在fakeroot下运行。否则，该任务将无法运行仅限root用户的操作，也无法看到其他任务设置的伪文件所有权和权限。您还需要添加对“virtual/fakeroot native:do_populate_sysroot”的依赖项，给出以下内容：

```

fakeroot do_mytask () {
...
}
do_mytask[depends] += "virtual/fakeroot-native:do_populate_sysroot"

```

:::


For more information, see the `FAKEROOT* <bitbake:FAKEROOT>`{.interpreted-text role="term"} variables in the BitBake User Manual. You can also reference the \"[Why Not Fakeroot?](https://github.com/wrpseudo/pseudo/wiki/WhyNotFakeroot)\" article for background information on Fakeroot and Pseudo.

> 有关更多信息，请参阅《bitbake用户手册》中的`FAKEROOT*<bitbake:FAKEROO>`{.expreted text role=“term”}变量。您也可以参考\“[为什么不伪造？](https://github.com/wrpseudo/pseudo/wiki/WhyNotFakeroot)\“关于Fakeroot和Pseudo的背景信息文章。
```
