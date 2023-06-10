---
tip: translate by openai@2023-06-07 20:57:25
title: Obtaining the SDK
---
# Working with the SDK components directly in a Yocto build

Please refer to section \"`sdk-manual/extensible:Setting up the Extensible SDK environment directly in a Yocto build`{.interpreted-text role="ref"}\"

> 请参阅“sdk-manual/extensible：直接在 Yocto 构建中设置可扩展 SDK 环境”部分。

Note that to use this feature effectively either a powerful build machine, or a well-functioning sstate cache infrastructure is required: otherwise significant time could be spent waiting for components to be built by BitBake from source code.

# Working with standalone SDK Installers

## Locating Pre-Built SDK Installers

You can use existing, pre-built toolchains by locating and running an SDK installer script that ships with the Yocto Project. Using this method, you select and download an architecture-specific SDK installer and then run the script to hand-install the toolchain.

> 你可以通过定位和运行 Yocto 项目附带的 SDK 安装程序脚本来使用现有的预构建工具链。使用这种方法，您可以选择并下载架构特定的 SDK 安装程序，然后运行脚本来手动安装工具链。

Follow these steps to locate and hand-install the toolchain:

> 按照以下步骤定位并手动安装工具链：

1. _Go to the Installers Directory:_ Go to :yocto_dl:[/releases/yocto/yocto-&DISTRO;/toolchain/]{.title-ref}

> 去安装程序目录：转到：yocto_dl：[/releases/yocto/yocto-&DISTRO;/toolchain/]{.title-ref}

2. _Open the Folder for Your Build Host:_ Open the folder that matches your `Build Host`{.interpreted-text role="term"} (i.e. `i686` for 32-bit machines or `x86_64` for 64-bit machines).

> 打开与您的构建主机匹配的文件夹（例如 32 位机器的 `i686` 或 64 位机器的 `x86_64`）。

3. _Locate and Download the SDK Installer:_ You need to find and download the installer appropriate for your build host, target hardware, and image type.

> *3. 找到并下载 SDK 安装程序：*您需要找到并下载适合您的构建主机、目标硬件和映像类型的安装程序。

The installer files (`*.sh`) follow this naming convention:

> 安装程序文件（`*.sh`）遵循以下命名约定：

```

poky-glibc-host_system-core-image-type-arch-toolchain[-ext]-release.sh

> poky-glibc-主机系统-核心图像类型-架构-工具链[-扩展]-发布.sh


Where:

> 在哪里？
    host_system is a string representing your development system:
           "i686" or "x86_64"

    type is a string representing the image:
          "sato" or "minimal"

    arch is a string representing the target architecture:
           "aarch64", "armv5e", "core2-64", "cortexa8hf-neon", "i586", "mips32r2",
           "mips64", or "ppc7400"

    release is the version of Yocto Project.

    NOTE:
       The standard SDK installer does not have the "-ext" string as
       part of the filename.
```

The toolchains provided by the Yocto Project are based off of the `core-image-sato` and `core-image-minimal` images and contain libraries appropriate for developing against those images.

> 配备的工具链由 Yocto 项目提供，基于 `core-image-sato` 和 `core-image-minimal` 映像，并包含适合开发这些映像的库。

For example, if your build host is a 64-bit x86 system and you need an extended SDK for a 64-bit core2 target, go into the `x86_64` folder and download the following installer:

> 例如，如果您的构建主机是 64 位 x86 系统，并且您需要一个用于 64 位 core2 目标的扩展 SDK，请进入 `x86_64` 文件夹并下载以下安装程序：

```

poky-glibc-x86_64-core-image-sato-core2-64-toolchain-ext-&DISTRO;.sh

> poky-glibc-x86_64-core-image-sato-core2-64-toolchain-ext-&DISTRO;.sh（Poky-GLIBC-x86_64核心图像SATO Core2-64工具链EXT-&DISTRO;.sh）
```

4. _Run the Installer:_ Be sure you have execution privileges and run the installer. Following is an example from the `Downloads` directory:

> 4. *运行安装程序：*确保您具有执行权限，然后运行安装程序。以下是 `下载` 目录中的示例：

```

$ ~/Downloads/poky-glibc-x86_64-core-image-sato-core2-64-toolchain-ext-&DISTRO;.sh

> $ ~/下载/poky-glibc-x86_64-core-image-sato-core2-64-toolchain-ext-&DISTRO;.sh
```

