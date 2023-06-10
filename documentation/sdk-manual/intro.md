---
tip: translate by openai@2023-06-07 21:32:32
...
---
title: Introduction
-------------------

# eSDK Introduction

Welcome to the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual. This manual explains how to use both the Yocto Project extensible and standard SDKs to develop applications and images.

> 欢迎来到 Yocto 项目应用程序开发和可扩展软件开发工具包（eSDK）手册。本手册介绍了如何使用 Yocto 项目可扩展和标准 SDK 来开发应用程序和图像。

All SDKs consist of the following:

> 所有的 SDK 都包括以下内容：

- *Cross-Development Toolchain*: This toolchain contains a compiler, debugger, and various associated tools.

> 这个工具链包含一个编译器、调试器和各种相关工具。

- *Libraries, Headers, and Symbols*: The libraries, headers, and symbols are specific to the image (i.e. they match the image against which the SDK was built).

> 库、头文件和符号：库、头文件和符号与图像特定（即它们与用于构建 SDK 的图像相匹配）。

- *Environment Setup Script*: This `*.sh` file, once sourced, sets up the cross-development environment by defining variables and preparing for SDK use.

> - *环境设置脚本*：一旦源被激活，这个 `*.sh` 文件就会通过定义变量和准备 SDK 使用来设置跨开发环境。

Additionally, an extensible SDK has tools that allow you to easily add new applications and libraries to an image, modify the source of an existing component, test changes on the target hardware, and easily integrate an application into the `OpenEmbedded Build System`{.interpreted-text role="term"}.

> 此外，可扩展的 SDK 提供了工具，可让您轻松地将新应用程序和库添加到镜像中，修改现有组件的源代码，在目标硬件上测试更改，并轻松地将应用程序集成到 OpenEmbedded 构建系统中。

You can use an SDK to independently develop and test code that is destined to run on some target machine. SDKs are completely self-contained. The binaries are linked against their own copy of `libc`, which results in no dependencies on the target system. To achieve this, the pointer to the dynamic loader is configured at install time since that path cannot be dynamically altered. This is the reason for a wrapper around the `populate_sdk` and `populate_sdk_ext` archives.

> 你可以使用 SDK 独立开发和测试将要在目标机器上运行的代码。 SDK 完全自包含。 二进制文件链接到自己的 `libc` 副本，因此不会对目标系统产生依赖关系。 为此，安装时配置动态加载器的指针，因为该路径无法动态更改。 这就是为什么 `populate_sdk` 和 `populate_sdk_ext` 存档周围有一个包装器的原因。

Another feature of the SDKs is that only one set of cross-compiler toolchain binaries are produced for any given architecture. This feature takes advantage of the fact that the target hardware can be passed to `gcc` as a set of compiler options. Those options are set up by the environment script and contained in variables such as `CC`{.interpreted-text role="term"} and `LD`{.interpreted-text role="term"}. This reduces the space needed for the tools. Understand, however, that every target still needs its own sysroot because those binaries are target-specific.

> SDK 的另一个特点是，只为给定架构生成一组交叉编译工具链二进制文件。这个特性利用了目标硬件可以作为一组编译器选项传递给 `gcc` 的事实。这些选项由环境脚本设置，并包含在 `CC` 和 `LD` 等变量中。这减少了工具所需的空间。但是要明白，每个目标仍然需要自己的 sysroot，因为这些二进制文件是特定于目标的。

The SDK development environment consists of the following:

> SDK 开发环境由以下组成：

- The self-contained SDK, which is an architecture-specific cross-toolchain and matching sysroots (target and native) all built by the OpenEmbedded build system (e.g. the SDK). The toolchain and sysroots are based on a `Metadata`{.interpreted-text role="term"} configuration and extensions, which allows you to cross-develop on the host machine for the target hardware. Additionally, the extensible SDK contains the `devtool` functionality.

> 自包含的 SDK，它是一个架构特定的跨工具链和匹配的 sysroots（目标和本地）都是由 OpenEmbedded 构建系统构建的（例如 SDK）。工具链和 sysroots 基于 `Metadata` 配置和扩展，允许您在主机上为目标硬件进行跨开发。此外，可扩展的 SDK 包含 `devtool` 功能。

- The Quick EMUlator (QEMU), which lets you simulate target hardware. QEMU is not literally part of the SDK. You must build and include this emulator separately. However, QEMU plays an important role in the development process that revolves around use of the SDK.

> 快速模拟器（QEMU）可以让您模拟目标硬件。QEMU 不是 SDK 的一部分，您必须单独构建并包含此模拟器。但是，QEMU 在围绕 SDK 使用的开发过程中起着重要的作用。

In summary, the extensible and standard SDK share many features. However, the extensible SDK has powerful development tools to help you more quickly develop applications. Following is a table that summarizes the primary differences between the standard and extensible SDK types when considering which to build:

> 总而言之，可扩展的标准 SDK 具有许多功能。但是，可扩展的 SDK 拥有强大的开发工具，可以帮助您更快地开发应用程序。以下是一张表，总结了标准和可扩展 SDK 类型之间的主要差异，以便考虑建立哪一个：

