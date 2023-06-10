---
tip: translate by openai@2023-06-10 10:51:25
...
---
title: Introducing the Yocto Project
------------------------------------

# What is the Yocto Project?

The Yocto Project is an open source collaboration project that helps developers create custom Linux-based systems that are designed for embedded products regardless of the product\'s hardware architecture. Yocto Project provides a flexible toolset and a development environment that allows embedded device developers across the world to collaborate through shared technologies, software stacks, configurations, and best practices used to create these tailored Linux images.

> 项目 Yocto 是一个开源合作项目，旨在帮助开发人员创建基于 Linux 的定制系统，无论其产品的硬件架构如何。Yocto Project 提供灵活的工具集和开发环境，使全球嵌入式设备开发人员能够通过共享技术、软件堆栈、配置和最佳实践来进行协作，以创建这些定制的 Linux 映像。

Thousands of developers worldwide have discovered that Yocto Project provides advantages in both systems and applications development, archival and management benefits, and customizations used for speed, footprint, and memory utilization. The project is a standard when it comes to delivering embedded software stacks. The project allows software customizations and build interchange for multiple hardware platforms as well as software stacks that can be maintained and scaled.

> 世界各地的数千名开发人员发现，Yocto 项目在系统和应用程序开发、存档和管理方面提供了优势，以及用于速度、占用空间和内存利用率的自定义。该项目是提供嵌入式软件堆栈的标准。该项目允许软件自定义和构建交换，用于多种硬件平台以及可维护和扩展的软件堆栈。

![image](figures/key-dev-elements.png)