During execution of the script, you choose the root location for the toolchain. See the \"`sdk-manual/appendix-obtain:installed standard sdk directory structure`{.interpreted-text role="ref"}\" section and the \"`sdk-manual/appendix-obtain:installed extensible sdk directory structure`{.interpreted-text role="ref"}\" section for more information.

> 在执行脚本期间，您可以选择工具链的根位置。有关更多信息，请参阅“ sdk-manual / appendix-obtain：安装标准 sdk 目录结构”部分和“ sdk-manual / appendix-obtain：安装可扩展 sdk 目录结构”部分。

## Building an SDK Installer

As an alternative to locating and downloading an SDK installer, you can build the SDK installer. Follow these steps:

> 作为寻找和下载 SDK 安装程序的替代方案，您可以构建 SDK 安装程序。按照以下步骤操作：

1. _Set Up the Build Environment:_ Be sure you are set up to use BitBake in a shell. See the \"`dev-manual/start:preparing the build host`{.interpreted-text role="ref"}\" section in the Yocto Project Development Tasks Manual for information on how to get a build host ready that is either a native Linux machine or a machine that uses CROPS.

> 1. *设置构建环境：*确保您已经准备好在 shell 中使用 BitBake。有关如何准备本地 Linux 机器或使用 CROPS 的机器的信息，请参阅 Yocto Project 开发任务手册中的“dev-manual/start：准备构建主机”部分。

2. _Clone the \`\`poky\`\` Repository:_ You need to have a local copy of the Yocto Project `Source Directory`{.interpreted-text role="term"} (i.e. a local `poky` repository). See the \"``dev-manual/start:cloning the \`\`poky\`\` repository``{.interpreted-text role="ref"}\" and possibly the \"`dev-manual/start:checking out by branch in poky`{.interpreted-text role="ref"}\" and \"`dev-manual/start:checking out by tag in poky`{.interpreted-text role="ref"}\" sections all in the Yocto Project Development Tasks Manual for information on how to clone the `poky` repository and check out the appropriate branch for your work.

> 2. _克隆 `poky` 仓库：_ 你需要有一个 Yocto 项目源码目录（即本地 `poky` 仓库）的本地副本。参见 Yocto 项目开发任务手册中的“克隆 `poky` 仓库”（dev-manual/start:cloning the `poky` repository）以及可能的“按分支在 poky 中检出”（dev-manual/start:checking out by branch in poky）和“按标签在 poky 中检出”（dev-manual/start:checking out by tag in poky）章节，了解如何克隆 `poky` 仓库并检出适合你工作的适当分支。

3. _Initialize the Build Environment:_ While in the root directory of the Source Directory (i.e. `poky`), run the `structure-core-script`{.interpreted-text role="ref"} environment setup script to define the OpenEmbedded build environment on your build host:

> *初始化构建环境：*在源目录（即 `poky`）的根目录下，运行 `structure-core-script`{.interpreted-text role="ref"}环境设置脚本，以在构建主机上定义 OpenEmbedded 构建环境。

```

$ source oe-init-build-env

> $ 源 oe-init-build-env
```

Among other things, the script creates the `Build Directory`{.interpreted-text role="term"}, which is `build` in this case and is located in the Source Directory. After the script runs, your current working directory is set to the `build` directory.

> 脚本还会创建 `构建目录`，在这种情况下，它的名称是 `build`，位于源目录中。脚本运行后，你的当前工作目录会被设置为 `build` 目录。

4. _Make Sure You Are Building an Installer for the Correct Machine:_ Check to be sure that your `MACHINE`{.interpreted-text role="term"} variable in the `local.conf` file in your `Build Directory`{.interpreted-text role="term"} matches the architecture for which you are building.

> 确保您正在为正确的机器构建安装程序：检查构建目录中 local.conf 文件中的 MACHINE 变量是否与您正在构建的架构匹配。

5. _Make Sure Your SDK Machine is Correctly Set:_ If you are building a toolchain designed to run on an architecture that differs from your current development host machine (i.e. the build host), be sure that the `SDKMACHINE`{.interpreted-text role="term"} variable in the `local.conf` file in your `Build Directory`{.interpreted-text role="term"} is correctly set.

> 确保你的 SDK 机器设置正确：如果您正在构建旨在运行在与当前开发主机机器（即构建主机）不同架构上的工具链，请确保在构建目录中的 local.conf 文件中的 SDKMACHINE 变量设置正确。

::: note
::: title

Note

> 注意
> :::

If you are building an SDK installer for the Extensible SDK, the `SDKMACHINE`{.interpreted-text role="term"} value must be set for the architecture of the machine you are using to build the installer. If `SDKMACHINE`{.interpreted-text role="term"} is not set appropriately, the build fails and provides an error message similar to the following:

> 如果您正在为可扩展 SDK 构建 SDK 安装程序，则必须为您用于构建安装程序的机器的架构设置 `SDKMACHINE`{.interpreted-text role="term"}值。如果 `SDKMACHINE`{.interpreted-text role="term"}没有正确设置，则构建将失败，并显示类似以下错误消息：

```

