---
tip: translate by openai@2023-06-10 10:00:51
...
---
title: Yocto Project Concepts
-----------------------------

This chapter provides explanations for Yocto Project concepts that go beyond the surface of \"how-to\" information and reference (or look-up) material. Concepts such as components, the `OpenEmbedded Build System`{.interpreted-text role="term"} workflow, cross-development toolchains, shared state cache, and so forth are explained.

> 本章提供了超越“如何”信息和参考（或查找）材料表面的 Yocto 项目概念的解释。概念，如组件、“OpenEmbedded 构建系统”{.interpreted-text role="term"}工作流、跨开发工具链、共享状态缓存等，将被解释。

# Yocto Project Components

The `BitBake`{.interpreted-text role="term"} task executor together with various types of configuration files form the `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"}. This section overviews these components by describing their use and how they interact.

> BitBake 任务执行器与各种类型的配置文件组成 OpenEmbedded-Core（OE-Core）。本节通过描述它们的使用及其如何相互作用来概述这些组件。

BitBake handles the parsing and execution of the data files. The data itself is of various types:

- *Recipes:* Provides details about particular pieces of software.
- *Class Data:* Abstracts common build information (e.g. how to build a Linux kernel).
- *Configuration Data:* Defines machine-specific settings, policy decisions, and so forth. Configuration data acts as the glue to bind everything together.

> *配置数据：*定义机器特定的设置、政策决定等。配置数据充当将所有内容结合在一起的胶水。

BitBake knows how to combine multiple data sources together and refers to each data source as a layer. For information on layers, see the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section of the Yocto Project Development Tasks Manual.

> BitBake 知道如何将多个数据源组合在一起，并将每个数据源称为层。有关层的信息，请参阅 Yocto 项目开发任务手册中的“dev-manual/layers：理解和创建层”部分。

Following are some brief details on these core components. For additional information on how these components interact during a build, see the \"`overview-manual/concepts:openembedded build system concepts`{.interpreted-text role="ref"}\" section.

> 以下是有关这些核心组件的简要信息。有关这些组件在构建过程中如何交互的更多信息，请参见“overview-manual/concepts:openembedded 构建系统概念”部分。

## BitBake

BitBake is the tool at the heart of the `OpenEmbedded Build System`{.interpreted-text role="term"} and is responsible for parsing the `Metadata`{.interpreted-text role="term"}, generating a list of tasks from it, and then executing those tasks.

> BitBake 是开放嵌入式构建系统的核心工具，负责解析元数据，生成任务列表，然后执行这些任务。

This section briefly introduces BitBake. If you want more information on BitBake, see the `BitBake User Manual <bitbake:index>`{.interpreted-text role="doc"}.

> 这一节简要介绍了 BitBake。如果您想了解更多关于 BitBake 的信息，请参阅“BitBake 用户手册”[bitbake:index](bitbake:index)。

To see a list of the options BitBake supports, use either of the following commands:

```
$ bitbake -h
$ bitbake --help
```

The most common usage for BitBake is `bitbake recipename`, where `recipename` is the name of the recipe you want to build (referred to as the \"target\"). The target often equates to the first part of a recipe\'s filename (e.g. \"foo\" for a recipe named `foo_1.3.0-r0.bb`). So, to process the `matchbox-desktop_1.2.3.bb` recipe file, you might type the following:

> 最常见的 BitBake 用法是 `bitbake recipename`，其中 `recipename` 是要构建的配方的名称（称为“目标”）。 目标通常等于配方文件名的第一部分（例如，对于名为 `foo_1.3.0-r0.bb` 的配方，可以是“foo”）。 因此，要处理 `matchbox-desktop_1.2.3.bb` 配方文件，您可以键入以下内容：

```
$ bitbake matchbox-desktop
```

Several different versions of `matchbox-desktop` might exist. BitBake chooses the one selected by the distribution configuration. You can get more details about how BitBake chooses between different target versions and providers in the \"`bitbake-user-manual/bitbake-user-manual-execution:preferences`{.interpreted-text role="ref"}\" section of the BitBake User Manual.

> 可能存在多个不同版本的 matchbox-desktop。BitBake 会选择发行版配置中选择的版本。您可以在 BitBake 用户手册的“bitbake-user-manual/bitbake-user-manual-execution:preferences”部分获取有关 BitBake 如何在不同目标版本和提供者之间进行选择的更多详细信息。

BitBake also tries to execute any dependent tasks first. So for example, before building `matchbox-desktop`, BitBake would build a cross compiler and `glibc` if they had not already been built.

> BitBake 也会先尝试执行任何依赖任务。 所以例如，在构建'matchbox-desktop'之前，如果尚未构建，BitBake 将构建交叉编译器和'glibc'。

A useful BitBake option to consider is the `-k` or `--continue` option. This option instructs BitBake to try and continue processing the job as long as possible even after encountering an error. When an error occurs, the target that failed and those that depend on it cannot be remade. However, when you use this option other dependencies can still be processed.

> 一个有用的 BitBake 选项值得考虑的是 `-k` 或 `--continue` 选项。此选项指示 BitBake 尽可能尝试继续处理作业，即使在遇到错误后也是如此。当发生错误时，失败的目标以及依赖于它的目标无法重新制作。但是，当您使用此选项时，其他依赖项仍可以处理。

## Recipes

Files that have the `.bb` suffix are \"recipes\" files. In general, a recipe contains information about a single piece of software. This information includes the location from which to download the unaltered source, any source patches to be applied to that source (if needed), which special configuration options to apply, how to compile the source files, and how to package the compiled output.

> 文件以 `.bb` 为后缀的文件是“配方”文件。一般来说，一个配方包含一个软件的信息。这些信息包括从何处下载未经改动的源文件，任何需要应用到该源文件的源补丁（如果需要），应用哪些特殊配置选项，如何编译源文件，以及如何打包编译输出。

The term \"package\" is sometimes used to refer to recipes. However, since the word \"package\" is used for the packaged output from the OpenEmbedded build system (i.e. `.ipk` or `.deb` files), this document avoids using the term \"package\" when referring to recipes.

> 有时会使用术语“包”来指代食谱。然而，由于 OpenEmbedded 构建系统使用“包”一词来指代打包输出（即 `.ipk` 或 `.deb` 文件），因此本文档在提及食谱时避免使用“包”一词。

## Classes

Class files (`.bbclass`) contain information that is useful to share between recipes files. An example is the `ref-classes-autotools`{.interpreted-text role="ref"} class, which contains common settings for any application that is built with the `GNU Autotools <GNU_Autotools>`{.interpreted-text role="wikipedia"}. The \"`ref-manual/classes:Classes`{.interpreted-text role="ref"}\" chapter in the Yocto Project Reference Manual provides details about classes and how to use them.

> 文件类（`.bbclass`）包含有用于在配方文件之间共享的信息。一个例子是 `ref-classes-autotools`{.interpreted-text role="ref"}类，它包含了使用 `GNU Autotools <GNU_Autotools>`{.interpreted-text role="wikipedia"}构建的任何应用程序的常见设置。Yocto Project 参考手册中的“`ref-manual/classes:Classes`{.interpreted-text role="ref"}”章节提供了有关类及其使用方法的详细信息。

## Configurations

The configuration files (`.conf`) define various configuration variables that govern the OpenEmbedded build process. These files fall into several areas that define machine configuration options, distribution configuration options, compiler tuning options, general common configuration options, and user configuration options in `conf/local.conf`, which is found in the `Build Directory`{.interpreted-text role="term"}.

> 配置文件（`.conf`）定义了控制 OpenEmbedded 构建过程的各种配置变量。这些文件分为几个区域，定义机器配置选项、分发配置选项、编译器调优选项、一般常见配置选项，以及用户配置选项，这些选项都在构建目录中的 `conf/local.conf` 中定义。

# Layers

Layers are repositories that contain related metadata (i.e. sets of instructions) that tell the OpenEmbedded build system how to build a target. `overview-manual/yp-intro:the yocto project layer model`{.interpreted-text role="ref"} facilitates collaboration, sharing, customization, and reuse within the Yocto Project development environment. Layers logically separate information for your project. For example, you can use a layer to hold all the configurations for a particular piece of hardware. Isolating hardware-specific configurations allows you to share other metadata by using a different layer where that metadata might be common across several pieces of hardware.

> 层是存储有关元数据（即指令集）的存储库，它们告诉 OpenEmbedded 构建系统如何构建目标。`overview-manual/yp-intro：Yocto项目层模型`{.interpreted-text role="ref"} 促进了 Yocto 项目开发环境中的协作、共享、定制和重用。层逻辑上分离您的项目信息。例如，您可以使用一个层来保存特定硬件的所有配置。隔离硬件特定的配置可以通过使用另一个层来共享其他元数据，其中这些元数据可能在多个硬件上是共同的。

There are many layers working in the Yocto Project development environment. The :yocto_home:[Yocto Project Compatible Layer Index \</software-overview/layers/\>]{.title-ref} and :oe_layerindex:[OpenEmbedded Layer Index \<\>]{.title-ref} both contain layers from which you can use or leverage.

> 在 Yocto 项目开发环境中有很多层次可供使用。:yocto_home:[Yocto 项目兼容层次索引 \</software-overview/layers/\>]{.title-ref} 和 :oe_layerindex:[OpenEmbedded 层次索引 \<\>]{.title-ref} 都包含可以使用或利用的层次。

By convention, layers in the Yocto Project follow a specific form. Conforming to a known structure allows BitBake to make assumptions during builds on where to find types of metadata. You can find procedures and learn about tools (i.e. `bitbake-layers`) for creating layers suitable for the Yocto Project in the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section of the Yocto Project Development Tasks Manual.

> 按照惯例，Yocto 项目中的层次遵循特定的形式。符合已知结构可以让 BitBake 在构建过程中做出假设，以找到元数据类型。您可以在 Yocto 项目开发任务手册的“dev-manual/layers：理解和创建层”部分找到用于创建适合 Yocto 项目的层的程序和了解工具（即 `bitbake-layers`）。

# OpenEmbedded Build System Concepts

This section takes a more detailed look inside the build process used by the `OpenEmbedded Build System`{.interpreted-text role="term"}, which is the build system specific to the Yocto Project. At the heart of the build system is BitBake, the task executor.

> 这一节将更详细地研究 Yocto 项目特有的构建系统 OpenEmbedded 构建系统的构建过程。构建系统的核心是任务执行器 BitBake。

The following diagram represents the high-level workflow of a build. The remainder of this section expands on the fundamental input, output, process, and metadata logical blocks that make up the workflow.

> 以下图表表示了构建的高级工作流程。本节的其余部分将深入研究组成工作流程的基本输入、输出、流程和元数据逻辑块。

![image](figures/YP-flow-diagram.png){width="100.0%"}

In general, the build\'s workflow consists of several functional areas:

- *User Configuration:* metadata you can use to control the build process.
- *Metadata Layers:* Various layers that provide software, machine, and distro metadata.
- *Source Files:* Upstream releases, local projects, and SCMs.
- *Build System:* Processes under the control of `BitBake`{.interpreted-text role="term"}. This block expands on how BitBake fetches source, applies patches, completes compilation, analyzes output for package generation, creates and tests packages, generates images, and generates cross-development tools.

> 系统构建：由 BitBake 控制的流程。此块内容扩展了 BitBake 如何获取源代码、应用补丁、完成编译、分析输出以生成软件包、创建和测试软件包、生成映像文件以及生成跨开发工具。

- *Package Feeds:* Directories containing output packages (RPM, DEB or IPK), which are subsequently used in the construction of an image or Software Development Kit (SDK), produced by the build system. These feeds can also be copied and shared using a web server or other means to facilitate extending or updating existing images on devices at runtime if runtime package management is enabled.

> 包订阅：包含由构建系统生成的输出包（RPM、DEB 或 IPK）的目录，用于构建映像或软件开发工具包（SDK）。这些订阅也可以使用 Web 服务器或其他方式复制和共享，以便在设备上运行时扩展或更新现有映像，如果启用了运行时包管理。

- *Images:* Images produced by the workflow.
- *Application Development SDK:* Cross-development tools that are produced along with an image or separately with BitBake.

## User Configuration

User configuration helps define the build. Through user configuration, you can tell BitBake the target architecture for which you are building the image, where to store downloaded source, and other build properties.

> 用户配置有助于定义构建。通过用户配置，您可以告诉 BitBake 为构建映像而构建的目标架构，存储下载的源位置以及其他构建属性。

The following figure shows an expanded representation of the \"User Configuration\" box of the `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"}:

> 下图展示了“用户配置”框的扩展表示，该框位于“概览手册/概念：开放式嵌入式构建系统概念”中：

![image](figures/user-configuration.png){width="100.0%"}

BitBake needs some basic configuration files in order to complete a build. These files are `*.conf` files. The minimally necessary ones reside as example files in the `build/conf` directory of the `Source Directory`{.interpreted-text role="term"}. For simplicity, this section refers to the Source Directory as the \"Poky Directory.\"

> BitBake 需要一些基本的配置文件才能完成构建。这些文件是 `*.conf` 文件。最小必要的文件位于 Poky 目录的 `build/conf` 目录中的示例文件中。为了简单起见，本节将源目录称为“Poky 目录”。

When you clone the `Poky`{.interpreted-text role="term"} Git repository or you download and unpack a Yocto Project release, you can set up the Source Directory to be named anything you want. For this discussion, the cloned repository uses the default name `poky`.

> 当你克隆 `Poky` Git 存储库或者你下载并解压缩 Yocto 项目发行版时，你可以将源目录命名为任何你想要的名字。在本讨论中，克隆的存储库使用默认名称 `poky`。

::: note
::: title
Note
:::

The Poky repository is primarily an aggregation of existing repositories. It is not a canonical upstream source.
:::

The `meta-poky` layer inside Poky contains a `conf` directory that has example configuration files. These example files are used as a basis for creating actual configuration files when you source `structure-core-script`{.interpreted-text role="ref"}, which is the build environment script.

> 在 Poky 内部的 `meta-poky` 层包含一个 `conf` 目录，其中包含示例配置文件。当您源自 `structure-core-script`{.interpreted-text role="ref"}时，这些示例文件将作为创建实际配置文件的基础使用，这是构建环境脚本。

Sourcing the build environment script creates a `Build Directory`{.interpreted-text role="term"} if one does not already exist. BitBake uses the `Build Directory`{.interpreted-text role="term"} for all its work during builds. The Build Directory has a `conf` directory that contains default versions of your `local.conf` and `bblayers.conf` configuration files. These default configuration files are created only if versions do not already exist in the `Build Directory`{.interpreted-text role="term"} at the time you source the build environment setup script.

> 源构建环境脚本会创建一个 `构建目录`（如果尚不存在）。BitBake 会使用 `构建目录` 中的所有内容进行构建。构建目录有一个 `conf` 目录，里面包含你的 `local.conf` 和 `bblayers.conf` 配置文件的默认版本。这些默认配置文件只有在你源构建环境设置脚本时，`构建目录` 中尚不存在版本时才会被创建。

Because the Poky repository is fundamentally an aggregation of existing repositories, some users might be familiar with running the `structure-core-script`{.interpreted-text role="ref"} script in the context of separate `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"} and BitBake repositories rather than a single Poky repository. This discussion assumes the script is executed from within a cloned or unpacked version of Poky.

> 由于 Poky 存储库本质上是现有存储库的聚合，因此一些用户可能熟悉在单独的 OpenEmbedded-Core（OE-Core）和 BitBake 存储库上运行 structure-core-script 脚本而不是单一的 Poky 存储库。本讨论假定脚本是从克隆或解压的 Poky 版本中执行的。

Depending on where the script is sourced, different sub-scripts are called to set up the `Build Directory`{.interpreted-text role="term"} (Yocto or OpenEmbedded). Specifically, the script `scripts/oe-setup-builddir` inside the poky directory sets up the `Build Directory`{.interpreted-text role="term"} and seeds the directory (if necessary) with configuration files appropriate for the Yocto Project development environment.

> 根据脚本的源位置，调用不同的子脚本来设置 `构建目录`（Yocto 或 OpenEmbedded）。具体来说，位于 poky 目录中的脚本 `scripts/oe-setup-builddir` 设置 `构建目录`，并根据需要将配置文件种子放入该目录，以适应 Yocto Project 开发环境。

::: note
::: title
Note
:::

The scripts/oe-setup-builddir script uses the `$TEMPLATECONF` variable to determine which sample configuration files to locate.
:::

The `local.conf` file provides many basic variables that define a build environment. Here is a list of a few. To see the default configurations in a `local.conf` file created by the build environment script, see the :yocto\_[git:%60local.conf.sample](git:%60local.conf.sample) \</poky/tree/meta-poky/conf/templates/default/local.conf.sample\>[ in the ]{.title-ref}[meta-poky]{.title-ref}\` layer:

> `local.conf` 文件提供定义构建环境的许多基本变量。以下是其中的一些。要查看构建环境脚本创建的 `local.conf` 文件中的默认配置，请参阅：yocto_[git:`local.conf.sample`](git:%60local.conf.sample%60)[在 meta-poky]{.title-ref}层中：

- *Target Machine Selection:* Controlled by the `MACHINE`{.interpreted-text role="term"} variable.
- *Download Directory:* Controlled by the `DL_DIR`{.interpreted-text role="term"} variable.
- *Shared State Directory:* Controlled by the `SSTATE_DIR`{.interpreted-text role="term"} variable.
- *Build Output:* Controlled by the `TMPDIR`{.interpreted-text role="term"} variable.
- *Distribution Policy:* Controlled by the `DISTRO`{.interpreted-text role="term"} variable.
- *Packaging Format:* Controlled by the `PACKAGE_CLASSES`{.interpreted-text role="term"} variable.
- *SDK Target Architecture:* Controlled by the `SDKMACHINE`{.interpreted-text role="term"} variable.
- *Extra Image Packages:* Controlled by the `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} variable.

::: note
::: title
Note
:::

Configurations set in the `conf/local.conf` file can also be set in the `conf/site.conf` and `conf/auto.conf` configuration files.
:::

The `bblayers.conf` file tells BitBake what layers you want considered during the build. By default, the layers listed in this file include layers minimally needed by the build system. However, you must manually add any custom layers you have created. You can find more information on working with the `bblayers.conf` file in the \"`dev-manual/layers:enabling your layer`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> `bblayers.conf` 文件告诉 BitBake 在构建过程中应该考虑哪些层。默认情况下，此文件中列出的层包括构建系统所需的最少层。但是，您必须手动添加任何自定义层。您可以在 Yocto 项目开发任务手册中的“dev-manual/layers：启用您的层”部分找到更多有关处理 bblayers.conf 文件的信息。

The files `site.conf` and `auto.conf` are not created by the environment initialization script. If you want the `site.conf` file, you need to create it yourself. The `auto.conf` file is typically created by an autobuilder:

> 文件 `site.conf` 和 `auto.conf` 不是由环境初始化脚本创建的。如果你想要 `site.conf` 文件，你需要自己创建它。`auto.conf` 文件通常是由自动构建器创建的。

- *site.conf:* You can use the `conf/site.conf` configuration file to configure multiple build directories. For example, suppose you had several build environments and they shared some common features. You can set these default build properties here. A good example is perhaps the packaging format to use through the `PACKAGE_CLASSES`{.interpreted-text role="term"} variable.

> 您可以使用 `conf/site.conf` 配置文件来配置多个构建目录。例如，假设您有几个构建环境并且它们共享一些共同的特性。您可以在此处设置这些默认构建属性。一个很好的例子可能是通过 `PACKAGE_CLASSES`{.interpreted-text role="term"}变量使用的打包格式。