For further introductory information on the Yocto Project, you might be interested in this [article](https://www.embedded.com/electronics-blogs/say-what-/4458600/Why-the-Yocto-Project-for-my-IoT-Project-) by Drew Moseley and in this short introductory [video](https://www.youtube.com/watch?v=utZpKM7i5Z4).

> 对于 Yocto 项目的更多介绍信息，您可能对 Drew Moseley 的这篇文章[文章](https://www.embedded.com/electronics-blogs/say-what-/4458600/Why-the-Yocto-Project-for-my-IoT-Project-)和这个简短的介绍[视频](https://www.youtube.com/watch?v=utZpKM7i5Z4)感兴趣。

The remainder of this section overviews advantages and challenges tied to the Yocto Project.

## Features

Here are features and advantages of the Yocto Project:

- *Widely Adopted Across the Industry:* Many semiconductor, operating system, software, and service vendors adopt and support the Yocto Project in their products and services. For a look at the Yocto Project community and the companies involved with the Yocto Project, see the \"COMMUNITY\" and \"ECOSYSTEM\" tabs on the :yocto_home:[Yocto Project \<\>] home page.

> - 广泛在行业中被采用：许多半导体、操作系统、软件和服务供应商都采用并支持 Yocto 项目，并将其纳入其产品和服务中。要查看 Yocto 项目社区和参与 Yocto 项目的公司，请访问 Yocto 项目主页上的“社区”和“生态系统”标签。

- *Architecture Agnostic:* Yocto Project supports Intel, ARM, MIPS, AMD, PPC and other architectures. Most ODMs, OSVs, and chip vendors create and supply BSPs that support their hardware. If you have custom silicon, you can create a BSP that supports that architecture.

> - *与体系结构无关：*Yocto Project 支持 Intel、ARM、MIPS、AMD、PPC 和其他体系结构。大多数 ODM、OSV 和芯片供应商创建并提供支持其硬件的 BSP。如果您有自定义硅片，可以创建支持该体系结构的 BSP。

Aside from broad architecture support, the Yocto Project fully supports a wide range of devices emulated by the Quick EMUlator (QEMU).

- *Images and Code Transfer Easily:* Yocto Project output can easily move between architectures without moving to new development environments. Additionally, if you have used the Yocto Project to create an image or application and you find yourself not able to support it, commercial Linux vendors such as Wind River, Mentor Graphics, Timesys, and ENEA could take it and provide ongoing support. These vendors have offerings that are built using the Yocto Project.

> Yocto 项目的输出可以轻松地在不同的架构之间传输，而无需更换新的开发环境。此外，如果您使用 Yocto 项目创建了一个镜像或应用程序，但发现自己无法支持它，Wind River，Mentor Graphics，Timesys 和 ENEA 等商业 Linux 供应商可以采用它并提供持续支持。这些供应商提供的产品是使用 Yocto 项目构建的。

- *Flexibility:* Corporations use the Yocto Project many different ways. One example is to create an internal Linux distribution as a code base the corporation can use across multiple product groups. Through customization and layering, a project group can leverage the base Linux distribution to create a distribution that works for their product needs.

> - *弹性：*公司使用 Yocto 项目以多种不同的方式。一个例子是创建一个内部的 Linux 发行版作为代码库，公司可以在多个产品组中使用。通过定制和分层，项目组可以利用基础 Linux 发行版来创建符合其产品需求的发行版。

- *Ideal for Constrained Embedded and IoT devices:* Unlike a full Linux distribution, you can use the Yocto Project to create exactly what you need for embedded devices. You only add the feature support or packages that you absolutely need for the device. For devices that have display hardware, you can use available system components such as X11, Wayland, GTK+, Qt, Clutter, and SDL (among others) to create a rich user experience. For devices that do not have a display or where you want to use alternative UI frameworks, you can choose to not build these components.

> 对于受限的嵌入式和物联网设备来说，非常理想：与完整的 Linux 发行版不同，您可以使用 Yocto 项目为嵌入式设备创建所需的内容。您只需添加设备绝对需要的功能支持或软件包。对于具有显示硬件的设备，您可以使用可用的系统组件，例如 X11，Wayland，GTK +，Qt，Clutter 和 SDL(等等)来创建丰富的用户体验。对于没有显示器或希望使用其他 UI 框架的设备，可以选择不构建这些组件。

- *Comprehensive Toolchain Capabilities:* Toolchains for supported architectures satisfy most use cases. However, if your hardware supports features that are not part of a standard toolchain, you can easily customize that toolchain through specification of platform-specific tuning parameters. And, should you need to use a third-party toolchain, mechanisms built into the Yocto Project allow for that.

> *全面的工具链功能：支持的架构的工具链可以满足大多数用例。但是，如果您的硬件支持标准工具链中没有的功能，您可以通过指定平台特定的调整参数轻松定制该工具链。而且，如果您需要使用第三方工具链，Yocto 项目中内置的机制可以实现这一目的。

- *Mechanism Rules Over Policy:* Focusing on mechanism rather than policy ensures that you are free to set policies based on the needs of your design instead of adopting decisions enforced by some system software provider.

> 机制优先于政策：专注于机制而不是政策，可以确保您可以根据设计的需要设定政策，而不是采用某些系统软件提供商强加的决定。

- *Uses a Layer Model:* The Yocto Project `layer infrastructure <overview-manual/yp-intro:the yocto project layer model>` groups related functionality into separate bundles. You can incrementally add these grouped functionalities to your project as needed. Using layers to isolate and group functionality reduces project complexity and redundancy, allows you to easily extend the system, make customizations, and keep functionality organized.

> 使用层模型：Yocto 项目的层架构建 <overview-manual/yp-intro:the yocto project layer model> 将相关功能分组为单独的包。您可以根据需要逐步添加这些分组功能到您的项目中。使用层来隔离和分组功能可以减少项目的复杂性和冗余，允许您轻松地扩展系统，进行自定义，并保持功能有序。

- *Supports Partial Builds:* You can build and rebuild individual packages as needed. Yocto Project accomplishes this through its `overview-manual/concepts:shared state cache` (sstate) scheme. Being able to build and debug components individually eases project development.

> 支持部分构建：您可以根据需要构建和重建单个软件包。Yocto 项目通过其 `overview-manual/concepts:shared state cache`(sstate)方案实现了这一点。能够单独构建和调试组件可以简化项目开发。

- *Releases According to a Strict Schedule:* Major releases occur on a `six-month cycle </ref-manual/release-process>` predictably in October and April. The most recent two releases support point releases to address common vulnerabilities and exposures. This predictability is crucial for projects based on the Yocto Project and allows development teams to plan activities.

> *按严格的时间表发布：主要版本按照每六个月的周期可预测地于 10 月和 4 月发布。最近的两个版本支持点发布以解决常见的漏洞和暴露。这种可预测性对基于 Yocto Project 的项目至关重要，可以帮助开发团队计划活动。

- *Rich Ecosystem of Individuals and Organizations:* For open source projects, the value of community is very important. Support forums, expertise, and active developers who continue to push the Yocto Project forward are readily available.

> - 丰富的个人和组织生态系统：对于开源项目来说，社区的价值非常重要。支持论坛、专业知识和积极的开发人员可以随时获得，继续推动 Yocto 项目的发展。

- *Binary Reproducibility:* The Yocto Project allows you to be very specific about dependencies and achieves very high percentages of binary reproducibility (e.g. 99.8% for `core-image-minimal`). When distributions are not specific about which packages are pulled in and in what order to support dependencies, other build systems can arbitrarily include packages.

> 二进制重现性：Yocto 项目允许您对依赖项非常具体，并实现非常高的二进制重现性(例如，对于 `core-image-minimal`，99.8％)。当分发不明确拉取哪些软件包以及支持依赖项的顺序时，其他构建系统可以任意包含软件包。

- *License Manifest:* The Yocto Project provides a `license manifest <dev-manual/licenses:maintaining open source license compliance during your product's lifecycle>` for review by people who need to track the use of open source licenses (e.g. legal teams).

> Yocto 项目提供了一份许可证清单(参见开发手册/许可证：在产品生命周期中维护开源许可证的合规性)，供需要跟踪开源许可证使用情况的人(如法律团队)进行审查。

## Challenges

Here are challenges you might encounter when developing using the Yocto Project:

- *Steep Learning Curve:* The Yocto Project has a steep learning curve and has many different ways to accomplish similar tasks. It can be difficult to choose between such ways.

> 高学习曲线：Yocto 项目具有很陡的学习曲线，有许多不同的方式来完成相似的任务。在这些方式之间选择可能会很困难。

- *Understanding What Changes You Need to Make For Your Design Requires Some Research:* Beyond the simple tutorial stage, understanding what changes need to be made for your particular design can require a significant amount of research and investigation. For information that helps you transition from trying out the Yocto Project to using it for your project, see the \"`what-i-wish-id-known:what i wish i'd known about yocto project`\" documents on the Yocto Project website.

> 了解您需要为设计而做出哪些更改需要一些研究：除了简单的教程阶段外，了解针对您的特定设计需要做出哪些更改可能需要大量的研究和调查。要获得帮助您从尝试 Yocto 项目转换为将其用于您的项目的信息，请参阅 Yocto 项目网站上的“我希望知道的关于 Yocto 项目”以及“过渡到自定义环境进行系统开发”文档。

- *Project Workflow Could Be Confusing:* The `Yocto Project workflow <overview-manual/development-environment:the yocto project development environment>` could be confusing if you are used to traditional desktop and server software development. In a desktop development environment, there are mechanisms to easily pull and install new packages, which are typically pre-compiled binaries from servers accessible over the Internet. Using the Yocto Project, you must modify your configuration and rebuild to add additional packages.

> 项目工作流程可能会令人困惑：如果您习惯于传统的桌面和服务器软件开发，则 Yocto 项目工作流程 <overview-manual/development-environment：the yocto project development environment> 可能会令人困惑。在桌面开发环境中，有一些机制可以轻松拉取和安装新的软件包，这些软件包通常是从可以通过互联网访问的服务器上预先编译的二进制文件。使用 Yocto 项目，您必须修改配置并重新构建以添加额外的软件包。

- *Working in a Cross-Build Environment Can Feel Unfamiliar:* When developing code to run on a target, compilation, execution, and testing done on the actual target can be faster than running a BitBake build on a development host and then deploying binaries to the target for test. While the Yocto Project does support development tools on the target, the additional step of integrating your changes back into the Yocto Project build environment would be required. Yocto Project supports an intermediate approach that involves making changes on the development system within the BitBake environment and then deploying only the updated packages to the target.

> 在跨构建环境中工作可能感到陌生：当开发用于目标的代码时，在实际目标上进行编译、执行和测试可能比在开发主机上运行 BitBake 构建并将二进制文件部署到目标进行测试要快。尽管 Yocto 项目支持在目标上开发工具，但是还需要额外的步骤将您的更改集成回 Yocto 项目构建环境中。Yocto 项目支持一种中间方法，即在 BitBake 环境中在开发系统上进行更改，然后仅将更新的软件包部署到目标。

The Yocto Project `OpenEmbedded Build System` produces packages in standard formats (i.e. RPM, DEB, IPK, and TAR). You can deploy these packages into the running system on the target by using utilities on the target such as `rpm` or `ipk`.

> 项目 Yocto 的 OpenEmbedded 构建系统可以生成标准格式的安装包(如 RPM、DEB、IPK 和 TAR)。您可以使用目标上的实用程序(如 rpm 或 ipk)将这些安装包部署到运行系统中。

- *Initial Build Times Can be Significant:* Long initial build times are unfortunately unavoidable due to the large number of packages initially built from scratch for a fully functioning Linux system. Once that initial build is completed, however, the shared-state (sstate) cache mechanism Yocto Project uses keeps the system from rebuilding packages that have not been \"touched\" since the last build. The sstate mechanism significantly reduces times for successive builds.

> *初始构建时间可能会很长：由于要构建一个完整的 Linux 系统所需的大量软件包，不幸的是初始构建时间会很长。但是一旦完成了初始构建，Yocto 项目使用的共享状态(sstate)缓存机制可以防止系统重新构建自上次构建以来没有“触及”的软件包。Sstate 机制显着减少了后续构建的时间。

# The Yocto Project Layer Model

The Yocto Project\'s \"Layer Model\" is a development model for embedded and IoT Linux creation that distinguishes the Yocto Project from other simple build systems. The Layer Model simultaneously supports collaboration and customization. Layers are repositories that contain related sets of instructions that tell the `OpenEmbedded Build System` what to do. You can collaborate, share, and reuse layers.

> 面向嵌入式和物联网 Linux 创建的 Yocto 项目的“层模型”是一个开发模型，使 Yocto 项目与其他简单的构建系统区分开来。层模型同时支持协作和定制。层是包含相关指令集的存储库，这些指令告诉 OpenEmbedded 构建系统应该做什么。您可以协作、共享和重用层。

Layers can contain changes to previous instructions or settings at any time. This powerful override capability is what allows you to customize previously supplied collaborative or community layers to suit your product requirements.

> 层可以随时包含对先前指令或设置的更改。这种强大的覆盖功能使您可以自定义先前提供的协作或社区层以适应您的产品要求。

You use different layers to logically separate information in your build. As an example, you could have BSP, GUI, distro configuration, middleware, or application layers. Putting your entire build into one layer limits and complicates future customization and reuse. Isolating information into layers, on the other hand, helps simplify future customizations and reuse. You might find it tempting to keep everything in one layer when working on a single project. However, the more modular your Metadata, the easier it is to cope with future changes.

> 你可以使用不同的层来逻辑地分离你的构建中的信息。例如，你可以有 BSP、GUI、发行版配置、中间件或应用层。将整个构建都放在一个层中会限制并使未来的定制和重用变得复杂。另一方面，将信息隔离到层中可以帮助简化未来的定制和重用。当只处理一个项目时，你可能会被诱惑把一切都放在一个层中。然而，元数据越模块化，应对未来变化就越容易。

::: note
::: title
Note
:::

- Use Board Support Package (BSP) layers from silicon vendors when possible.
- Familiarize yourself with the :yocto_home:[Yocto Project Compatible Layers \</software-overview/layers/\>]. The latter contains more layers but they are less universally validated.

> 熟悉:yocto_home:[Yocto 项目兼容层\</software-overview/layers/\>]。后者包含更多的层，但它们的验证不那么普遍。

- Layers support the inclusion of technologies, hardware components, and software components. The `Yocto Project Compatible <dev-manual/layers:making sure your layer is compatible with yocto project>` designation provides a minimum level of standardization that contributes to a strong ecosystem. \"YP Compatible\" is applied to appropriate products and software components such as BSPs, other OE-compatible layers, and related open-source projects, allowing the producer to use Yocto Project badges and branding assets.

> 支持将技术、硬件组件和软件组件包含在图层中。“Yocto Project Compatible”(<dev-manual/layers:making sure your layer is compatible with yocto project>)认证提供了一定的最低标准，有助于形成强大的生态系统。“YP Compatible”被应用于适当的产品和软件组件，如 BSP，其他 OE 兼容层和相关的开源项目，使生产商可以使用 Yocto Project 徽章和品牌资产。
> :::

To illustrate how layers are used to keep things modular, consider machine customizations. These types of customizations typically reside in a special layer, rather than a general layer, called a BSP Layer. Furthermore, the machine customizations should be isolated from recipes and Metadata that support a new GUI environment, for example. This situation gives you a couple of layers: one for the machine configurations, and one for the GUI environment. It is important to understand, however, that the BSP layer can still make machine-specific additions to recipes within the GUI environment layer without polluting the GUI layer itself with those machine-specific changes. You can accomplish this through a recipe that is a BitBake append (`.bbappend`) file, which is described later in this section.

> 为了说明如何使用层来保持模块化，考虑机器定制。这些类型的定制通常位于一个特殊的层中，而不是一个通用层，称为 BSP 层。此外，机器定制应该与支持新 GUI 环境的配方和元数据隔离开来。这种情况给你一些层：一个用于机器配置，一个用于 GUI 环境。但是，重要的是要明白，BSP 层仍然可以在 GUI 环境层中向配方添加机器特定的添加，而不会污染 GUI 层本身。您可以通过一个 BitBake 附加(.bbappend)文件来实现这一点，该文件将在本节的后面介绍。

::: note
::: title
Note
:::

For general information on BSP layer structure, see the `/bsp-guide/index`.
:::

The `Source Directory` contains both general layers and BSP layers right out of the box. You can easily identify layers that ship with a Yocto Project release in the Source Directory by their names. Layers typically have names that begin with the string `meta-`.

> 源目录中除了一般的层外，还有 Yocto 项目发布的 BSP 层。您可以通过它们的名称轻松地识别出源目录中发布的层。层的名称通常以“meta-”开头。

::: note
::: title
Note
:::

It is not a requirement that a layer name begin with the prefix `meta-`, but it is a commonly accepted standard in the Yocto Project community.
:::

For example, if you were to examine the :yocto_[git:%60tree](git:%60tree) view \</poky/tree/\>[ of the ]\`. Each of these repositories represents a distinct layer.

> 如果您检查 poky 仓库的 yocto_[git:%60tree](git:%60tree) 视图\</poky/tree/\>，您会看到几个层次：meta、meta-skeleton、meta-selftest、meta-poky 和 meta-yocto-bsp。每个仓库都代表一个独特的层次。

For procedures on how to create layers, see the \"`dev-manual/layers:understanding and creating layers`\" section in the Yocto Project Development Tasks Manual.

> 查看 Yocto 项目开发任务手册中的“dev-manual/layers：理解和创建层”部分，了解如何创建层的步骤。

# Components and Tools

The Yocto Project employs a collection of components and tools used by the project itself, by project developers, and by those using the Yocto Project. These components and tools are open source projects and metadata that are separate from the reference distribution (`Poky`. Most of the components and tools are downloaded separately.

> 项目 Yocto 使用了一系列的组件和工具，由项目本身，项目开发者，以及使用 Yocto 项目的人使用。这些组件和工具是开源项目和元数据，独立于参考发行(Poky)和 OpenEmbedded 构建系统。大多数组件和工具都是单独下载的。

This section provides brief overviews of the components and tools associated with the Yocto Project.

## Development Tools

Here are tools that help you develop images and applications using the Yocto Project:

- *CROPS:* [CROPS](https://github.com/crops/poky-container/) is an open source, cross-platform development framework that leverages [Docker Containers](https://www.docker.com/). CROPS provides an easily managed, extensible environment that allows you to build binaries for a variety of architectures on Windows, Linux and Mac OS X hosts.

> CROPS 是一个开源的跨平台开发框架，利用 Docker 容器技术。CROPS 提供一个易于管理和可扩展的环境，可以在 Windows、Linux 和 Mac OS X 主机上为各种架构构建二进制文件。

- *devtool:* This command-line tool is available as part of the extensible SDK (eSDK) and is its cornerstone. You can use `devtool` to help build, test, and package software within the eSDK. You can use the tool to optionally integrate what you build into an image built by the OpenEmbedded build system.

> `devtool：这个命令行工具是可扩展SDK(eSDK)的一部分，也是它的基石。您可以使用` devtool `来帮助在eSDK中构建，测试和打包软件。您可以使用此工具可选地将您构建的内容集成到OpenEmbedded构建系统构建的映像中。`

The `devtool` command employs a number of sub-commands that allow you to add, modify, and upgrade recipes. As with the OpenEmbedded build system, \"recipes\" represent software packages within `devtool`. When you use `devtool add`, a recipe is automatically created. When you use `devtool modify`, the specified existing recipe is used in order to determine where to get the source code and how to patch it. In both cases, an environment is set up so that when you build the recipe a source tree that is under your control is used in order to allow you to make changes to the source as desired. By default, both new recipes and the source go into a \"workspace\" directory under the eSDK. The `devtool upgrade` command updates an existing recipe so that you can build it for an updated set of source files.

> 命令 `devtool` 拥有许多子命令，可以帮助您添加、修改和升级配方。与 OpenEmbedded 构建系统一样，“配方”代表 `devtool` 中的软件包。当您使用 `devtool add` 时，会自动创建一个配方。当您使用 `devtool modify` 时，会使用指定的现有配方来确定获取源代码的位置以及如何补丁它。在这两种情况下，都会设置一个环境，以便在构建配方时使用您控制的源树，以允许您按需对源代码进行更改。默认情况下，新配方和源文件都会放置在 eSDK 下的“工作区”目录中。命令 `devtool upgrade` 可以更新现有配方，以便您可以使用更新的源文件集来构建它。

You can read about the `devtool` workflow in the Yocto Project Application Development and Extensible Software Development Kit (eSDK) Manual in the \"``sdk-manual/extensible:using \`\`devtool\`\` in your sdk workflow``\" section.

> 你可以在 Yocto Project 应用开发和可扩展软件开发工具(eSDK)手册中的“sdk-manual / extensible：在您的 sdk 工作流程中使用 `devtool`”部分阅读有关 `devtool` 工作流的信息。

- *Extensible Software Development Kit (eSDK):* The eSDK provides a cross-development toolchain and libraries tailored to the contents of a specific image. The eSDK makes it easy to add new applications and libraries to an image, modify the source for an existing component, test changes on the target hardware, and integrate into the rest of the OpenEmbedded build system. The eSDK gives you a toolchain experience supplemented with the powerful set of `devtool` commands tailored for the Yocto Project environment.

> 可扩展软件开发工具包 (eSDK)：eSDK 提供了一个跨开发工具链和库，专门针对特定镜像的内容进行设计。eSDK 可以轻松地将新的应用程序和库添加到镜像中，修改现有组件的源代码，在目标硬件上测试更改，并将其集成到 OpenEmbedded 构建系统的其余部分中。eSDK 为您提供了一个工具链体验，并补充了为 Yocto Project 环境定制的强大的 `devtool` 命令集。

For information on the eSDK, see the `/sdk-manual/index` Manual.

- *Toaster:* Toaster is a web interface to the Yocto Project OpenEmbedded build system. Toaster allows you to configure, run, and view information about builds. For information on Toaster, see the `/toaster-manual/index`.

> 烤面包机：烤面包机是 Yocto Project OpenEmbedded 构建系统的 Web 界面。烤面包机可以帮助您配置、运行和查看构建信息。有关烤面包机的更多信息，请参阅/toaster-manual/index。

## Production Tools

Here are tools that help with production related activities using the Yocto Project:

- *Auto Upgrade Helper:* This utility when used in conjunction with the `OpenEmbedded Build System` for how to set it up.

> - *自动升级助手：* 当与“开放嵌入式构建系统”(BitBake 和 OE-Core)一起使用时，该实用程序可以根据上游发布的新版本自动生成升级的配方。有关如何设置的详细信息，请参阅“dev-manual / upgrading-recipes：使用自动升级助手(auh)”。

- *Recipe Reporting System:* The Recipe Reporting System tracks recipe versions available for Yocto Project. The main purpose of the system is to help you manage the recipes you maintain and to offer a dynamic overview of the project. The Recipe Reporting System is built on top of the :oe_layerindex:[OpenEmbedded Layer Index \<\>], which is a website that indexes OpenEmbedded-Core layers.

> 烹饪报告系统：烹饪报告系统跟踪 Yocto 项目可用的 recipes 版本。该系统的主要目的是帮助您管理您维护的 recipes，并提供动态的项目概览。烹饪报告系统是建立在:oe_layerindex:[开放式嵌入式层次索引 \<\>]之上的，这是一个索引开放式嵌入式核心层的网站。

- *Patchwork:* [Patchwork](https://patchwork.yoctoproject.org/) is a fork of a project originally started by [OzLabs](https://ozlabs.org/). The project is a web-based tracking system designed to streamline the process of bringing contributions into a project. The Yocto Project uses Patchwork as an organizational tool to handle patches, which number in the thousands for every release.

> Patchwork：[Patchwork](https://patchwork.yoctoproject.org/) 是由 [OzLabs](https://ozlabs.org/) 最初创建的项目的分支。该项目是一个基于 Web 的跟踪系统，旨在简化将贡献带入项目的过程。Yocto Project 使用 Patchwork 作为组织工具来处理补丁，每个发行版的补丁数量都达到数千。

- *AutoBuilder:* AutoBuilder is a project that automates build tests and quality assurance (QA). By using the public AutoBuilder, anyone can determine the status of the current development branch of Poky.

> AutoBuilder：AutoBuilder 是一个自动化构建测试和质量保障(QA)的项目。通过使用公共的 AutoBuilder，任何人都可以确定 Poky 当前开发分支的状态。

::: note
::: title
Note
:::

AutoBuilder is based on buildbot.
:::

A goal of the Yocto Project is to lead the open source industry with a project that automates testing and QA procedures. In doing so, the project encourages a development community that publishes QA and test plans, publicly demonstrates QA and test plans, and encourages development of tools that automate and test and QA procedures for the benefit of the development community.

> 项目 Yocto 的一个目标是带头开源行业，提供一个可以自动化测试和质量保证流程的项目。为此，该项目鼓励开发社区发布质量保证和测试计划，公开展示质量保证和测试计划，并鼓励开发出可以自动化测试和质量保证流程的工具，以造福开发社区。

You can learn more about the AutoBuilder used by the Yocto Project Autobuilder `here </test-manual/understand-autobuilder>`.

> 您可以在此处了解有关 Yocto Project Autobuilder 使用的 AutoBuilder 的更多信息：<test-manual/understand-autobuilder>。

- *Pseudo:* Pseudo is the Yocto Project implementation of [fakeroot](http://man.he.net/man1/fakeroot), which is used to run commands in an environment that seemingly has root privileges.

> 伪: 伪是 Yocto 项目对 [fakeroot](http://man.he.net/man1/fakeroot) 的实现，用于在似乎具有 root 特权的环境中运行命令。

During a build, it can be necessary to perform operations that require system administrator privileges. For example, file ownership or permissions might need to be defined. Pseudo is a tool that you can either use directly or through the environment variable `LD_PRELOAD`. Either method allows these operations to succeed even without system administrator privileges.

> 在构建过程中，可能需要执行需要系统管理员权限的操作。例如，可能需要定义文件所有权或权限。Pseudo 是一种可以直接使用或通过环境变量“LD_PRELOAD”使用的工具。无论使用哪种方法，即使没有系统管理员权限，这些操作也可以成功完成。

Thanks to Pseudo, the Yocto Project never needs root privileges to build images for your target system.

You can read more about Pseudo in the \"`overview-manual/concepts:fakeroot and pseudo`\" section.

## Open-Embedded Build System Components

Here are components associated with the `OpenEmbedded Build System`:

- *BitBake:* BitBake is a core component of the Yocto Project and is used by the OpenEmbedded build system to build images. While BitBake is key to the build system, BitBake is maintained separately from the Yocto Project.

> BitBake 是 Yocto 项目的核心组件，被 OpenEmbedded 构建系统用来构建镜像。虽然 BitBake 对构建系统至关重要，但它与 Yocto 项目是独立维护的。

BitBake is a generic task execution engine that allows shell and Python tasks to be run efficiently and in parallel while working within complex inter-task dependency constraints. In short, BitBake is a build engine that works through recipes written in a specific format in order to perform sets of tasks.

> BitBake 是一种通用任务执行引擎，它可以有效且并行地运行 Shell 和 Python 任务，同时处理复杂的任务间依赖约束。简而言之，BitBake 是一种构建引擎，可以通过以特定格式编写的配方来执行一系列任务。

You can learn more about BitBake in the `BitBake User Manual <bitbake:index>`.

- *OpenEmbedded-Core:* OpenEmbedded-Core (OE-Core) is a common layer of metadata (i.e. recipes, classes, and associated files) used by OpenEmbedded-derived systems, which includes the Yocto Project. The Yocto Project and the OpenEmbedded Project both maintain the OpenEmbedded-Core. You can find the OE-Core metadata in the Yocto Project :yocto_[git:%60Source](git:%60Source) Repositories \</poky/tree/meta\>\`.

> 开放嵌入式核心(OpenEmbedded-Core，简称 OE-Core)是 Yocto 项目及其衍生系统所使用的公共元数据(例如配方、类和相关文件)层。Yocto 项目和 OpenEmbedded 项目都维护着 OE-Core 元数据。您可以在 Yocto 项目的 yocto_[git:`Source`](git:%60Source%60)存储库 </poky/tree/meta> 中找到 OE-Core 元数据。

Historically, the Yocto Project integrated the OE-Core metadata throughout the Yocto Project source repository reference system (Poky). After Yocto Project Version 1.0, the Yocto Project and OpenEmbedded agreed to work together and share a common core set of metadata (OE-Core), which contained much of the functionality previously found in Poky. This collaboration achieved a long-standing OpenEmbedded objective for having a more tightly controlled and quality-assured core. The results also fit well with the Yocto Project objective of achieving a smaller number of fully featured tools as compared to many different ones.

> 历史上，Yocto 项目将 OE-Core 元数据整合到 Yocto 项目源代码库参考系统(Poky)中。在 Yocto 项目 1.0 版本之后，Yocto 项目和 OpenEmbedded 同意一起合作，共享一个公共核心元数据集(OE-Core)，其中包含了以前在 Poky 中找到的许多功能。这一合作实现了 OpenEmbedded 的一个长期目标，即拥有一个更严格控制和质量保证的核心。结果也符合 Yocto 项目的目标，即实现较少的功能完整的工具，而不是许多不同的工具。

Sharing a core set of metadata results in Poky as an integration layer on top of OE-Core. You can see that in this `figure <overview-manual/yp-intro:what is the yocto project?>`. The Yocto Project combines various components such as BitBake, OE-Core, script \"glue\", and documentation for its build system.

> 分享核心元数据结果，Poky 作为 OE-Core 的集成层。您可以在这个图中看到：<overview-manual/yp-intro:what is the yocto project?>。Yocto 项目结合了诸如 BitBake、OE-Core、脚本“粘合剂”和文档等各种组件，以构建系统。

## Reference Distribution (Poky)

Poky is the Yocto Project reference distribution. It contains the `OpenEmbedded Build System`\" section for an illustration that shows Poky and its relationship with other parts of the Yocto Project.

> Poky 是 Yocto 项目的参考分布。它包含 `开放嵌入式构建系统`(BitBake 和 OE-Core)以及一组元数据，可帮助您开始构建自己的分布。请参见“`overview-manual/yp-intro：什么是 Yocto 项目？`”部分的图片，以了解 Poky 与 Yocto 项目的其他部分之间的关系。

To use the Yocto Project tools and components, you can download (`clone`) Poky and use it to bootstrap your own distribution.

::: note
::: title
Note
:::

Poky does not contain binary files. It is a working example of how to build your own custom Linux distribution from source.
:::

You can read more about Poky in the \"`overview-manual/yp-intro:reference embedded distribution (poky)`\" section.

## Packages for Finished Targets

Here are components associated with packages for finished targets:

- *Matchbox:* Matchbox is an Open Source, base environment for the X Window System running on non-desktop, embedded platforms such as handhelds, set-top boxes, kiosks, and anything else for which screen space, input mechanisms, or system resources are limited.

> Matchbox 是一个开源的基础环境，可以在非桌面、嵌入式平台(如手持设备、机顶盒、自助服务亭等)上运行 X Window 系统，这些平台的屏幕空间、输入机制或系统资源都有限。

Matchbox consists of a number of interchangeable and optional applications that you can tailor to a specific, non-desktop platform to enhance usability in constrained environments.

> 火柴盒由许多可互换和可选的应用程序组成，您可以根据特定的非桌面平台来定制，以增强受限环境中的可用性。

You can find the Matchbox source in the Yocto Project :yocto_[git:%60Source](git:%60Source) Repositories \<\>\`.

- *Opkg:* Open PacKaGe management (opkg) is a lightweight package management system based on the itsy package (ipkg) management system. Opkg is written in C and resembles Advanced Package Tool (APT) and Debian Package (dpkg) in operation.

> Opkg：开放式包管理(Opkg)是一种基于 Itsy 软件包(Ipk)管理系统的轻量级软件包管理系统。Opkg 用 C 编写，在操作上类似于高级软件包工具(APT)和 Debian 软件包(Dpkg)。

Opkg is intended for use on embedded Linux devices and is used in this capacity in the :oe_home:[OpenEmbedded \<\>] and [OpenWrt](https://openwrt.org/) projects, as well as the Yocto Project.

> Opkg 旨在用于嵌入式 Linux 设备，并在此能力中使用：oe_home:[OpenEmbedded \<\>] 和 [OpenWrt](https://openwrt.org/) 项目以及 Yocto 项目。

::: note
::: title
Note
:::

As best it can, opkg maintains backwards compatibility with ipkg and conforms to a subset of Debian\'s policy manual regarding control files.
:::

You can find the opkg source in the Yocto Project :yocto_[git:%60Source](git:%60Source) Repositories \<\>\`.

## Archived Components

The Build Appliance is a virtual machine image that enables you to build and boot a custom embedded Linux image with the Yocto Project using a non-Linux development system.

> 虚拟机镜像 Build Appliance 可以使您使用非 Linux 开发系统来使用 Yocto 项目构建和启动自定义嵌入式 Linux 镜像。

Historically, the Build Appliance was the second of three methods by which you could use the Yocto Project on a system that was not native to Linux.

1. *Hob:* Hob, which is now deprecated and is no longer available since the 2.1 release of the Yocto Project provided a rudimentary, GUI-based interface to the Yocto Project. Toaster has fully replaced Hob.

> *Hob:* Hob，自 Yocto 项目 2.1 版本发布以来已经不再可用，它提供了一个基础的基于 GUI 的界面来访问 Yocto 项目。Toaster 已经完全取代了 Hob。

2. *Build Appliance:* Post Hob, the Build Appliance became available. It was never recommended that you use the Build Appliance as a day-to-day production development environment with the Yocto Project. Build Appliance was useful as a way to try out development in the Yocto Project environment.

> 2. *构建设备：*在 Post Hob 之后，构建设备可用。不建议您将构建设备用作 Yocto 项目的日常生产开发环境。构建设备有助于尝试 Yocto 项目环境中的开发。

3. *CROPS:* The final and best solution available now for developing using the Yocto Project on a system not native to Linux is with `CROPS <overview-manual/yp-intro:development tools>`.

> 3. *农作物：* 在非本地 Linux 系统上使用 Yocto Project 开发的最终最佳解决方案是使用“CROPS <overview-manual/yp-intro:development tools>”。

# Development Methods

The Yocto Project development environment usually involves a `Build Host` and target hardware. You use the Build Host to build images and develop applications, while you use the target hardware to execute deployed software.

> 默认情况下，Yocto 项目的开发环境包括一个构建主机和目标硬件。您可以使用构建主机构建镜像和开发应用程序，而您可以使用目标硬件来执行部署的软件。

This section provides an introduction to the choices or development methods you have when setting up your Build Host. Depending on your particular workflow preference and the type of operating system your Build Host runs, you have several choices.

> 这一节提供了一个关于设置构建主机时可供选择的开发方法的介绍。根据您的特定工作流程偏好以及构建主机运行的操作系统类型，您有几种选择。

::: note
::: title
Note
:::

For additional detail about the Yocto Project development environment, see the \"`/overview-manual/development-environment`\" chapter.

> 对于关于 Yocto 项目开发环境的更多详细信息，请参阅“/overview-manual/development-environment”章节。
> :::

- *Native Linux Host:* By far the best option for a Build Host. A system running Linux as its native operating system allows you to develop software by directly using the `BitBake` tool. You can accomplish all aspects of development from a regular shell in a supported Linux distribution.

> 本地 Linux 主机：最佳构建主机的选择。使用 Linux 作为其本机操作系统的系统允许您直接使用 `BitBake` 工具开发软件。您可以从受支持的 Linux 发行版的常规 shell 中完成所有开发方面。

For information on how to set up a Build Host on a system running Linux as its native operating system, see the \"`dev-manual/start:setting up a native linux host`\" section in the Yocto Project Development Tasks Manual.

> 要了解如何在以 Linux 作为本机操作系统的系统上设置构建主机的信息，请参阅 Yocto 项目开发任务手册中的“dev-manual / start：在本机 Linux 主机上设置”部分。

- *CROss PlatformS (CROPS):* Typically, you use [CROPS](https://github.com/crops/poky-container/), which leverages [Docker Containers](https://www.docker.com/), to set up a Build Host that is not running Linux (e.g. Microsoft Windows or macOS).

> 通常，您可以使用 CROPS 来利用 Docker 容器设置一个不运行 Linux(例如 Microsoft Windows 或 macOS)的构建主机。

::: note
::: title
Note
:::

You can, however, use CROPS on a Linux-based system.
:::

CROPS is an open source, cross-platform development framework that provides an easily managed, extensible environment for building binaries targeted for a variety of architectures on Windows, macOS, or Linux hosts. Once the Build Host is set up using CROPS, you can prepare a shell environment to mimic that of a shell being used on a system natively running Linux.

> CROPS 是一个开源的跨平台开发框架，可以为 Windows、macOS 或 Linux 主机上的各种架构提供便于管理和可扩展的环境，用于构建二进制文件。一旦使用 CROPS 设置了构建主机，就可以准备一个模拟本地运行 Linux 系统的 shell 环境。

For information on how to set up a Build Host with CROPS, see the \"`dev-manual/start:setting up to use cross platforms (crops)`\" section in the Yocto Project Development Tasks Manual.

> 要了解如何使用 CROPS 设置构建主机的信息，请参阅 Yocto Project 开发任务手册中的“dev-manual/start：设置跨平台(crops)”部分。

- *Windows Subsystem For Linux (WSL 2):* You may use Windows Subsystem For Linux version 2 to set up a Build Host using Windows 10 or later, or Windows Server 2019 or later.

> 可以使用 Windows Subsystem For Linux 版本 2 在 Windows 10 或更高版本，或 Windows Server 2019 或更高版本上设置构建主机。

The Windows Subsystem For Linux allows Windows to run a real Linux kernel inside of a lightweight virtual machine (VM).

For information on how to set up a Build Host with WSL 2, see the \"`dev-manual/start:setting up to use windows subsystem for linux (wsl 2)`\" section in the Yocto Project Development Tasks Manual.

> 要了解如何使用 WSL 2 设置构建主机的信息，请参见 Yocto 项目开发任务手册中的“dev-manual/start：使用 Windows 子系统进行 Linux(WSL 2)设置”部分。

- *Toaster:* Regardless of what your Build Host is running, you can use Toaster to develop software using the Yocto Project. Toaster is a web interface to the Yocto Project\'s `OpenEmbedded Build System`. The interface allows you to configure and run your builds. Information about builds is collected and stored in a database. You can use Toaster to configure and start builds on multiple remote build servers.

> 无论您的构建主机运行什么，您都可以使用 Toaster 来使用 Yocto 项目开发软件。Toaster 是 Yocto 项目的“OpenEmbedded 构建系统”的 Web 界面。该界面允许您配置和运行构建。有关构建的信息被收集并存储在数据库中。您可以使用 Toaster 在多个远程构建服务器上配置和启动构建。

For information about and how to use Toaster, see the `/toaster-manual/index`.

# Reference Embedded Distribution (Poky)

\"Poky\", which is pronounced *Pock*-ee, is the name of the Yocto Project\'s reference distribution or Reference OS Kit. Poky contains the `OpenEmbedded Build System` to get you started building your own distro. In other words, Poky is a base specification of the functionality needed for a typical embedded system as well as the components from the Yocto Project that allow you to build a distribution into a usable binary image.

> "Poky"，发音为 *Pock*-ee，是 Yocto Project 的参考发行版或参考操作系统套件的名称。Poky 包含了“开放嵌入式构建系统”(BitBake 和 OpenEmbedded-Core(OE-Core))以及一组用于开始构建自己的发行版的“元数据”。换句话说，Poky 是一个嵌入式系统典型功能的基础规范，以及来自 Yocto Project 的组件，允许您将发行版构建为可用的二进制映像。

Poky is a combined repository of BitBake, OpenEmbedded-Core (which is found in `meta`), `meta-poky`, `meta-yocto-bsp`, and documentation provided all together and known to work well together. You can view these items that make up the Poky repository in the :yocto_[git:%60Source](git:%60Source) Repositories \</poky/tree/\>\`.

> Poky 是一个组合仓库，包括 BitBake、OpenEmbedded-Core(可在 `meta` 中找到)、`meta-poky`、`meta-yocto-bsp` 以及文档，它们能够很好地一起工作。您可以在 :yocto_[git:%60Source](git:%60Source) Repositories \</poky/tree/\>\`中查看构成 Poky 仓库的项目。

::: note
::: title
Note
:::

If you are interested in all the contents of the poky Git repository, see the \"`ref-manual/structure:top-level core components`\" section in the Yocto Project Reference Manual.

> 如果您对 Poky Git 存储库的所有内容感兴趣，请参见 Yocto Project 参考手册中的“ref-manual / structure：顶级核心组件”部分。
> :::

The following figure illustrates what generally comprises Poky:

![image](figures/poky-reference-distribution.png)

- BitBake is a task executor and scheduler that is the heart of the OpenEmbedded build system.
- `meta-poky`, which is Poky-specific metadata.
- `meta-yocto-bsp`, which are Yocto Project-specific Board Support Packages (BSPs).
- OpenEmbedded-Core (OE-Core) metadata, which includes shared configurations, global variable definitions, shared classes, packaging, and recipes. Classes define the encapsulation and inheritance of build logic. Recipes are the logical units of software and images to be built.

> 开放式嵌入式核心(OE-Core)元数据，其中包括共享配置、全局变量定义、共享类、打包和配方。类定义构建逻辑的封装和继承。配方是要构建的软件和镜像的逻辑单元。

- Documentation, which contains the Yocto Project source files used to make the set of user manuals.

::: note
::: title
Note
:::

While Poky is a \"complete\" distribution specification and is tested and put through QA, you cannot use it as a product \"out of the box\" in its current form.

> 虽然 Poky 是一个完整的发行规范，经过测试和质量保证，但是你不能把它作为当前形式的“现成产品”使用。
> :::

To use the Yocto Project tools, you can use Git to clone (download) the Poky repository then use your local copy of the reference distribution to bootstrap your own distribution.

> 要使用 Yocto Project 工具，您可以使用 Git 克隆(下载)Poky 存储库，然后使用您本地的参考分发版本来引导您自己的分发版本。

::: note
::: title
Note
:::

Poky does not contain binary files. It is a working example of how to build your own custom Linux distribution from source.
:::

Poky has a regular, well established, six-month release cycle under its own version. Major releases occur at the same time major releases (point releases) occur for the Yocto Project, which are typically in the Spring and Fall. For more information on the Yocto Project release schedule and cadence, see the \"`/ref-manual/release-process`\" chapter in the Yocto Project Reference Manual.

> Poky 有自己的版本，每六个月发布一次正式版本。与 Yocto Project 的主要版本(点发布版本)发布时间一致，通常在春季和秋季发布。有关 Yocto Project 发布计划和节奏的更多信息，请参阅 Yocto Project 参考手册中的“/ref-manual/release-process”章节。

Much has been said about Poky being a \"default configuration\". A default configuration provides a starting image footprint. You can use Poky out of the box to create an image ranging from a shell-accessible minimal image all the way up to a Linux Standard Base-compliant image that uses a GNOME Mobile and Embedded (GMAE) based reference user interface called Sato.

> 关于 Poky 作为“默认配置”已经有很多讨论。默认配置提供了一个起始的镜像足迹。您可以直接使用 Poky 来创建一个从可以访问 shell 的最小镜像到使用基于 GNOME Mobile 和嵌入式(GMAE)的参考用户界面 Sato 的 Linux 标准基础(LSB)兼容镜像的镜像。

One of the most powerful properties of Poky is that every aspect of a build is controlled by the metadata. You can use metadata to augment these base image types by adding metadata `layers <overview-manual/yp-intro:the yocto project layer model>` that extend functionality. These layers can provide, for example, an additional software stack for an image type, add a board support package (BSP) for additional hardware, or even create a new image type.

> 一个最强大的 Poky 属性是每个构建的方面都由元数据控制。您可以使用元数据通过添加元数据层 <overview-manual/yp-intro：yocto 项目层模型 > 来增强这些基本镜像类型。这些层可以提供例如为镜像类型提供额外的软件堆栈，为其他硬件添加板支持包(BSP)，甚至创建新的镜像类型。

Metadata is loosely grouped into configuration files or package recipes. A recipe is a collection of non-executable metadata used by BitBake to set variables or define additional build-time tasks. A recipe contains fields such as the recipe description, the recipe version, the license of the package and the upstream source repository. A recipe might also indicate that the build process uses autotools, make, distutils or any other build process, in which case the basic functionality can be defined by the classes it inherits from the OE-Core layer\'s class definitions in `./meta/classes`. Within a recipe you can also define additional tasks as well as task prerequisites. Recipe syntax through BitBake also supports both `:prepend` and `:append` operators as a method of extending task functionality. These operators inject code into the beginning or end of a task. For information on these BitBake operators, see the \"`bitbake-user-manual/bitbake-user-manual-metadata:appending and prepending (override style syntax)`\" section in the BitBake User\'s Manual.

> 元数据通常分组到配置文件或软件包配方中。配方是一组非可执行的元数据，由 BitBake 使用来设置变量或定义额外的构建时任务。配方包括配方描述、配方版本、软件包许可证和上游源代码存储库等字段。配方还可以指示构建过程使用自动工具、make、distutils 或其他构建过程，在这种情况下，基本功能可以由它从 OE-Core 层的类定义中继承的类定义来定义。在配方中，您还可以定义额外的任务以及任务的先决条件。BitBake 的配方语法还支持 `:prepend` 和 `:append` 操作符，作为扩展任务功能的一种方法。这些操作符将代码插入任务的开头或末尾。有关这些 BitBake 操作符的信息，请参阅 BitBake 用户手册中的“bitbake-user-manual/bitbake-user-manual-metadata:appending and prepending (override style syntax)”部分。

# The OpenEmbedded Build System Workflow

The `OpenEmbedded Build System` uses a \"workflow\" to accomplish image and SDK generation. The following figure overviews that workflow:

> 开放式嵌入式构建系统使用“工作流程”来完成镜像和 SDK 生成。下图概述了该工作流程：

![image](figures/YP-flow-diagram.png)

Following is a brief summary of the \"workflow\":

1. Developers specify architecture, policies, patches and configuration details.
2. The build system fetches and downloads the source code from the specified location. The build system supports standard methods such as tarballs or source code repositories systems such as Git.

> 系统从指定位置获取和下载源代码。该构建系统支持标准方法，如压缩文件或源代码存储库系统(如 Git)。

3. Once source code is downloaded, the build system extracts the sources into a local work area where patches are applied and common steps for configuring and compiling the software are run.

> 一旦源代码下载完成，构建系统将源代码提取到本地工作区，在这里应用补丁，并运行配置和编译软件的常见步骤。

4. The build system then installs the software into a temporary staging area where the binary package format you select (DEB, RPM, or IPK) is used to roll up the software.

> 系统接着将软件安装到一个临时的分发区，您所选择的二进制包格式(DEB、RPM 或 IPK)将用于将软件打包。

5. Different QA and sanity checks run throughout entire build process.
6. After the binaries are created, the build system generates a binary package feed that is used to create the final root file image.
7. The build system generates the file system image and a customized Extensible SDK (eSDK) for application development in parallel.

For a very detailed look at this workflow, see the \"`overview-manual/concepts:openembedded build system concepts`\" section.

> 欲了解此工作流程的详细信息，请参阅“overview-manual/concepts：开放嵌入式构建系统概念”部分。

# Some Basic Terms

It helps to understand some basic fundamental terms when learning the Yocto Project. Although there is a list of terms in the \"`Yocto Project Terms </ref-manual/terms>`\" section of the Yocto Project Reference Manual, this section provides the definitions of some terms helpful for getting started:

> 学习 Yocto 项目时，了解一些基本的基本术语有助于理解。尽管 Yocto 项目参考手册中有一个术语列表 <ref-manual/terms>，但本节提供了一些有助于入门的术语定义：

- *Configuration Files:* Files that hold global definitions of variables, user-defined variables, and hardware configuration information. These files tell the `OpenEmbedded Build System` what to build and what to put into the image to support a particular platform.

> *配置文件：*存储全局变量定义、用户定义变量和硬件配置信息的文件。这些文件告诉 OpenEmbedded 构建系统构建什么以及支持特定平台需要放入镜像中的内容。

- *Extensible Software Development Kit (eSDK):* A custom SDK for application developers. This eSDK allows developers to incorporate their library and programming changes back into the image to make their code available to other application developers. For information on the eSDK, see the `/sdk-manual/index` manual.

> 可扩展软件开发工具包(eSDK)：一个定制的 SDK，用于应用开发人员。该 eSDK 允许开发人员将其库和编程更改合并回映像中，以使其代码可供其他应用开发人员使用。有关 eSDK 的信息，请参见“/sdk-manual/index”手册。

- *Layer:* A collection of related recipes. Layers allow you to consolidate related metadata to customize your build. Layers also isolate information used when building for multiple architectures. Layers are hierarchical in their ability to override previous specifications. You can include any number of available layers from the Yocto Project and customize the build by adding your own layers after them. You can search the Layer Index for layers used within Yocto Project.

> 层：一系列相关的配方。层可以让您整合相关的元数据以定制构建。层还可以隔离构建多个体系结构时使用的信息。层在覆盖先前规格的能力上是分层的。您可以从 Yocto 项目中包含任何数量的可用层，并通过在它们之后添加自己的层来自定义构建。您可以在层索引中搜索在 Yocto 项目中使用的层。

For more detailed information on layers, see the \"`dev-manual/layers:understanding and creating layers`\" section in the Yocto Project Board Support Packages (BSP) Developer\'s Guide.

> 对于层的更详细信息，请参阅 Yocto 项目开发任务手册中的“dev-manual/layers：理解和创建层”部分。关于 BSP 层的讨论，请参阅 Yocto 项目板支持包(BSP)开发者指南中的“bsp-guide/bsp：bsp 层”部分。

- *Metadata:* A key element of the Yocto Project is the Metadata that is used to construct a Linux distribution and is contained in the files that the OpenEmbedded build system parses when building an image. In general, Metadata includes recipes, configuration files, and other information that refers to the build instructions themselves, as well as the data used to control what things get built and the effects of the build. Metadata also includes commands and data used to indicate what versions of software are used, from where they are obtained, and changes or additions to the software itself (patches or auxiliary files) that are used to fix bugs or customize the software for use in a particular situation. OpenEmbedded-Core is an important set of validated metadata.

> 在 Yocto 项目中，元数据是构建 Linux 发行版的关键要素，它包含在 OpenEmbedded 构建系统解析的文件中，以构建镜像。通常，元数据包括配方、配置文件和其他指向构建指令本身的信息，以及用于控制什么东西被构建以及构建的效果的数据。元数据还包括用于指示使用的软件的哪个版本、从何处获取它们以及用于修复错误或为特定情况下的软件定制的更改或添加(补丁或辅助文件)的命令和数据。OpenEmbedded-Core 是一组重要的验证元数据。

- *OpenEmbedded Build System:* The terms \"BitBake\" and \"build system\" are sometimes used for the OpenEmbedded Build System.

  BitBake is a task scheduler and execution engine that parses instructions (i.e. recipes) and configuration data. After a parsing phase, BitBake creates a dependency tree to order the compilation, schedules the compilation of the included code, and finally executes the building of the specified custom Linux image (distribution). BitBake is similar to the `make` tool.

> BitBake 是一种任务调度器和执行引擎，它解析指令(即配方)和配置数据。在解析阶段之后，BitBake 创建一个依赖树来排序编译，调度包含的代码的编译，最后执行构建指定的自定义 Linux 映像(发行版)。BitBake 类似于“make”工具。

During a build process, the build system tracks dependencies and performs a native or cross-compilation of each package. As a first step in a cross-build setup, the framework attempts to create a cross-compiler toolchain (i.e. Extensible SDK) suited for the target platform.

> 在构建过程中，构建系统跟踪依赖关系并对每个软件包执行本地或跨编译。作为跨构建设置的第一步，该框架尝试创建适用于目标平台的跨编译器工具链(即可扩展 SDK)。

- *OpenEmbedded-Core (OE-Core):* OE-Core is metadata comprised of foundation recipes, classes, and associated files that are meant to be common among many different OpenEmbedded-derived systems, including the Yocto Project. OE-Core is a curated subset of an original repository developed by the OpenEmbedded community that has been pared down into a smaller, core set of continuously validated recipes. The result is a tightly controlled and quality-assured core set of recipes.

> 开放嵌入式核心(OE-Core)：OE-Core 是由基础配方、类和相关文件组成的元数据，旨在在许多不同的 OpenEmbedded 衍生系统(包括 Yocto 项目)之间共享。OE-Core 是由 OpenEmbedded 社区开发的原始存储库的精心挑选的子集，已经缩减成较小的核心集合，持续验证配方。结果是一个紧密控制和质量保证的核心配方集合。

You can see the Metadata in the `meta` directory of the Yocto Project :yocto_[git:%60Source](git:%60Source) Repositories \<\>\`.

- *Packages:* In the context of the Yocto Project, this term refers to a recipe\'s packaged output produced by BitBake (i.e. a \"baked recipe\"). A package is generally the compiled binaries produced from the recipe\'s sources. You \"bake\" something by running it through BitBake.

> 在 Yocto 项目的上下文中，此术语指的是 BitBake(即“烘焙 recipes”)产生的配方输出的包装。包通常是从配方源代码编译生成的二进制文件。通过运行 BitBake 可以“烘焙”东西。

It is worth noting that the term \"package\" can, in general, have subtle meanings. For example, the packages referred to in the \"`ref-manual/system-requirements:required packages for the build host`\" section in the Yocto Project Reference Manual are compiled binaries that, when installed, add functionality to your host Linux distribution.

> 值得注意的是，“包”这个词通常有微妙的含义。例如，在 Yocto 项目参考手册中的“ref-manual/system-requirements：构建主机所需的包”部分所指的包是编译过的二进制文件，安装后可以为您的主机 Linux 发行版增加功能。

Another point worth noting is that historically within the Yocto Project, recipes were referred to as packages \-\-- thus, the existence of several BitBake variables that are seemingly mis-named, (e.g. `PR`).

> 另一个值得注意的一点是，在 Yocto 项目的历史上，recipes 被称为包-因此，存在一些似乎命名不当的 BitBake 变量(例如 `PR`)。

- *Poky:* Poky is a reference embedded distribution and a reference test configuration. Poky provides the following:

  - A base-level functional distro used to illustrate how to customize a distribution.
  - A means by which to test the Yocto Project components (i.e. Poky is used to validate the Yocto Project).
  - A vehicle through which you can download the Yocto Project.

  Poky is not a product level distro. Rather, it is a good starting point for customization.

  ::: note
  ::: title
  Note
  :::

  Poky is an integration layer on top of OE-Core.
  :::
- *Recipe:* The most common form of metadata. A recipe contains a list of settings and tasks (i.e. instructions) for building packages that are then used to build the binary image. A recipe describes where you get source code and which patches to apply. Recipes describe dependencies for libraries or for other recipes as well as configuration and compilation options. Related recipes are consolidated into a layer.

> recipes：最常见的元数据形式。recipes 包含一系列设置和任务(即指令)，用于构建用于构建二进制镜像的包。recipes 描述了您获取源代码的位置以及应用哪些补丁。recipes 还描述了库或其他 recipes 的依赖关系以及配置和编译选项。相关的 recipes 被合并到一个层中。
