---
tip: translate by baidu@2023-06-07 10:47:13
...
---
title: Introducing the Yocto Project
------------------------------------

# What is the Yocto Project?

The Yocto Project is an open source collaboration project that helps developers create custom Linux-based systems that are designed for embedded products regardless of the product\'s hardware architecture. Yocto Project provides a flexible toolset and a development environment that allows embedded device developers across the world to collaborate through shared technologies, software stacks, configurations, and best practices used to create these tailored Linux images.

> Yocto 项目是一个开源协作项目，它帮助开发人员创建定制的基于 Linux 的系统，这些系统是为嵌入式产品设计的，而不考虑产品的硬件架构。Yocto 项目提供了一个灵活的工具集和开发环境，允许世界各地的嵌入式设备开发人员通过共享技术、软件堆栈、配置和最佳实践进行协作，以创建这些定制的 Linux 映像。

Thousands of developers worldwide have discovered that Yocto Project provides advantages in both systems and applications development, archival and management benefits, and customizations used for speed, footprint, and memory utilization. The project is a standard when it comes to delivering embedded software stacks. The project allows software customizations and build interchange for multiple hardware platforms as well as software stacks that can be maintained and scaled.

> 全球数千名开发人员发现，Yocto 项目在系统和应用程序开发、归档和管理方面都具有优势，并可用于速度、占地面积和内存利用率的定制。该项目是交付嵌入式软件堆栈的标准。该项目允许对多个硬件平台以及可以维护和扩展的软件堆栈进行软件定制和构建交换。

![image](figures/key-dev-elements.png){width="100.0%"}