- *auto.conf:* The file is usually created and written to by an autobuilder. The settings put into the file are typically the same as you would find in the `conf/local.conf` or the `conf/site.conf` files.

> -*auto.conf:* 这个文件通常是由自动构建器创建和写入的。放入该文件的设置通常与 `conf/local.conf` 或 `conf/site.conf` 文件中的设置相同。

You can edit all configuration files to further define any particular build environment. This process is represented by the \"User Configuration Edits\" box in the figure.

> 你可以编辑所有配置文件来进一步定义任何特定的构建环境。这个过程由图中的“用户配置编辑”框表示。

When you launch your build with the `bitbake target` command, BitBake sorts out the configurations to ultimately define your build environment. It is important to understand that the `OpenEmbedded Build System`{.interpreted-text role="term"} reads the configuration files in a specific order: `site.conf`, `auto.conf`, and `local.conf`. And, the build system applies the normal assignment statement rules as described in the \"`bitbake:bitbake-user-manual/bitbake-user-manual-metadata`{.interpreted-text role="doc"}\" chapter of the BitBake User Manual. Because the files are parsed in a specific order, variable assignments for the same variable could be affected. For example, if the `auto.conf` file and the `local.conf` set variable1 to different values, because the build system parses `local.conf` after `auto.conf`, variable1 is assigned the value from the `local.conf` file.

> 当你使用 `bitbake target` 命令启动构建时，BitBake 会对配置进行排序，最终定义你的构建环境。重要的是要明白，`OpenEmbedded Build System` 读取配置文件的顺序是：`site.conf`、`auto.conf` 和 `local.conf`。而且，构建系统会按照 BitBake 用户手册中“`bitbake:bitbake-user-manual/bitbake-user-manual-metadata`”章节中描述的正常赋值语句规则来应用。由于文件是按特定顺序解析的，相同变量的变量赋值可能会受到影响。例如，如果 `auto.conf` 文件和 `local.conf` 设置变量 1 为不同的值，因为构建系统在解析 `auto.conf` 之后解析 `local.conf`，变量 1 将被分配来自 `local.conf` 文件的值。

## Metadata, Machine Configuration, and Policy Configuration

The previous section described the user configurations that define BitBake\'s global behavior. This section takes a closer look at the layers the build system uses to further control the build. These layers provide Metadata for the software, machine, and policies.

> 上一节描述了定义 BitBake 全局行为的用户配置。本节将更深入地研究建立系统所使用的层，以进一步控制建立。这些层提供了软件、机器和政策的元数据。

In general, there are three types of layer input. You can see them below the \"User Configuration\" box in the \`general workflow figure \<overview-manual/concepts:openembedded build system concepts\>\`:

> 一般来说，有三种类型的层输入。你可以在“用户配置”框下方在“一般工作流程图”<overview-manual/concepts:openembedded build system concepts> 中看到它们：