The extensible SDK can currently only be built for the same

> 可扩展的SDK目前只能为同一个构建

architecture as the machine being built on --- :term:`SDK_ARCH`

> 建筑作为机器建立在--- :term:`SDK_ARCH`上

is set to ``i686`` (likely via setting :term:`SDKMACHINE`) which is

> 被设置为“i686”（可能是通过设置:term:`SDKMACHINE`）

different from the architecture of the build machine (``x86_64``).

> 不同于构建机器（“x86_64”）的架构。
```

:::

6. _Build the SDK Installer:_ To build the SDK installer for a standard SDK and populate the SDK image, use the following command form. Be sure to replace `image` with an image (e.g. \"core-image-sato\"):

> 6. *构建 SDK 安装程序：*要构建标准 SDK 的 SDK 安装程序并填充 SDK 映像，请使用以下命令表单。请确保将 `image` 替换为图像（例如“core-image-sato”）：

```

$ bitbake image -c populate_sdk

> $ bitbake 影像 -c 填满 SDK
```

You can do the same for the extensible SDK using this command form:

> 你可以使用这个命令格式来为可扩展的 SDK 做同样的事情：

```

$ bitbake image -c populate_sdk_ext

> $ bitbake 图像 -c 填充 SDK 扩展
```

These commands produce an SDK installer that contains the sysroot that matches your target root filesystem.

> 这些命令生成一个 SDK 安装程序，其中包含与目标根文件系统匹配的 sysroot。

When the `bitbake` command completes, the SDK installer will be in `tmp/deploy/sdk` in the `Build Directory`{.interpreted-text role="term"}.

> 当 `bitbake` 命令完成时，SDK 安装程序将位于构建目录 `tmp/deploy/sdk` 中。

::: note
::: title

Note

> 注意
> :::

- By default, the previous BitBake command does not build static binaries. If you want to use the toolchain to build these types of libraries, you need to be sure your SDK has the appropriate static development libraries. Use the `TOOLCHAIN_TARGET_TASK`{.interpreted-text role="term"} variable inside your `local.conf` file before building the SDK installer. Doing so ensures that the eventual SDK installation process installs the appropriate library packages as part of the SDK. Following is an example using `libc` static development libraries: TOOLCHAIN_TARGET_TASK:append = \" libc-staticdev\"

> 默认情况下，之前的 BitBake 命令不会构建静态二进制文件。如果要使用工具链构建这些类型的库，您需要确保 SDK 具有适当的静态开发库。在构建 SDK 安装程序之前，请在 `local.conf` 文件中使用 `TOOLCHAIN_TARGET_TASK` 变量。这样可以确保最终的 SDK 安装过程将适当的库软件包作为 SDK 的一部分安装。以下是使用 `libc` 静态开发库的示例：TOOLCHAIN_TARGET_TASK：append =\" libc-staticdev\"
> :::

7. _Run the Installer:_ You can now run the SDK installer from `tmp/deploy/sdk` in the `Build Directory`{.interpreted-text role="term"}. Following is an example:

> 现在，您可以从 `Build Directory` 中的 `tmp/deploy/sdk` 处运行 SDK 安装程序。以下是一个示例：

```

$ cd poky/build/tmp/deploy/sdk

> $ cd poky/build/tmp/deploy/sdk（进入到poky/build/tmp/deploy/sdk目录）

$ ./poky-glibc-x86_64-core-image-sato-core2-64-toolchain-ext-&DISTRO;.sh

> $ ./poky-glibc-x86_64-core-image-sato-core2-64-toolchain-ext-&DISTRO;.sh