For further introductory information on the Yocto Project, you might be interested in this [article](https://www.embedded.com/electronics-blogs/say-what-/4458600/Why-the-Yocto-Project-for-my-IoT-Project-) by Drew Moseley and in this short introductory [video](https://www.youtube.com/watch?v=utZpKM7i5Z4).

> 有关 Yocto 项目的更多介绍性信息，您可能对这篇[文章]感兴趣([https://www.embedded.com/electronics-blogs/say-what-/4458600/Why-the-Yocto-Project-for-my-IoT-Project-](https://www.embedded.com/electronics-blogs/say-what-/4458600/Why-the-Yocto-Project-for-my-IoT-Project-))由 Drew Moseley 和在这个简短的介绍[视频](https://www.youtube.com/watch?v=utZpKM7i5Z4)。

The remainder of this section overviews advantages and challenges tied to the Yocto Project.

> 本节的其余部分概述了与 Yocto 项目相关的优势和挑战。

## Features

Here are features and advantages of the Yocto Project:

> 以下是 Yocto 项目的特点和优势：

- *Widely Adopted Across the Industry:* Many semiconductor, operating system, software, and service vendors adopt and support the Yocto Project in their products and services. For a look at the Yocto Project community and the companies involved with the Yocto Project, see the \"COMMUNITY\" and \"ECOSYSTEM\" tabs on the :yocto_home:[Yocto Project \<\>]{.title-ref} home page.

> -*在整个行业广泛采用：*许多半导体、操作系统、软件和服务供应商在其产品和服务中采用并支持 Yocto 项目。有关 Yocto Project 社区和参与 Yocto 项目的公司的信息，请参阅：Yocto_home:[Yocto Project\<\>]{.title-ref}主页上的\“community\”和\“ECOSYSTEM\”选项卡。

- *Architecture Agnostic:* Yocto Project supports Intel, ARM, MIPS, AMD, PPC and other architectures. Most ODMs, OSVs, and chip vendors create and supply BSPs that support their hardware. If you have custom silicon, you can create a BSP that supports that architecture.

> -*架构不可知：*Yocto 项目支持 Intel、ARM、MIPS、AMD、PPC 等架构。大多数 ODM、OSV 和芯片供应商创建并提供支持其硬件的 BSP。如果您有定制的硅，您可以创建一个支持该体系结构的 BSP。

Aside from broad architecture support, the Yocto Project fully supports a wide range of devices emulated by the Quick EMUlator (QEMU).

> 除了广泛的体系结构支持外，Yocto 项目还完全支持 Quick EMUlator（QEMU）模拟的各种设备。

- *Images and Code Transfer Easily:* Yocto Project output can easily move between architectures without moving to new development environments. Additionally, if you have used the Yocto Project to create an image or application and you find yourself not able to support it, commercial Linux vendors such as Wind River, Mentor Graphics, Timesys, and ENEA could take it and provide ongoing support. These vendors have offerings that are built using the Yocto Project.

> -*图像和代码传输方便：*Yocto 项目的输出可以在架构之间轻松移动，而无需移动到新的开发环境。此外，如果您使用 Yocto 项目创建了一个映像或应用程序，但发现自己无法支持它，Wind River、Mentor Graphics、Timesys 和 ENEA 等商业 Linux 供应商可以接受它并提供持续的支持。这些供应商提供的产品是使用 Yocto 项目构建的。

- *Flexibility:* Corporations use the Yocto Project many different ways. One example is to create an internal Linux distribution as a code base the corporation can use across multiple product groups. Through customization and layering, a project group can leverage the base Linux distribution to create a distribution that works for their product needs.

> -*灵活性：*公司使用 Yocto 项目的方式多种多样。一个例子是创建一个内部 Linux 发行版，作为公司可以跨多个产品组使用的代码库。通过定制和分层，项目组可以利用基本的 Linux 发行版来创建一个满足其产品需求的发行版。

- *Ideal for Constrained Embedded and IoT devices:* Unlike a full Linux distribution, you can use the Yocto Project to create exactly what you need for embedded devices. You only add the feature support or packages that you absolutely need for the device. For devices that have display hardware, you can use available system components such as X11, Wayland, GTK+, Qt, Clutter, and SDL (among others) to create a rich user experience. For devices that do not have a display or where you want to use alternative UI frameworks, you can choose to not build these components.

> -*非常适合受限制的嵌入式和物联网设备：*与完整的 Linux 发行版不同，您可以使用 Yocto Project 来创建嵌入式设备所需的产品。您只需添加设备绝对需要的功能支持或软件包。对于具有显示硬件的设备，您可以使用 X11、Wayland、GTK+、Qt、Clutter 和 SDL 等可用的系统组件来创建丰富的用户体验。对于没有显示器或希望使用替代 UI 框架的设备，可以选择不构建这些组件。

- *Comprehensive Toolchain Capabilities:* Toolchains for supported architectures satisfy most use cases. However, if your hardware supports features that are not part of a standard toolchain, you can easily customize that toolchain through specification of platform-specific tuning parameters. And, should you need to use a third-party toolchain, mechanisms built into the Yocto Project allow for that.

> -*全面的工具链功能：*支持体系结构的工具链满足大多数用例。然而，如果您的硬件支持不属于标准工具链的功能，您可以通过指定特定于平台的调整参数来轻松地自定义该工具链。而且，如果您需要使用第三方工具链，Yocto 项目中内置的机制允许这样做。

- *Mechanism Rules Over Policy:* Focusing on mechanism rather than policy ensures that you are free to set policies based on the needs of your design instead of adopting decisions enforced by some system software provider.

> -*机制规则高于策略：*关注机制而非策略可确保您可以根据设计需求自由设置策略，而不是采用某些系统软件提供商强制执行的决策。

- *Uses a Layer Model:* The Yocto Project `layer infrastructure <overview-manual/yp-intro:the yocto project layer model>`{.interpreted-text role="ref"} groups related functionality into separate bundles. You can incrementally add these grouped functionalities to your project as needed. Using layers to isolate and group functionality reduces project complexity and redundancy, allows you to easily extend the system, make customizations, and keep functionality organized.

> -*使用层模型：*Yocto 项目 `Layer infrastructure<overview manual/yp intro:Yocto项目层模型>`{.depredicted text role=“ref”}将相关功能分组到单独的捆绑包中。您可以根据需要将这些分组功能增量添加到项目中。使用层来隔离和分组功能可以降低项目的复杂性和冗余度，使您能够轻松地扩展系统、进行自定义并保持功能的有序性。

- *Supports Partial Builds:* You can build and rebuild individual packages as needed. Yocto Project accomplishes this through its `overview-manual/concepts:shared state cache`{.interpreted-text role="ref"} (sstate) scheme. Being able to build and debug components individually eases project development.

> -*支持部分生成：*您可以根据需要生成和重新生成单个包。Yocto 项目通过其“概述手册/概念：共享状态缓存”{.depredicted text role=“ref”}（sstate）方案实现了这一点。能够单独构建和调试组件简化了项目开发。

- *Releases According to a Strict Schedule:* Major releases occur on a `six-month cycle </ref-manual/release-process>`{.interpreted-text role="doc"} predictably in October and April. The most recent two releases support point releases to address common vulnerabilities and exposures. This predictability is crucial for projects based on the Yocto Project and allows development teams to plan activities.

> -*根据严格的时间表发布：*主要发布按 `六个月的周期</ref manual/release process>`{.depredicted text role=“doc”}发生，可预测地发生在 10 月和 4 月。最近的两个版本支持点发布，以解决常见的漏洞和暴露。这种可预测性对于基于 Yocto 项目的项目至关重要，并允许开发团队规划活动。

- *Rich Ecosystem of Individuals and Organizations:* For open source projects, the value of community is very important. Support forums, expertise, and active developers who continue to push the Yocto Project forward are readily available.

> -*丰富的个人和组织生态系统：*对于开源项目来说，社区的价值非常重要。支持论坛、专业知识和积极推动 Yocto 项目的开发人员随时可用。

- *Binary Reproducibility:* The Yocto Project allows you to be very specific about dependencies and achieves very high percentages of binary reproducibility (e.g. 99.8% for `core-image-minimal`). When distributions are not specific about which packages are pulled in and in what order to support dependencies, other build systems can arbitrarily include packages.

> -*二进制再现性：*Yocto 项目允许您非常具体地说明相关性，并实现非常高百分比的二进制再现性（例如，“核心图像最小值”为 99.8%）。当发行版不特定于引入哪些包以及支持依赖关系的顺序时，其他构建系统可以任意包含包。

- *License Manifest:* The Yocto Project provides a `license manifest <dev-manual/licenses:maintaining open source license compliance during your product's lifecycle>`{.interpreted-text role="ref"} for review by people who need to track the use of open source licenses (e.g. legal teams).

> -*许可证清单：*Yocto 项目提供了一份“许可证清单 <dev manual/licenses:在产品生命周期内保持开源许可证合规性 >`{.depredicted text role=“ref”}，供需要跟踪开源许可证使用情况的人员（如法律团队）审查。

## Challenges

Here are challenges you might encounter when developing using the Yocto Project:

> 以下是使用 Yocto 项目进行开发时可能遇到的挑战：

- *Steep Learning Curve:* The Yocto Project has a steep learning curve and has many different ways to accomplish similar tasks. It can be difficult to choose between such ways.

> -*陡峭的学习曲线：*Yocto 项目的学习曲线陡峭，有许多不同的方法来完成类似的任务。在这些方式之间做出选择可能很困难。

- *Understanding What Changes You Need to Make For Your Design Requires Some Research:* Beyond the simple tutorial stage, understanding what changes need to be made for your particular design can require a significant amount of research and investigation. For information that helps you transition from trying out the Yocto Project to using it for your project, see the \"`what-i-wish-id-known:what i wish i'd known about yocto project`{.interpreted-text role="ref"}\" and \"`transitioning-to-a-custom-environment:transitioning to a custom environment for systems development`{.interpreted-text role="ref"}\" documents on the Yocto Project website.

> -*了解您需要对设计进行哪些更改需要一些研究：*除了简单的教程阶段，了解您的特定设计需要进行哪些更改可能需要大量的研究和调查。有关帮助您从试用 Yocto 项目过渡到将其用于您的项目的信息，请参阅 Yocto Project 网站上的“`what-i-wish-id-known:我希望我了解的Yocto项目的内容`{.depredicted text role=“ref”}\”和“`transitioning-to-custom-environment:过渡到用于系统开发的自定义环境`{.deverdicted text role=“ref”}”文档。

- *Project Workflow Could Be Confusing:* The `Yocto Project workflow <overview-manual/development-environment:the yocto project development environment>`{.interpreted-text role="ref"} could be confusing if you are used to traditional desktop and server software development. In a desktop development environment, there are mechanisms to easily pull and install new packages, which are typically pre-compiled binaries from servers accessible over the Internet. Using the Yocto Project, you must modify your configuration and rebuild to add additional packages.

> -*项目工作流程可能令人困惑：*如果您习惯于传统的桌面和服务器软件开发，`Yocto项目工作流程<概述手册/开发环境：Yocto项目开发环境>`{.depreced text role=“ref”}可能会令人困惑。在桌面开发环境中，有一些机制可以轻松地提取和安装新的包，这些包通常是从可通过 Internet 访问的服务器中预编译的二进制文件。使用 Yocto 项目，您必须修改配置并重新生成以添加其他包。

- *Working in a Cross-Build Environment Can Feel Unfamiliar:* When developing code to run on a target, compilation, execution, and testing done on the actual target can be faster than running a BitBake build on a development host and then deploying binaries to the target for test. While the Yocto Project does support development tools on the target, the additional step of integrating your changes back into the Yocto Project build environment would be required. Yocto Project supports an intermediate approach that involves making changes on the development system within the BitBake environment and then deploying only the updated packages to the target.

> -*在跨构建环境中工作可能会感到不熟悉：*当开发要在目标上运行的代码时，在实际目标上进行编译、执行和测试可能比在开发主机上运行 BitBake 构建然后将二进制文件部署到目标进行测试更快。虽然 Yocto 项目确实支持目标上的开发工具，但还需要将您的更改重新集成到 Yocto Project 构建环境中。Yocto 项目支持一种中间方法，即在 BitBake 环境中对开发系统进行更改，然后仅将更新后的包部署到目标。

The Yocto Project `OpenEmbedded Build System`{.interpreted-text role="term"} produces packages in standard formats (i.e. RPM, DEB, IPK, and TAR). You can deploy these packages into the running system on the target by using utilities on the target such as `rpm` or `ipk`.

> Yocto 项目 `OpenEmbedded Build System`｛.explored text role=“term”｝生成标准格式的包（即 RPM、DEB、IPK 和 TAR）。您可以使用目标上的实用程序（如“rpm”或“ipk”）将这些包部署到目标上正在运行的系统中。

- *Initial Build Times Can be Significant:* Long initial build times are unfortunately unavoidable due to the large number of packages initially built from scratch for a fully functioning Linux system. Once that initial build is completed, however, the shared-state (sstate) cache mechanism Yocto Project uses keeps the system from rebuilding packages that have not been \"touched\" since the last build. The sstate mechanism significantly reduces times for successive builds.

> -*初始构建时间可能很长：*不幸的是，由于最初为一个功能齐全的 Linux 系统从头开始构建了大量软件包，因此长的初始构建时间是不可避免的。然而，一旦初始构建完成，Yocto Project 使用的共享状态（sstate）缓存机制将阻止系统重建自上次构建以来从未“接触”过的包。sstate 机制显著减少了连续构建的时间。

# The Yocto Project Layer Model

The Yocto Project\'s \"Layer Model\" is a development model for embedded and IoT Linux creation that distinguishes the Yocto Project from other simple build systems. The Layer Model simultaneously supports collaboration and customization. Layers are repositories that contain related sets of instructions that tell the `OpenEmbedded Build System`{.interpreted-text role="term"} what to do. You can collaborate, share, and reuse layers.

> Yocto 项目的“层模型”是一个用于嵌入式和物联网 Linux 创建的开发模型，它将 Yocto 计划与其他简单构建系统区分开来。层模型同时支持协作和自定义。层是包含相关指令集的存储库，这些指令集告诉 `OpenEmbedded Build System`｛.explored text role=“term”｝该做什么。您可以协作、共享和重用层。

Layers can contain changes to previous instructions or settings at any time. This powerful override capability is what allows you to customize previously supplied collaborative or community layers to suit your product requirements.

> 图层可以随时包含对先前指令或设置的更改。这种强大的覆盖功能允许您自定义以前提供的协作或社区层，以满足您的产品需求。

You use different layers to logically separate information in your build. As an example, you could have BSP, GUI, distro configuration, middleware, or application layers. Putting your entire build into one layer limits and complicates future customization and reuse. Isolating information into layers, on the other hand, helps simplify future customizations and reuse. You might find it tempting to keep everything in one layer when working on a single project. However, the more modular your Metadata, the easier it is to cope with future changes.

> 您可以使用不同的层在构建中以逻辑方式分离信息。例如，您可以拥有 BSP、GUI、发行版配置、中间件或应用程序层。将您的整个构建放在一个层中会限制并使未来的定制和重用变得复杂。另一方面，将信息隔离到层中有助于简化未来的定制和重用。在处理单个项目时，您可能会发现将所有内容都放在一层很诱人。然而，元数据模块化程度越高，就越容易应对未来的变化。

::: note
::: title
Note
:::

- Use Board Support Package (BSP) layers from silicon vendors when possible.
- Familiarize yourself with the :yocto_home:[Yocto Project Compatible Layers \</software-overview/layers/\>]{.title-ref} or the :oe_layerindex:[OpenEmbedded Layer Index \<\>]{.title-ref}. The latter contains more layers but they are less universally validated.

> -熟悉：yocto_home:[yocto Project Compatible Layers\</software overview/Layers/\>]｛.title-ref｝或：oe_layerindex:[OpenEmbedded Layer Index\<\>]{.title-ref｝。后者包含更多的层，但没有得到普遍验证。

- Layers support the inclusion of technologies, hardware components, and software components. The `Yocto Project Compatible <dev-manual/layers:making sure your layer is compatible with yocto project>`{.interpreted-text role="ref"} designation provides a minimum level of standardization that contributes to a strong ecosystem. \"YP Compatible\" is applied to appropriate products and software components such as BSPs, other OE-compatible layers, and related open-source projects, allowing the producer to use Yocto Project badges and branding assets.

> -层支持包含技术、硬件组件和软件组件。`Yocto Project Compatible<devmanual/layers:确保您的层与Yocto Project兼容>`{.depredicted text role=“ref”}名称提供了有助于强大生态系统的最低标准化水平\“YP Compatible\”适用于适当的产品和软件组件，如 BSP、其他 OE 兼容层和相关的开源项目，允许生产商使用 Yocto 项目徽章和品牌资产。

:::

> ：：：

To illustrate how layers are used to keep things modular, consider machine customizations. These types of customizations typically reside in a special layer, rather than a general layer, called a BSP Layer. Furthermore, the machine customizations should be isolated from recipes and Metadata that support a new GUI environment, for example. This situation gives you a couple of layers: one for the machine configurations, and one for the GUI environment. It is important to understand, however, that the BSP layer can still make machine-specific additions to recipes within the GUI environment layer without polluting the GUI layer itself with those machine-specific changes. You can accomplish this through a recipe that is a BitBake append (`.bbappend`) file, which is described later in this section.

> 为了说明如何使用层来保持模块化，请考虑机器定制。这些类型的自定义通常位于一个特殊层，而不是一个称为 BSP 层的通用层。此外，例如，机器定制应该与支持新 GUI 环境的配方和元数据隔离。这种情况为您提供了两个层：一个用于机器配置，另一个用于 GUI 环境。然而，重要的是要理解，BSP 层仍然可以在 GUI 环境层内对配方进行机器特定的添加，而不会因这些机器特定的更改而污染 GUI 层本身。您可以通过一个 BitBake 附加（`.bappend`）文件的配方来实现这一点，本节稍后将对此进行描述。

::: note
::: title
Note
:::

For general information on BSP layer structure, see the `/bsp-guide/index`{.interpreted-text role="doc"}.

> 有关 BSP 层结构的一般信息，请参阅 `nbsp guide/index`｛.depreted text role=“doc”｝。
> :::

The `Source Directory`{.interpreted-text role="term"} contains both general layers and BSP layers right out of the box. You can easily identify layers that ship with a Yocto Project release in the Source Directory by their names. Layers typically have names that begin with the string `meta-`.

> “源目录”｛.explored text role=“term”｝包含常规层和 BSP 层。您可以通过名称在源目录中轻松识别 Yocto Project 版本附带的图层。层的名称通常以字符串“meta-”开头。

::: note
::: title
Note
:::

It is not a requirement that a layer name begin with the prefix `meta-`, but it is a commonly accepted standard in the Yocto Project community.

> 图层名称不要求以前缀“meta-”开头，但这是 Yocto Project 社区中普遍接受的标准。
> :::

For example, if you were to examine the :yocto\_[git:%60tree](git:%60tree) view \</poky/tree/\>[ of the ]{.title-ref}[poky]{.title-ref}[ repository, you will see several layers: ]{.title-ref}[meta]{.title-ref}[, ]{.title-ref}[meta-skeleton]{.title-ref}[, ]{.title-ref}[meta-selftest]{.title-ref}[, ]{.title-ref}[meta-poky]{.title-ref}[, and ]{.title-ref}[meta-yocto-bsp]{.title-ref}\`. Each of these repositories represents a distinct layer.

> 例如如果您要检查[｛.title-ref｝[poky]｛.title-ref｝[存储库的：yocto\_[git:%60tree]（git:%60tree）视图\</poky/tree/\>[，您将看到几个层：]｛.ttitle-ref}[meta]｛.title-ref}[，]｛\title-rev｝[meta skeleton]｛_title-rev][，]｝.title-ref｝[，]{.title-def｝[meta self-test]｛%title-ref-｝[，〕｛.title-ref〕〔meta-poky〕｛.title ref｝〔，和〕｛.title-ref｝〔meta-yocto bsp〕｛。title ref｝\`。每个存储库代表一个不同的层。

For procedures on how to create layers, see the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 有关如何创建层的步骤，请参阅 Yocto 项目开发任务手册中的\“`dev manual/layers:understanding and createing layers`｛.depreted text role=“ref”｝\”一节。

# Components and Tools

The Yocto Project employs a collection of components and tools used by the project itself, by project developers, and by those using the Yocto Project. These components and tools are open source projects and metadata that are separate from the reference distribution (`Poky`{.interpreted-text role="term"}) and the `OpenEmbedded Build System`{.interpreted-text role="term"}. Most of the components and tools are downloaded separately.

> Yocto 项目采用了项目本身、项目开发人员和使用 Yocto 的人员使用的组件和工具的集合。这些组件和工具是开源项目和元数据，与引用分发（`Poky`｛.depredicted text role=“term”｝）和 `OpenEmbedded Build System`｛.epredicted textrole=”term“｝）是分开的。大多数组件和工具都是单独下载的。

This section provides brief overviews of the components and tools associated with the Yocto Project.

> 本节简要概述了与 Yocto 项目相关的组件和工具。

## Development Tools

Here are tools that help you develop images and applications using the Yocto Project:

> 以下是帮助您使用 Yocto 项目开发图像和应用程序的工具：

- *CROPS:* [CROPS](https://github.com/crops/poky-container/) is an open source, cross-platform development framework that leverages [Docker Containers](https://www.docker.com/). CROPS provides an easily managed, extensible environment that allows you to build binaries for a variety of architectures on Windows, Linux and Mac OS X hosts.

> -作物([https://github.com/crops/poky-container/](https://github.com/crops/poky-container/))是一个开源的跨平台开发框架，利用了 [Docker Containers](https://www.docker.com/)。CROPS 提供了一个易于管理、可扩展的环境，允许您在 Windows、Linux 和 Mac OS X 主机上为各种体系结构构建二进制文件。

- *devtool:* This command-line tool is available as part of the extensible SDK (eSDK) and is its cornerstone. You can use `devtool` to help build, test, and package software within the eSDK. You can use the tool to optionally integrate what you build into an image built by the OpenEmbedded build system.

> -*devtool:*此命令行工具是可扩展 SDK（eSDK）的一部分，是其基石。您可以使用“devtool”来帮助在 eSDK 中构建、测试和打包软件。您可以使用该工具选择性地将构建的内容集成到由 OpenEmbedded 构建系统构建的映像中。

The `devtool` command employs a number of sub-commands that allow you to add, modify, and upgrade recipes. As with the OpenEmbedded build system, \"recipes\" represent software packages within `devtool`. When you use `devtool add`, a recipe is automatically created. When you use `devtool modify`, the specified existing recipe is used in order to determine where to get the source code and how to patch it. In both cases, an environment is set up so that when you build the recipe a source tree that is under your control is used in order to allow you to make changes to the source as desired. By default, both new recipes and the source go into a \"workspace\" directory under the eSDK. The `devtool upgrade` command updates an existing recipe so that you can build it for an updated set of source files.

> “devtool”命令使用了许多子命令，允许您添加、修改和升级配方。与 OpenEmbedded 构建系统一样，“配方”表示“devtool”中的软件包。当您使用“devtool add”时，会自动创建一个配方。当您使用“devtool-modify”时，会使用指定的现有配方来确定在哪里获取源代码以及如何对其进行修补。在这两种情况下，都会设置一个环境，以便在构建配方时使用您控制的源树，以便您根据需要对源代码进行更改。默认情况下，新配方和源代码都进入 eSDK 下的“workspace\”目录。“devtool upgrade”命令更新现有配方，以便您可以为更新的源文件集构建它。

You can read about the `devtool` workflow in the Yocto Project Application Development and Extensible Software Development Kit (eSDK) Manual in the \"``sdk-manual/extensible:using \`\`devtool\`\` in your sdk workflow``{.interpreted-text role="ref"}\" section.

> 您可以阅读 Yocto 项目应用程序开发和可扩展软件开发工具包（eSDK）手册中的“devtool”工作流，该手册位于\“`` sdk Manual/Extensible：在您的sdk工作流`` 中使用\`\`devtool\``”部分。

- *Extensible Software Development Kit (eSDK):* The eSDK provides a cross-development toolchain and libraries tailored to the contents of a specific image. The eSDK makes it easy to add new applications and libraries to an image, modify the source for an existing component, test changes on the target hardware, and integrate into the rest of the OpenEmbedded build system. The eSDK gives you a toolchain experience supplemented with the powerful set of `devtool` commands tailored for the Yocto Project environment.

> -*可扩展软件开发工具包（eSDK）：*eSDK 提供了一个跨开发工具链和库，可根据特定映像的内容进行定制。eSDK 可以轻松地将新的应用程序和库添加到映像中，修改现有组件的源代码，测试目标硬件上的更改，并集成到 OpenEmbedded 构建系统的其余部分。eSDK 为您提供了工具链体验，并为 Yocto Project 环境量身定制了一组强大的“devtool”命令。

For information on the eSDK, see the `/sdk-manual/index`{.interpreted-text role="doc"} Manual.

> 有关 eSDK 的信息，请参阅 `/sdk manual/index`｛.depreted text role=“doc”｝手册。

- *Toaster:* Toaster is a web interface to the Yocto Project OpenEmbedded build system. Toaster allows you to configure, run, and view information about builds. For information on Toaster, see the `/toaster-manual/index`{.interpreted-text role="doc"}.

> -*Toaster:*Toaster 是 Yocto Project OpenEmbedded 构建系统的 web 界面。Toster 允许您配置、运行和查看有关生成的信息。有关 Toaster 的信息，请参阅 `/Toaster manual/index`｛.depreted text role=“doc”｝。

## Production Tools

Here are tools that help with production related activities using the Yocto Project:

> 以下是有助于使用 Yocto 项目开展生产相关活动的工具：

- *Auto Upgrade Helper:* This utility when used in conjunction with the `OpenEmbedded Build System`{.interpreted-text role="term"} (BitBake and OE-Core) automatically generates upgrades for recipes that are based on new versions of the recipes published upstream. See `dev-manual/upgrading-recipes:using the auto upgrade helper (auh)`{.interpreted-text role="ref"} for how to set it up.

> -*自动升级帮助程序：*此实用程序与 `OpenEmbedded Build System`｛.explored text role=“term”｝（BitBake 和 OE Core）结合使用时，会自动为基于上游发布的新版本配方的配方生成升级。请参阅“dev manual/upgrading recipes:using the auto upgrade helper（auh）”｛.depreced text role=“ref”｝了解如何设置它。

- *Recipe Reporting System:* The Recipe Reporting System tracks recipe versions available for Yocto Project. The main purpose of the system is to help you manage the recipes you maintain and to offer a dynamic overview of the project. The Recipe Reporting System is built on top of the :oe_layerindex:[OpenEmbedded Layer Index \<\>]{.title-ref}, which is a website that indexes OpenEmbedded-Core layers.

> -配方报告系统：配方报告系统跟踪 Yocto 项目可用的配方版本。该系统的主要目的是帮助您管理您维护的配方，并提供项目的动态概述。配方报告系统建立在：oe_layeindex:[OpenEmbedded Layer Index\<\>]｛.title ref｝之上，该网站为 OpenEmbedded Core 层编制索引。

- *Patchwork:* [Patchwork](https://patchwork.yoctoproject.org/) is a fork of a project originally started by [OzLabs](https://ozlabs.org/). The project is a web-based tracking system designed to streamline the process of bringing contributions into a project. The Yocto Project uses Patchwork as an organizational tool to handle patches, which number in the thousands for every release.

> -*补丁：*[补丁](https://patchwork.yoctoproject.org/)是最初由 OzLabs 启动的一个项目的分支([https://ozlabs.org/](https://ozlabs.org/))。该项目是一个基于网络的跟踪系统，旨在简化将捐款纳入项目的过程。Yocto 项目使用 Patchwork 作为一种组织工具来处理补丁，每个版本的补丁数量都有数千个。

- *AutoBuilder:* AutoBuilder is a project that automates build tests and quality assurance (QA). By using the public AutoBuilder, anyone can determine the status of the current development branch of Poky.

> -*AutoBuilder：*AutoBuilder 是一个自动化构建测试和质量保证（QA）的项目。通过使用公共的 AutoBuilder，任何人都可以确定 Poky 当前开发分支的状态。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

AutoBuilder is based on buildbot.

> AutoBuilder 是基于 buildbot 的。

:::

> ：：：

A goal of the Yocto Project is to lead the open source industry with a project that automates testing and QA procedures. In doing so, the project encourages a development community that publishes QA and test plans, publicly demonstrates QA and test plans, and encourages development of tools that automate and test and QA procedures for the benefit of the development community.

> Yocto 项目的一个目标是通过一个自动化测试和 QA 程序的项目来领导开源行业。在这样做的过程中，该项目鼓励开发社区发布 QA 和测试计划，公开演示 QA 和测试程序，并鼓励开发自动化和测试 QA 程序的工具，以造福开发社区。

You can learn more about the AutoBuilder used by the Yocto Project Autobuilder `here </test-manual/understand-autobuilder>`{.interpreted-text role="doc"}.

> 您可以在此处了解 Yocto Project AutoBuilder 使用的 AutoBuilder 的更多信息 </test manual/understand AutoBuilder>`｛.depreted text role=“doc”｝。

- *Pseudo:* Pseudo is the Yocto Project implementation of [fakeroot](http://man.he.net/man1/fakeroot), which is used to run commands in an environment that seemingly has root privileges.

> -*伪：*伪是[fakeroot]的 Yocto 项目实现([http://man.he.net/man1/fakeroot](http://man.he.net/man1/fakeroot))，用于在似乎具有 root 权限的环境中运行命令。

During a build, it can be necessary to perform operations that require system administrator privileges. For example, file ownership or permissions might need to be defined. Pseudo is a tool that you can either use directly or through the environment variable `LD_PRELOAD`. Either method allows these operations to succeed even without system administrator privileges.

> 在生成过程中，可能需要执行需要系统管理员权限的操作。例如，可能需要定义文件所有权或权限。Pseudo 是一种可以直接使用或通过环境变量“LD_PRELOAD”使用的工具。即使没有系统管理员权限，任何一种方法都可以使这些操作成功。

Thanks to Pseudo, the Yocto Project never needs root privileges to build images for your target system.

> 多亏了 Pseudo，Yocto 项目从不需要 root 权限来为您的目标系统构建映像。

You can read more about Pseudo in the \"`overview-manual/concepts:fakeroot and pseudo`{.interpreted-text role="ref"}\" section.

> 您可以在\“`overview manual/concepts:fakeroot and Pseudo`｛.explored text role=”ref“｝\”一节中阅读更多关于伪的信息。

## Open-Embedded Build System Components

Here are components associated with the `OpenEmbedded Build System`{.interpreted-text role="term"}:

> 以下是与 `OpenEmbedded Build System`｛.depredicted text role=“term”｝相关联的组件：

- *BitBake:* BitBake is a core component of the Yocto Project and is used by the OpenEmbedded build system to build images. While BitBake is key to the build system, BitBake is maintained separately from the Yocto Project.

> -*BitBake:*BitBake 是 Yocto 项目的核心组件，由 OpenEmbedded 构建系统用于构建图像。虽然 BitBake 是构建系统的关键，但 BitBake 与 Yocto 项目是分开维护的。

BitBake is a generic task execution engine that allows shell and Python tasks to be run efficiently and in parallel while working within complex inter-task dependency constraints. In short, BitBake is a build engine that works through recipes written in a specific format in order to perform sets of tasks.

> BitBake 是一个通用的任务执行引擎，它允许 shell 和 Python 任务在复杂的任务间依赖关系约束下高效并行运行。简而言之，BitBake 是一个构建引擎，它通过以特定格式编写的食谱来执行一组任务。

You can learn more about BitBake in the `BitBake User Manual <bitbake:index>`{.interpreted-text role="doc"}.

> 您可以在 `BitBake用户手册<BitBake:index>`{.depreted text role=“doc”}中了解更多关于 BitBake 的信息。

- *OpenEmbedded-Core:* OpenEmbedded-Core (OE-Core) is a common layer of metadata (i.e. recipes, classes, and associated files) used by OpenEmbedded-derived systems, which includes the Yocto Project. The Yocto Project and the OpenEmbedded Project both maintain the OpenEmbedded-Core. You can find the OE-Core metadata in the Yocto Project :yocto\_[git:%60Source](git:%60Source) Repositories \</poky/tree/meta\>\`.

> -*OpenEmbedded Core：*OpenEmbeded Core（OE Core）是一个常见的元数据层（即配方、类和相关文件），由 OpenEmbedding 派生系统使用，包括 Yocto 项目。Yocto 项目和 OpenEmbedded 项目都维护了 OpenEmbeddedCore。您可以在 Yocto 项目中找到 OE 核心元数据：Yocto\_[git:%60Source]（git:%60Source）存储库\</poky/tree/meta\>\`。

Historically, the Yocto Project integrated the OE-Core metadata throughout the Yocto Project source repository reference system (Poky). After Yocto Project Version 1.0, the Yocto Project and OpenEmbedded agreed to work together and share a common core set of metadata (OE-Core), which contained much of the functionality previously found in Poky. This collaboration achieved a long-standing OpenEmbedded objective for having a more tightly controlled and quality-assured core. The results also fit well with the Yocto Project objective of achieving a smaller number of fully featured tools as compared to many different ones.

> 从历史上看，Yocto 项目将 OE 核心元数据集成到整个 Yocto Project 源存储库参考系统（Poky）中。在 Yocto Project Version 1.0 之后，Yocto 项目和 OpenEmbedded 同意合作并共享一组共同的核心元数据（OE core），其中包含以前在 Poky 中发现的大部分功能。这种合作实现了 OpenEmbedded 的一个长期目标，即拥有一个更严格控制和质量保证的核心。结果也很符合 Yocto 项目的目标，即与许多不同的工具相比，实现更少的全功能工具。

Sharing a core set of metadata results in Poky as an integration layer on top of OE-Core. You can see that in this `figure <overview-manual/yp-intro:what is the yocto project?>`{.interpreted-text role="ref"}. The Yocto Project combines various components such as BitBake, OE-Core, script \"glue\", and documentation for its build system.

> 共享一组核心元数据导致 Poky 成为 OE 核心之上的集成层。你可以在这个“图 ＜ 概览手册/yp 简介：什么是 yocto 项目？＞`｛.解释文本角色=“ref”｝。Yocto 项目结合了各种组件，如 BitBake、OE Core、脚本“胶水”和构建系统的文档。

## Reference Distribution (Poky)

Poky is the Yocto Project reference distribution. It contains the `OpenEmbedded Build System`{.interpreted-text role="term"} (BitBake and OE-Core) as well as a set of metadata to get you started building your own distribution. See the figure in \"`overview-manual/yp-intro:what is the yocto project?`{.interpreted-text role="ref"}\" section for an illustration that shows Poky and its relationship with other parts of the Yocto Project.

> Poky 是 Yocto 项目的参考分布。它包含 `OpenEmbedded Build System`｛.explored text role=“term”｝（BitBake 和 OE Core）以及一组元数据，以帮助您开始构建自己的发行版。有关 Poky 及其与 yocto 项目其他部分的关系的说明，请参见“`overview manual/yp intro:什么是yocto项目？`{.depreted text role=“ref”}\”部分中的图。

To use the Yocto Project tools and components, you can download (`clone`) Poky and use it to bootstrap your own distribution.

> 要使用 Yocto Project 工具和组件，您可以下载（“克隆”）Poky 并使用它引导您自己的发行版。

::: note
::: title
Note
:::

Poky does not contain binary files. It is a working example of how to build your own custom Linux distribution from source.

> Poky 不包含二进制文件。这是一个如何从源代码构建自己的自定义 Linux 发行版的工作示例。
> :::

You can read more about Poky in the \"`overview-manual/yp-intro:reference embedded distribution (poky)`{.interpreted-text role="ref"}\" section.

> 您可以在\“`overview manual/yp intro:reference embedded distribution（Poky）`{.depreted text role=”ref“}\”一节中阅读更多关于 Poky 的信息。

## Packages for Finished Targets

Here are components associated with packages for finished targets:

> 以下是与已完成目标的包相关联的组件：

- *Matchbox:* Matchbox is an Open Source, base environment for the X Window System running on non-desktop, embedded platforms such as handhelds, set-top boxes, kiosks, and anything else for which screen space, input mechanisms, or system resources are limited.

> -*火柴盒：*火柴盒是 X Window 系统的开源基础环境，运行在非桌面嵌入式平台上，如手持设备、机顶盒、信息亭以及屏幕空间、输入机制或系统资源有限的任何其他平台上。

Matchbox consists of a number of interchangeable and optional applications that you can tailor to a specific, non-desktop platform to enhance usability in constrained environments.

> Matchbox 由许多可互换和可选的应用程序组成，您可以根据特定的非桌面平台进行定制，以增强受限环境中的可用性。

You can find the Matchbox source in the Yocto Project :yocto\_[git:%60Source](git:%60Source) Repositories \<\>\`.

> 您可以在 Yocto 项目中找到火柴盒来源：Yocto\_[git:%60Source]（git:%60Source）存储库\<\>\`。

- *Opkg:* Open PacKaGe management (opkg) is a lightweight package management system based on the itsy package (ipkg) management system. Opkg is written in C and resembles Advanced Package Tool (APT) and Debian Package (dpkg) in operation.

> -*Opkg:*Open PacKaGe 管理（Opkg）是一个基于 itsy 包（ipkg）管理系统的轻量级包管理系统。Opkg 是用 C 编写的，在操作上类似于 Advanced Package Tool（APT）和 Debian Package（dpkg）。

Opkg is intended for use on embedded Linux devices and is used in this capacity in the :oe_home:[OpenEmbedded \<\>]{.title-ref} and [OpenWrt](https://openwrt.org/) projects, as well as the Yocto Project.

> Opkg 旨在用于嵌入式 Linux 设备，并在以下位置使用：oe_home:[OpenEmbedded\<\>]｛.title-ref｝和 [OpenWrt](https://openwrt.org/) 项目以及 Yocto 项目。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

As best it can, opkg maintains backwards compatibility with ipkg and conforms to a subset of Debian\'s policy manual regarding control files.

> 尽其所能，opkg 保持了与 ipkg 的向后兼容性，并符合 Debian\关于控制文件的政策手册的一个子集。

:::

> ：：：

You can find the opkg source in the Yocto Project :yocto\_[git:%60Source](git:%60Source) Repositories \<\>\`.

> 您可以在 Yocto 项目中找到 opkg 源：Yocto\_[git:%60Source]（git:%60Source）Repositorys\<\>\`。

## Archived Components

The Build Appliance is a virtual machine image that enables you to build and boot a custom embedded Linux image with the Yocto Project using a non-Linux development system.

> Build Appliance 是一个虚拟机映像，使您能够使用非 Linux 开发系统通过 Yocto Project 构建和启动自定义嵌入式 Linux 映像。

Historically, the Build Appliance was the second of three methods by which you could use the Yocto Project on a system that was not native to Linux.

> 从历史上看，Build Appliance 是在非 Linux 本机系统上使用 Yocto 项目的三种方法中的第二种。

1. *Hob:* Hob, which is now deprecated and is no longer available since the 2.1 release of the Yocto Project provided a rudimentary, GUI-based interface to the Yocto Project. Toaster has fully replaced Hob.

> 1.*Hob:*Hob，现在已经被弃用，并且由于 Yocto 项目的 2.1 版本为 Yocto 提供了一个基本的、基于 GUI 的界面而不再可用。烤面包机已经完全取代了烤面包机。

2. *Build Appliance:* Post Hob, the Build Appliance became available. It was never recommended that you use the Build Appliance as a day-to-day production development environment with the Yocto Project. Build Appliance was useful as a way to try out development in the Yocto Project environment.

> 2.*构建设备：*Hob 之后，构建设备可用。从未建议您将 Build Appliance 用作 Yocto 项目的日常生产开发环境。Build Appliance 作为在 Yocto 项目环境中尝试开发的一种方式非常有用。

3. *CROPS:* The final and best solution available now for developing using the Yocto Project on a system not native to Linux is with `CROPS <overview-manual/yp-intro:development tools>`{.interpreted-text role="ref"}.

> 3.*CROPS:*在非 Linux 原生系统上使用 Yocto 项目进行开发的最终和最佳解决方案是 `CROPS<overview manual/yp intro:development tools>`{.depreted text role=“ref”}。

# Development Methods

The Yocto Project development environment usually involves a `Build Host`{.interpreted-text role="term"} and target hardware. You use the Build Host to build images and develop applications, while you use the target hardware to execute deployed software.

> Yocto 项目开发环境通常涉及“构建主机”｛.explored text role=“term”｝和目标硬件。您使用构建主机来构建映像和开发应用程序，同时使用目标硬件来执行已部署的软件。

This section provides an introduction to the choices or development methods you have when setting up your Build Host. Depending on your particular workflow preference and the type of operating system your Build Host runs, you have several choices.

> 本节介绍设置生成主机时的选择或开发方法。根据您的特定工作流首选项和构建主机运行的操作系统类型，您有几个选择。

::: note
::: title
Note
:::

For additional detail about the Yocto Project development environment, see the \"`/overview-manual/development-environment`{.interpreted-text role="doc"}\" chapter.

> 有关 Yocto 项目开发环境的更多详细信息，请参阅\“`/overview manual/development environment`｛.depreted text role=“doc”｝\”一章。
> :::

- *Native Linux Host:* By far the best option for a Build Host. A system running Linux as its native operating system allows you to develop software by directly using the `BitBake`{.interpreted-text role="term"} tool. You can accomplish all aspects of development from a regular shell in a supported Linux distribution.

> -*本机 Linux 主机：*到目前为止，构建主机的最佳选择。将 Linux 作为其本机操作系统运行的系统允许您直接使用“BitBake”｛.explored text role=“term”｝工具来开发软件。您可以在受支持的 Linux 发行版中从常规 shell 完成开发的各个方面。

For information on how to set up a Build Host on a system running Linux as its native operating system, see the \"`dev-manual/start:setting up a native linux host`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 有关如何在运行 Linux 作为其本机操作系统的系统上设置构建主机的信息，请参阅 Yocto 项目开发任务手册中的\“`dev manual/start:setting a native Linux Host`｛.depreted text role=“ref”｝\”一节。

- *CROss PlatformS (CROPS):* Typically, you use [CROPS](https://github.com/crops/poky-container/), which leverages [Docker Containers](https://www.docker.com/), to set up a Build Host that is not running Linux (e.g. Microsoft Windows or macOS).

> -*CROss 平台（CROPS）：*通常，您使用 [CROPS](https://github.com/crops/poky-container/)，利用 [Docker Containers](https://www.docker.com/)，以设置未运行 Linux 的构建主机（例如，Microsoft Windows 或 macOS）。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

You can, however, use CROPS on a Linux-based system.

> 但是，您可以在基于 Linux 的系统上使用 CROPS。

:::

> ：：：

CROPS is an open source, cross-platform development framework that provides an easily managed, extensible environment for building binaries targeted for a variety of architectures on Windows, macOS, or Linux hosts. Once the Build Host is set up using CROPS, you can prepare a shell environment to mimic that of a shell being used on a system natively running Linux.

> CROPS 是一个开源、跨平台的开发框架，为在 Windows、macOS 或 Linux 主机上构建各种体系结构的二进制文件提供了一个易于管理、可扩展的环境。一旦使用 CROPS 设置了构建主机，您就可以准备一个 shell 环境来模拟在本机运行 Linux 的系统上使用的 shell 环境。

For information on how to set up a Build Host with CROPS, see the \"`dev-manual/start:setting up to use cross platforms (crops)`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 有关如何使用 CROPS 设置构建主机的信息，请参阅 Yocto 项目开发任务手册中的\“`dev manual/start:设置以使用跨平台（CROPS）`{.depreted text role=“ref”}\”一节。

- *Windows Subsystem For Linux (WSL 2):* You may use Windows Subsystem For Linux version 2 to set up a Build Host using Windows 10 or later, or Windows Server 2019 or later.

> -*Windows Subsystem For Linux（WSL 2）：*您可以使用 Windows Subsystem For Linux 版本 2，使用 Windows 10 或更高版本，或 Windows Server 2019 或更高级别设置构建主机。

The Windows Subsystem For Linux allows Windows to run a real Linux kernel inside of a lightweight virtual machine (VM).

> Linux 的 Windows 子系统允许 Windows 在轻量级虚拟机（VM）中运行真正的 Linux 内核。

For information on how to set up a Build Host with WSL 2, see the \"`dev-manual/start:setting up to use windows subsystem for linux (wsl 2)`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual.

> 有关如何使用 WSL 2 设置构建主机的信息，请参阅 Yocto 项目开发任务手册中的\“`dev manual/start:setting to use windows subsystem For linux（WSL 2）`{.depreted text role=“ref”}\”一节。

- *Toaster:* Regardless of what your Build Host is running, you can use Toaster to develop software using the Yocto Project. Toaster is a web interface to the Yocto Project\'s `OpenEmbedded Build System`{.interpreted-text role="term"}. The interface allows you to configure and run your builds. Information about builds is collected and stored in a database. You can use Toaster to configure and start builds on multiple remote build servers.

> -*Toaster：*无论您的构建主机正在运行什么，您都可以使用 Toaster 使用 Yocto 项目开发软件。Toaster 是 Yocto Project 的“OpenEmbedded Build System”的 web 界面。该界面允许您配置和运行构建。有关生成的信息被收集并存储在数据库中。您可以使用 Toast 在多个远程生成服务器上配置和启动生成。

For information about and how to use Toaster, see the `/toaster-manual/index`{.interpreted-text role="doc"}.

> 有关如何使用 Toast 的信息，请参阅 `/Toaster manual/index`｛.depreted text role=“doc”｝。

# Reference Embedded Distribution (Poky)

\"Poky\", which is pronounced *Pock*-ee, is the name of the Yocto Project\'s reference distribution or Reference OS Kit. Poky contains the `OpenEmbedded Build System`{.interpreted-text role="term"} (`BitBake`{.interpreted-text role="term"} and `OpenEmbedded-Core (OE-Core)`{.interpreted-text role="term"}) as well as a set of `Metadata`{.interpreted-text role="term"} to get you started building your own distro. In other words, Poky is a base specification of the functionality needed for a typical embedded system as well as the components from the Yocto Project that allow you to build a distribution into a usable binary image.

> \“Poky”发音为 *Pock*-ee，是 Yocto 项目的参考发行版或参考操作系统工具包的名称。Poky 包含 `OpenEmbedded Build System`｛.depredicted text role=“term”｝（`BitBake`｛.epredicted text role=”term“｝和 `OpenEmbeded Core（OE Core）`｛.expredicted textrole=（term）”）以及一组 `Metadata`｛.repredicted extrole=。换句话说，Poky 是典型嵌入式系统所需功能的基本规范，也是 Yocto 项目中允许您将分发构建为可用二进制映像的组件。

Poky is a combined repository of BitBake, OpenEmbedded-Core (which is found in `meta`), `meta-poky`, `meta-yocto-bsp`, and documentation provided all together and known to work well together. You can view these items that make up the Poky repository in the :yocto\_[git:%60Source](git:%60Source) Repositories \</poky/tree/\>\`.

> Poky 是 BitBake、OpenEmbedded Core（可在“meta”中找到）、“meta Poky”、“meta yocto bsp”和文档的组合存储库，这些文档一起提供，并且已知可以很好地协同工作。您可以在以下位置查看组成 Poky 存储库的这些项目：yocto\_[git:%60Source]（git:%60Source）存储库\</Poky/tree/\>\`。

::: note
::: title
Note
:::

If you are interested in all the contents of the poky Git repository, see the \"`ref-manual/structure:top-level core components`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual.

> 如果您对 poky Git 存储库的所有内容感兴趣，请参阅 Yocto 项目参考手册中的“`ref manual/structure:top level core components`｛.depreted text role=“ref”｝”一节。
> :::

The following figure illustrates what generally comprises Poky:

> 下图说明了 Poky 的一般组成：

![image](figures/poky-reference-distribution.png){width="100.0%"}

- BitBake is a task executor and scheduler that is the heart of the OpenEmbedded build system.
- `meta-poky`, which is Poky-specific metadata.
- `meta-yocto-bsp`, which are Yocto Project-specific Board Support Packages (BSPs).
- OpenEmbedded-Core (OE-Core) metadata, which includes shared configurations, global variable definitions, shared classes, packaging, and recipes. Classes define the encapsulation and inheritance of build logic. Recipes are the logical units of software and images to be built.

> -OpenEmbedded Core（OE Core）元数据，包括共享配置、全局变量定义、共享类、打包和配方。类定义了构建逻辑的封装和继承。配方是要构建的软件和映像的逻辑单元。

- Documentation, which contains the Yocto Project source files used to make the set of user manuals.

> -文档，其中包含用于制作用户手册集的 Yocto 项目源文件。

::: note
::: title
Note
:::

While Poky is a \"complete\" distribution specification and is tested and put through QA, you cannot use it as a product \"out of the box\" in its current form.

> 虽然 Poky 是一个“完整”的分发规范，经过测试并通过 QA，但您不能将其作为当前形式的“开箱即用”产品。
> :::

To use the Yocto Project tools, you can use Git to clone (download) the Poky repository then use your local copy of the reference distribution to bootstrap your own distribution.

> 要使用 Yocto Project 工具，您可以使用 Git 克隆（下载）Poky 存储库，然后使用参考发行版的本地副本来引导您自己的发行版。

::: note
::: title
Note
:::

Poky does not contain binary files. It is a working example of how to build your own custom Linux distribution from source.

> Poky 不包含二进制文件。这是一个如何从源代码构建自己的自定义 Linux 发行版的工作示例。
> :::

Poky has a regular, well established, six-month release cycle under its own version. Major releases occur at the same time major releases (point releases) occur for the Yocto Project, which are typically in the Spring and Fall. For more information on the Yocto Project release schedule and cadence, see the \"`/ref-manual/release-process`{.interpreted-text role="doc"}\" chapter in the Yocto Project Reference Manual.

> Poky 有一个固定的、完善的、六个月的发行周期。主要发布与 Yocto 项目的主要发布（点发布）同时发生，通常在春季和秋季。有关 Yocto 项目发布时间表和节奏的更多信息，请参阅《Yocto Project Reference manual》中的“`/ref manual/release process`{.depreted text role=“doc”}\”一章。

Much has been said about Poky being a \"default configuration\". A default configuration provides a starting image footprint. You can use Poky out of the box to create an image ranging from a shell-accessible minimal image all the way up to a Linux Standard Base-compliant image that uses a GNOME Mobile and Embedded (GMAE) based reference user interface called Sato.

> 关于 Poky 是“默认配置”的说法已经很多了。默认配置提供一个起始映像封装外形。您可以使用开箱即用的 Poky 创建一个映像，范围从可访问外壳的最小映像一直到使用 GNOME Mobile and Embedded（GMAE）参考用户界面 Sato 的 Linux Standard Base 兼容映像。

One of the most powerful properties of Poky is that every aspect of a build is controlled by the metadata. You can use metadata to augment these base image types by adding metadata `layers <overview-manual/yp-intro:the yocto project layer model>`{.interpreted-text role="ref"} that extend functionality. These layers can provide, for example, an additional software stack for an image type, add a board support package (BSP) for additional hardware, or even create a new image type.

Metadata is loosely grouped into configuration files or package recipes. A recipe is a collection of non-executable metadata used by BitBake to set variables or define additional build-time tasks. A recipe contains fields such as the recipe description, the recipe version, the license of the package and the upstream source repository. A recipe might also indicate that the build process uses autotools, make, distutils or any other build process, in which case the basic functionality can be defined by the classes it inherits from the OE-Core layer\'s class definitions in `./meta/classes`. Within a recipe you can also define additional tasks as well as task prerequisites. Recipe syntax through BitBake also supports both `:prepend` and `:append` operators as a method of extending task functionality. These operators inject code into the beginning or end of a task. For information on these BitBake operators, see the \"`bitbake-user-manual/bitbake-user-manual-metadata:appending and prepending (override style syntax)`{.interpreted-text role="ref"}\" section in the BitBake User\'s Manual.

> 元数据松散地分为配置文件或包配方。配方是 BitBake 用来设置变量或定义额外构建时任务的不可执行元数据的集合。配方包含诸如配方描述、配方版本、包的许可证和上游源存储库等字段。配方还可能表明构建过程使用自动工具、制造、提取或任何其他构建过程，在这种情况下，基本功能可以由其从 OE 核心层的类定义中继承的类定义/元/类 `。在配方中，您还可以定义其他任务以及任务先决条件。BitBake中的配方语法还支持“：prepend”和“：append”运算符作为扩展任务功能的方法。这些运算符将代码注入任务的开头或结尾。有关这些BitBake运算符的信息，请参阅《BitBake用户手册》中的“` BitBake 用户手册/BitBake 用户手册元数据：附加和前置（重写样式语法）`{.depreted text role=“ref”}\”一节。

# The OpenEmbedded Build System Workflow

The `OpenEmbedded Build System`{.interpreted-text role="term"} uses a \"workflow\" to accomplish image and SDK generation. The following figure overviews that workflow:

> `OpenEmbedded Build System`｛.explored text role=“term”｝使用\“工作流\”来完成图像和 SDK 的生成。下图概述了该工作流程：

![image](figures/YP-flow-diagram.png){width="100.0%"}

Following is a brief summary of the \"workflow\":

> 以下是“工作流”的简要概述：

1. Developers specify architecture, policies, patches and configuration details.

> 1.开发人员指定体系结构、策略、修补程序和配置细节。

2. The build system fetches and downloads the source code from the specified location. The build system supports standard methods such as tarballs or source code repositories systems such as Git.

> 2.构建系统从指定的位置获取并下载源代码。构建系统支持诸如 tarball 之类的标准方法或诸如 Git 之类的源代码存储库系统。

3. Once source code is downloaded, the build system extracts the sources into a local work area where patches are applied and common steps for configuring and compiling the software are run.

> 3.一旦下载了源代码，构建系统就会将源代码提取到本地工作区，在那里应用补丁，并运行配置和编译软件的常见步骤。

4. The build system then installs the software into a temporary staging area where the binary package format you select (DEB, RPM, or IPK) is used to roll up the software.

> 4.然后，构建系统将软件安装到一个临时暂存区，在该临时暂存区中，您选择的二进制包格式（DEB、RPM 或 IPK）用于汇总软件。

5. Different QA and sanity checks run throughout entire build process.

> 5.不同的 QA 和健全性检查贯穿于整个构建过程。

6. After the binaries are created, the build system generates a binary package feed that is used to create the final root file image.

> 6.创建二进制文件后，构建系统生成一个二进制包提要，用于创建最终的根文件映像。

7. The build system generates the file system image and a customized Extensible SDK (eSDK) for application development in parallel.

> 7.构建系统并行生成文件系统映像和用于应用程序开发的定制可扩展 SDK（eSDK）。

For a very detailed look at this workflow, see the \"`overview-manual/concepts:openembedded build system concepts`{.interpreted-text role="ref"}\" section.

> 有关此工作流的详细信息，请参阅\“`overview manual/concepts:openembedded build system concepts`｛.depreted text role=”ref“｝\”一节。

# Some Basic Terms

It helps to understand some basic fundamental terms when learning the Yocto Project. Although there is a list of terms in the \"`Yocto Project Terms </ref-manual/terms>`{.interpreted-text role="doc"}\" section of the Yocto Project Reference Manual, this section provides the definitions of some terms helpful for getting started:

> 在学习 Yocto 项目时，它有助于理解一些基本的基本术语。尽管《Yocto 项目参考手册》的\“`Yocto Project terms</ref manual/terms>`{.depredicted text role=“doc”}\”一节中有一个术语列表，但本节提供了一些有助于入门的术语的定义：

- *Configuration Files:* Files that hold global definitions of variables, user-defined variables, and hardware configuration information. These files tell the `OpenEmbedded Build System`{.interpreted-text role="term"} what to build and what to put into the image to support a particular platform.

> -*配置文件：*保存变量、用户定义变量和硬件配置信息的全局定义的文件。这些文件告诉 `OpenEmbedded Build System`｛.explored text role=“term”｝要构建什么以及要将什么放入图像中以支持特定平台。

- *Extensible Software Development Kit (eSDK):* A custom SDK for application developers. This eSDK allows developers to incorporate their library and programming changes back into the image to make their code available to other application developers. For information on the eSDK, see the `/sdk-manual/index`{.interpreted-text role="doc"} manual.

> -*可扩展软件开发工具包（eSDK）：*为应用程序开发人员定制的 SDK。这个 eSDK 允许开发人员将他们的库和编程更改合并回图像中，以使其他应用程序开发人员可以使用他们的代码。有关 eSDK 的信息，请参阅 `/sdk manual/index`{.depreted text role=“doc”}手册。

- *Layer:* A collection of related recipes. Layers allow you to consolidate related metadata to customize your build. Layers also isolate information used when building for multiple architectures. Layers are hierarchical in their ability to override previous specifications. You can include any number of available layers from the Yocto Project and customize the build by adding your own layers after them. You can search the Layer Index for layers used within Yocto Project.

> -*图层：*相关食谱的集合。层允许您整合相关元数据以自定义构建。层还隔离为多个体系结构构建时使用的信息。层在覆盖先前规范的能力方面是分层的。您可以从 Yocto 项目中包括任意数量的可用层，并通过在这些层之后添加自己的层来自定义构建。您可以在图层索引中搜索 Yocto 项目中使用的图层。

For more detailed information on layers, see the \"`dev-manual/layers:understanding and creating layers`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual. For a discussion specifically on BSP Layers, see the \"`bsp-guide/bsp:bsp layers`{.interpreted-text role="ref"}\" section in the Yocto Project Board Support Packages (BSP) Developer\'s Guide.

> 有关层的更多详细信息，请参阅 Yocto 项目开发任务手册中的\“`dev manual/layers:understanding and createing layers`｛.depreted text role=“ref”｝\”一节。有关 BSP 层的详细讨论，请参阅 Yocto 项目委员会支持包（BSP）开发人员指南中的“`nbsp guide/BSP：nbsp Layers`｛.explored text role=“ref”｝”部分。

- *Metadata:* A key element of the Yocto Project is the Metadata that is used to construct a Linux distribution and is contained in the files that the OpenEmbedded build system parses when building an image. In general, Metadata includes recipes, configuration files, and other information that refers to the build instructions themselves, as well as the data used to control what things get built and the effects of the build. Metadata also includes commands and data used to indicate what versions of software are used, from where they are obtained, and changes or additions to the software itself (patches or auxiliary files) that are used to fix bugs or customize the software for use in a particular situation. OpenEmbedded-Core is an important set of validated metadata.

> -*元数据：*Yocto 项目的一个关键元素是用于构建 Linux 发行版的元数据，该元数据包含在 OpenEmbedded 构建系统在构建映像时解析的文件中。通常，元数据包括配方、配置文件和其他涉及构建指令本身的信息，以及用于控制构建内容和构建效果的数据。元数据还包括用于指示使用哪些版本的软件、从何处获得这些软件的命令和数据，以及用于修复错误或自定义软件以在特定情况下使用的对软件本身的更改或添加（补丁或辅助文件）。OpenEmbedded Core 是一组经过验证的重要元数据。

- *OpenEmbedded Build System:* The terms \"BitBake\" and \"build system\" are sometimes used for the OpenEmbedded Build System.

> -*OpenEmbedded 构建系统：*术语“BitBake”和“构建系统”有时用于 OpenEmbeddedBuild 系统。

BitBake is a task scheduler and execution engine that parses instructions (i.e. recipes) and configuration data. After a parsing phase, BitBake creates a dependency tree to order the compilation, schedules the compilation of the included code, and finally executes the building of the specified custom Linux image (distribution). BitBake is similar to the `make` tool.

> BitBake 是一个任务调度器和执行引擎，用于解析指令（即配方）和配置数据。在解析阶段之后，BitBake 创建一个依赖树来对编译进行排序，安排所包含代码的编译，并最终执行指定自定义 Linux 映像（发行版）的构建。BitBake 类似于“制作”工具。

During a build process, the build system tracks dependencies and performs a native or cross-compilation of each package. As a first step in a cross-build setup, the framework attempts to create a cross-compiler toolchain (i.e. Extensible SDK) suited for the target platform.

> 在构建过程中，构建系统跟踪依赖关系，并对每个包执行本机编译或交叉编译。作为交叉构建设置的第一步，该框架试图创建适合目标平台的交叉编译器工具链（即可扩展 SDK）。

- *OpenEmbedded-Core (OE-Core):* OE-Core is metadata comprised of foundation recipes, classes, and associated files that are meant to be common among many different OpenEmbedded-derived systems, including the Yocto Project. OE-Core is a curated subset of an original repository developed by the OpenEmbedded community that has been pared down into a smaller, core set of continuously validated recipes. The result is a tightly controlled and quality-assured core set of recipes.

> -*OpenEmbedded Core（OE Core）：*OE Core 是由基础配方、类和相关文件组成的元数据，这些文件在许多不同的 OpenEmbedded-派生系统中是常见的，包括 Yocto 项目。OE Core 是由 OpenEmbedded 社区开发的原始存储库的一个子集，该存储库已被缩减为一组较小的、不断验证的核心配方。其结果是一套严格控制和质量保证的核心配方。

You can see the Metadata in the `meta` directory of the Yocto Project :yocto\_[git:%60Source](git:%60Source) Repositories \<\>\`.

> 您可以在 Yocto 项目的“meta”目录中看到元数据：Yocto\_[git:%60Source]（git:%60Source）Repositorys\<\>\`。

- *Packages:* In the context of the Yocto Project, this term refers to a recipe\'s packaged output produced by BitBake (i.e. a \"baked recipe\"). A package is generally the compiled binaries produced from the recipe\'s sources. You \"bake\" something by running it through BitBake.

> -*包装：*在 Yocto 项目的上下文中，这个术语指的是 BitBake 生产的配方的包装输出（即“烘焙配方”）。包通常是从配方的源代码中生成的编译后的二进制文件。通过运行 BitBake 可以“烘焙”一些东西。

It is worth noting that the term \"package\" can, in general, have subtle meanings. For example, the packages referred to in the \"`ref-manual/system-requirements:required packages for the build host`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual are compiled binaries that, when installed, add functionality to your host Linux distribution.

> 值得注意的是，“一揽子计划”一词通常具有微妙的含义。例如，Yocto 项目参考手册中的\“`ref manual/system requirements:build host`｛.depredicted text role=“ref”｝\”部分中提到的包是经过编译的二进制文件，安装后可为主机 Linux 发行版添加功能。

Another point worth noting is that historically within the Yocto Project, recipes were referred to as packages \-\-- thus, the existence of several BitBake variables that are seemingly mis-named, (e.g. `PR`{.interpreted-text role="term"}, `PV`{.interpreted-text role="term"}, and `PE`{.interpreted-text role="term"}).

> 另一点值得注意的是，历史上，在 Yocto 项目中，配方被称为包，因此，存在几个似乎命名错误的 BitBake 变量（例如，`PR`｛.explored text role=“term”｝、`PV`｛..explored text role=”term“｝和 `PE`｛.explored textrole=。

- *Poky:* Poky is a reference embedded distribution and a reference test configuration. Poky provides the following:

> -*Poky：*Poky 是一个参考嵌入式分布和参考测试配置。Poky 提供以下功能：

- A base-level functional distro used to illustrate how to customize a distribution.
- A means by which to test the Yocto Project components (i.e. Poky is used to validate the Yocto Project).

> -测试 Yocto 项目组件的一种方法（即 Poky 用于验证 Yocto 工程）。

- A vehicle through which you can download the Yocto Project.

Poky is not a product level distro. Rather, it is a good starting point for customization.

> Poky 不是一个产品级别的发行版。相反，它是定制的一个很好的起点。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

Poky is an integration layer on top of OE-Core.

> Poky 是 OE 核心之上的集成层。

:::

> ：：：

- *Recipe:* The most common form of metadata. A recipe contains a list of settings and tasks (i.e. instructions) for building packages that are then used to build the binary image. A recipe describes where you get source code and which patches to apply. Recipes describe dependencies for libraries or for other recipes as well as configuration and compilation options. Related recipes are consolidated into a layer.

> -*配方：*最常见的元数据形式。配方包含用于构建包的设置和任务（即指令）列表，然后用于构建二进制图像。配方描述了从哪里获得源代码以及应用哪些补丁。配方描述了库或其他配方的依赖关系，以及配置和编译选项。相关配方合并为一个层。