- *Metadata (.bb + Patches):* Software layers containing user-supplied recipe files, patches, and append files. A good example of a software layer might be the :oe_layer:[meta-qt5 layer \</meta-qt5\>]{.title-ref} from the :oe_layerindex:[OpenEmbedded Layer Index \<\>]{.title-ref}. This layer is for version 5.0 of the popular [Qt](https://wiki.qt.io/About_Qt) cross-platform application development framework for desktop, embedded and mobile.

> -.bb 和补丁：包含用户提供的配方文件、补丁和附加文件的软件层。一个软件层的好例子是：来自开放嵌入式层索引的：oe_layer:[meta-qt5 层\</meta-qt5\>]{.title-ref}。此层用于流行的 [Qt](https://wiki.qt.io/About_Qt) 跨平台应用开发框架的 5.0 版本，用于桌面、嵌入式和移动端。

- *Machine BSP Configuration:* Board Support Package (BSP) layers (i.e. \"BSP Layer\" in the following figure) providing machine-specific configurations. This type of information is specific to a particular target architecture. A good example of a BSP layer from the `overview-manual/yp-intro:reference distribution (poky)`{.interpreted-text role="ref"} is the :yocto\_[git:%60meta-yocto-bsp](git:%60meta-yocto-bsp) \</poky/tree/meta-yocto-bsp\>\` layer.

> 配置机器 BSP：提供机器特定配置的板支持包（BSP）层（即以下图中的“BSP 层”）。此类信息特定于特定目标架构。来自 `概览手册/yp-intro：参考分发（poky）`{.interpreted-text role="ref"}的 BSP 层的一个很好的例子是：yocto_[git:`meta-yocto-bsp`](git:%60meta-yocto-bsp%60) </poky/tree/meta-yocto-bsp>` 层。

- *Policy Configuration:* Distribution Layers (i.e. \"Distro Layer\" in the following figure) providing top-level or general policies for the images or SDKs being built for a particular distribution. For example, in the Poky Reference Distribution the distro layer is the :yocto\_[git:%60meta-poky](git:%60meta-poky) \</poky/tree/meta-poky\>[ layer. Within the distro layer is a ]{.title-ref}[conf/distro]{.title-ref}[ directory that contains distro configuration files (e.g. :yocto_git:\`poky.conf \</poky/tree/meta-poky/conf/distro/poky.conf\>]{.title-ref} that contain many policy configurations for the Poky distribution.

> 配置政策：分发层（以下图中的“Distro Layer”为例）提供为特定分发构建的映像或 SDK 提供顶级或通用策略。例如，在 Poky 参考分发中，distro 层是：yocto_[git:%60meta-poky](git:%60meta-poky) \</poky/tree/meta-poky\>层。在 distro 层中，有一个]{.title-ref}[conf/distro]{.title-ref}[目录，其中包含分发配置文件（例如：yocto_git:\`poky.conf \</poky/tree/meta-poky/conf/distro/poky.conf\>]{.title-ref}，其中包含 Poky 分发的许多配置政策。

The following figure shows an expanded representation of these three layers from the `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"}:

> 下图展示了从“一般工作流程图 <overview-manual/concepts:openembedded build system concepts>”中这三层的扩展表示：

![image](figures/layer-input.png){.align-center width="70.0%"}

In general, all layers have a similar structure. They all contain a licensing file (e.g. `COPYING.MIT`) if the layer is to be distributed, a `README` file as good practice and especially if the layer is to be distributed, a configuration directory, and recipe directories. You can learn about the general structure for layers used with the Yocto Project in the \"`dev-manual/layers:creating your own layer`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual. For a general discussion on layers and the many layers from which you can draw, see the \"`overview-manual/concepts:layers`{.interpreted-text role="ref"}\" and \"`overview-manual/yp-intro:the yocto project layer model`{.interpreted-text role="ref"}\" sections both earlier in this manual.

> 一般来说，所有层都有相似的结构。如果要分发层，它们都包含一个许可文件（例如 `COPYING.MIT`），一个作为良好实践的 `README` 文件，特别是如果要分发层，一个配置目录和食谱目录。您可以在 Yocto 项目开发任务手册中的“dev-manual / layers：创建自己的层”部分了解与 Yocto 项目一起使用的层的一般结构。有关层及您可以从中提取的许多层的一般讨论，请参见本手册中较早的“overview-manual / concepts：layers”和“overview-manual / yp-intro：yocto 项目层模型”部分。

If you explored the previous links, you discovered some areas where many layers that work with the Yocto Project exist. The :yocto\_[git:%60Source](git:%60Source) Repositories \<\>\` also shows layers categorized under \"Yocto Metadata Layers.\"

> 如果您探索了以前的链接，您会发现一些与 Yocto 项目一起工作的多层次区域。":yocto_[git:%60Source](git:%60Source) Repositories <>"也显示了在“Yocto Metadata Layers”下分类的图层。

::: note
::: title
Note
:::

There are layers in the Yocto Project Source Repositories that cannot be found in the OpenEmbedded Layer Index. Such layers are either deprecated or experimental in nature.

> 在 Yocto 项目源仓库中有一些层是在 OpenEmbedded 层索引中找不到的。这些层要么是过时的，要么是实验性质的。
> :::

BitBake uses the `conf/bblayers.conf` file, which is part of the user configuration, to find what layers it should be using as part of the build.

### Distro Layer

The distribution layer provides policy configurations for your distribution. Best practices dictate that you isolate these types of configurations into their own layer. Settings you provide in `conf/distro/distro.conf` override similar settings that BitBake finds in your `conf/local.conf` file in the `Build Directory`{.interpreted-text role="term"}.

> 分发层为您的分发提供策略配置。最佳实践规定，您应将这些类型的配置隔离到自己的层中。您在 `Build Directory` 中的 `conf / distro / distro.conf` 中提供的设置将覆盖 BitBake 在 `conf / local.conf` 文件中找到的类似设置。

The following list provides some explanation and references for what you typically find in the distribution layer:

- *classes:* Class files (`.bbclass`) hold common functionality that can be shared among recipes in the distribution. When your recipes inherit a class, they take on the settings and functions for that class. You can read more about class files in the \"`ref-manual/classes:Classes`{.interpreted-text role="ref"}\" chapter of the Yocto Reference Manual.

> 类文件（`.bbclass`）包含可在发行版中的食谱之间共享的常见功能。当您的食谱继承一个类时，它们会接受该类的设置和函数。您可以在 Yocto 参考手册的“ref-manual/classes:Classes”章节中阅读有关类文件的更多信息。

- *conf:* This area holds configuration files for the layer (`conf/layer.conf`), the distribution (`conf/distro/distro.conf`), and any distribution-wide include files.

> 这个区域保存着层的配置文件（`conf/layer.conf`）、发行版的配置文件（`conf/distro/distro.conf`）和任何发行版的全局包含文件。

- *recipes-*:\* Recipes and append files that affect common functionality across the distribution. This area could include recipes and append files to add distribution-specific configuration, initialization scripts, custom image recipes, and so forth. Examples of `recipes-*` directories are `recipes-core` and `recipes-extra`. Hierarchy and contents within a `recipes-*` directory can vary. Generally, these directories contain recipe files (`*.bb`), recipe append files (`*.bbappend`), directories that are distro-specific for configuration files, and so forth.

> -*配方-*：这个区域包含影响整个发行版的常见功能的配方和附加文件。这个区域可以包括添加特定发行版配置、初始化脚本、自定义镜像配方等等的配方和附加文件。`recipes-*` 目录的例子有 `recipes-core` 和 `recipes-extra`。`recipes-*` 目录的层级和内容可以有所不同。一般来说，这些目录包含配方文件（`*.bb`）、配方附加文件（`*.bbappend`）、特定发行版配置文件的目录等等。

### BSP Layer

The BSP Layer provides machine configurations that target specific hardware. Everything in this layer is specific to the machine for which you are building the image or the SDK. A common structure or form is defined for BSP layers. You can learn more about this structure in the `/bsp-guide/index`{.interpreted-text role="doc"}.

> BSP 层提供针对特定硬件的机器配置。 该层中的所有内容都与您正在构建映像或 SDK 的机器相关。 为 BSP 层定义了一个常见的结构或形式。 您可以在“/ bsp-guide/index”中了解有关此结构的更多信息。

::: note
::: title
Note
:::

In order for a BSP layer to be considered compliant with the Yocto Project, it must meet some structural requirements.
:::

The BSP Layer\'s configuration directory contains configuration files for the machine (`conf/machine/machine.conf`) and, of course, the layer (`conf/layer.conf`).

> 配置目录包含机器的配置文件（`conf/machine/machine.conf`）和当然也包含 BSP 层（`conf/layer.conf`）的配置文件。

The remainder of the layer is dedicated to specific recipes by function: `recipes-bsp`, `recipes-core`, `recipes-graphics`, `recipes-kernel`, and so forth. There can be metadata for multiple formfactors, graphics support systems, and so forth.

> 剩余的层被用于按功能指定的特定配方：`recipes-bsp`，`recipes-core`，`recipes-graphics`，`recipes-kernel` 等等。 可能有多种形式的元数据，图形支持系统等等。

::: note
::: title
Note
:::

While the figure shows several recipes-\* directories, not all these directories appear in all BSP layers.
:::

### Software Layer

The software layer provides the Metadata for additional software packages used during the build. This layer does not include Metadata that is specific to the distribution or the machine, which are found in their respective layers.

> 软件层提供了在构建期间使用的其他软件包的元数据。此层不包括特定于发行版或机器的元数据，这些元数据位于它们各自的层中。

This layer contains any recipes, append files, and patches, that your project needs.

## Sources

In order for the OpenEmbedded build system to create an image or any target, it must be able to access source files. The `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"} represents source files using the \"Upstream Project Releases\", \"Local Projects\", and \"SCMs (optional)\" boxes. The figure represents mirrors, which also play a role in locating source files, with the \"Source Materials\" box.

> 为了让 OpenEmbedded 构建系统创建图像或任何目标，它必须能够访问源文件。“概览手册/概念：OpenEmbedded 构建系统概念”中的“通用工作流程图”使用“上游项目发布”、“本地项目”和“SCM（可选）”框表示源文件。该图表使用“源材料”框表示镜像，镜像也可以在定位源文件时发挥作用。

The method by which source files are ultimately organized is a function of the project. For example, for released software, projects tend to use tarballs or other archived files that can capture the state of a release guaranteeing that it is statically represented. On the other hand, for a project that is more dynamic or experimental in nature, a project might keep source files in a repository controlled by a Source Control Manager (SCM) such as Git. Pulling source from a repository allows you to control the point in the repository (the revision) from which you want to build software. A combination of the two is also possible.

> 方式，源文件最终被组织的方式取决于项目。例如，对于发布的软件，项目往往使用 tarball 或其他归档文件，可以捕获发行版本的状态，以保证静态表示。另一方面，对于更动态或实验性质的项目，项目可能会将源文件保存在由源代码管理器（SCM）如 Git 控制的存储库中。从存储库中拉取源代码可以控制存储库中的点（修订版）从中构建软件。两者也可以结合使用。

BitBake uses the `SRC_URI`{.interpreted-text role="term"} variable to point to source files regardless of their location. Each recipe must have a `SRC_URI`{.interpreted-text role="term"} variable that points to the source.

> BitBake 使用 `SRC_URI` 变量指向无论其位置如何的源文件。每个配方必须有一个指向源的 `SRC_URI` 变量。

Another area that plays a significant role in where source files come from is pointed to by the `DL_DIR`{.interpreted-text role="term"} variable. This area is a cache that can hold previously downloaded source. You can also instruct the OpenEmbedded build system to create tarballs from Git repositories, which is not the default behavior, and store them in the `DL_DIR`{.interpreted-text role="term"} by using the `BB_GENERATE_MIRROR_TARBALLS`{.interpreted-text role="term"} variable.

> 另一个重要的源文件来源是由 `DL_DIR` 变量指向的。这个区域是一个缓存，可以保存之前下载的源代码。您还可以指示 OpenEmbedded 构建系统从 Git 存储库中创建 tarballs，这不是默认行为，并使用 `BB_GENERATE_MIRROR_TARBALLS` 变量将它们存储在 `DL_DIR` 中。

Judicious use of a `DL_DIR`{.interpreted-text role="term"} directory can save the build system a trip across the Internet when looking for files. A good method for using a download directory is to have `DL_DIR`{.interpreted-text role="term"} point to an area outside of your `Build Directory`{.interpreted-text role="term"}. Doing so allows you to safely delete the `Build Directory`{.interpreted-text role="term"} if needed without fear of removing any downloaded source file.

> 使用 `DL_DIR`{.interpreted-text role="term"}目录的明智之举可以让构建系统在搜索文件时节省跨越互联网的时间。使用下载目录的好方法是将 `DL_DIR`{.interpreted-text role="term"}指向构建目录 `Build Directory`{.interpreted-text role="term"}之外的区域。这样做可以让您安全地删除 `Build Directory`{.interpreted-text role="term"}，而不必担心会删除任何已下载的源文件。

The remainder of this section provides a deeper look into the source files and the mirrors. Here is a more detailed look at the source file area of the `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"}:

> 本节的其余部分将提供对源文件和镜像的更深入的了解。以下是对“概览手册/概念：开放嵌入式构建系统概念”中源文件区域的更详细的介绍：

![image](figures/source-input.png){.align-center width="70.0%"}

### Upstream Project Releases

Upstream project releases exist anywhere in the form of an archived file (e.g. tarball or zip file). These files correspond to individual recipes. For example, the figure uses specific releases each for BusyBox, Qt, and Dbus. An archive file can be for any released product that can be built using a recipe.

> 上游项目发布存在任何形式的存档文件（例如 tarball 或 zip 文件）中。这些文件对应于单独的配方。例如，该图使用特定版本的 BusyBox，Qt 和 Dbus。存档文件可以是任何可以使用配方构建的发布产品。

### Local Projects

Local projects are custom bits of software the user provides. These bits reside somewhere local to a project \-\-- perhaps a directory into which the user checks in items (e.g. a local directory containing a development source tree used by the group).

> 本地项目是用户提供的自定义软件片段。这些片段位于项目的某个本地位置--可能是用户检查项目的目录（例如，包含组使用的开发源树的本地目录）。

The canonical method through which to include a local project is to use the `ref-classes-externalsrc`{.interpreted-text role="ref"} class to include that local project. You use either the `local.conf` or a recipe\'s append file to override or set the recipe to point to the local directory on your disk to pull in the whole source tree.

> 本地项目的标准方法是使用 `ref-classes-externalsrc`{.interpreted-text role="ref"} 类来包含本地项目。您可以使用 `local.conf` 或菜谱的附加文件来覆盖或设置菜谱以指向磁盘上的本地目录以拉取整个源树。

### Source Control Managers (Optional)

Another place from which the build system can get source files is with `bitbake-user-manual/bitbake-user-manual-fetching:fetchers`{.interpreted-text role="ref"} employing various Source Control Managers (SCMs) such as Git or Subversion. In such cases, a repository is cloned or checked out. The `ref-tasks-fetch`{.interpreted-text role="ref"} task inside BitBake uses the `SRC_URI`{.interpreted-text role="term"} variable and the argument\'s prefix to determine the correct fetcher module.

> 另一个可以让构建系统获取源文件的地方是使用各种源代码管理器（SCM），如 Git 或 Subversion，在这种情况下，将克隆或检出一个存储库。BitBake 中的 `ref-tasks-fetch` 任务使用 `SRC_URI` 变量和参数的前缀来确定正确的获取模块。

::: note
::: title
Note
:::

For information on how to have the OpenEmbedded build system generate tarballs for Git repositories and place them in the `DL_DIR`{.interpreted-text role="term"} directory, see the `BB_GENERATE_MIRROR_TARBALLS`{.interpreted-text role="term"} variable in the Yocto Project Reference Manual.

> 要了解如何让 OpenEmbedded 构建系统为 Git 存储库生成 tarball 并将其放入 `DL_DIR`{.interpreted-text role="term"}目录中，请参阅 Yocto Project 参考手册中的 `BB_GENERATE_MIRROR_TARBALLS`{.interpreted-text role="term"}变量。
> :::

When fetching a repository, BitBake uses the `SRCREV`{.interpreted-text role="term"} variable to determine the specific revision from which to build.

> 当获取仓库时，BitBake 使用 `SRCREV` 变量来确定要构建的特定修订版本。

### Source Mirror(s)

There are two kinds of mirrors: pre-mirrors and regular mirrors. The `PREMIRRORS`{.interpreted-text role="term"} and `MIRRORS`{.interpreted-text role="term"} variables point to these, respectively. BitBake checks pre-mirrors before looking upstream for any source files. Pre-mirrors are appropriate when you have a shared directory that is not a directory defined by the `DL_DIR`{.interpreted-text role="term"} variable. A Pre-mirror typically points to a shared directory that is local to your organization.

> 有两种镜子：预先镜子和普通镜子。变量 `PREMIRRORS`{.interpreted-text role="term"}和 `MIRRORS`{.interpreted-text role="term"}分别指向这些镜子。BitBake 在查找任何源文件之前会先检查预先镜子。当您有一个未由 `DL_DIR`{.interpreted-text role="term"}变量定义的共享目录时，预先镜子是合适的。预先镜子通常指向您组织内部的共享目录。

Regular mirrors can be any site across the Internet that is used as an alternative location for source code should the primary site not be functioning for some reason or another.

> 正常的镜像可以是互联网上任何一个作为源代码的备用位置的网站，在主站因为某些原因不能正常工作时可以使用。

## Package Feeds

When the OpenEmbedded build system generates an image or an SDK, it gets the packages from a package feed area located in the `Build Directory`{.interpreted-text role="term"}. The `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"} shows this package feeds area in the upper-right corner.

> 当 OpenEmbedded 构建系统生成一个镜像或 SDK 时，它会从位于构建目录中的软件包发布区获取软件包。通用工作流图 <overview-manual/concepts: openembedded build system concepts> 将这个软件包发布区显示在右上角。

This section looks a little closer into the package feeds area used by the build system. Here is a more detailed look at the area:

![image](figures/package-feeds.png){width="100.0%"}

Package feeds are an intermediary step in the build process. The OpenEmbedded build system provides classes to generate different package types, and you specify which classes to enable through the `PACKAGE_CLASSES`{.interpreted-text role="term"} variable. Before placing the packages into package feeds, the build process validates them with generated output quality assurance checks through the `ref-classes-insane`{.interpreted-text role="ref"} class.

> 包订阅是构建过程中的中间步骤。OpenEmbedded 构建系统提供类来生成不同的包类型，您可以通过 `PACKAGE_CLASSES` 变量指定要启用哪些类。在将包放入包订阅之前，构建过程会通过 `ref-classes-insane` 类对它们进行生成输出质量保证检查。

The package feed area resides in the `Build Directory`{.interpreted-text role="term"}. The directory the build system uses to temporarily store packages is determined by a combination of variables and the particular package manager in use. See the \"Package Feeds\" box in the illustration and note the information to the right of that area. In particular, the following defines where package files are kept:

> 包发布区域位于“构建目录”中。构建系统用来临时存储包的目录是由变量和正在使用的特定包管理器组合决定的。请参阅图中的“包发布”框，并注意其右侧的信息。特别是，以下定义了存放包文件的位置：

- `DEPLOY_DIR`{.interpreted-text role="term"}: Defined as `tmp/deploy` in the `Build Directory`{.interpreted-text role="term"}.
- `DEPLOY_DIR_*`: Depending on the package manager used, the package type sub-folder. Given RPM, IPK, or DEB packaging and tarball creation, the `DEPLOY_DIR_RPM`{.interpreted-text role="term"}, `DEPLOY_DIR_IPK`{.interpreted-text role="term"}, or `DEPLOY_DIR_DEB`{.interpreted-text role="term"} variables are used, respectively.

> `DEPLOY_DIR_*`：取决于使用的包管理器，该包类型子文件夹。给出 RPM、IPK 或 DEB 打包和归档创建，分别使用 `DEPLOY_DIR_RPM`{.interpreted-text role="term"}、`DEPLOY_DIR_IPK`{.interpreted-text role="term"}和 `DEPLOY_DIR_DEB`{.interpreted-text role="term"}变量。

- `PACKAGE_ARCH`{.interpreted-text role="term"}: Defines architecture-specific sub-folders. For example, packages could be available for the i586 or qemux86 architectures.

> `- ` PACKAGE_ARCH`{.interpreted-text role="term"}：定义特定于架构的子文件夹。例如，可以为 i586 或 qemux86 架构提供包。

BitBake uses the `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"} tasks to generate packages and place them into the package holding area (e.g. `do_package_write_ipk` for IPK packages). See the \"`ref-tasks-package_write_deb`{.interpreted-text role="ref"}\", \"`ref-tasks-package_write_ipk`{.interpreted-text role="ref"}\", and \"`ref-tasks-package_write_rpm`{.interpreted-text role="ref"}\" sections in the Yocto Project Reference Manual for additional information. As an example, consider a scenario where an IPK packaging manager is being used and there is package architecture support for both i586 and qemux86. Packages for the i586 architecture are placed in `build/tmp/deploy/ipk/i586`, while packages for the qemux86 architecture are placed in `build/tmp/deploy/ipk/qemux86`.

> BitBake 使用 `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"}任务来生成软件包，并将它们放置到软件包保存区（例如，使用 IPK 软件包管理器时，使用 `do_package_write_ipk`）。有关更多信息，请参见 Yocto 项目参考手册中的“`ref-tasks-package_write_deb`{.interpreted-text role="ref"}”、“`ref-tasks-package_write_ipk`{.interpreted-text role="ref"}”和“`ref-tasks-package_write_rpm`{.interpreted-text role="ref"}”部分。例如，考虑一种使用 IPK 软件包管理器并且支持 i586 和 qemux86 两种软件包体系结构的情况。i586 架构的软件包放置在 `build/tmp/deploy/ipk/i586` 中，而 qemux86 架构的软件包放置在 `build/tmp/deploy/ipk/qemux86` 中。

## BitBake Tool

The OpenEmbedded build system uses `BitBake`{.interpreted-text role="term"} to produce images and Software Development Kits (SDKs). You can see from the `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"}, the BitBake area consists of several functional areas. This section takes a closer look at each of those areas.

> 开放式嵌入式构建系统使用 BitBake 来生成图像和软件开发套件（SDK）。您可以从通用工作流程图（overview-manual/concepts:openembedded build system concepts）看到，BitBake 区由几个功能区组成。本节将对这些区域进行更详细的介绍。

::: note
::: title
Note
:::

Documentation for the BitBake tool is available separately. See the `BitBake User Manual <bitbake:index>`{.interpreted-text role="doc"} for reference material on BitBake.

> 文档以单独的方式提供 BitBake 工具。有关 BitBake 的参考资料，请参见《BitBake 用户手册》[bitbake:index](bitbake:index)。
> :::

### Source Fetching

The first stages of building a recipe are to fetch and unpack the source code:

![image](figures/source-fetching.png){width="100.0%"}

The `ref-tasks-fetch`{.interpreted-text role="ref"} and `ref-tasks-unpack`{.interpreted-text role="ref"} tasks fetch the source files and unpack them into the `Build Directory`{.interpreted-text role="term"}.

> `ref-tasks-fetch`{.interpreted-text role="ref"} 和 `ref-tasks-unpack`{.interpreted-text role="ref"} 任务会获取源文件并将它们解压缩到 `Build Directory`{.interpreted-text role="term"}中。

::: note
::: title
Note
:::

For every local file (e.g. `file://`) that is part of a recipe\'s `SRC_URI`{.interpreted-text role="term"} statement, the OpenEmbedded build system takes a checksum of the file for the recipe and inserts the checksum into the signature for the `ref-tasks-fetch`{.interpreted-text role="ref"} task. If any local file has been modified, the `ref-tasks-fetch`{.interpreted-text role="ref"} task and all tasks that depend on it are re-executed.

> 对于作为配方 `SRC_URI`{.interpreted-text role="term"}声明的每个本地文件（例如 `file://`），OpenEmbedded 构建系统会对该文件进行校验和，并将校验和插入到 `ref-tasks-fetch`{.interpreted-text role="ref"}任务的签名中。如果任何本地文件被修改，则重新执行 `ref-tasks-fetch`{.interpreted-text role="ref"}任务及其所有依赖任务。
> :::

By default, everything is accomplished in the `Build Directory`{.interpreted-text role="term"}, which has a defined structure. For additional general information on the `Build Directory`{.interpreted-text role="term"}, see the \"`structure-core-build`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual.

> 默认情况下，所有操作都在具有定义结构的“构建目录”中完成。有关“构建目录”的其他一般信息，请参阅 Yocto 项目参考手册中的“structure-core-build”部分。

Each recipe has an area in the `Build Directory`{.interpreted-text role="term"} where the unpacked source code resides. The `S`{.interpreted-text role="term"} variable points to this area for a recipe\'s unpacked source code. The name of that directory for any given recipe is defined from several different variables. The preceding figure and the following list describe the `Build Directory`{.interpreted-text role="term"}\'s hierarchy:

> 每个配方都有一个区域在构建目录中，其中解压缩的源代码所在。S 变量指向这个区域，用于配方的解压缩源代码。对于任何给定的配方，该目录的名称是由几个不同的变量定义的。前面的图和下面的列表描述了构建目录的层次结构：

- `TMPDIR`{.interpreted-text role="term"}: The base directory where the OpenEmbedded build system performs all its work during the build. The default base directory is the `tmp` directory.

> -`TMPDIR`：OpenEmbedded 构建系统在构建过程中执行所有工作的基本目录。默认的基本目录是 `tmp` 目录。

- `PACKAGE_ARCH`{.interpreted-text role="term"}: The architecture of the built package or packages. Depending on the eventual destination of the package or packages (i.e. machine architecture, `Build Host`{.interpreted-text role="term"}, SDK, or specific machine), `PACKAGE_ARCH`{.interpreted-text role="term"} varies. See the variable\'s description for details.

> `PACKAGE_ARCH`{.interpreted-text role="term"}：构建的软件包或软件包的体系结构。根据软件包或软件包最终的目的地（即机器体系结构、`Build Host`{.interpreted-text role="term"}、SDK 或特定机器），`PACKAGE_ARCH`{.interpreted-text role="term"}会有所不同。有关详细信息，请参阅变量的描述。

- `TARGET_OS`{.interpreted-text role="term"}: The operating system of the target device. A typical value would be \"linux\" (e.g. \"qemux86-poky-linux\").

> `- ` TARGET_OS`{.interpreted-text role="term"}：目标设备的操作系统。典型值为“linux”（例如“qemux86-poky-linux”）。

- `PN`{.interpreted-text role="term"}: The name of the recipe used to build the package. This variable can have multiple meanings. However, when used in the context of input files, `PN`{.interpreted-text role="term"} represents the name of the recipe.

> `- PN：用于构建软件包的配方的名称。此变量可以有多种含义。但是，在输入文件的上下文中，PN代表配方的名称。`

- `WORKDIR`{.interpreted-text role="term"}: The location where the OpenEmbedded build system builds a recipe (i.e. does the work to create the package).

> `WORKDIR`：OpenEmbedded 构建系统构建配方（即创建软件包）的位置。

- `PV`{.interpreted-text role="term"}: The version of the recipe used to build the package.
- `PR`{.interpreted-text role="term"}: The revision of the recipe used to build the package.
- `S`{.interpreted-text role="term"}: Contains the unpacked source files for a given recipe.

  - `BPN`{.interpreted-text role="term"}: The name of the recipe used to build the package. The `BPN`{.interpreted-text role="term"} variable is a version of the `PN`{.interpreted-text role="term"} variable but with common prefixes and suffixes removed.

> BPN：用于构建软件包的配方的名称。BPN 变量是 PN 变量的一个版本，但是已经移除了常见的前缀和后缀。

- `PV`{.interpreted-text role="term"}: The version of the recipe used to build the package.

::: note
::: title
Note
:::

In the previous figure, notice that there are two sample hierarchies: one based on package architecture (i.e. `PACKAGE_ARCH`{.interpreted-text role="term"}) and one based on a machine (i.e. `MACHINE`{.interpreted-text role="term"}). The underlying structures are identical. The differentiator being what the OpenEmbedded build system is using as a build target (e.g. general architecture, a build host, an SDK, or a specific machine).

> 在上一张图中，可以看到有两个样本层次结构：一个基于包架构（即 `PACKAGE_ARCH`{.interpreted-text role="term"}），另一个基于机器（即 `MACHINE`{.interpreted-text role="term"}）。两者的基础结构是相同的。区别在于 OpenEmbedded 构建系统使用什么作为构建目标（例如通用架构、构建主机、SDK 或特定机器）。
> :::

### Patching

Once source code is fetched and unpacked, BitBake locates patch files and applies them to the source files:

![image](figures/patching.png){width="100.0%"}

The `ref-tasks-patch`{.interpreted-text role="ref"} task uses a recipe\'s `SRC_URI`{.interpreted-text role="term"} statements and the `FILESPATH`{.interpreted-text role="term"} variable to locate applicable patch files.

> 任务 `ref-tasks-patch`{.interpreted-text role="ref"}使用配方的 `SRC_URI`{.interpreted-text role="term"}声明和 `FILESPATH`{.interpreted-text role="term"}变量来定位适用的补丁文件。

Default processing for patch files assumes the files have either `*.patch` or `*.diff` file types. You can use `SRC_URI`{.interpreted-text role="term"} parameters to change the way the build system recognizes patch files. See the `ref-tasks-patch`{.interpreted-text role="ref"} task for more information.

> 默认处理补丁文件假定文件具有 `*.patch` 或 `*.diff` 文件类型。 您可以使用 `SRC_URI`{.interpreted-text role="term"}参数来更改构建系统识别补丁文件的方式。 有关详细信息，请参阅 `ref-tasks-patch`{.interpreted-text role="ref"}任务。

BitBake finds and applies multiple patches for a single recipe in the order in which it locates the patches. The `FILESPATH`{.interpreted-text role="term"} variable defines the default set of directories that the build system uses to search for patch files. Once found, patches are applied to the recipe\'s source files, which are located in the `S`{.interpreted-text role="term"} directory.

> BitBake 按照它找到补丁的顺序，为单个配方应用多个补丁。`FILESPATH` 变量定义了构建系统用来搜索补丁文件的默认集合的目录。一旦找到，补丁将应用于配方的源文件，这些文件位于 `S` 目录中。

For more information on how the source directories are created, see the \"`overview-manual/concepts:source fetching`{.interpreted-text role="ref"}\" section. For more information on how to create patches and how the build system processes patches, see the \"`dev-manual/new-recipe:patching code`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual. You can also see the \"``sdk-manual/extensible:use \`\`devtool modify\`\` to modify the source of an existing component``{.interpreted-text role="ref"}\" section in the Yocto Project Application Development and the Extensible Software Development Kit (SDK) manual and the \"`kernel-dev/common:using traditional kernel development to patch the kernel`{.interpreted-text role="ref"}\" section in the Yocto Project Linux Kernel Development Manual.

> 关于如何创建源目录的更多信息，请参见 Yocto 项目开发任务手册中的“overview-manual/concepts:source fetching”部分。有关如何创建补丁以及构建系统如何处理补丁的更多信息，请参见 Yocto 项目开发任务手册中的“dev-manual/new-recipe:patching code”部分。您还可以参见 Yocto 项目应用开发和可扩展软件开发套件（SDK）手册中的“sdk-manual/extensible:use `devtool modify` to modify the source of an existing component”部分，以及 Yocto 项目 Linux 内核开发手册中的“kernel-dev/common:using traditional kernel development to patch the kernel”部分。

### Configuration, Compilation, and Staging

After source code is patched, BitBake executes tasks that configure and compile the source code. Once compilation occurs, the files are copied to a holding area (staged) in preparation for packaging:

> 在源代码补丁完成后，BitBake 执行任务来配置和编译源代码。编译完成后，文件会被复制到一个保存区域（暂存），准备进行打包。

![image](figures/configuration-compile-autoreconf.png){width="100.0%"}

This step in the build process consists of the following tasks:

- `ref-tasks-prepare_recipe_sysroot`{.interpreted-text role="ref"}: This task sets up the two sysroots in `${``WORKDIR`{.interpreted-text role="term"}`}` (i.e. `recipe-sysroot` and `recipe-sysroot-native`) so that during the packaging phase the sysroots can contain the contents of the `ref-tasks-populate_sysroot`{.interpreted-text role="ref"} tasks of the recipes on which the recipe containing the tasks depends. A sysroot exists for both the target and for the native binaries, which run on the host system.

> 这个任务在 `${WORKDIR}` 中设置了两个 sysroots（即 recipe-sysroot 和 recipe-sysroot-native），以便在打包阶段，sysroots 可以包含包含任务的食谱所依赖的 ref-tasks-populate_sysroot 任务的内容。无论是目标还是本机二进制文件，都有一个 sysroot 存在于主机系统上。

- *do_configure*: This task configures the source by enabling and disabling any build-time and configuration options for the software being built. Configurations can come from the recipe itself as well as from an inherited class. Additionally, the software itself might configure itself depending on the target for which it is being built.

> - *do_configure*：此任务通过启用和禁用正在构建的软件的任何构建时和配置选项来配置源。配置可以来自配方本身以及从继承的类中获得。此外，软件本身可能根据要构建的目标进行配置。

The configurations handled by the `ref-tasks-configure`{.interpreted-text role="ref"} task are specific to configurations for the source code being built by the recipe.

> 配置由 `ref-tasks-configure`{.interpreted-text role="ref"}任务处理，这些配置特定于食谱构建的源代码的配置。

If you are using the `ref-classes-autotools`{.interpreted-text role="ref"} class, you can add additional configuration options by using the `EXTRA_OECONF`{.interpreted-text role="term"} or `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"} variables. For information on how this variable works within that class, see the `ref-classes-autotools`{.interpreted-text role="ref"} class :yocto\_[git:%60here](git:%60here) \</poky/tree/meta/classes-recipe/autotools.bbclass\>\`.

> 如果您使用 `ref-classes-autotools`{.interpreted-text role="ref"}类，您可以通过使用 `EXTRA_OECONF`{.interpreted-text role="term"}或 `PACKAGECONFIG_CONFARGS`{.interpreted-text role="term"}变量添加其他配置选项。有关该变量如何在该类中工作的信息，请参见 `ref-classes-autotools`{.interpreted-text role="ref"}类：yocto\_[git:%60here](git:%60here) \</poky/tree/meta/classes-recipe/autotools.bbclass\>\`.

- *do_compile*: Once a configuration task has been satisfied, BitBake compiles the source using the `ref-tasks-compile`{.interpreted-text role="ref"} task. Compilation occurs in the directory pointed to by the `B`{.interpreted-text role="term"} variable. Realize that the `B`{.interpreted-text role="term"} directory is, by default, the same as the `S`{.interpreted-text role="term"} directory.

> 一旦配置任务已经满足，BitBake 使用 `ref-tasks-compile`{.interpreted-text role="ref"}任务编译源代码。编译在 `B`{.interpreted-text role="term"}变量指向的目录中进行。请注意，默认情况下，`B`{.interpreted-text role="term"}目录与 `S`{.interpreted-text role="term"}目录相同。

- *do_install*: After compilation completes, BitBake executes the `ref-tasks-install`{.interpreted-text role="ref"} task. This task copies files from the `B`{.interpreted-text role="term"} directory and places them in a holding area pointed to by the `D`{.interpreted-text role="term"} variable. Packaging occurs later using files from this holding directory.

> 编译完成后，BitBake 执行 `ref-tasks-install` 任务。该任务将文件从 `B` 目录复制到由 `D` 变量指向的一个保留区域。稍后使用此保留目录中的文件进行打包。

### Package Splitting

After source code is configured, compiled, and staged, the build system analyzes the results and splits the output into packages:

![image](figures/analysis-for-package-splitting.png){width="100.0%"}

The `ref-tasks-package`{.interpreted-text role="ref"} and `ref-tasks-packagedata`{.interpreted-text role="ref"} tasks combine to analyze the files found in the `D`{.interpreted-text role="term"} directory and split them into subsets based on available packages and files. Analysis involves the following as well as other items: splitting out debugging symbols, looking at shared library dependencies between packages, and looking at package relationships.

> `ref-tasks-package`{.interpreted-text role="ref"}和 `ref-tasks-packagedata`{.interpreted-text role="ref"}任务结合在一起，分析 `D`{.interpreted-text role="term"}目录中的文件，并根据可用的软件包和文件将它们分割成子集。 分析还包括以下内容以及其他内容：拆分调试符号，查看软件包之间的共享库依赖关系以及查看软件包关系。

The `ref-tasks-packagedata`{.interpreted-text role="ref"} task creates package metadata based on the analysis such that the build system can generate the final packages. The `ref-tasks-populate_sysroot`{.interpreted-text role="ref"} task stages (copies) a subset of the files installed by the `ref-tasks-install`{.interpreted-text role="ref"} task into the appropriate sysroot. Working, staged, and intermediate results of the analysis and package splitting process use several areas:

> 任务 `ref-tasks-packagedata`{.interpreted-text role="ref"}根据分析创建包元数据，以便构建系统可以生成最终包。任务 `ref-tasks-populate_sysroot`{.interpreted-text role="ref"}将任务 `ref-tasks-install`{.interpreted-text role="ref"}安装的一小部分文件拷贝到适当的 sysroot 中。分析和包拆分过程的工作、阶段和中间结果使用几个区域：

- `PKGD`{.interpreted-text role="term"}: The destination directory (i.e. `package`) for packages before they are split into individual packages.
- `PKGDESTWORK`{.interpreted-text role="term"}: A temporary work area (i.e. `pkgdata`) used by the `ref-tasks-package`{.interpreted-text role="ref"} task to save package metadata.

> PKGDESTWORK：`ref-tasks-package` 任务用来保存包元数据的临时工作区（即 `pkgdata`）。

- `PKGDEST`{.interpreted-text role="term"}: The parent directory (i.e. `packages-split`) for packages after they have been split.
- `PKGDATA_DIR`{.interpreted-text role="term"}: A shared, global-state directory that holds packaging metadata generated during the packaging process. The packaging process copies metadata from `PKGDESTWORK`{.interpreted-text role="term"} to the `PKGDATA_DIR`{.interpreted-text role="term"} area where it becomes globally available.

> `PKGDATA_DIR`{.interpreted-text role="term"}：在打包过程中生成的打包元数据的共享全局状态目录。打包过程将元数据从 `PKGDESTWORK`{.interpreted-text role="term"}复制到 `PKGDATA_DIR`{.interpreted-text role="term"}区域，在此变成全局可用的。

- `STAGING_DIR_HOST`{.interpreted-text role="term"}: The path for the sysroot for the system on which a component is built to run (i.e. `recipe-sysroot`).

> `-` STAGING_DIR_HOST `：用于构建组件运行的系统的sysroot路径（即` recipe-sysroot`）。

- `STAGING_DIR_NATIVE`{.interpreted-text role="term"}: The path for the sysroot used when building components for the build host (i.e. `recipe-sysroot-native`).

> - `STAGING_DIR_NATIVE`：用于构建构建主机上的组件时使用的 sysroot 的路径（即 `recipe-sysroot-native`）。

- `STAGING_DIR_TARGET`{.interpreted-text role="term"}: The path for the sysroot used when a component that is built to execute on a system and it generates code for yet another machine (e.g. `ref-classes-cross-canadian`{.interpreted-text role="ref"} recipes).

> `STAGING_DIR_TARGET`：用于构建用于在系统上执行并且为另一台机器生成代码（例如 `ref-classes-cross-canadian` 食谱）的组件时使用的 sysroot 的路径。

The `FILES`{.interpreted-text role="term"} variable defines the files that go into each package in `PACKAGES`{.interpreted-text role="term"}. If you want details on how this is accomplished, you can look at :yocto\_[git:%60package.bbclass](git:%60package.bbclass) \</poky/tree/meta/classes-global/package.bbclass\>\`.

> `FILES`{.interpreted-text role="term"}变量定义了每个 `PACKAGES`{.interpreted-text role="term"}包中的文件。如果您想了解有关如何实现此目的的详细信息，您可以查看：yocto_[git：%60package.bbclass](git%EF%BC%9A%60package.bbclass) \</poky/tree/meta/classes-global/package.bbclass\>\`。

Depending on the type of packages being created (RPM, DEB, or IPK), the `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"} task creates the actual packages and places them in the Package Feed area, which is `${TMPDIR}/deploy`. You can see the \"`overview-manual/concepts:package feeds`{.interpreted-text role="ref"}\" section for more detail on that part of the build process.

> 根據要創建的包類型（RPM、DEB 或 IPK），`do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"}任務將創建實際的包，並將它們放置在 Package Feed 區域中，該區域為 `${TMPDIR}/deploy`。有關構建過程的更多細節，請參閱“`overview-manual/concepts:package feeds`{.interpreted-text role="ref"}”部分。

::: note
::: title
Note
:::

Support for creating feeds directly from the `deploy/*` directories does not exist. Creating such feeds usually requires some kind of feed maintenance mechanism that would upload the new packages into an official package feed (e.g. the Ångström distribution). This functionality is highly distribution-specific and thus is not provided out of the box.

> 不支持直接从'deploy/*'目录创建源。创建这样的源通常需要某种类型的源维护机制，可以将新的软件包上传到正式的软件包源（例如 Ångström 发行版）中。这个功能高度依赖于发行版，因此不会提供开箱即用的功能。
> :::

### Image Generation

Once packages are split and stored in the Package Feeds area, the build system uses BitBake to generate the root filesystem image:

![image](figures/image-generation.png){width="100.0%"}

The image generation process consists of several stages and depends on several tasks and variables. The `ref-tasks-rootfs`{.interpreted-text role="ref"} task creates the root filesystem (file and directory structure) for an image. This task uses several key variables to help create the list of packages to actually install:

> 图像生成过程包括几个阶段，并取决于几项任务和变量。`ref-tasks-rootfs`{.interpreted-text role="ref"} 任务为图像创建根文件系统（文件和目录结构）。该任务使用几个关键变量来帮助创建要实际安装的软件包列表。

- `IMAGE_INSTALL`{.interpreted-text role="term"}: Lists out the base set of packages from which to install from the Package Feeds area.
- `PACKAGE_EXCLUDE`{.interpreted-text role="term"}: Specifies packages that should not be installed into the image.
- `IMAGE_FEATURES`{.interpreted-text role="term"}: Specifies features to include in the image. Most of these features map to additional packages for installation.

> `图像特征`：指定要包含在图像中的特征。大多数这些特性映射到需要安装的附加包。

- `PACKAGE_CLASSES`{.interpreted-text role="term"}: Specifies the package backend (e.g. RPM, DEB, or IPK) to use and consequently helps determine where to locate packages within the Package Feeds area.

> `-` PACKAGE_CLASSES{.interpreted-text role="term"}：指定要使用的包后端（例如 RPM、DEB 或 IPK），从而有助于确定在 Package Feeds 区域中查找包的位置。

- `IMAGE_LINGUAS`{.interpreted-text role="term"}: Determines the language(s) for which additional language support packages are installed.
- `PACKAGE_INSTALL`{.interpreted-text role="term"}: The final list of packages passed to the package manager for installation into the image.

With `IMAGE_ROOTFS`{.interpreted-text role="term"} pointing to the location of the filesystem under construction and the `PACKAGE_INSTALL`{.interpreted-text role="term"} variable providing the final list of packages to install, the root file system is created.

> 随着 `IMAGE_ROOTFS` 指向正在构建的文件系统的位置，以及 `PACKAGE_INSTALL` 变量提供最终要安装的软件包列表，根文件系统就被创建了。

Package installation is under control of the package manager (e.g. dnf/rpm, opkg, or apt/dpkg) regardless of whether or not package management is enabled for the target. At the end of the process, if package management is not enabled for the target, the package manager\'s data files are deleted from the root filesystem. As part of the final stage of package installation, post installation scripts that are part of the packages are run. Any scripts that fail to run on the build host are run on the target when the target system is first booted. If you are using a `read-only root filesystem <dev-manual/read-only-rootfs:creating a read-only root filesystem>`{.interpreted-text role="ref"}, all the post installation scripts must succeed on the build host during the package installation phase since the root filesystem on the target is read-only.

> 安装包受包管理器（例如 dnf/rpm、opkg 或 apt/dpkg）的控制，无论目标是否启用了包管理。在处理完成后，如果目标没有启用包管理，包管理器的数据文件将从根文件系统中删除。作为安装包安装的最后一个阶段，将运行作为包一部分的安装后脚本。在构建主机上运行失败的任何脚本都将在目标系统首次启动时运行。如果您使用的是“只读根文件系统”，则所有安装后脚本都必须在包安装阶段在构建主机上成功，因为目标上的根文件系统是只读的。

The final stages of the `ref-tasks-rootfs`{.interpreted-text role="ref"} task handle post processing. Post processing includes creation of a manifest file and optimizations.

> 最终阶段的 `ref-tasks-rootfs`{.interpreted-text role="ref"}任务处理后期处理。后期处理包括创建清单文件和优化。

The manifest file (`.manifest`) resides in the same directory as the root filesystem image. This file lists out, line-by-line, the installed packages. The manifest file is useful for the `ref-classes-testimage`{.interpreted-text role="ref"} class, for example, to determine whether or not to run specific tests. See the `IMAGE_MANIFEST`{.interpreted-text role="term"} variable for additional information.

> manifest 文件（`.manifest`）位于与根文件系统映像相同的目录中。该文件列出了已安装的软件包，每行一个。manifest 文件对于 `ref-classes-testimage`{.interpreted-text role="ref"}类来说很有用，例如，可以用来确定是否要运行特定测试。有关更多信息，请参阅 `IMAGE_MANIFEST`{.interpreted-text role="term"}变量。

Optimizing processes that are run across the image include `mklibs` and any other post-processing commands as defined by the `ROOTFS_POSTPROCESS_COMMAND`{.interpreted-text role="term"} variable. The `mklibs` process optimizes the size of the libraries.

> 优化跨图像运行的进程包括 `mklibs` 和由 `ROOTFS_POSTPROCESS_COMMAND` 变量定义的任何其他后处理命令。`mklibs` 进程优化了库的大小。

After the root filesystem is built, processing begins on the image through the `ref-tasks-image`{.interpreted-text role="ref"} task. The build system runs any pre-processing commands as defined by the `IMAGE_PREPROCESS_COMMAND`{.interpreted-text role="term"} variable. This variable specifies a list of functions to call before the build system creates the final image output files.

> 当根文件系统构建完成后，通过 `ref-tasks-image`{.interpreted-text role="ref"} 任务开始处理映像。构建系统运行由 `IMAGE_PREPROCESS_COMMAND`{.interpreted-text role="term"} 变量定义的任何预处理命令。此变量指定在构建系统创建最终映像输出文件之前要调用的函数列表。

The build system dynamically creates `do_image_* <ref-tasks-image>`{.interpreted-text role="ref"} tasks as needed, based on the image types specified in the `IMAGE_FSTYPES`{.interpreted-text role="term"} variable. The process turns everything into an image file or a set of image files and can compress the root filesystem image to reduce the overall size of the image. The formats used for the root filesystem depend on the `IMAGE_FSTYPES`{.interpreted-text role="term"} variable. Compression depends on whether the formats support compression.

> 系统构建根据 `IMAGE_FSTYPES` 变量中指定的图像类型动态创建 `do_image_* <ref-tasks-image>` 任务。该过程将所有内容转换为图像文件或一组图像文件，并可以压缩根文件系统映像以减少映像的整体大小。根文件系统的格式取决于 `IMAGE_FSTYPES` 变量。压缩取决于格式是否支持压缩。

As an example, a dynamically created task when creating a particular image type would take the following form:

```
do_image_type
```

So, if the type as specified by the `IMAGE_FSTYPES`{.interpreted-text role="term"} were `ext4`, the dynamically generated task would be as follows:

```
do_image_ext4
```

The final task involved in image creation is the `do_image_complete <ref-tasks-image-complete>`{.interpreted-text role="ref"} task. This task completes the image by applying any image post processing as defined through the `IMAGE_POSTPROCESS_COMMAND`{.interpreted-text role="term"} variable. The variable specifies a list of functions to call once the build system has created the final image output files.

> 最后一项任务涉及图像创建是 `do_image_complete <ref-tasks-image-complete>`{.interpreted-text role="ref"} 任务。 此任务通过应用通过 `IMAGE_POSTPROCESS_COMMAND`{.interpreted-text role="term"} 变量定义的任何图像后期处理来完成图像。 该变量指定在构建系统创建最终图像输出文件后调用的函数列表。

::: note
::: title
Note
:::

The entire image generation process is run under Pseudo. Running under Pseudo ensures that the files in the root filesystem have correct ownership.
:::

### SDK Generation

The OpenEmbedded build system uses BitBake to generate the Software Development Kit (SDK) installer scripts for both the standard SDK and the extensible SDK (eSDK):

> 开放式嵌入式构建系统使用 BitBake 生成标准 SDK 和可扩展 SDK（eSDK）的软件开发工具包（SDK）安装脚本：

![image](figures/sdk-generation.png){width="100.0%"}

::: note
::: title
Note
:::

For more information on the cross-development toolchain generation, see the \"`overview-manual/concepts:cross-development toolchain generation`{.interpreted-text role="ref"}\" section. For information on advantages gained when building a cross-development toolchain using the `ref-tasks-populate_sdk`{.interpreted-text role="ref"} task, see the \"`sdk-manual/appendix-obtain:building an sdk installer`{.interpreted-text role="ref"}\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.

> 欲了解有关跨开发工具链生成的更多信息，请参见“概述手册/概念：跨开发工具链生成”部分。要了解使用 `ref-tasks-populate_sdk`{.interpreted-text role="ref"}任务构建跨开发工具链时获得的优势，请参阅 Yocto 项目应用程序开发和可扩展软件开发工具包（eSDK）手册中的“SDK 手册/附录：构建 SDK 安装程序”部分。
> :::

Like image generation, the SDK script process consists of several stages and depends on many variables. The `ref-tasks-populate_sdk`{.interpreted-text role="ref"} and `ref-tasks-populate_sdk_ext`{.interpreted-text role="ref"} tasks use these key variables to help create the list of packages to actually install. For information on the variables listed in the figure, see the \"`overview-manual/concepts:application development sdk`{.interpreted-text role="ref"}\" section.

> 像图像生成一样，SDK 脚本处理包括几个阶段，取决于许多变量。 `ref-tasks-populate_sdk`{.interpreted-text role="ref"} 和 `ref-tasks-populate_sdk_ext`{.interpreted-text role="ref"} 任务使用这些关键变量来帮助创建要实际安装的软件包列表。 有关图中列出的变量的信息，请参见“`overview-manual/concepts:application development sdk`{.interpreted-text role="ref"}”部分。

The `ref-tasks-populate_sdk`{.interpreted-text role="ref"} task helps create the standard SDK and handles two parts: a target part and a host part. The target part is the part built for the target hardware and includes libraries and headers. The host part is the part of the SDK that runs on the `SDKMACHINE`{.interpreted-text role="term"}.

> 任务 `ref-tasks-populate_sdk`{.interpreted-text role="ref"}有助于创建标准 SDK，并处理两部分：目标部分和主机部分。目标部分是为目标硬件构建的部分，包括库和头文件。主机部分是运行在 `SDKMACHINE`{.interpreted-text role="term"}上的 SDK 的一部分。

The `ref-tasks-populate_sdk_ext`{.interpreted-text role="ref"} task helps create the extensible SDK and handles host and target parts differently than its counter part does for the standard SDK. For the extensible SDK, the task encapsulates the build system, which includes everything needed (host and target) for the SDK.

> 任务 `ref-tasks-populate_sdk_ext`{.interpreted-text role="ref"}有助于创建可扩展的 SDK，与标准 SDK 的对应部分不同，它以不同的方式处理主机和目标部分。对于可扩展的 SDK，该任务封装了构建系统，其中包括 SDK 所需的一切（主机和目标）。

Regardless of the type of SDK being constructed, the tasks perform some cleanup after which a cross-development environment setup script and any needed configuration files are created. The final output is the Cross-development toolchain installation script (`.sh` file), which includes the environment setup script.

> 不管构建的是什么类型的 SDK，任务都会先进行清理，然后创建跨开发环境设置脚本和任何需要的配置文件。最终输出是跨开发工具链安装脚本（`.sh` 文件），其中包括环境设置脚本。

### Stamp Files and the Rerunning of Tasks

For each task that completes successfully, BitBake writes a stamp file into the `STAMPS_DIR`{.interpreted-text role="term"} directory. The beginning of the stamp file\'s filename is determined by the `STAMP`{.interpreted-text role="term"} variable, and the end of the name consists of the task\'s name and current `input checksum <overview-manual/concepts:checksums (signatures)>`{.interpreted-text role="ref"}.

> 每当一项任务成功完成时，BitBake 会在 `STAMPS_DIR` 目录中写入一个印记文件。印记文件名的开头由 `STAMP` 变量确定，结尾由任务的名称和当前的输入校验和组成 <overview-manual/concepts:checksums (signatures)>。

::: note
::: title
Note
:::

This naming scheme assumes that `BB_SIGNATURE_HANDLER`{.interpreted-text role="term"} is \"OEBasicHash\", which is almost always the case in current OpenEmbedded.

> 这个命名方案假定 `BB_SIGNATURE_HANDLER` 是"OEBasicHash"，在当前的 OpenEmbedded 中几乎总是这样。
> :::

To determine if a task needs to be rerun, BitBake checks if a stamp file with a matching input checksum exists for the task. In this case, the task\'s output is assumed to exist and still be valid. Otherwise, the task is rerun.

> 为了确定是否需要重新运行任务，BitBake 检查是否存在与输入校验和匹配的戳文件。在这种情况下，假定任务的输出已经存在且仍然有效。否则，将重新运行任务。

::: note
::: title
Note
:::

The stamp mechanism is more general than the shared state (sstate) cache mechanism described in the \"`overview-manual/concepts:setscene tasks and shared state`{.interpreted-text role="ref"}\" section. BitBake avoids rerunning any task that has a valid stamp file, not just tasks that can be accelerated through the sstate cache.

> 印记机制比在“概览手册/概念：设置场景任务和共享状态”部分描述的共享状态（sstate）缓存机制更加通用。BitBake 避免重新运行具有有效印记文件的任何任务，而不仅仅是可以通过 sstate 缓存加速的任务。

However, you should realize that stamp files only serve as a marker that some work has been done and that these files do not record task output. The actual task output would usually be somewhere in `TMPDIR`{.interpreted-text role="term"} (e.g. in some recipe\'s `WORKDIR`{.interpreted-text role="term"}.) What the sstate cache mechanism adds is a way to cache task output that can then be shared between build machines.

> 然而，你应该意识到，印花文件只是作为一个标记，表明已经完成了一些工作，而这些文件不记录任务输出。实际的任务输出通常会在 `TMPDIR` 中（例如某个食谱的 `WORKDIR` 中）。sstate 缓存机制带来的是一种缓存任务输出的方式，然后可以在构建机器之间共享。
> :::

Since `STAMPS_DIR`{.interpreted-text role="term"} is usually a subdirectory of `TMPDIR`{.interpreted-text role="term"}, removing `TMPDIR`{.interpreted-text role="term"} will also remove `STAMPS_DIR`{.interpreted-text role="term"}, which means tasks will properly be rerun to repopulate `TMPDIR`{.interpreted-text role="term"}.

> 由于 `STAMPS_DIR` 通常是 `TMPDIR` 的子目录，因此删除 `TMPDIR` 也会删除 `STAMPS_DIR`，这意味着任务将被重新运行以重新填充 `TMPDIR`。

If you want some task to always be considered \"out of date\", you can mark it with the `nostamp <bitbake-user-manual/bitbake-user-manual-metadata:variable flags>`{.interpreted-text role="ref"} varflag. If some other task depends on such a task, then that task will also always be considered out of date, which might not be what you want.

> 如果你想要某个任务总是被视为“过期”，你可以用 `nostamp <bitbake-user-manual/bitbake-user-manual-metadata:variable flags>`{.interpreted-text role="ref"} varflag 来标记它。如果其他任务依赖于这样的任务，那么这个任务也会被视为过期，这可能不是你想要的结果。

For details on how to view information about a task\'s signature, see the \"`dev-manual/debugging:viewing task variable dependencies`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 要了解有关任务签名的更多信息，请参见 Yocto 项目开发任务手册中的“dev-manual / debugging：查看任务变量依赖关系”部分。

### Setscene Tasks and Shared State

The description of tasks so far assumes that BitBake needs to build everything and no available prebuilt objects exist. BitBake does support skipping tasks if prebuilt objects are available. These objects are usually made available in the form of a shared state (sstate) cache.

> 迄今为止的任务描述假设 BitBake 需要构建所有内容，并且不存在可用的预构建对象。BitBake 确实支持在可用的预构建对象时跳过任务。这些对象通常以共享状态（sstate）缓存的形式提供。

::: note
::: title
Note
:::

For information on variables affecting sstate, see the `SSTATE_DIR`{.interpreted-text role="term"} and `SSTATE_MIRRORS`{.interpreted-text role="term"} variables.

> 对于影响 sstate 的变量信息，请参阅 `SSTATE_DIR`{.interpreted-text role="term"}和 `SSTATE_MIRRORS`{.interpreted-text role="term"}变量。
> :::

The idea of a setscene task (i.e `do_taskname_setscene`) is a version of the task where instead of building something, BitBake can skip to the end result and simply place a set of files into specific locations as needed. In some cases, it makes sense to have a setscene task variant (e.g. generating package files in the `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"} task). In other cases, it does not make sense (e.g. a `ref-tasks-patch`{.interpreted-text role="ref"} task or a `ref-tasks-unpack`{.interpreted-text role="ref"} task) since the work involved would be equal to or greater than the underlying task.

> 提出一个 setscene 任务（即 `do_taskname_setscene`）的想法是，与其建立某物，BitBake 可以跳到结果，并将一组文件放置到特定位置。在某些情况下，有一个 setscene 任务变体是有意义的（例如在 `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"}任务中生成包文件）。在其他情况下，这是没有意义的（例如 `ref-tasks-patch`{.interpreted-text role="ref"}任务或 `ref-tasks-unpack`{.interpreted-text role="ref"}任务），因为所涉及的工作量等于或大于底层任务。

In the build system, the common tasks that have setscene variants are `ref-tasks-package`{.interpreted-text role="ref"}, `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"}, `ref-tasks-deploy`{.interpreted-text role="ref"}, `ref-tasks-packagedata`{.interpreted-text role="ref"}, and `ref-tasks-populate_sysroot`{.interpreted-text role="ref"}. Notice that these tasks represent most of the tasks whose output is an end result.

> 在构建系统中，具有 setscene 变体的常见任务是 `ref-tasks-package`{.interpreted-text role="ref"}、`do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"}、`ref-tasks-deploy`{.interpreted-text role="ref"}、`ref-tasks-packagedata`{.interpreted-text role="ref"}和 `ref-tasks-populate_sysroot`{.interpreted-text role="ref"}。请注意，这些任务代表了大多数输出为最终结果的任务。

The build system has knowledge of the relationship between these tasks and other preceding tasks. For example, if BitBake runs `do_populate_sysroot_setscene` for something, it does not make sense to run any of the `ref-tasks-fetch`{.interpreted-text role="ref"}, `ref-tasks-unpack`{.interpreted-text role="ref"}, `ref-tasks-patch`{.interpreted-text role="ref"}, `ref-tasks-configure`{.interpreted-text role="ref"}, `ref-tasks-compile`{.interpreted-text role="ref"}, and `ref-tasks-install`{.interpreted-text role="ref"} tasks. However, if `ref-tasks-package`{.interpreted-text role="ref"} needs to be run, BitBake needs to run those other tasks.

> 编译系统了解这些任务与其他前面的任务之间的关系。例如，如果 BitBake 对某事运行 `do_populate_sysroot_setscene`，运行 `ref-tasks-fetch`{.interpreted-text role="ref"}、`ref-tasks-unpack`{.interpreted-text role="ref"}、`ref-tasks-patch`{.interpreted-text role="ref"}、`ref-tasks-configure`{.interpreted-text role="ref"}、`ref-tasks-compile`{.interpreted-text role="ref"}和 `ref-tasks-install`{.interpreted-text role="ref"}任务是没有意义的。但是，如果需要运行 `ref-tasks-package`{.interpreted-text role="ref"}，BitBake 需要运行其他任务。

It becomes more complicated if everything can come from an sstate cache because some objects are simply not required at all. For example, you do not need a compiler or native tools, such as quilt, if there isn\'t anything to compile or patch. If the `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"} packages are available from sstate, BitBake does not need the `ref-tasks-package`{.interpreted-text role="ref"} task data.

> 如果所有东西都可以从 sstate 缓存中获取，情况就变得更复杂，因为有些对象根本不需要。例如，如果没有任何东西需要编译或补丁，则不需要编译器或本机工具（如 quilt）。如果 `do_package_write_* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"} 包可以从 sstate 中获取，BitBake 就不需要 `ref-tasks-package`{.interpreted-text role="ref"} 任务数据了。

To handle all these complexities, BitBake runs in two phases. The first is the \"setscene\" stage. During this stage, BitBake first checks the sstate cache for any targets it is planning to build. BitBake does a fast check to see if the object exists rather than doing a complete download. If nothing exists, the second phase, which is the setscene stage, completes and the main build proceeds.

> 为了处理这些复杂性，BitBake 运行在两个阶段。第一个是“setscene”阶段。在这个阶段，BitBake 首先检查 sstate 缓存，看它要构建的任何目标。BitBake 进行快速检查，看看对象是否存在，而不是完成下载。如果什么都不存在，第二阶段，即 setscene 阶段，完成后，主要构建继续进行。

If objects are found in the sstate cache, the build system works backwards from the end targets specified by the user. For example, if an image is being built, the build system first looks for the packages needed for that image and the tools needed to construct an image. If those are available, the compiler is not needed. Thus, the compiler is not even downloaded. If something was found to be unavailable, or the download or setscene task fails, the build system then tries to install dependencies, such as the compiler, from the cache.

> 如果在 sstate 缓存中发现了对象，构建系统将从用户指定的最终目标开始倒推。例如，如果正在构建图像，构建系统会首先查找该图像所需的软件包和构建图像所需的工具。如果这些都可用，则不需要编译器。因此，甚至不会下载编译器。如果发现某些内容不可用，或者下载或 setscene 任务失败，构建系统将尝试从缓存中安装依赖项，例如编译器。

The availability of objects in the sstate cache is handled by the function specified by the `BB_HASHCHECK_FUNCTION`{.interpreted-text role="term"} variable and returns a list of available objects. The function specified by the `BB_SETSCENE_DEPVALID`{.interpreted-text role="term"} variable is the function that determines whether a given dependency needs to be followed, and whether for any given relationship the function needs to be passed. The function returns a True or False value.

> 可用对象的可用性在 sstate 缓存中由 `BB_HASHCHECK_FUNCTION` 变量指定的函数处理，并返回可用对象的列表。`BB_SETSCENE_DEPVALID` 变量指定的函数确定是否需要遵循给定的依赖关系，以及是否需要传递给任何给定关系的函数。该函数返回 True 或 False 值。

## Images

The images produced by the build system are compressed forms of the root filesystem and are ready to boot on a target device. You can see from the `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"} that BitBake output, in part, consists of images. This section takes a closer look at this output:

> 系统构建生成的图像是根文件系统的压缩形式，可以在目标设备上启动。从“一般工作流程图”<overview-manual/concepts:openembedded build system concepts>{.interpreted-text role="ref"}可以看到，BitBake 的输出部分包括图像。本节将对此输出进行更详细的查看：

![image](figures/images.png){.align-center width="75.0%"}

::: note
::: title
Note
:::

For a list of example images that the Yocto Project provides, see the \"`/ref-manual/images`{.interpreted-text role="doc"}\" chapter in the Yocto Project Reference Manual.

> 对于 Yocto 项目提供的示例图像列表，请参阅 Yocto 项目参考手册中的“/ ref-manual/images”章节。
> :::

The build process writes images out to the `Build Directory`{.interpreted-text role="term"} inside the `tmp/deploy/images/machine/` folder as shown in the figure. This folder contains any files expected to be loaded on the target device. The `DEPLOY_DIR`{.interpreted-text role="term"} variable points to the `deploy` directory, while the `DEPLOY_DIR_IMAGE`{.interpreted-text role="term"} variable points to the appropriate directory containing images for the current configuration.

> 构建过程将图像写入图中所示的 `Build Directory`{.interpreted-text role="term"}文件夹中的 `tmp/deploy/images/machine/` 文件夹。此文件夹包含预期加载到目标设备上的任何文件。`DEPLOY_DIR`{.interpreted-text role="term"}变量指向 `deploy` 目录，而 `DEPLOY_DIR_IMAGE`{.interpreted-text role="term"}变量指向包含当前配置图像的适当目录。

- kernel-image: A kernel binary file. The `KERNEL_IMAGETYPE`{.interpreted-text role="term"} variable determines the naming scheme for the kernel image file. Depending on this variable, the file could begin with a variety of naming strings. The `deploy/images/` machine directory can contain multiple image files for the machine.

> 内核镜像：内核二进制文件。`KERNEL_IMAGETYPE`{.interpreted-text role="term"}变量确定内核映像文件的命名方案。根据此变量，文件可以以各种命名字符串开头。机器目录 `deploy/images/` 可以包含多个机器的图像文件。

- root-filesystem-image: Root filesystems for the target device (e.g. `*.ext3` or `*.bz2` files). The `IMAGE_FSTYPES`{.interpreted-text role="term"} variable determines the root filesystem image type. The `deploy/images/` machine directory can contain multiple root filesystems for the machine.

> 根文件系统映像：用于目标设备的根文件系统（例如 `*.ext3` 或 `*.bz2` 文件）。变量 `IMAGE_FSTYPES` 确定根文件系统映像类型。机器目录 `deploy/images/` 可以包含多个机器的根文件系统。

- kernel-modules: Tarballs that contain all the modules built for the kernel. Kernel module tarballs exist for legacy purposes and can be suppressed by setting the `MODULE_TARBALL_DEPLOY`{.interpreted-text role="term"} variable to \"0\". The `deploy/images/` machine directory can contain multiple kernel module tarballs for the machine.

> 模块内核：包含为内核构建的所有模块的压缩包。内核模块压缩包是为了遗留目的而存在，可以通过将 `MODULE_TARBALL_DEPLOY` 变量设置为“0”来禁止它们。机器目录 `deploy/images/` 可以包含机器的多个内核模块压缩包。

- bootloaders: If applicable to the target machine, bootloaders supporting the image. The `deploy/images/` machine directory can contain multiple bootloaders for the machine.

> 如果适用于目标机器，则支持图像的引导程序。`deploy/images/` 机器目录可以包含多个机器的引导程序。

- symlinks: The `deploy/images/` machine folder contains a symbolic link that points to the most recently built file for each machine. These links might be useful for external scripts that need to obtain the latest version of each file.

> `部署/图像/` 机器文件夹中包含一个符号链接，指向每台机器最新构建的文件。这些链接可能对需要获取每个文件最新版本的外部脚本很有用。

## Application Development SDK

In the `general workflow figure <overview-manual/concepts:openembedded build system concepts>`{.interpreted-text role="ref"}, the output labeled \"Application Development SDK\" represents an SDK. The SDK generation process differs depending on whether you build an extensible SDK (e.g. `bitbake -c populate_sdk_ext` imagename) or a standard SDK (e.g. `bitbake -c populate_sdk` imagename). This section takes a closer look at this output:

> 在通用工作流程图（<overview-manual/concepts:openembedded build system concepts>）中，标记为“应用开发 SDK”的输出代表一个 SDK。根据您是构建可扩展 SDK（例如 `bitbake -c populate_sdk_ext` imagename）还是标准 SDK（例如 `bitbake -c populate_sdk` imagename），SDK 生成过程会有所不同。本节将对此输出进行更深入的探讨：

![image](figures/sdk.png){width="100.0%"}

The specific form of this output is a set of files that includes a self-extracting SDK installer (`*.sh`), host and target manifest files, and files used for SDK testing. When the SDK installer file is run, it installs the SDK. The SDK consists of a cross-development toolchain, a set of libraries and headers, and an SDK environment setup script. Running this installer essentially sets up your cross-development environment. You can think of the cross-toolchain as the \"host\" part because it runs on the SDK machine. You can think of the libraries and headers as the \"target\" part because they are built for the target hardware. The environment setup script is added so that you can initialize the environment before using the tools.

> 这种输出的具体形式是一组文件，其中包括一个自解压 SDK 安装程序（`* .sh`），主机和目标清单文件以及用于 SDK 测试的文件。运行 SDK 安装程序文件时，它将安装 SDK。SDK 由跨开发工具链，一组库和头文件以及 SDK 环境设置脚本组成。运行此安装程序本质上是为您设置跨开发环境。您可以将跨工具链视为“主机”部分，因为它运行在 SDK 机器上。您可以将库和头文件视为“目标”部分，因为它们是为目标硬件构建的。添加环境设置脚本，以便在使用工具之前可以初始化环境。

::: note
::: title
Note
:::

- The Yocto Project supports several methods by which you can set up this cross-development environment. These methods include downloading pre-built SDK installers or building and installing your own SDK installer.

> 项目 Yocto 支持几种方法来设置跨平台开发环境。这些方法包括下载预先构建的 SDK 安装程序或构建和安装自己的 SDK 安装程序。

- For background information on cross-development toolchains in the Yocto Project development environment, see the \"`overview-manual/concepts:cross-development toolchain generation`{.interpreted-text role="ref"}\" section.

> 对于 Yocto Project 开发环境中的跨开发工具链的背景信息，请参见“overview-manual/concepts：跨开发工具链生成”部分。

- For information on setting up a cross-development environment, see the `/sdk-manual/index`{.interpreted-text role="doc"} manual.
  :::

All the output files for an SDK are written to the `deploy/sdk` folder inside the `Build Directory`{.interpreted-text role="term"} as shown in the previous figure. Depending on the type of SDK, there are several variables to configure these files. Here are the variables associated with an extensible SDK:

> 所有 SDK 的输出文件都写入前面图中所示的“构建目录”的“部署/ sdk”文件夹中。根据 SDK 的类型，有几个变量可以配置这些文件。以下是与可扩展 SDK 相关的变量：

- `DEPLOY_DIR`{.interpreted-text role="term"}: Points to the `deploy` directory.
- `SDK_EXT_TYPE`{.interpreted-text role="term"}: Controls whether or not shared state artifacts are copied into the extensible SDK. By default, all required shared state artifacts are copied into the SDK.

> `SDK_EXT_TYPE`：控制是否将共享状态工件复制到可扩展 SDK 中。默认情况下，所有必需的共享状态工件都会复制到 SDK 中。

- `SDK_INCLUDE_PKGDATA`{.interpreted-text role="term"}: Specifies whether or not packagedata is included in the extensible SDK for all recipes in the \"world\" target.

> - `SDK_INCLUDE_PKGDATA`：指定是否在"world"目标中的所有配方中的可扩展 SDK 中包括 packagedata。

- `SDK_INCLUDE_TOOLCHAIN`{.interpreted-text role="term"}: Specifies whether or not the toolchain is included when building the extensible SDK.
- `ESDK_LOCALCONF_ALLOW`{.interpreted-text role="term"}: A list of variables allowed through from the build system configuration into the extensible SDK configuration.

> `- ESDK_LOCALCONF_ALLOW`：允许从构建系统配置传入可扩展 SDK 配置的变量列表。

- `ESDK_LOCALCONF_REMOVE`{.interpreted-text role="term"}: A list of variables not allowed through from the build system configuration into the extensible SDK configuration.

> - ESDK_LOCALCONF_REMOVE：从构建系统配置中不允许传递到可扩展 SDK 配置的变量列表。

- `ESDK_CLASS_INHERIT_DISABLE`{.interpreted-text role="term"}: A list of classes to remove from the `INHERIT`{.interpreted-text role="term"} value globally within the extensible SDK configuration.

> - `ESDK_CLASS_INHERIT_DISABLE`：在可扩展 SDK 配置中全局删除 `INHERIT` 值的类列表。

This next list, shows the variables associated with a standard SDK:

- `DEPLOY_DIR`{.interpreted-text role="term"}: Points to the `deploy` directory.
- `SDKMACHINE`{.interpreted-text role="term"}: Specifies the architecture of the machine on which the cross-development tools are run to create packages for the target hardware.

> SDKMACHINE：指定用于为目标硬件创建包的跨开发工具运行的机器架构。

- `SDKIMAGE_FEATURES`{.interpreted-text role="term"}: Lists the features to include in the \"target\" part of the SDK.
- `TOOLCHAIN_HOST_TASK`{.interpreted-text role="term"}: Lists packages that make up the host part of the SDK (i.e. the part that runs on the `SDKMACHINE`{.interpreted-text role="term"}). When you use `bitbake -c populate_sdk imagename` to create the SDK, a set of default packages apply. This variable allows you to add more packages.

> TOOLCHAIN_HOST_TASK：列出构成 SDK 主机部分（即运行在 SDKMACHINE 上的部分）的包。当您使用 bitbake -c populate_sdk imagename 来创建 SDK 时，将应用一组默认包。此变量允许您添加更多包。

- `TOOLCHAIN_TARGET_TASK`{.interpreted-text role="term"}: Lists packages that make up the target part of the SDK (i.e. the part built for the target hardware).

> `TOOLCHAIN_TARGET_TASK`：列出构成 SDK 目标部分（即为目标硬件构建的部分）的软件包。

- `SDKPATH`{.interpreted-text role="term"}: Defines the default SDK installation path offered by the installation script.
- `SDK_HOST_MANIFEST`{.interpreted-text role="term"}: Lists all the installed packages that make up the host part of the SDK. This variable also plays a minor role for extensible SDK development as well. However, it is mainly used for the standard SDK.

> `- SDK_HOST_MANIFEST`：列出所有组成主机部分 SDK 的已安装包。此变量也可以在可扩展 SDK 开发中发挥一定作用。但主要用于标准 SDK。

- `SDK_TARGET_MANIFEST`{.interpreted-text role="term"}: Lists all the installed packages that make up the target part of the SDK. This variable also plays a minor role for extensible SDK development as well. However, it is mainly used for the standard SDK.

> `SDK_TARGET_MANIFEST`：列出组成目标部分 SDK 的所有已安装的软件包。此变量还可以用于可扩展 SDK 开发。但主要用于标准 SDK。

# Cross-Development Toolchain Generation

The Yocto Project does most of the work for you when it comes to creating `sdk-manual/intro:the cross-development toolchain`{.interpreted-text role="ref"}. This section provides some technical background on how cross-development toolchains are created and used. For more information on toolchains, you can also see the `/sdk-manual/index`{.interpreted-text role="doc"} manual.

> 针对创建“sdk-manual/intro：交叉开发工具链”，Yocto 项目为您完成大部分工作。本节提供了有关如何创建和使用交叉开发工具链的技术背景。有关工具链的更多信息，您还可以参阅/sdk-manual/index 手册。

In the Yocto Project development environment, cross-development toolchains are used to build images and applications that run on the target hardware. With just a few commands, the OpenEmbedded build system creates these necessary toolchains for you.

> 在 Yocto 项目开发环境中，使用交叉开发工具链来构建运行在目标硬件上的图像和应用程序。只需要几个命令，OpenEmbedded 构建系统就可以为您创建这些必要的工具链。

The following figure shows a high-level build environment regarding toolchain construction and use.

![image](figures/cross-development-toolchains.png){width="100.0%"}

Most of the work occurs on the Build Host. This is the machine used to build images and generally work within the Yocto Project environment. When you run `BitBake`{.interpreted-text role="term"} to create an image, the OpenEmbedded build system uses the host `gcc` compiler to bootstrap a cross-compiler named `gcc-cross`. The `gcc-cross` compiler is what BitBake uses to compile source files when creating the target image. You can think of `gcc-cross` simply as an automatically generated cross-compiler that is used internally within BitBake only.

> 大部分工作发生在构建主机上。这是用于构建镜像和通常在 Yocto 项目环境中工作的机器。当您运行 `BitBake` 以创建镜像时，OpenEmbedded 构建系统使用主机 `gcc` 编译器来引导名为 `gcc-cross` 的交叉编译器。BitBake 在创建目标镜像时使用 `gcc-cross` 编译器来编译源文件。您可以将 `gcc-cross` 简单地视为 BitBake 内部仅使用的自动生成的交叉编译器。

::: note
::: title
Note
:::

The extensible SDK does not use `gcc-cross-canadian` since this SDK ships a copy of the OpenEmbedded build system and the sysroot within it contains `gcc-cross`.

> 可扩展的 SDK 不使用 `gcc-cross-canadian`，因为该 SDK 附带了一份 OpenEmbedded 构建系统，其中的 sysroot 包含 `gcc-cross`。
> :::

The chain of events that occurs when the standard toolchain is bootstrapped:

```
binutils-cross -> linux-libc-headers -> gcc-cross -> libgcc-initial -> glibc -> libgcc -> gcc-runtime
```

- `gcc`: The compiler, GNU Compiler Collection (GCC).
- `binutils-cross`: The binary utilities needed in order to run the `gcc-cross` phase of the bootstrap operation and build the headers for the C library.

> `binutils-cross`：运行引导操作的 `gcc-cross` 阶段和构建 C 库头文件所需的二进制工具。

- `linux-libc-headers`: Headers needed for the cross-compiler and C library build.
- `libgcc-initial`: An initial version of the gcc support library needed to bootstrap `glibc`.
- `libgcc`: The final version of the gcc support library which can only be built once there is a C library to link against.
- `glibc`: The GNU C Library.
- `gcc-cross`: The final stage of the bootstrap process for the cross-compiler. This stage results in the actual cross-compiler that BitBake uses when it builds an image for a targeted device.

> `- gcc-cross`：跨编译器的引导过程的最终阶段。此阶段导致 BitBake 在为目标设备构建映像时使用的实际跨编译器。

This tool is a \"native\" tool (i.e. it is designed to run on the build host).

- `gcc-runtime`: Runtime libraries resulting from the toolchain bootstrapping process. This tool produces a binary that consists of the runtime libraries need for the targeted device.

> gcc-runtime：由工具链引导过程产生的运行时库。该工具生成的二进制文件包含了针对目标设备所需的运行时库。

You can use the OpenEmbedded build system to build an installer for the relocatable SDK used to develop applications. When you run the installer, it installs the toolchain, which contains the development tools (e.g., `gcc-cross-canadian`, `binutils-cross-canadian`, and other `nativesdk-*` tools), which are tools native to the SDK (i.e. native to `SDK_ARCH`{.interpreted-text role="term"}), you need to cross-compile and test your software. The figure shows the commands you use to easily build out this toolchain. This cross-development toolchain is built to execute on the `SDKMACHINE`{.interpreted-text role="term"}, which might or might not be the same machine as the Build Host.

> 您可以使用 OpenEmbedded 构建系统构建可重定位 SDK 的安装程序，用于开发应用程序。当您运行安装程序时，它会安装工具链，其中包含开发工具（例如 `gcc-cross-canadian`，`binutils-cross-canadian` 和其他 `nativesdk-*` 工具），这些工具本地属于 SDK（即属于 `SDK_ARCH`{.interpreted-text role="term"}），您需要跨平台编译和测试软件。图显示了您用于轻松构建此工具链的命令。此跨开发工具链构建为在 `SDKMACHINE`{.interpreted-text role="term"}上执行，该机器可能与构建主机相同也可能不同。

::: note
::: title
Note
:::

If your target architecture is supported by the Yocto Project, you can take advantage of pre-built images that ship with the Yocto Project and already contain cross-development toolchain installers.

> 如果您的目标架构受 Yocto 项目支持，您可以利用随 Yocto 项目发布的预构建映像，其中已包含跨开发工具链安装程序。
> :::

Here is the bootstrap process for the relocatable toolchain:

```
gcc -> binutils-crosssdk -> gcc-crosssdk-initial -> linux-libc-headers -> glibc-initial -> nativesdk-glibc -> gcc-crosssdk -> gcc-cross-canadian
```

- `gcc`: The build host\'s GNU Compiler Collection (GCC).
- `binutils-crosssdk`: The bare minimum binary utilities needed in order to run the `gcc-crosssdk-initial` phase of the bootstrap operation.
- `gcc-crosssdk-initial`: An early stage of the bootstrap process for creating the cross-compiler. This stage builds enough of the `gcc-crosssdk` and supporting pieces so that the final stage of the bootstrap process can produce the finished cross-compiler. This tool is a \"native\" binary that runs on the build host.

> `gcc-crosssdk-初始化`：用于创建跨编译器的引导过程的早期阶段。此阶段构建足够的 `gcc-crosssdk` 和支持部件，以便引导过程的最终阶段可以生成完成的跨编译器。此工具是在构建主机上运行的“本机”二进制文件。

- `linux-libc-headers`: Headers needed for the cross-compiler.
- `glibc-initial`: An initial version of the Embedded GLIBC needed to bootstrap `nativesdk-glibc`.
- `nativesdk-glibc`: The Embedded GLIBC needed to bootstrap the `gcc-crosssdk`.
- `gcc-crosssdk`: The final stage of the bootstrap process for the relocatable cross-compiler. The `gcc-crosssdk` is a transitory compiler and never leaves the build host. Its purpose is to help in the bootstrap process to create the eventual `gcc-cross-canadian` compiler, which is relocatable. This tool is also a \"native\" package (i.e. it is designed to run on the build host).

> gcc-crosssdk：可重定位跨编译器的引导过程的最终阶段。`gcc-crosssdk` 是一个过渡编译器，从不离开构建主机。它的目的是帮助引导过程来创建最终的可重定位的 `gcc-cross-canadian` 编译器。这个工具也是一个“本地”软件包（即它是专门为构建主机设计的）。

- `gcc-cross-canadian`: The final relocatable cross-compiler. When run on the `SDKMACHINE`{.interpreted-text role="term"}, this tool produces executable code that runs on the target device. Only one cross-canadian compiler is produced per architecture since they can be targeted at different processor optimizations using configurations passed to the compiler through the compile commands. This circumvents the need for multiple compilers and thus reduces the size of the toolchains.

> GCC 跨加拿大：最终可重定位的跨编译器。当在 `SDKMACHINE`{.interpreted-text role="term"}上运行时，此工具会生成可在目标设备上运行的可执行代码。每个架构仅生成一个跨加拿大编译器，因为它们可以通过编译命令传递给编译器的配置来针对不同的处理器优化。这避免了需要多个编译器，从而减少了工具链的大小。

::: note
::: title
Note
:::

For information on advantages gained when building a cross-development toolchain installer, see the \"`sdk-manual/appendix-obtain:building an sdk installer`{.interpreted-text role="ref"}\" appendix in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual.

> 要了解构建跨开发工具链安装程序所获得的优势，请参阅 Yocto 项目应用开发和可扩展软件开发套件（eSDK）手册中的“sdk-manual/appendix-obtain:building an sdk installer”附录。
> :::

# Shared State Cache

By design, the OpenEmbedded build system builds everything from scratch unless `BitBake`{.interpreted-text role="term"} can determine that parts do not need to be rebuilt. Fundamentally, building from scratch is attractive as it means all parts are built fresh and there is no possibility of stale data that can cause problems. When developers hit problems, they typically default back to building from scratch so they have a known state from the start.

> 设计上，OpenEmbedded 构建系统从头开始构建一切，除非 BitBake 可以确定部分不需要重新构建。从根本上讲，从头开始构建是有吸引力的，因为它意味着所有部分都是新构建的，没有可能导致问题的过时数据。当开发人员遇到问题时，他们通常会默认回到从头开始构建，这样他们就可以从一个已知的状态开始。

Building an image from scratch is both an advantage and a disadvantage to the process. As mentioned in the previous paragraph, building from scratch ensures that everything is current and starts from a known state. However, building from scratch also takes much longer as it generally means rebuilding things that do not necessarily need to be rebuilt.

> 建立一张从零开始的图像既有利又有弊。正如前一段所提到的，从零开始确保一切都是最新的，并从一个已知的状态开始。但是，从零开始也需要更长的时间，因为它通常意味着重建不一定需要重建的东西。

The Yocto Project implements shared state code that supports incremental builds. The implementation of the shared state code answers the following questions that were fundamental roadblocks within the OpenEmbedded incremental build support system:

> 顗子項目實施了共享狀態代碼，支持增量構建。共享狀態代碼的實施回答了 OpenEmbedded 增量構建支持系統中的以下基本障礙問題：

- What pieces of the system have changed and what pieces have not changed?
- How are changed pieces of software removed and replaced?
- How are pre-built components that do not need to be rebuilt from scratch used when they are available?

For the first question, the build system detects changes in the \"inputs\" to a given task by creating a checksum (or signature) of the task\'s inputs. If the checksum changes, the system assumes the inputs have changed and the task needs to be rerun. For the second question, the shared state (sstate) code tracks which tasks add which output to the build process. This means the output from a given task can be removed, upgraded or otherwise manipulated. The third question is partly addressed by the solution for the second question assuming the build system can fetch the sstate objects from remote locations and install them if they are deemed to be valid.

> 对于第一个问题，构建系统通过创建给定任务的输入的校验和（或签名）来检测输入的变化。如果校验和发生变化，系统假定输入发生了变化，需要重新运行该任务。对于第二个问题，共享状态（sstate）代码跟踪哪些任务添加了哪些输出到构建过程中。这意味着给定任务的输出可以被删除，升级或者其他方式操作。第三个问题部分由第二个问题的解决方案来解决，假设构建系统可以从远程位置获取 sstate 对象并且如果它们被认为是有效的，就安装它们。

::: note
::: title
Note
:::

- The build system does not maintain `PR`{.interpreted-text role="term"} information as part of the shared state packages. Consequently, there are considerations that affect maintaining shared state feeds. For information on how the build system works with packages and can track incrementing `PR`{.interpreted-text role="term"} information, see the \"`dev-manual/packages:automatically incrementing a package version number`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 系统构建不会将 PR 信息作为共享状态软件包的一部分进行维护。因此，有一些影响共享状态软件包维护的因素。要了解系统构建如何处理软件包以及如何跟踪增量 PR 信息，请参阅 Yocto 项目开发任务手册中的“dev-manual/packages：自动增加软件包版本号”部分。

- The code in the build system that supports incremental builds is complex. For techniques that help you work around issues related to shared state code, see the \"`dev-manual/debugging:viewing metadata used to create the input signature of a shared state task`{.interpreted-text role="ref"}\" and \"`dev-manual/debugging:invalidating shared state to force a task to run`{.interpreted-text role="ref"}\" sections both in the Yocto Project Development Tasks Manual.

> 编译系统中支持增量构建的代码很复杂。要了解有关解决共享状态代码问题的技术，请参阅 Yocto Project 开发任务手册中的“dev-manual/debugging：查看用于创建共享状态任务输入签名的元数据”和“dev-manual/debugging：使共享状态失效以强制运行任务”部分。
> :::

The rest of this section goes into detail about the overall incremental build architecture, the checksums (signatures), and shared state.

## Overall Architecture

When determining what parts of the system need to be built, BitBake works on a per-task basis rather than a per-recipe basis. You might wonder why using a per-task basis is preferred over a per-recipe basis. To help explain, consider having the IPK packaging backend enabled and then switching to DEB. In this case, the `ref-tasks-install`{.interpreted-text role="ref"} and `ref-tasks-package`{.interpreted-text role="ref"} task outputs are still valid. However, with a per-recipe approach, the build would not include the `.deb` files. Consequently, you would have to invalidate the whole build and rerun it. Rerunning everything is not the best solution. Also, in this case, the core must be \"taught\" much about specific tasks. This methodology does not scale well and does not allow users to easily add new tasks in layers or as external recipes without touching the packaged-staging core.

> 当确定系统需要构建哪些部分时，BitBake 以每项任务为基础，而不是每个配方为基础。您可能想知道为什么使用每项任务的方式比每个配方的方式更受青睐。为了帮助解释，考虑启用 IPK 包装后端，然后切换到 DEB。在这种情况下，`ref-tasks-install`{.interpreted-text role="ref"}和 `ref-tasks-package`{.interpreted-text role="ref"}任务输出仍然有效。但是，使用每个配方的方法，构建将不包括 `.deb` 文件。因此，您必须使整个构建失效，并重新运行它。重新运行所有内容不是最好的解决方案。此外，在这种情况下，核心必须“学习”有关特定任务的许多内容。这种方法不能很好地扩展，也不允许用户在不触及打包分期核心的情况下轻松地在层中或作为外部配方添加新任务。

## Checksums (Signatures)

The shared state code uses a checksum, which is a unique signature of a task\'s inputs, to determine if a task needs to be run again. Because it is a change in a task\'s inputs that triggers a rerun, the process needs to detect all the inputs to a given task. For shell tasks, this turns out to be fairly easy because the build process generates a \"run\" shell script for each task and it is possible to create a checksum that gives you a good idea of when the task\'s data changes.

> 共享状态代码使用校验和，这是任务输入的唯一签名，以确定是否需要再次运行任务。由于任务输入的更改会触发重新运行，因此该过程需要检测给定任务的所有输入。对于 shell 任务，这变得相当容易，因为构建过程为每个任务生成一个“运行”shell 脚本，并且可以创建一个校验和，让您对任务数据何时更改有一个很好的了解。

To complicate the problem, there are things that should not be included in the checksum. First, there is the actual specific build path of a given task \-\-- the `WORKDIR`{.interpreted-text role="term"}. It does not matter if the work directory changes because it should not affect the output for target packages. Also, the build process has the objective of making native or cross packages relocatable.

> 问题让人头疼的是，有些东西不应该包含在校验和中。首先是给定任务的实际构建路径--`WORKDIR`{.interpreted-text role="term"}。无论工作目录是否改变，都无关紧要，因为它不应该影响目标软件包的输出。此外，构建过程的目标是使本机或跨平台软件包可重定位。

::: note
::: title
Note
:::

Both native and cross packages run on the build host. However, cross packages generate output for the target architecture.
:::

The checksum therefore needs to exclude `WORKDIR`{.interpreted-text role="term"}. The simplistic approach for excluding the work directory is to set `WORKDIR`{.interpreted-text role="term"} to some fixed value and create the checksum for the \"run\" script.

> 因此，校验和需要排除 `WORKDIR`{.interpreted-text role="term"}。排除工作目录的简单方法是将 `WORKDIR`{.interpreted-text role="term"}设置为某个固定值，并为“运行”脚本创建校验和。

Another problem results from the \"run\" scripts containing functions that might or might not get called. The incremental build solution contains code that figures out dependencies between shell functions. This code is used to prune the \"run\" scripts down to the minimum set, thereby alleviating this problem and making the \"run\" scripts much more readable as a bonus.

> 另一个问题来自于可能被调用也可能不被调用的“运行”脚本。增量构建解决方案包含了代码，用于计算 shell 函数之间的依赖关系。这些代码用于剪裁“运行”脚本，从而减轻这个问题，并作为奖励使“运行”脚本变得更加可读。

So far, there are solutions for shell scripts. What about Python tasks? The same approach applies even though these tasks are more difficult. The process needs to figure out what variables a Python function accesses and what functions it calls. Again, the incremental build solution contains code that first figures out the variable and function dependencies, and then creates a checksum for the data used as the input to the task.

> 迄今为止，有解决方案可以用于 shell 脚本。那么 Python 任务呢？相同的方法也适用，尽管这些任务更加困难。这个过程需要弄清楚 Python 函数访问哪些变量，以及它调用哪些函数。同样，增量构建解决方案包含的代码首先要找出变量和函数依赖关系，然后为作为任务输入的数据创建校验和。

Like the `WORKDIR`{.interpreted-text role="term"} case, there can be situations where dependencies should be ignored. For these situations, you can instruct the build process to ignore a dependency by using a line like the following:

> 就像 `WORKDIR`{.interpreted-text role="term"} 的情况一样，有时候可能需要忽略依赖关系。要处理这种情况，你可以使用下面这样的一行指令来指示构建过程忽略依赖关系：

```
PACKAGE_ARCHS[vardepsexclude] = "MACHINE"
```

This example ensures that the `PACKAGE_ARCHS`{.interpreted-text role="term"} variable does not depend on the value of `MACHINE`{.interpreted-text role="term"}, even if it does reference it.

> 这个例子确保 `PACKAGE_ARCHS`{.interpreted-text role="term"}变量不取决于 `MACHINE`{.interpreted-text role="term"}的值，即使它引用了它。

Equally, there are cases where you need to add dependencies BitBake is not able to find. You can accomplish this by using a line like the following:

```
PACKAGE_ARCHS[vardeps] = "MACHINE"
```

This example explicitly adds the `MACHINE`{.interpreted-text role="term"} variable as a dependency for `PACKAGE_ARCHS`{.interpreted-text role="term"}.

> 这个例子明确地将 `MACHINE` 变量作为 `PACKAGE_ARCHS` 的依赖项。

As an example, consider a case with in-line Python where BitBake is not able to figure out dependencies. When running in debug mode (i.e. using `-DDD`), BitBake produces output when it discovers something for which it cannot figure out dependencies. The Yocto Project team has currently not managed to cover those dependencies in detail and is aware of the need to fix this situation.

> 例如，考虑一个使用内联 Python 的情况，BitBake 无法确定依赖关系。当以调试模式（即使用 `-DDD`）运行时，BitBake 在发现无法确定依赖关系的情况时会产生输出。Yocto 项目团队目前尚未详细管理这些依赖关系，并且意识到有必要解决这个问题。

Thus far, this section has limited discussion to the direct inputs into a task. Information based on direct inputs is referred to as the \"basehash\" in the code. However, the question of a task\'s indirect inputs still exits \-\-- items already built and present in the `Build Directory`{.interpreted-text role="term"}. The checksum (or signature) for a particular task needs to add the hashes of all the tasks on which the particular task depends. Choosing which dependencies to add is a policy decision. However, the effect is to generate a checksum that combines the basehash and the hashes of the task\'s dependencies.

> 迄今为止，本节仅限于讨论任务的直接输入。基于直接输入的信息在代码中被称为“basehash”。然而，任务的间接输入的问题仍然存在-已经存在于“构建目录”中的项目。特定任务的校验和（或签名）需要添加所有依赖于该特定任务的任务的哈希值。选择依赖项添加是一个政策决定。然而，其影响是生成一个结合 basehash 和任务依赖项哈希值的校验和。

At the code level, there are multiple ways by which both the basehash and the dependent task hashes can be influenced. Within the BitBake configuration file, you can give BitBake some extra information to help it construct the basehash. The following statement effectively results in a list of global variable dependency excludes (i.e. variables never included in any checksum):

> 在代码层面，有多种方法可以影响基础哈希和依赖任务哈希。在 BitBake 配置文件中，您可以向 BitBake 提供一些额外的信息来帮助它构建基础哈希。以下语句有效地导致全局变量依赖排除列表（即从不包含在任何校验和中的变量）：

```
BB_BASEHASH_IGNORE_VARS ?= "TMPDIR FILE PATH PWD BB_TASKHASH BBPATH DL_DIR \\
    SSTATE_DIR THISDIR FILESEXTRAPATHS FILE_DIRNAME HOME LOGNAME SHELL TERM \\
    USER FILESPATH STAGING_DIR_HOST STAGING_DIR_TARGET COREBASE PRSERV_HOST \\
    PRSERV_DUMPDIR PRSERV_DUMPFILE PRSERV_LOCKDOWN PARALLEL_MAKE \\
    CCACHE_DIR EXTERNAL_TOOLCHAIN CCACHE CCACHE_DISABLE LICENSE_PATH SDKPKGSUFFIX"
```

The previous example does not include `WORKDIR`{.interpreted-text role="term"} since that variable is actually constructed as a path within `TMPDIR`{.interpreted-text role="term"}, which is included above.

> 上面的示例没有包括 `WORKDIR`，因为该变量实际上是在 `TMPDIR` 中构建的路径，上面已经包括了。

The rules for deciding which hashes of dependent tasks to include through dependency chains are more complex and are generally accomplished with a Python function. The code in `meta/lib/oe/sstatesig.py` shows two examples of this and also illustrates how you can insert your own policy into the system if so desired. This file defines the two basic signature generators `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"} uses: \"OEBasic\" and \"OEBasicHash\". By default, a dummy \"noop\" signature handler is enabled in BitBake. This means that behavior is unchanged from previous versions. OE-Core uses the \"OEBasicHash\" signature handler by default through this setting in the `bitbake.conf` file:

> 决定要通过依赖链包含哪些依赖任务的哈希值的规则更复杂，通常使用 Python 函数来实现。`meta/lib/oe/sstatesig.py` 中的代码展示了两个示例，也揭示了如果需要的话可以将自己的策略插入系统中。该文件定义了 OpenEmbedded-Core（OE-Core）使用的两种基本签名生成器：“OEBasic”和“OEBasicHash”。BitBake 默认启用了一个虚拟的“noop”签名处理器。这意味着行为与以前的版本保持不变。OE-Core 通过 `bitbake.conf` 文件中的此设置默认使用“OEBasicHash”签名处理程序：

```
BB_SIGNATURE_HANDLER ?= "OEBasicHash"
```

The \"OEBasicHash\" `BB_SIGNATURE_HANDLER`{.interpreted-text role="term"} is the same as the \"OEBasic\" version but adds the task hash to the `stamp files <overview-manual/concepts:stamp files and the rerunning of tasks>`{.interpreted-text role="ref"}. This results in any metadata change that changes the task hash, automatically causing the task to be run again. This removes the need to bump `PR`{.interpreted-text role="term"} values, and changes to metadata automatically ripple across the build.

> "OEBasicHash" 的 BB_SIGNATURE_HANDLER 与 "OEBasic" 版本相同，但会将任务哈希值添加到印章文件中（参见“概览手册/概念：印章文件和重新运行任务”）。这样，任何更改任务哈希值的元数据变更都会自动导致任务重新运行。这样就无需提升 PR 值，元数据的变更会自动在构建中扩散。

It is also worth noting that the end result of these signature generators is to make some dependency and hash information available to the build. This information includes:

> 值得注意的是，这些签名生成器的最终结果是为构建提供一些依赖关系和哈希信息。这些信息包括：

- `BB_BASEHASH:task-` taskname: The base hashes for each task in the recipe.
- `BB_BASEHASH_` filename `:` taskname: The base hashes for each dependent task.
- `BB_TASKHASH`{.interpreted-text role="term"}: The hash of the currently running task.

## Shared State

Checksums and dependencies, as discussed in the previous section, solve half the problem of supporting a shared state. The other half of the problem is being able to use checksum information during the build and being able to reuse or rebuild specific components.

> 校验和与依赖关系，如上一节所讨论的，只解决了支持共享状态的一半问题。另一半问题是在构建过程中能够使用校验和信息，以及能够重用或重建特定组件。

The `ref-classes-sstate`{.interpreted-text role="ref"} class is a relatively generic implementation of how to \"capture\" a snapshot of a given task. The idea is that the build process does not care about the source of a task\'s output. Output could be freshly built or it could be downloaded and unpacked from somewhere. In other words, the build process does not need to worry about its origin.

> 这个 `ref-classes-sstate`{.interpreted-text role="ref"}类是一个相对通用的实现，用于捕获给定任务的快照。这个想法是，构建过程不关心任务输出的来源。输出可以是新构建的，也可以从某处下载和解压缩的。换句话说，构建过程不需要担心它的起源。

Two types of output exist. One type is just about creating a directory in `WORKDIR`{.interpreted-text role="term"}. A good example is the output of either `ref-tasks-install`{.interpreted-text role="ref"} or `ref-tasks-package`{.interpreted-text role="ref"}. The other type of output occurs when a set of data is merged into a shared directory tree such as the sysroot.

> 两种输出存在。一种是只是在 `WORKDIR` 中创建一个目录。一个很好的例子是 `ref-tasks-install` 或 `ref-tasks-package` 的输出。另一种类型的输出是将一组数据合并到共享目录树中，例如 sysroot。

The Yocto Project team has tried to keep the details of the implementation hidden in the `ref-classes-sstate`{.interpreted-text role="ref"} class. From a user\'s perspective, adding shared state wrapping to a task is as simple as this `ref-tasks-deploy`{.interpreted-text role="ref"} example taken from the `ref-classes-deploy`{.interpreted-text role="ref"} class:

> Yocto 项目团队已尝试将实施细节保持在 `ref-classes-sstate`{.interpreted-text role="ref"} 类中隐藏。从用户的角度来看，为任务添加共享状态封装就像从 `ref-classes-deploy`{.interpreted-text role="ref"} 类中提取的 `ref-tasks-deploy`{.interpreted-text role="ref"} 示例一样简单。

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

- Adding `do_deploy` to `SSTATETASKS` adds some required sstate-related processing, which is implemented in the `ref-classes-sstate`{.interpreted-text role="ref"} class, to before and after the `ref-tasks-deploy`{.interpreted-text role="ref"} task.

> 将 `do_deploy` 添加到 `SSTATETASKS` 会增加一些必要的 sstate 相关处理，这些处理在 `ref-classes-sstate`{.interpreted-text role="ref"}类中实现，在 `ref-tasks-deploy`{.interpreted-text role="ref"}任务之前和之后。

- The `do_deploy[sstate-inputdirs] = "${DEPLOYDIR}"` declares that `ref-tasks-deploy`{.interpreted-text role="ref"} places its output in `${DEPLOYDIR}` when run normally (i.e. when not using the sstate cache). This output becomes the input to the shared state cache.

> `- ` do_deploy[sstate-inputdirs] = "${DEPLOYDIR}"`声明，当正常运行（即不使用sstate缓存时），`ref-tasks-deploy`{.interpreted-text role="ref"}将其输出放在`${DEPLOYDIR}` 中。此输出成为共享状态缓存的输入。

- The `do_deploy[sstate-outputdirs] = "${DEPLOY_DIR_IMAGE}"` line causes the contents of the shared state cache to be copied to `${DEPLOY_DIR_IMAGE}`.

> - 这一行 `do_deploy[sstate-outputdirs] = "${DEPLOY_DIR_IMAGE}"` 会导致共享状态缓存的内容被复制到 `${DEPLOY_DIR_IMAGE}`。

::: note
::: title
Note
:::

If `ref-tasks-deploy`{.interpreted-text role="ref"} is not already in the shared state cache or if its input checksum (signature) has changed from when the output was cached, the task runs to populate the shared state cache, after which the contents of the shared state cache is copied to \${`DEPLOY_DIR_IMAGE`{.interpreted-text role="term"}}. If `ref-tasks-deploy`{.interpreted-text role="ref"} is in the shared state cache and its signature indicates that the cached output is still valid (i.e. if no relevant task inputs have changed), then the contents of the shared state cache copies directly to \${`DEPLOY_DIR_IMAGE`{.interpreted-text role="term"}} by the `do_deploy_setscene` task instead, skipping the `ref-tasks-deploy`{.interpreted-text role="ref"} task.

> 如果 `ref-tasks-deploy`{.interpreted-text role="ref"}还没有在共享状态缓存中，或者它的输入校验和（签名）与缓存输出时的不同，任务将运行以填充共享状态缓存，之后共享状态缓存的内容将被复制到\${`DEPLOY_DIR_IMAGE`{.interpreted-text role="term"}}。如果 `ref-tasks-deploy`{.interpreted-text role="ref"}存在于共享状态缓存中，且其签名表明缓存输出仍然有效（即如果没有相关任务输入发生变化），则 `do_deploy_setscene` 任务将直接从共享状态缓存复制内容到\${`DEPLOY_DIR_IMAGE`{.interpreted-text role="term"}}，而跳过 `ref-tasks-deploy`{.interpreted-text role="ref"}任务。
> :::

- The following task definition is glue logic needed to make the previous settings effective:

  ```
  python do_deploy_setscene () {
      sstate_setscene(d)
  }
  addtask do_deploy_setscene
  ```

  `sstate_setscene()` takes the flags above as input and accelerates the `ref-tasks-deploy`{.interpreted-text role="ref"} task through the shared state cache if possible. If the task was accelerated, `sstate_setscene()` returns True. Otherwise, it returns False, and the normal `ref-tasks-deploy`{.interpreted-text role="ref"} task runs. For more information, see the \"`bitbake-user-manual/bitbake-user-manual-execution:setscene`{.interpreted-text role="ref"}\" section in the BitBake User Manual.

> `sstate_setscene()` 将上述标志作为输入，如果可能，通过共享状态缓存加速 `ref-tasks-deploy`{.interpreted-text role="ref"} 任务。如果任务被加速，`sstate_setscene()` 返回 True。否则，它返回 False，正常的 `ref-tasks-deploy`{.interpreted-text role="ref"} 任务运行。有关更多信息，请参见 BitBake 用户手册中的“`bitbake-user-manual/bitbake-user-manual-execution:setscene`{.interpreted-text role="ref"}”部分。

- The `do_deploy[dirs] = "${DEPLOYDIR} ${B}"` line creates `${DEPLOYDIR}` and `${B}` before the `ref-tasks-deploy`{.interpreted-text role="ref"} task runs, and also sets the current working directory of `ref-tasks-deploy`{.interpreted-text role="ref"} to `${B}`. For more information, see the \"`bitbake-user-manual/bitbake-user-manual-metadata:variable flags`{.interpreted-text role="ref"}\" section in the BitBake User Manual.

> - 该 `do_deploy[dirs] = "${DEPLOYDIR} ${B}"` 行在 `ref-tasks-deploy`{.interpreted-text role="ref"} 任务运行之前创建 `${DEPLOYDIR}` 和 `${B}`，并将 `ref-tasks-deploy`{.interpreted-text role="ref"} 的当前工作目录设置为 `${B}`。有关更多信息，请参见 BitBake 用户手册中的“`bitbake-user-manual/bitbake-user-manual-metadata:variable flags`{.interpreted-text role="ref"}”部分。

::: note
::: title
Note
:::

In cases where `sstate-inputdirs` and `sstate-outputdirs` would be the same, you can use `sstate-plaindirs`. For example, to preserve the \${`PKGD`{.interpreted-text role="term"}} and \${`PKGDEST`{.interpreted-text role="term"}} output from the `ref-tasks-package`{.interpreted-text role="ref"} task, use the following:

> 在 `sstate-inputdirs` 和 `sstate-outputdirs` 相同的情况下，您可以使用 `sstate-plaindirs`。例如，要保留 `ref-tasks-package`{.interpreted-text role="ref"}任务的\${`PKGD`{.interpreted-text role="term"}}和\${`PKGDEST`{.interpreted-text role="term"}}输出，请使用以下内容：

```
do_package[sstate-plaindirs] = "${PKGD} ${PKGDEST}"
```

:::

- The `do_deploy[stamp-extra-info] = "${MACHINE_ARCH}"` line appends extra metadata to the `stamp file <overview-manual/concepts:stamp files and the rerunning of tasks>`{.interpreted-text role="ref"}. In this case, the metadata makes the task specific to a machine\'s architecture. See the \"`bitbake-user-manual/bitbake-user-manual-execution:the task list`{.interpreted-text role="ref"}\" section in the BitBake User Manual for more information on the `stamp-extra-info` flag.

> 这一行将额外的元数据附加到 stamp 文件 <overview-manual/concepts:stamp files and the rerunning of tasks>{.interpreted-text role="ref"}。在这种情况下，元数据使任务特定于机器的体系结构。有关 stamp-extra-info 标志的更多信息，请参阅 BitBake 用户手册中的“bitbake-user-manual/bitbake-user-manual-execution:the task list”{.interpreted-text role="ref"}部分。

- `sstate-inputdirs` and `sstate-outputdirs` can also be used with multiple directories. For example, the following declares `PKGDESTWORK`{.interpreted-text role="term"} and `SHLIBWORK` as shared state input directories, which populates the shared state cache, and `PKGDATA_DIR`{.interpreted-text role="term"} and `SHLIBSDIR` as the corresponding shared state output directories:

> `-` sstate-inputdirs 和 sstate-outputdirs 也可以用于多个目录。例如，以下声明 PKGDESTWORK 和 SHLIBWORK 为共享状态输入目录，它们将填充共享状态缓存，以及 PKGDATA_DIR 和 SHLIBSDIR 为对应的共享状态输出目录：

```
do_package[sstate-inputdirs] = "${PKGDESTWORK} ${SHLIBSWORKDIR}"
do_package[sstate-outputdirs] = "${PKGDATA_DIR} ${SHLIBSDIR}"
```

- These methods also include the ability to take a lockfile when manipulating shared state directory structures, for cases where file additions or removals are sensitive:

> 这些方法还包括在操作共享状态目录结构时采取锁定文件的能力，以处理文件添加或删除敏感的情况：

```
do_package[sstate-lockfile] = "${PACKAGELOCK}"
```

Behind the scenes, the shared state code works by looking in `SSTATE_DIR`{.interpreted-text role="term"} and `SSTATE_MIRRORS`{.interpreted-text role="term"} for shared state files. Here is an example:

> 在幕后，共享状态代码通过查看 `SSTATE_DIR`{.interpreted-text role="term"}和 `SSTATE_MIRRORS`{.interpreted-text role="term"}中的共享状态文件来工作。这里有一个例子：

```
SSTATE_MIRRORS ?= "\
    file://.* https://someserver.tld/share/sstate/PATH;downloadfilename=PATH \
    file://.* file:///some/local/dir/sstate/PATH"
```

::: note
::: title
Note
:::

The shared state directory (`SSTATE_DIR`{.interpreted-text role="term"}) is organized into two-character subdirectories, where the subdirectory names are based on the first two characters of the hash. If the shared state directory structure for a mirror has the same structure as `SSTATE_DIR`{.interpreted-text role="term"}, you must specify \"PATH\" as part of the URI to enable the build system to map to the appropriate subdirectory.

> 共享状态目录（`SSTATE_DIR`{.interpreted-text role="term"}）分为两个字符的子目录，其中子目录名基于哈希的前两个字符。如果镜像的共享状态目录结构与 `SSTATE_DIR`{.interpreted-text role="term"}相同，则必须指定 URI 的一部分为“PATH”，以便构建系统映射到适当的子目录。
> :::

The shared state package validity can be detected just by looking at the filename since the filename contains the task checksum (or signature) as described earlier in this section. If a valid shared state package is found, the build process downloads it and uses it to accelerate the task.

> 可以仅通过查看文件名就可以检测共享状态包的有效性，因为文件名包含了前面本节中描述的任务校验和（或签名）。如果找到有效的共享状态包，构建过程将下载并使用它来加速任务。

The build processes use the `*_setscene` tasks for the task acceleration phase. BitBake goes through this phase before the main execution code and tries to accelerate any tasks for which it can find shared state packages. If a shared state package for a task is available, the shared state package is used. This means the task and any tasks on which it is dependent are not executed.

> 构建过程使用 `*_setscene` 任务来进行任务加速阶段。BitBake 在主执行代码之前经历此阶段，并尝试加速任何可以找到共享状态包的任务。如果一个任务的共享状态包可用，则使用共享状态包。这意味着不会执行该任务及其任何依赖的任务。

As a real world example, the aim is when building an IPK-based image, only the `ref-tasks-package_write_ipk`{.interpreted-text role="ref"} tasks would have their shared state packages fetched and extracted. Since the sysroot is not used, it would never get extracted. This is another reason why a task-based approach is preferred over a recipe-based approach, which would have to install the output from every task.

> 作为一个现实世界的例子，在构建基于 IPK 的镜像时，只有 `ref-tasks-package_write_ipk` 任务的共享状态包会被抓取和解压。由于 sysroot 不会被使用，它永远不会被解压。这是为什么基于任务的方法比基于配方的方法更受青睐的另一个原因，因为后者必须安装每个任务的输出。

## Hash Equivalence

The above section explained how BitBake skips the execution of tasks whose output can already be found in the Shared State cache.

During a build, it may often be the case that the output / result of a task might be unchanged despite changes in the task\'s input values. An example might be whitespace changes in some input C code. In project terms, this is what we define as \"equivalence\".

> 在构建过程中，尽管任务的输入值发生了变化，但任务的输出/结果可能没有变化。例如，一些输入 C 代码中的空格变化。从项目的角度来看，这就是我们定义的“等价性”。

To keep track of such equivalence, BitBake has to manage three hashes for each task:

- The *task hash* explained earlier: computed from the recipe metadata, the task code and the task hash values from its dependencies. When changes are made, these task hashes are therefore modified, causing the task to re-execute. The task hashes of tasks depending on this task are therefore modified too, causing the whole dependency chain to re-execute.

> 任务哈希之前解释过：由配方元数据、任务代码和其依赖的任务哈希值计算得出。当发生更改时，这些任务哈希就会被修改，导致任务重新执行。依赖于此任务的任务哈希也会被修改，从而导致整个依赖链重新执行。

- The *output hash*, a new hash computed from the output of Shared State tasks, tasks that save their resulting output to a Shared State tarball. The mapping between the task hash and its output hash is reported to a new *Hash Equivalence* server. This mapping is stored in a database by the server for future reference.

> 输出哈希，一种新的哈希，是从共享状态任务的输出计算而来，这些任务将其结果输出保存到共享状态归档文件中。任务哈希与其输出哈希之间的映射会报告给一个新的哈希等价服务器，该映射会被服务器存储在数据库中，以供将来参考。

- The *unihash*, a new hash, initially set to the task hash for the task. This is used to track the *unicity* of task output, and we will explain how its value is maintained.

> - 新的哈希值 *unihash* 最初设置为任务哈希值。这用于跟踪任务输出的*唯一性*，我们将解释它的值是如何维持的。

When Hash Equivalence is enabled, BitBake computes the task hash for each task by using the unihash of its dependencies, instead of their task hash.

Now, imagine that a Shared State task is modified because of a change in its code or metadata, or because of a change in its dependencies. Since this modifies its task hash, this task will need re-executing. Its output hash will therefore be computed again.

> 现在，想象一个共享状态任务因其代码或元数据的变化，或者因其依赖项的变化而被修改。由于此操作会修改其任务哈希，因此该任务需要重新执行。因此，其输出哈希将被重新计算。

Then, the new mapping between the new task hash and its output hash will be reported to the Hash Equivalence server. The server will let BitBake know whether this output hash is the same as a previously reported output hash, for a different task hash.

> 然后，新任务哈希与其输出哈希之间的新映射将报告给哈希等价服务器。服务器将让 BitBake 知道这个输出哈希是否与先前报告的不同任务哈希的输出哈希相同。

If the output hash is already known, BitBake will update the task\'s unihash to match the original task hash that generated that output. Thanks to this, the depending tasks will keep a previously recorded task hash, and BitBake will be able to retrieve their output from the Shared State cache, instead of re-executing them. Similarly, the output of further downstream tasks can also be retrieved from Shared State.

> 如果输出哈希已经知道，BitBake 将更新任务的 unihash 以匹配生成该输出的原始任务哈希。由于这一点，依赖任务将保留先前记录的任务哈希，BitBake 将能够从共享状态缓存中检索它们的输出，而不是重新执行它们。类似地，进一步下游任务的输出也可以从共享状态中检索。

If the output hash is unknown, a new entry will be created on the Hash Equivalence server, matching the task hash to that output. The depending tasks, still having a new task hash because of the change, will need to re-execute as expected. The change propagates to the depending tasks.

> 如果输出哈希值未知，则会在哈希等价服务器上创建一个新条目，将任务哈希值与该输出匹配。由于更改，受影响的任务仍然具有新的任务哈希值，因此需要重新执行。更改传播到受影响的任务。

To summarize, when Hash Equivalence is enabled, a change in one of the tasks in BitBake\'s run queue doesn\'t have to propagate to all the downstream tasks that depend on the output of this task, causing a full rebuild of such tasks, and so on with the next depending tasks. Instead, when the output of this task remains identical to previously recorded output, BitBake can safely retrieve all the downstream task output from the Shared State cache.

> 总而言之，当启用哈希等价时，BitBake 运行队列中的一个任务的更改不必传播到所有依赖于此任务输出的下游任务，从而导致这些任务的全部重建，以及与下一个依赖任务。相反，当此任务的输出与先前记录的输出完全相同时，BitBake 可以安全地从共享状态缓存中检索所有下游任务输出。

::: note
::: title
Note
:::

Having `/test-manual/reproducible-builds`{.interpreted-text role="doc"} is a key ingredient for the stability of the task\'s output hash. Therefore, the effectiveness of Hash Equivalence strongly depends on it.

> 拥有/test-manual/reproducible-builds 是任务输出哈希的稳定性的关键要素。因此，哈希等价性的有效性高度依赖于它。
> :::

This applies to multiple scenarios:

- A \"trivial\" change to a recipe that doesn\'t impact its generated output, such as whitespace changes, modifications to unused code paths or in the ordering of variables.

> 一个对配方没有影响其生成输出的"无关紧要"的变更，比如空格的改变、修改未使用的代码路径或者变量的排序。

- Shared library updates, for example to fix a security vulnerability. For sure, the programs using such a library should be rebuilt, but their new binaries should remain identical. The corresponding tasks should have a different output hash because of the change in the hash of their library dependency, but thanks to their output being identical, Hash Equivalence will stop the propagation down the dependency chain.

> 比如为了修复安全漏洞，更新共享库。当然，使用这个库的程序应该重新构建，但是它们的新二进制文件应该保持一致。由于它们的依赖库哈希值的变化，相应的任务应该有不同的输出哈希，但是由于它们的输出是一致的，哈希等价性将阻止它们在依赖链中的传播。

- Native tool updates. Though the depending tasks should be rebuilt, it\'s likely that they will generate the same output and be marked as equivalent.

> 尽管依赖任务需要重新构建，但它们很可能会产生相同的输出并被标记为等效的本机工具更新。

This mechanism is enabled by default in Poky, and is controlled by three variables:

- `bitbake:BB_HASHSERVE`{.interpreted-text role="term"}, specifying a local or remote Hash Equivalence server to use.
- `BB_HASHSERVE_UPSTREAM`{.interpreted-text role="term"}, when `BB_HASHSERVE = "auto"`, allowing to connect the local server to an upstream one.
- `bitbake:BB_SIGNATURE_HANDLER`{.interpreted-text role="term"}, which must be set to `OEEquivHash`.

Therefore, the default configuration in Poky corresponds to the below settings:

```
BB_HASHSERVE = "auto"
BB_SIGNATURE_HANDLER = "OEEquivHash"
```

Rather than starting a local server, another possibility is to rely on a Hash Equivalence server on a network, by setting:

```
BB_HASHSERVE = "<HOSTNAME>:<PORT>"
```

::: note
::: title
Note
:::

The shared Hash Equivalence server needs to be maintained together with the Shared State cache. Otherwise, the server could report Shared State hashes that only exist on specific clients.

> 共享哈希等价服务器需要与共享状态缓存一起维护。否则，服务器可能报告只存在于特定客户端上的共享状态哈希。

We therefore recommend that one Hash Equivalence server be set up to correspond with a given Shared State cache, and to start this server in *read-only mode*, so that it doesn\'t store equivalences for Shared State caches that are local to clients.

> 我们因此建议设置一个哈希等价服务器来对应一个给定的共享状态缓存，并以*只读模式*启动此服务器，以免存储本地客户端的共享状态缓存的等价关系。

See the `BB_HASHSERVE`{.interpreted-text role="term"} reference for details about starting a Hash Equivalence server.
:::

See the [video](https://www.youtube.com/watch?v=zXEdqGS62Wc) of Joshua Watt\'s [Hash Equivalence and Reproducible Builds](https://elinux.org/images/3/37/Hash_Equivalence_and_Reproducible_Builds.pdf) presentation at ELC 2020 for a very synthetic introduction to the Hash Equivalence implementation in the Yocto Project.

> 请查看乔舒亚·瓦特（Joshua Watt）在 ELC 2020 上发表的《哈希等价性与可重现构建》（[Hash Equivalence and Reproducible Builds]([https://elinux.org/images/3/37/Hash_Equivalence_and_Reproducible_Builds.pdf](https://elinux.org/images/3/37/Hash_Equivalence_and_Reproducible_Builds.pdf)））演讲的[视频](https://www.youtube.com/watch?v=zXEdqGS62Wc)，以了解 Yocto 项目中哈希等价性实施的简要介绍。

# Automatically Added Runtime Dependencies

The OpenEmbedded build system automatically adds common types of runtime dependencies between packages, which means that you do not need to explicitly declare the packages using `RDEPENDS`{.interpreted-text role="term"}. There are three automatic mechanisms (`shlibdeps`, `pcdeps`, and `depchains`) that handle shared libraries, package configuration (pkg-config) modules, and `-dev` and `-dbg` packages, respectively. For other types of runtime dependencies, you must manually declare the dependencies.

> 开放嵌入式构建系统会自动添加各种类型的运行时依赖关系，这意味着您不必显式声明使用 `RDEPENDS`{.interpreted-text role="term"}的软件包。有三种自动机制（`shlibdeps`，`pcdeps` 和 `depchains`）可处理共享库、包配置（pkg-config）模块以及 `-dev` 和 `-dbg` 软件包。对于其他类型的运行时依赖关系，您必须手动声明依赖关系。

- `shlibdeps`: During the `ref-tasks-package`{.interpreted-text role="ref"} task of each recipe, all shared libraries installed by the recipe are located. For each shared library, the package that contains the shared library is registered as providing the shared library. More specifically, the package is registered as providing the `soname <Soname>`{.interpreted-text role="wikipedia"} of the library. The resulting shared-library-to-package mapping is saved globally in `PKGDATA_DIR`{.interpreted-text role="term"} by the `ref-tasks-packagedata`{.interpreted-text role="ref"} task.

> -`shlibdeps`：在每个配方的 `ref-tasks-package` 任务期间，找到由配方安装的所有共享库。对于每个共享库，将包含共享库的包注册为提供共享库。更具体地说，该包将被注册为提供库的 `soname <Soname>`。由 `ref-tasks-packagedata` 任务在 `PKGDATA_DIR` 中将结果共享库到包映射全局保存。

Simultaneously, all executables and shared libraries installed by the recipe are inspected to see what shared libraries they link against. For each shared library dependency that is found, `PKGDATA_DIR`{.interpreted-text role="term"} is queried to see if some package (likely from a different recipe) contains the shared library. If such a package is found, a runtime dependency is added from the package that depends on the shared library to the package that contains the library.

> 同时，检查所有由食谱安装的可执行文件和共享库，看它们是否链接到共享库。对于每个找到的共享库依赖项，查询 `PKGDATA_DIR`{.interpreted-text role="term"}以查看是否有某个包（可能来自不同的食谱）包含共享库。如果找到这样的包，则从依赖共享库的包添加运行时依赖项到包含该库的包。

The automatically added runtime dependency also includes a version restriction. This version restriction specifies that at least the current version of the package that provides the shared library must be used, as if \"package (\>= version)\" had been added to `RDEPENDS`{.interpreted-text role="term"}. This forces an upgrade of the package containing the shared library when installing the package that depends on the library, if needed.

> 自动添加的运行时依赖性也包括版本限制。此版本限制表示必须使用至少当前版本的提供共享库的软件包，就好像将"package（> = version）"添加到“RDEPENDS”中一样。如果需要，这将强制安装依赖于该库的软件包时更新包含共享库的软件包。

If you want to avoid a package being registered as providing a particular shared library (e.g. because the library is for internal use only), then add the library to `PRIVATE_LIBS`{.interpreted-text role="term"} inside the package\'s recipe.

> 如果你想避免一个软件包被注册为提供特定的共享库（例如，因为该库仅供内部使用），那么请在软件包的配方中添加该库到 `PRIVATE_LIBS`。

- `pcdeps`: During the `ref-tasks-package`{.interpreted-text role="ref"} task of each recipe, all pkg-config modules (`*.pc` files) installed by the recipe are located. For each module, the package that contains the module is registered as providing the module. The resulting module-to-package mapping is saved globally in `PKGDATA_DIR`{.interpreted-text role="term"} by the `ref-tasks-packagedata`{.interpreted-text role="ref"} task.

> 在每个配方的 `ref-tasks-package` 任务期间，所有由配方安装的 pkg-config 模块（`*.pc` 文件）都将被定位。对于每个模块，包含该模块的包将被注册为提供该模块。由 `ref-tasks-packagedata` 任务将生成的模块到包映射保存到 `PKGDATA_DIR` 中。

Simultaneously, all pkg-config modules installed by the recipe are inspected to see what other pkg-config modules they depend on. A module is seen as depending on another module if it contains a \"Requires:\" line that specifies the other module. For each module dependency, `PKGDATA_DIR`{.interpreted-text role="term"} is queried to see if some package contains the module. If such a package is found, a runtime dependency is added from the package that depends on the module to the package that contains the module.

> 同时，检查由配方安装的所有 pkg-config 模块，以查看它们是否依赖其他 pkg-config 模块。如果包含指定其他模块的“Requires：”行，则将模块视为依赖于另一个模块。对于每个模块依赖项，查询 `PKGDATA_DIR`{.interpreted-text role="term"}以查看是否有某个包包含该模块。如果找到此类包，则从依赖模块的包添加运行时依赖项到包含模块的包。

::: note
::: title
Note
:::

The pcdeps mechanism most often infers dependencies between -dev packages.
:::

- `depchains`: If a package `foo` depends on a package `bar`, then `foo-dev` and `foo-dbg` are also made to depend on `bar-dev` and `bar-dbg`, respectively. Taking the `-dev` packages as an example, the `bar-dev` package might provide headers and shared library symlinks needed by `foo-dev`, which shows the need for a dependency between the packages.

> 如果一个软件包 `foo` 依赖于另一个软件包 `bar`，那么 `foo-dev` 和 `foo-dbg` 也会依赖 `bar-dev` 和 `bar-dbg`，分别依赖。以 `-dev` 软件包为例，`bar-dev` 软件包可能提供 `foo-dev` 所需的头文件和共享库符号链接，这表明了这些软件包之间存在依赖关系。

The dependencies added by `depchains` are in the form of `RRECOMMENDS`{.interpreted-text role="term"}.

::: note
::: title
Note
:::

By default, `foo-dev` also has an `RDEPENDS`{.interpreted-text role="term"}-style dependency on `foo`, because the default value of `RDEPENDS:${PN}-dev` (set in `bitbake.conf`) includes \"\${PN}\".

> 默认情况下，“foo-dev”也有一个“RDEPENDS”类型的依赖关系，因为“bitbake.conf”中“RDEPENDS：${PN}-dev”的默认值包括“\${PN}”。
> :::

To ensure that the dependency chain is never broken, `-dev` and `-dbg` packages are always generated by default, even if the packages turn out to be empty. See the `ALLOW_EMPTY`{.interpreted-text role="term"} variable for more information.

> 为了确保依赖链永不断开，即使包最终为空，也会默认生成 `-dev` 和 `-dbg` 包。有关更多信息，请参阅 `ALLOW_EMPTY` 变量。

The `ref-tasks-package`{.interpreted-text role="ref"} task depends on the `ref-tasks-packagedata`{.interpreted-text role="ref"} task of each recipe in `DEPENDS`{.interpreted-text role="term"} through use of a `[``deptask <bitbake-user-manual/bitbake-user-manual-metadata:variable flags>`{.interpreted-text role="ref"}`]` declaration, which guarantees that the required shared-library / module-to-package mapping information will be available when needed as long as `DEPENDS`{.interpreted-text role="term"} has been correctly set.

> 任务 `ref-tasks-package`{.interpreted-text role="ref"}依赖于每个配方中的 `ref-tasks-packagedata`{.interpreted-text role="ref"}任务，通过使用 `[``deptask <bitbake-user-manual/bitbake-user-manual-metadata:variable flags>`{.interpreted-text role="ref"}`]` 声明，保证只要 `DEPENDS`{.interpreted-text role="term"}被正确设置，所需的共享库/模块到包映射信息就会在需要的时候可用。

# Fakeroot and Pseudo

Some tasks are easier to implement when allowed to perform certain operations that are normally reserved for the root user (e.g. `ref-tasks-install`{.interpreted-text role="ref"}, `do_package_write* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"}, `ref-tasks-rootfs`{.interpreted-text role="ref"}, and `do_image_* <ref-tasks-image>`{.interpreted-text role="ref"}). For example, the `ref-tasks-install`{.interpreted-text role="ref"} task benefits from being able to set the UID and GID of installed files to arbitrary values.

> 一些任务可以在允许执行通常只供 root 用户使用的操作时更容易实施（例如 `ref-tasks-install`{.interpreted-text role="ref"}、`do_package_write* <ref-tasks-package_write_deb>`{.interpreted-text role="ref"}、`ref-tasks-rootfs`{.interpreted-text role="ref"}和 `do_image_* <ref-tasks-image>`{.interpreted-text role="ref"}）。例如，`ref-tasks-install`{.interpreted-text role="ref"}任务可以从将安装文件的 UID 和 GID 设置为任意值中受益。

One approach to allowing tasks to perform root-only operations would be to require `BitBake`{.interpreted-text role="term"} to run as root. However, this method is cumbersome and has security issues. The approach that is actually used is to run tasks that benefit from root privileges in a \"fake\" root environment. Within this environment, the task and its child processes believe that they are running as the root user, and see an internally consistent view of the filesystem. As long as generating the final output (e.g. a package or an image) does not require root privileges, the fact that some earlier steps ran in a fake root environment does not cause problems.

> 一种允许任务执行仅限 root 操作的方法是要求 BitBake 以 root 身份运行。但是，这种方法麻烦且存在安全问题。实际使用的方法是在“伪”根环境中运行受益于 root 特权的任务。在此环境中，任务及其子进程认为它们正在以 root 用户身份运行，并看到文件系统的内部一致视图。只要生成最终输出（例如包或映像）不需要 root 特权，一些较早步骤在伪根环境中运行的事实就不会造成问题。

The capability to run tasks in a fake root environment is known as \"[fakeroot](http://man.he.net/man1/fakeroot)\", which is derived from the BitBake keyword/variable flag that requests a fake root environment for a task.

> 能够在虚拟根环境中运行任务的能力被称为"[fakeroot](http://man.he.net/man1/fakeroot)"，它源自 BitBake 关键字/变量标志，该标志请求任务的虚拟根环境。

In the `OpenEmbedded Build System`{.interpreted-text role="term"}, the program that implements fakeroot is known as :yocto_home:[Pseudo \</software-item/pseudo/\>]{.title-ref}. Pseudo overrides system calls by using the environment variable `LD_PRELOAD`, which results in the illusion of running as root. To keep track of \"fake\" file ownership and permissions resulting from operations that require root permissions, Pseudo uses an SQLite 3 database. This database is stored in `${``WORKDIR`{.interpreted-text role="term"}`}/pseudo/files.db` for individual recipes. Storing the database in a file as opposed to in memory gives persistence between tasks and builds, which is not accomplished using fakeroot.

> 在开放式嵌入式构建系统中，实现 fakeroot 的程序被称为 yocto_home：[伪/软件项目/伪/]。伪使用环境变量 LD_PRELOAD 覆盖系统调用，从而产生仿真根的错觉。为了跟踪需要根权限才能执行的操作产生的“伪”文件所有权和权限，伪使用 SQLite 3 数据库。这个数据库存储在单个配方的 `${WORKDIR}/pseudo/files.db` 中。将数据库存储在文件中而不是内存中，可以在任务和构建之间持久保存，而使用 fakeroot 无法实现。

::: note
::: title
Note
:::

If you add your own task that manipulates the same files or directories as a fakeroot task, then that task also needs to run under fakeroot. Otherwise, the task cannot run root-only operations, and cannot see the fake file ownership and permissions set by the other task. You need to also add a dependency on `virtual/fakeroot-native:do_populate_sysroot`, giving the following:

> 如果您添加自己的任务，该任务与 fakeroot 任务操作相同的文件或目录，那么该任务也需要在 fakeroot 下运行。否则，该任务无法运行 root-only 操作，也无法看到其他任务设置的虚拟文件所有权和权限。您还需要添加对 `virtual/fakeroot-native:do_populate_sysroot` 的依赖，如下所示：

```
fakeroot do_mytask () {
    ...
}
do_mytask[depends] += "virtual/fakeroot-native:do_populate_sysroot"
```

:::

For more information, see the `FAKEROOT* <bitbake:FAKEROOT>`{.interpreted-text role="term"} variables in the BitBake User Manual. You can also reference the \"[Why Not Fakeroot?](https://github.com/wrpseudo/pseudo/wiki/WhyNotFakeroot)\" article for background information on Fakeroot and Pseudo.

> 更多信息，请参阅 BitBake 用户手册中的 `FAKEROOT* <bitbake:FAKEROOT>` 变量。您还可以参考“[为什么不使用 Fakeroot？](https://github.com/wrpseudo/pseudo/wiki/WhyNotFakeroot)”文章，了解有关 Fakeroot 和 Pseudo 的背景信息。