简体中文：$ ./poky-glibc-x86_64-core-image-sato-core2-64-toolchain-ext-&DISTRO;.sh
```

During execution of the script, you choose the root location for the toolchain. See the \"`sdk-manual/appendix-obtain:installed standard sdk directory structure`{.interpreted-text role="ref"}\" section and the \"`sdk-manual/appendix-obtain:installed extensible sdk directory structure`{.interpreted-text role="ref"}\" section for more information.

> 在执行脚本期间，您可以为工具链选择根位置。有关更多信息，请参阅“sdk-manual / appendix-obtain：已安装的标准 sdk 目录结构”部分和“sdk-manual / appendix-obtain：已安装的可扩展 sdk 目录结构”部分。

# Extracting the Root Filesystem

After installing the toolchain, for some use cases you might need to separately extract a root filesystem:

> 安装工具链后，对于某些用例，您可能需要单独提取根文件系统：

- You want to boot the image using NFS.
- You want to use the root filesystem as the target sysroot.
- You want to develop your target application using the root filesystem as the target sysroot.

Follow these steps to extract the root filesystem:

> 按照以下步骤提取根文件系统：

1. _Locate and Download the Tarball for the Pre-Built Root Filesystem Image File:_ You need to find and download the root filesystem image file that is appropriate for your target system. These files are kept in machine-specific folders in the :yocto_dl:[Index of Releases \</releases/yocto/yocto-&DISTRO;/machines/\>]{.title-ref} in the \"machines\" directory.

> 请找到并下载适合您的目标系统的根文件系统映像文件。这些文件都存储在:yocto_dl:[发布索引 \</releases/yocto/yocto-&DISTRO;/machines/\>]{.title-ref} 中的“machines”目录中的机器特定文件夹中。

The machine-specific folders of the \"machines\" directory contain tarballs (`*.tar.bz2`) for supported machines. These directories also contain flattened root filesystem image files (`*.ext4`), which you can use with QEMU directly.

> "machines" 目录的机器特定文件夹包含支持的机器的 tarballs（`*.tar.bz2`）。这些目录还包含压缩的根文件系统映像文件（`*.ext4`），可以直接使用 QEMU。

The pre-built root filesystem image files follow these naming conventions:

> 预构建的根文件系统映像文件遵循以下命名约定：

```

core-image-profile-arch.tar.bz2

> 核心图像配置文件-arch.tar.bz2


Where:

> 在哪里？
    profile is the filesystem image's profile:
              lsb, lsb-dev, lsb-sdk, minimal, minimal-dev, minimal-initramfs,
              sato, sato-dev, sato-sdk, sato-sdk-ptest. For information on
              these types of image profiles, see the "Images" chapter in
              the Yocto Project Reference Manual.

    arch is a string representing the target architecture:
              beaglebone-yocto, beaglebone-yocto-lsb, edgerouter, edgerouter-lsb,
              genericx86, genericx86-64, genericx86-64-lsb, genericx86-lsb and qemu*.
```

The root filesystems provided by the Yocto Project are based off of the `core-image-sato` and `core-image-minimal` images.

> 项目 Yocto 提供的根文件系统基于 `core-image-sato` 和 `core-image-minimal` 映像。

For example, if you plan on using a BeagleBone device as your target hardware and your image is a `core-image-sato-sdk` image, you can download the following file:

> 例如，如果您计划使用 BeagleBone 设备作为目标硬件，而您的映像是 `core-image-sato-sdk` 映像，您可以下载以下文件：

```

core-image-sato-sdk-beaglebone-yocto.tar.bz2

> 核心图像SATO SDK Beaglebone Yocto.tar.bz2
```

2. _Initialize the Cross-Development Environment:_ You must `source` the cross-development environment setup script to establish necessary environment variables.

> 2. *初始化交叉开发环境：*您必须运行 `source` 命令来加载交叉开发环境设置脚本，以建立必要的环境变量。

This script is located in the top-level directory in which you installed the toolchain (e.g. `poky_sdk`).

> 这个脚本位于您安装工具链的顶级目录（例如 `poky_sdk`）中。

Following is an example based on the toolchain installed in the \"`sdk-manual/appendix-obtain:locating pre-built sdk installers`{.interpreted-text role="ref"}\" section:

> 以下是根据“sdk-manual / appendix-obtain：定位预构建的 sdk 安装程序”部分安装的工具链的示例：

```

$ source poky_sdk/environment-setup-core2-64-poky-linux