---

*Feature*               *Standard SDK*          *Extensible SDK*

> 特征               标准 SDK            可扩展 SDK

---

Toolchain               Yes                     Yes[^1]

> 工具链                   是                     是[^1]

Debugger                Yes                     Yes[^2]

> 调试器                是                     是[^2]

Size                    100+ MBytes             1+ GBytes (or 300+ MBytes for minimal w/toolchain)

> 大小：100MB 以上，1GB 以上（或最小安装时 300MB 以上）

`devtool`               No                      Yes

> 不是的，是的

Build Images            No                      Yes

> 建立图像            不                      是

Updateable              No                      Yes

> 可更新：否                      是

Managed Sysroot[^3]     No                      Yes

> 管理的 Sysroot[^3]     不                      是

Installed Packages      No[^4]                  Yes[^5]

> 已安装的软件包      没有[^4]                  是的[^5]

Construction            Packages                Shared State

> 建设包裹共享状态

---

## The Cross-Development Toolchain

The `Cross-Development Toolchain`{.interpreted-text role="term"} consists of a cross-compiler, cross-linker, and cross-debugger that are used to develop user-space applications for targeted hardware; in addition, the extensible SDK comes with built-in `devtool` functionality. This toolchain is created by running a SDK installer script or through a `Build Directory`{.interpreted-text role="term"} that is based on your metadata configuration or extension for your targeted device. The cross-toolchain works with a matching target sysroot.

> 工具链（Cross-Development Toolchain）由交叉编译器、交叉链接器和交叉调试器组成，用于为目标硬件开发用户空间应用程序；此外，可扩展的 SDK 还带有内置的 `devtool` 功能。可以通过运行 SDK 安装程序脚本或基于您的元数据配置或为目标设备扩展的构建目录（Build Directory）来创建此工具链。交叉工具链需要与之匹配的目标 sysroot 一起使用。

## Sysroots

The native and target sysroots contain needed headers and libraries for generating binaries that run on the target architecture. The target sysroot is based on the target root filesystem image that is built by the OpenEmbedded build system and uses the same metadata configuration used to build the cross-toolchain.

> 本机和目标系统根目录包含生成能够在目标架构上运行的二进制文件所需的头文件和库。目标系统根目录基于 OpenEmbedded 构建系统构建的目标根文件系统映像，并使用用于构建交叉工具链的相同元数据配置。

## The QEMU Emulator

The QEMU emulator allows you to simulate your hardware while running your application or image. QEMU is not part of the SDK but is automatically installed and available if you have done any one of the following:

> QEMU 模拟器可以在运行应用程序或镜像时模拟您的硬件。QEMU 不是 SDK 的一部分，但如果您已经完成以下任何一项，则会自动安装并可用：

- cloned the `poky` Git repository to create a `Source Directory`{.interpreted-text role="term"} and sourced the environment setup script.

> 克隆了 `poky` Git 存储库以创建 `源目录` 并且源自环境设置脚本。

- downloaded a Yocto Project release and unpacked it to create a Source Directory and sourced the environment setup script.

> 已下載 Yocto Project 版本並將其解壓縮以建立源目錄並源自環境設定腳本。

- installed the cross-toolchain tarball and sourced the toolchain\'s setup environment script.

# SDK Development Model

Fundamentally, the SDK fits into the development process as follows:

> 基本上，SDK 的开发流程如下：

![image](figures/sdk-environment.png){width="100.0%"}

The SDK is installed on any machine and can be used to develop applications, images, and kernels. An SDK can even be used by a QA Engineer or Release Engineer. The fundamental concept is that the machine that has the SDK installed does not have to be associated with the machine that has the Yocto Project installed. A developer can independently compile and test an object on their machine and then, when the object is ready for integration into an image, they can simply make it available to the machine that has the Yocto Project. Once the object is available, the image can be rebuilt using the Yocto Project to produce the modified image.

> SDK 可以安装在任何机器上，可用于开发应用程序、图像和内核。甚至还可以被质量保证工程师或发布工程师使用。基本概念是，安装了 SDK 的机器不必与安装了 Yocto 项目的机器相关联。开发人员可以独立地在自己的机器上编译和测试对象，然后当对象准备好集成到图像中时，他们可以将其简单地提供给安装了 Yocto 项目的机器。一旦对象可用，就可以使用 Yocto 项目重新构建图像以生成修改后的图像。

You just need to follow these general steps:

> 你只需要遵循这些一般步骤：

1. *Install the SDK for your target hardware:* For information on how to install the SDK, see the \"`sdk-manual/using:installing the sdk`{.interpreted-text role="ref"}\" section.

> *安装面向目标硬件的 SDK：* 有关如何安装 SDK 的信息，请参见“sdk-manual/using：安装 sdk”部分。

2. *Download or Build the Target Image:* The Yocto Project supports several target architectures and has many pre-built kernel images and root filesystem images.

