---
tip: translate by openai@2023-06-07 21:37:40
...
---
title: Using the Standard SDK
-----------------------------

This chapter describes the standard SDK and how to install it. Information includes unique installation and setup aspects for the standard SDK.

> 本章节描述了标准 SDK 的安装和安装方法。信息包括标准 SDK 的独特安装和设置方面。

::: note
::: title
Note
:::

For a side-by-side comparison of main features supported for a standard SDK as compared to an extensible SDK, see the \"`sdk-manual/intro:introduction`{.interpreted-text role="ref"}\" section.

> 若要比较标准 SDK 与可扩展 SDK 的主要功能，请参见“sdk-manual/intro:introduction”部分。
> :::

You can use a standard SDK to work on Makefile and Autotools-based projects. See the \"`sdk-manual/working-projects:using the sdk toolchain directly`{.interpreted-text role="ref"}\" chapter for more information.

> 你可以使用标准的 SDK 来处理基于 Makefile 和 Autotools 的项目。有关更多信息，请参阅“sdk-manual / working-projects：直接使用 sdk 工具链”一章。

# Why use the Standard SDK and What is in It?

The Standard SDK provides a cross-development toolchain and libraries tailored to the contents of a specific image. You would use the Standard SDK if you want a more traditional toolchain experience as compared to the extensible SDK, which provides an internal build system and the `devtool` functionality.

> 标准 SDK 提供了一个跨开发工具链和库，这些库都是针对特定图像的内容进行定制的。如果您希望比可扩展 SDK 提供的内部构建系统和 `devtool` 功能更具传统的工具链体验，那么您就可以使用标准 SDK。

The installed Standard SDK consists of several files and directories. Basically, it contains an SDK environment setup script, some configuration files, and host and target root filesystems to support usage. You can see the directory structure in the \"`sdk-manual/appendix-obtain:installed standard sdk directory structure`{.interpreted-text role="ref"}\" section.

> 安装的标准 SDK 包括几个文件和目录。基本上，它包含一个 SDK 环境设置脚本、一些配置文件以及支持使用的主机和目标根文件系统。您可以在“sdk-manual/appendix-obtain：安装的标准 SDK 目录结构”部分查看目录结构。

# Installing the SDK

The first thing you need to do is install the SDK on your `Build Host`{.interpreted-text role="term"} by running the `*.sh` installation script.

> 首先你需要做的是在你的构建主机上运行 `*.sh` 安装脚本来安装 SDK。

You can download a tarball installer, which includes the pre-built toolchain, the `runqemu` script, and support files from the appropriate :yocto_dl:[toolchain \</releases/yocto/yocto-&DISTRO;/toolchain/\>]{.title-ref} directory within the Index of Releases. Toolchains are available for several 32-bit and 64-bit architectures with the `x86_64` directories, respectively. The toolchains the Yocto Project provides are based off the `core-image-sato` and `core-image-minimal` images and contain libraries appropriate for developing against the corresponding image.

> 你可以从适当的 Index of Releases 目录中下载 tarball 安装程序，其中包括预构建的工具链、`runqemu` 脚本和支持文件。Yocto 项目提供的工具链基于 `core-image-sato` 和 `core-image-minimal` 映像，并包含适合开发相应映像的库。这些工具链分别位于 32 位和 64 位架构的 `x86_64` 目录中。

The names of the tarball installer scripts are such that a string representing the host system appears first in the filename and then is immediately followed by a string representing the target architecture:

> 文件名中首先出现的是代表主机系统的字符串，然后立即跟随着代表目标架构的字符串，这就是 tarball 安装脚本的名称。

```
poky-glibc-host_system-image_type-arch-toolchain-release_version.sh

Where:
    host_system is a string representing your development system:

               i686 or x86_64.

    image_type is the image for which the SDK was built:

               core-image-minimal or core-image-sato.

    arch is a string representing the tuned target architecture:

               aarch64, armv5e, core2-64, i586, mips32r2, mips64, ppc7400, or cortexa8hf-neon.

    release_version is a string representing the release number of the Yocto Project:

               &DISTRO;, &DISTRO;+snapshot
```

For example, the following SDK installer is for a 64-bit development host system and a i586-tuned target architecture based off the SDK for `core-image-sato` and using the current DISTRO snapshot:

> 例如，以下 SDK 安装程序是为基于 SDK 的 64 位开发主机系统和基于 `core-image-sato` 的 i586 调整目标体系结构，并使用当前 DISTRO 快照：

```
poky-glibc-x86_64-core-image-sato-i586-toolchain-DISTRO.sh
```