> $ 源 poky_sdk/environment-setup-core2-64-poky-linux
```

3. _Extract the Root Filesystem:_ Use the `runqemu-extract-sdk` command and provide the root filesystem image.

> 使用 `runqemu-extract-sdk` 命令，提供根文件系统映像。

Following is an example command that extracts the root filesystem from a previously built root filesystem image that was downloaded from the :yocto_dl:[Index of Releases \</releases/yocto/yocto-&DISTRO;/machines/\>]{.title-ref}. This command extracts the root filesystem into the `core2-64-sato` directory:

> 以下是一个示例命令，用于从 :yocto_dl:[发行版索引 \</releases/yocto/yocto-&DISTRO;/machines/\>]{.title-ref} 中下载的先前构建的根文件系统映像中提取根文件系统。该命令将根文件系统提取到 `core2-64-sato` 目录：

```

$ runqemu-extract-sdk ~/Downloads/core-image-sato-sdk-beaglebone-yocto.tar.bz2 ~/beaglebone-sato

> 运行qemu-extract-sdk从~/Downloads/core-image-sato-sdk-beaglebone-yocto.tar.bz2提取到~/beaglebone-sato
```

You could now point to the target sysroot at `beaglebone-sato`.

> 你现在可以指向目标系统根目录 'beaglebone-sato' 了。

# Installed Standard SDK Directory Structure

The following figure shows the resulting directory structure after you install the Standard SDK by running the `*.sh` SDK installation script:

> 下图显示了在运行 `*.sh` SDK 安装脚本后的安装结果目录结构：

![image](figures/sdk-installed-standard-sdk-directory.png)

The installed SDK consists of an environment setup script for the SDK, a configuration file for the target, a version file for the target, and the root filesystem (`sysroots`) needed to develop objects for the target system.

> 已安装的 SDK 包括 SDK 的环境设置脚本、目标的配置文件、目标的版本文件以及为目标系统开发对象所需的根文件系统（`sysroots`）。

Within the figure, italicized text is used to indicate replaceable portions of the file or directory name. For example, install_dir/version is the directory where the SDK is installed. By default, this directory is `/opt/poky/`. And, version represents the specific snapshot of the SDK (e.g. &DISTRO;). Furthermore, target represents the target architecture (e.g. `i586`) and host represents the development system\'s architecture (e.g. `x86_64`). Thus, the complete names of the two directories within the `sysroots` could be `i586-poky-linux` and `x86_64-pokysdk-linux` for the target and host, respectively.

> 在图中，斜体文本用于表示文件或目录名称中可替换的部分。例如，install_dir/version 是 SDK 安装的目录。默认情况下，此目录为 `/opt/poky/`。而 version 表示 SDK 的特定快照（例如 &DISTRO;）。此外，target 表示目标架构（例如 `i586`），而 host 表示开发系统的架构（例如 `x86_64`）。因此，`sysroots` 中的两个目录的完整名称可以分别为 `i586-poky-linux` 和 `x86_64-pokysdk-linux`，用于目标和主机。

# Installed Extensible SDK Directory Structure

The following figure shows the resulting directory structure after you install the Extensible SDK by running the `*.sh` SDK installation script:

> 以下图示显示了运行 `*.sh` SDK 安装脚本后的目录结构结果：

![image](figures/sdk-installed-extensible-sdk-directory.png){.align-center}

The installed directory structure for the extensible SDK is quite different than the installed structure for the standard SDK. The extensible SDK does not separate host and target parts in the same manner as does the standard SDK. The extensible SDK uses an embedded copy of the OpenEmbedded build system, which has its own sysroots.

> 安装的可扩展 SDK 的目录结构与标准 SDK 安装的结构有很大的不同。可扩展 SDK 不像标准 SDK 那样将主机和目标部分分开。可扩展 SDK 使用一个嵌入式 OpenEmbedded 构建系统的副本，它有自己的 sysroots。

Of note in the directory structure are an environment setup script for the SDK, a configuration file for the target, a version file for the target, and log files for the OpenEmbedded build system preparation script run by the installer and BitBake.

> 在目录结构中值得注意的是 SDK 的环境设置脚本、目标的配置文件、目标的版本文件以及安装程序和 BitBake 运行的 OpenEmbedded 构建系统准备脚本的日志文件。

Within the figure, italicized text is used to indicate replaceable portions of the file or directory name. For example, install_dir is the directory where the SDK is installed, which is `poky_sdk` by default, and target represents the target architecture (e.g. `i586`).

> 在图中，斜体文本用于表示文件或目录名称的可替换部分。例如，install_dir 是 SDK 安装的目录，默认为'poky_sdk'，target 表示目标架构（例如 `i586`）。