> 2. *下载或构建目标镜像：* Yocto 项目支持多种目标架构，并提供许多预构建的内核镜像和根文件系统镜像。

If you are going to develop your application on hardware, go to the :yocto_dl:[machines \</releases/yocto/yocto-&DISTRO;/machines/\>]{.title-ref} download area and choose a target machine area from which to download the kernel image and root filesystem. This download area could have several files in it that support development using actual hardware. For example, the area might contain `.hddimg` files that combine the kernel image with the filesystem, boot loaders, and so forth. Be sure to get the files you need for your particular development process.

> 如果您要在硬件上开发应用程序，请转到:yocto_dl:[machines \</releases/yocto/yocto-&DISTRO;/machines/\>]{.title-ref}下载区域，并从中选择一个目标机器区域以下载内核映像和根文件系统。此下载区域可能包含几个支持使用实际硬件开发的文件。例如，该区域可能包含将内核映像与文件系统、引导加载程序等组合在一起的 `.hddimg` 文件。请务必获取您特定开发过程所需的文件。

If you are going to develop your application and then run and test it using the QEMU emulator, go to the :yocto_dl:[machines/qemu \</releases/yocto/yocto-&DISTRO;/machines/qemu\>]{.title-ref} download area. From this area, go down into the directory for your target architecture (e.g. `qemux86_64` for an Intel-based 64-bit architecture). Download the kernel, root filesystem, and any other files you need for your process.

> 如果您要使用 QEMU 模拟器开发应用程序，然后运行和测试它，请转到:yocto_dl:[machines/qemu \</releases/yocto/yocto-&DISTRO;/machines/qemu\>]{.title-ref} 下载区域。 从这个区域，进入您的目标架构的目录（例如，Intel 基于 64 位架构的 `qemux86_64`）。 下载内核、根文件系统和您需要的其他文件以完成过程。

::: note
::: title

Note

> 注意
> :::

To use the root filesystem in QEMU, you need to extract it. See the \"`sdk-manual/appendix-obtain:extracting the root filesystem`{.interpreted-text role="ref"}\" section for information on how to do this extraction.

> 要在 QEMU 中使用根文件系统，您需要提取它。有关如何执行此提取的信息，请参阅“sdk-manual / appendix-obtain：提取根文件系统”部分。
> :::

3. *Develop and Test your Application:* At this point, you have the tools to develop your application. If you need to separately install and use the QEMU emulator, you can go to [QEMU Home Page](https://wiki.qemu.org/Main_Page) to download and learn about the emulator. See the \"`/dev-manual/qemu`{.interpreted-text role="doc"}\" chapter in the Yocto Project Development Tasks Manual for information on using QEMU within the Yocto Project.

> 3. *开发和测试您的应用程序：* 现在，您有开发应用程序的工具。如果您需要单独安装和使用 QEMU 模拟器，可以转到 [QEMU 主页](https://wiki.qemu.org/Main_Page)下载并了解模拟器。有关在 Yocto 项目中使用 QEMU 的信息，请参阅 Yocto 项目开发任务手册中的“/dev-manual/qemu”章节。

The remainder of this manual describes how to use the extensible and standard SDKs. There is also information in appendix form describing how you can build, install, and modify an SDK.

> 本手册的其余部分描述了如何使用可扩展和标准 SDK。附录中还有有关如何构建、安装和修改 SDK 的信息。

[^1]: Extensible SDK contains the toolchain and debugger if `SDK_EXT_TYPE`{.interpreted-text role="term"} is \"full\" or `SDK_INCLUDE_TOOLCHAIN`{.interpreted-text role="term"} is \"1\", which is the default.


> 如果 `SDK_EXT_TYPE`{.interpreted-text role="term"}为“full”或 `SDK_INCLUDE_TOOLCHAIN`{.interpreted-text role="term"}为“1”（默认值），可扩展的 SDK 包含工具链和调试器。

[^2]: Extensible SDK contains the toolchain and debugger if `SDK_EXT_TYPE`{.interpreted-text role="term"} is \"full\" or `SDK_INCLUDE_TOOLCHAIN`{.interpreted-text role="term"} is \"1\", which is the default.


> 如果 `SDK_EXT_TYPE`{.interpreted-text role="term"}为“full”或 `SDK_INCLUDE_TOOLCHAIN`{.interpreted-text role="term"}为“1”（默认值），可扩展的 SDK 包含工具链和调试器。

[^3]: Sysroot is managed through the use of `devtool`. Thus, it is less likely that you will corrupt your SDK sysroot when you try to add additional libraries.


> Sysroot 通过使用 `devtool` 来管理。因此，当你尝试添加额外的库时，你损坏你的 SDK sysroot 的可能性较低。

[^4]: You can add runtime package management to the standard SDK but it is not supported by default.


> 你可以在标准 SDK 中添加运行时包管理，但默认情况下不支持。

[^5]: You must build and make the shared state available to extensible SDK users for \"packages\" you want to enable users to install.


> 你必须构建并使可扩展 SDK 用户可以安装的“包”的共享状态可用。