::: note
::: title
Note
:::

As an alternative to downloading an SDK, you can build the SDK installer. For information on building the installer, see the \"`sdk-manual/appendix-obtain:building an sdk installer`{.interpreted-text role="ref"}\" section.

> 作为下载 SDK 的替代方案，您可以构建 SDK 安装程序。有关构建安装程序的信息，请参阅“sdk-manual / appendix-obtain：构建 SDK 安装程序”部分。
> :::

The SDK and toolchains are self-contained and by default are installed into the `poky_sdk` folder in your home directory. You can choose to install the extensible SDK in any location when you run the installer. However, because files need to be written under that directory during the normal course of operation, the location you choose for installation must be writable for whichever users need to use the SDK.

> SDK 和工具链都是自包含的，默认情况下会安装到您的家目录下的 `poky_sdk` 文件夹中。在运行安装程序时，您可以选择在任何位置安装可扩展的 SDK。但是，由于文件在正常操作过程中需要写入该目录，因此您选择的安装位置必须对需要使用 SDK 的任何用户都可写。

The following command shows how to run the installer given a toolchain tarball for a 64-bit x86 development host system and a 64-bit x86 target architecture. The example assumes the SDK installer is located in `~/Downloads/` and has execution rights:

> 以下命令显示如何为 64 位 x86 开发主机系统和 64 位 x86 目标架构运行安装程序，给出工具链 tarball。示例假设 SDK 安装程序位于 `~/Downloads/`，并具有执行权限：

```
$ ./Downloads/poky-glibc-x86_64-core-image-sato-i586-toolchain-&DISTRO;.sh
Poky (Yocto Project Reference Distro) SDK installer version &DISTRO;
===============================================================
Enter target directory for SDK (default: /opt/poky/&DISTRO;):
You are about to install the SDK to "/opt/poky/&DISTRO;". Proceed [Y/n]? Y
Extracting SDK........................................ ..............................done
Setting it up...done
SDK has been successfully set up and is ready to be used.
Each time you wish to use the SDK in a new shell session, you need to source the environment setup script e.g.
 $ . /opt/poky/&DISTRO;/environment-setup-i586-poky-linux
```

::: note
::: title
Note
:::

If you do not have write permissions for the directory into which you are installing the SDK, the installer notifies you and exits. For that case, set up the proper permissions in the directory and run the installer again.

> 如果您没有安装 SDK 所需的写入权限，安装程序会通知您并退出。在这种情况下，请在目录中设置正确的权限，然后再次运行安装程序。
> :::

Again, reference the \"`sdk-manual/appendix-obtain:installed standard sdk directory structure`{.interpreted-text role="ref"}\" section for more details on the resulting directory structure of the installed SDK.

> 再次参考“sdk-manual/appendix-obtain：安装的标准 sdk 目录结构”部分，了解安装的 SDK 的最终目录结构的更多细节。

# Running the SDK Environment Setup Script

Once you have the SDK installed, you must run the SDK environment setup script before you can actually use the SDK. This setup script resides in the directory you chose when you installed the SDK, which is either the default `/opt/poky/&DISTRO;` directory or the directory you chose during installation.

> 一旦安装了 SDK，在使用 SDK 之前，必须先运行 SDK 环境设置脚本。此设置脚本位于您在安装 SDK 时选择的目录中，该目录是默认的 `/opt/poky/&DISTRO;` 目录或您在安装时选择的目录。

Before running the script, be sure it is the one that matches the architecture for which you are developing. Environment setup scripts begin with the string \"`environment-setup`\" and include as part of their name the tuned target architecture. As an example, the following commands set the working directory to where the SDK was installed and then source the environment setup script. In this example, the setup script is for an IA-based target machine using i586 tuning:

> 在运行脚本之前，请确保它与您正在开发的架构匹配。环境设置脚本以“environment-setup”字符串开头，并包含调整的目标架构的名称。例如，以下命令将工作目录设置为 SDK 安装的位置，然后源环境设置脚本。在此示例中，设置脚本是针对基于 IA 的目标机器使用 i586 调整：

```
$ source /opt/poky/&DISTRO;/environment-setup-i586-poky-linux
```

When you run the setup script, the same environment variables are defined as are when you run the setup script for an extensible SDK. See the \"`sdk-manual/appendix-obtain:installed extensible sdk directory structure`{.interpreted-text role="ref"}\" section for more information.

> 当你运行安装脚本时，定义的环境变量与运行可扩展 SDK 的安装脚本时相同。有关更多信息，请参见“sdk-manual / appendix-obtain：安装可扩展 SDK 目录结构”部分。
