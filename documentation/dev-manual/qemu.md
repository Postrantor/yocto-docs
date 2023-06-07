---
tip: translate by baidu@2023-06-07 17:15:31
...
---
title: Using the Quick EMUlator (QEMU)
--------------------------------------

The Yocto Project uses an implementation of the Quick EMUlator (QEMU) Open Source project as part of the Yocto Project development \"tool set\". This chapter provides both procedures that show you how to use the Quick EMUlator (QEMU) and other QEMU information helpful for development purposes.

> Yocto 项目使用 Quick EMUlator（QEMU）开源项目的实现，作为 Yocto 开发“工具集”的一部分。本章提供了两个过程，向您展示如何使用 Quick EMUlator（QEMU）和其他有助于开发目的的 QEMU 信息。

# Overview

Within the context of the Yocto Project, QEMU is an emulator and virtualization machine that allows you to run a complete image you have built using the Yocto Project as just another task on your build system. QEMU is useful for running and testing images and applications on supported Yocto Project architectures without having actual hardware. Among other things, the Yocto Project uses QEMU to run automated Quality Assurance (QA) tests on final images shipped with each release.

> 在 Yocto 项目的上下文中，QEMU 是一个模拟器和虚拟化机器，它允许您将使用 Yocto Project 构建的完整映像作为构建系统上的另一项任务来运行。QEMU 对于在没有实际硬件的情况下在支持的 Yocto 项目架构上运行和测试图像和应用程序非常有用。除其他外，Yocto 项目使用 QEMU 对每个版本附带的最终图像进行自动质量保证（QA）测试。

::: note
::: title
Note
:::

This implementation is not the same as QEMU in general.

> 这种实现与一般的 QEMU 不同。
> :::

This section provides a brief reference for the Yocto Project implementation of QEMU.

> 本节为 QEMU 的 Yocto 项目实施提供了简要参考。

For official information and documentation on QEMU in general, see the following references:

> 有关 QEMU 的官方信息和文件，请参阅以下参考文献：

- [QEMU Website](https://wiki.qemu.org/Main_Page)*:* The official website for the QEMU Open Source project.
- [Documentation](https://wiki.qemu.org/Manual)*:* The QEMU user manual.

# Running QEMU

To use QEMU, you need to have QEMU installed and initialized as well as have the proper artifacts (i.e. image files and root filesystems) available. Follow these general steps to run QEMU:

> 要使用 QEMU，您需要安装和初始化 QEMU，并提供适当的工件（即映像文件和根文件系统）。按照以下一般步骤运行 QEMU：

1. *Install QEMU:* QEMU is made available with the Yocto Project a number of ways. One method is to install a Software Development Kit (SDK). See \"`sdk-manual/intro:the qemu emulator`{.interpreted-text role="ref"}\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual for information on how to install QEMU.

> 1.*安装 QEMU：*Yocto 项目通过多种方式提供了 QEMU。一种方法是安装软件开发工具包（SDK）。有关如何安装 qemu 的信息，请参阅 Yocto 项目应用程序开发和可扩展软件开发工具包（eSDK）手册中的“` sdk manual/intro:the qemu emulator`｛.depreced text role=“ref”｝\”一节。

2. *Setting Up the Environment:* How you set up the QEMU environment depends on how you installed QEMU:

> 2.*设置环境：*如何设置 QEMU 环境取决于您如何安装 QEMU：

- If you cloned the `poky` repository or you downloaded and unpacked a Yocto Project release tarball, you can source the build environment script (i.e. `structure-core-script`{.interpreted-text role="ref"}):

> -如果您克隆了“poky”存储库，或者下载并解包了 Yocto Project 发行版 tarball，则可以获取构建环境脚本（即“structure core script”｛.depreted text role=“ref”｝）：

```
 ```
 $ cd poky
 $ source oe-init-build-env
 ```
```

- If you installed a cross-toolchain, you can run the script that initializes the toolchain. For example, the following commands run the initialization script from the default `poky_sdk` directory:

> -如果安装了跨工具链，则可以运行初始化工具链的脚本。例如，以下命令从默认的“poky_sdk”目录运行初始化脚本：

```
 ```
 . poky_sdk/environment-setup-core2-64-poky-linux
 ```
```

3. *Ensure the Artifacts are in Place:* You need to be sure you have a pre-built kernel that will boot in QEMU. You also need the target root filesystem for your target machine\'s architecture:

> 3.*确保工件到位：*您需要确保您有一个预构建的内核，它将在 QEMU 中启动。您还需要目标机器体系结构的目标根文件系统：

- If you have previously built an image for QEMU (e.g. `qemux86`, `qemuarm`, and so forth), then the artifacts are in place in your `Build Directory`{.interpreted-text role="term"}.

> -如果您之前已经为 QEMU 构建了一个映像（例如“qemux86”、“qemuarm”等），那么工件就在您的“构建目录”｛.depreced text role=“term”｝中。

- If you have not built an image, you can go to the :yocto_dl:[machines/qemu \</releases/yocto/yocto-&DISTRO;/machines/qemu/\>]{.title-ref} area and download a pre-built image that matches your architecture and can be run on QEMU.

> -如果您还没有构建映像，您可以转到：yocto_dl:[machines/qemu\</releases/yocto/yocto-&DISTRO；/machines/qemu/\>]｛.title-ref｝区域，下载一个与您的体系结构匹配的预构建映像，该映像可以在 qemu 上运行。

See the \"`sdk-manual/appendix-obtain:extracting the root filesystem`{.interpreted-text role="ref"}\" section in the Yocto Project Application Development and the Extensible Software Development Kit (eSDK) manual for information on how to extract a root filesystem.

> 有关如何提取根文件系统的信息，请参阅 Yocto 项目应用程序开发和可扩展软件开发工具包（eSDK）手册中的\“`sdk manual/appendment get:extracting the root filesystem `｛.depredicted text role=“ref”｝\”一节。

4. *Run QEMU:* The basic `runqemu` command syntax is as follows:

> 4.*运行 QEMU:*“runqemu”命令的基本语法如下：

```

> ```

$ runqemu [option ] [...]

> $runqemu[选项][…]

```

> ```
> ```

Based on what you provide on the command line, `runqemu` does a good job of figuring out what you are trying to do. For example, by default, QEMU looks for the most recently built image according to the timestamp when it needs to look for an image. Minimally, through the use of options, you must provide either a machine name, a virtual machine image (`*wic.vmdk`), or a kernel image (`*.bin`).

> 根据您在命令行上提供的内容，“runqemu”可以很好地弄清楚您要做什么。例如，默认情况下，QEMU 在需要查找图像时，会根据时间戳查找最近构建的图像。至少，通过使用选项，您必须提供机器名称、虚拟机映像（`*wic.vmdk`）或内核映像（`*.bin`）。

Here are some additional examples to help illustrate further QEMU:

> 以下是一些额外的示例，有助于进一步说明 QEMU：

- This example starts QEMU with MACHINE set to \"qemux86-64\". Assuming a standard `Build Directory`{.interpreted-text role="term"}, `runqemu` automatically finds the `bzImage-qemux86-64.bin` image file and the `core-image-minimal-qemux86-64-20200218002850.rootfs.ext4` (assuming the current build created a `core-image-minimal` image):

> -本例在 MACHINE 设置为“qemux86-64\”的情况下启动 QEMU。假设标准的 `Build Directory`｛.explored text role=“term”｝，`runqemu` 会自动找到 `bzImage-qemux86-64.bin` 图像文件和 `core-image-minimal-qemu x86-64-20200218002850.rootfs.ext4`（假设当前构建创建了 `core-image minimu` 图像）：

```
 ```
 $ runqemu qemux86-64
 ```

 ::: note
 ::: title
 Note
 :::

 When more than one image with the same name exists, QEMU finds and uses the most recently built image according to the timestamp.
 :::
```

- This example produces the exact same results as the previous example. This command, however, specifically provides the image and root filesystem type:

> -此示例产生与上一示例完全相同的结果。但是，此命令专门提供映像和根文件系统类型：

```
 ```
 $ runqemu qemux86-64 core-image-minimal ext4
 ```
```

- This example specifies to boot an `Initramfs`{.interpreted-text role="term"} image and to enable audio in QEMU. For this case, `runqemu` sets the internal variable `FSTYPE` to `cpio.gz`. Also, for audio to be enabled, an appropriate driver must be installed (see the `audio` option in ``dev-manual/qemu:\`\`runqemu\`\` command-line options``{.interpreted-text role="ref"} for more information):

> -此示例指定启动 `Initramfs`｛.explored text role=“term”｝映像并在 QEMU 中启用音频。在这种情况下，“runqemu”将内部变量“FSTYPE”设置为“cpio.gz”。此外，要启用音频，必须安装适当的驱动程序（有关详细信息，请参阅“dev manual/qemu:\”\`runqemu\`\`命令行选项 ``｛.explored text role=“ref”｝中的“audio”选项）：

```
 ```
 $ runqemu qemux86-64 ramfs audio
 ```
```

- This example does not provide enough information for QEMU to launch. While the command does provide a root filesystem type, it must also minimally provide a [MACHINE]{.title-ref}, [KERNEL]{.title-ref}, or [VM]{.title-ref} option:

> -这个例子没有为 QEMU 的启动提供足够的信息。虽然该命令确实提供了根文件系统类型，但它还必须至少提供[MACHINE]｛.title-ref｝、[KERNEL]｛.title-ref｝或[VM]｛\title-rev｝选项：

```
 ```
 $ runqemu ext4
 ```
```

- This example specifies to boot a virtual machine image (`.wic.vmdk` file). From the `.wic.vmdk`, `runqemu` determines the QEMU architecture ([MACHINE]{.title-ref}) to be \"qemux86-64\" and the root filesystem type to be \"vmdk\":

> -此示例指定启动虚拟机映像（`.wic.vmdk` 文件）。根据 `.wic.vmdk`，`runqemu` 确定 QEMU 体系结构（[MACHINE]｛.title-ref｝）为\“qemux86-64\”，根文件系统类型为\“vmdk\”：

```
 ```
 $ runqemu /home/scott-lenovo/vm/core-image-minimal-qemux86-64.wic.vmdk
 ```
```

# Switching Between Consoles

When booting or running QEMU, you can switch between supported consoles by using Ctrl+Alt+number. For example, Ctrl+Alt+3 switches you to the serial console as long as that console is enabled. Being able to switch consoles is helpful, for example, if the main QEMU console breaks for some reason.

> 启动或运行 QEMU 时，可以使用 Ctrl+Alt+number 在支持的控制台之间切换。例如，只要串行控制台处于启用状态，Ctrl+Alt+3 即可将您切换到串行控制台。例如，如果主 QEMU 控制台由于某种原因而中断，那么能够切换控制台是有帮助的。

::: note
::: title
Note
:::

Usually, \"2\" gets you to the main console and \"3\" gets you to the serial console.

> 通常情况下，“2”会将您带到主控制台，“3”会将你带到串行控制台。
> :::

# Removing the Splash Screen

You can remove the splash screen when QEMU is booting by using Alt+left. Removing the splash screen allows you to see what is happening in the background.

> 当 QEMU 启动时，您可以使用 Alt+left 移除启动屏幕。移除启动屏幕可以让您看到背景中发生了什么。

# Disabling the Cursor Grab

The default QEMU integration captures the cursor within the main window. It does this since standard mouse devices only provide relative input and not absolute coordinates. You then have to break out of the grab using the \"Ctrl+Alt\" key combination. However, the Yocto Project\'s integration of QEMU enables the wacom USB touch pad driver by default to allow input of absolute coordinates. This default means that the mouse can enter and leave the main window without the grab taking effect leading to a better user experience.

> 默认的 QEMU 集成捕获主窗口中的光标。它这样做是因为标准鼠标设备只提供相对输入，而不提供绝对坐标。然后，您必须使用\“Ctrl+Alt+”组合键脱离抓取。然而，Yocto 项目对 QEMU 的集成使 wacom USB 触摸板驱动程序默认允许输入绝对坐标。这个默认值意味着鼠标可以进入和离开主窗口，而抓取不会生效，从而获得更好的用户体验。

# Running Under a Network File System (NFS) Server

One method for running QEMU is to run it on an NFS server. This is useful when you need to access the same file system from both the build and the emulated system at the same time. It is also worth noting that the system does not need root privileges to run. It uses a user space NFS server to avoid that. Follow these steps to set up for running QEMU using an NFS server.

> 运行 QEMU 的一种方法是在 NFS 服务器上运行它。当您需要同时从构建系统和模拟系统访问同一个文件系统时，这很有用。同样值得注意的是，系统不需要 root 权限即可运行。它使用用户空间 NFS 服务器来避免这种情况。按照以下步骤设置使用 NFS 服务器运行 QEMU。

1. *Extract a Root Filesystem:* Once you are able to run QEMU in your environment, you can use the `runqemu-extract-sdk` script, which is located in the `scripts` directory along with the `runqemu` script.

> 1.*提取根文件系统：*一旦您能够在环境中运行 QEMU，您就可以使用“runqemu Extract-sdk”脚本，该脚本与“runqemo”脚本一起位于“scripts”目录中。

The `runqemu-extract-sdk` takes a root filesystem tarball and extracts it into a location that you specify. Here is an example that takes a file system and extracts it to a directory named `test-nfs`:

> “runqemu extract-sdk”获取根文件系统 tarball，并将其提取到您指定的位置。下面是一个示例，它采用一个文件系统并将其提取到名为“test-nfs”的目录中：

```none

> ```无

runqemu-extract-sdk ./tmp/deploy/images/qemux86-64/core-image-sato-qemux86-64.tar.bz2 test-nfs

> runqemu提取sdk/tmp/deploy/images/qemux86-64/core-image-sa-qemux86-64.tar.bz2测试nfs

```

> ```
> ```

2. *Start QEMU:* Once you have extracted the file system, you can run `runqemu` normally with the additional location of the file system. You can then also make changes to the files within `./test-nfs` and see those changes appear in the image in real time. Here is an example using the `qemux86` image:

> 2.*启动 QEMU:*一旦提取了文件系统，就可以使用文件系统的附加位置正常运行“runqemu”。然后，您还可以对 ` 中的文件进行更改/测试 nfs，并实时查看这些更改显示在映像中。以下是使用“qemux86”图像的示例：

```none

> ```无

runqemu qemux86-64 ./test-nfs

> 运行qemu qemu x86-64/测试nfs

```

> ```
> ```

::: note
::: title
Note
:::

Should you need to start, stop, or restart the NFS share, you can use the following commands:

> 如果需要启动、停止或重新启动 NFS 共享，可以使用以下命令：

- To start the NFS share:

  ```
  runqemu-export-rootfs start file-system-location
  ```
- To stop the NFS share:

  ```
  runqemu-export-rootfs stop file-system-location
  ```
- To restart the NFS share:

  ```
  runqemu-export-rootfs restart file-system-location
  ```

:::

# QEMU CPU Compatibility Under KVM

By default, the QEMU build compiles for and targets 64-bit and x86 Intel Core2 Duo processors and 32-bit x86 Intel Pentium II processors. QEMU builds for and targets these CPU types because they display a broad range of CPU feature compatibility with many commonly used CPUs.

> 默认情况下，QEMU 内部版本编译 64 位和 x86 Intel Core2 Duo 处理器以及 32 位 x86 Intel Pentium II 处理器，并以它们为目标。QEMU 为这些 CPU 类型构建并以它们为目标，因为它们与许多常用的 CPU 具有广泛的 CPU 功能兼容性。

Despite this broad range of compatibility, the CPUs could support a feature that your host CPU does not support. Although this situation is not a problem when QEMU uses software emulation of the feature, it can be a problem when QEMU is running with KVM enabled. Specifically, software compiled with a certain CPU feature crashes when run on a CPU under KVM that does not support that feature. To work around this problem, you can override QEMU\'s runtime CPU setting by changing the `QB_CPU_KVM` variable in `qemuboot.conf` in the `Build Directory`{.interpreted-text role="term"} `deploy/image` directory. This setting specifies a `-cpu` option passed into QEMU in the `runqemu` script. Running `qemu -cpu help` returns a list of available supported CPU types.

> 尽管有广泛的兼容性，但 CPU 可以支持主机 CPU 不支持的功能。虽然当 QEMU 使用该功能的软件仿真时，这种情况不是问题，但当 QEMU 在启用 KVM 的情况下运行时，可能会出现问题。具体来说，使用特定 CPU 功能编译的软件在 KVM 下的 CPU 上运行时会崩溃，而该 CPU 不支持该功能。要解决此问题，您可以通过更改“构建目录”｛.depreted text role=“term”｝“deploy/image”目录中的“qemuboot.conf”中的“QB_CPU_KVM”变量来覆盖 QEMU\的运行时 CPU 设置。此设置指定在“runqemu”脚本中传递给 QEMU 的“-cpu”选项。运行“qemu-cpu help”返回可用的受支持 cpu 类型的列表。

# QEMU Performance

Using QEMU to emulate your hardware can result in speed issues depending on the target and host architecture mix. For example, using the `qemux86` image in the emulator on an Intel-based 32-bit (x86) host machine is fast because the target and host architectures match. On the other hand, using the `qemuarm` image on the same Intel-based host can be slower. But, you still achieve faithful emulation of ARM-specific issues.

> 根据目标和主机体系结构的混合，使用 QEMU 来模拟硬件可能会导致速度问题。例如，在基于英特尔的 32 位（x86）主机上的模拟器中使用“qemux86”映像速度很快，因为目标和主机架构匹配。另一方面，在同一台基于英特尔的主机上使用“qemuarm”映像可能会更慢。但是，您仍然可以忠实地模拟 ARM 特定的问题。

To speed things up, the QEMU images support using `distcc` to call a cross-compiler outside the emulated system. If you used `runqemu` to start QEMU, and the `distccd` application is present on the host system, any BitBake cross-compiling toolchain available from the build system is automatically used from within QEMU simply by calling `distcc`. You can accomplish this by defining the cross-compiler variable (e.g. `export CC="distcc"`). Alternatively, if you are using a suitable SDK image or the appropriate stand-alone toolchain is present, the toolchain is also automatically used.

> 为了加快速度，QEMU 镜像支持使用“distcc”在模拟系统之外调用交叉编译器。如果您使用“runqemu”来启动 QEMU，并且主机系统上存在“distccd”应用程序，那么构建系统中可用的任何 BitBake 交叉编译工具链都可以通过调用“distcc”在 QEMU 中自动使用。您可以通过定义交叉编译器变量（例如 `export CC=“distcc”`）来实现这一点。或者，如果您正在使用合适的 SDK 映像或存在合适的独立工具链，则也会自动使用该工具链。

::: note
::: title
Note
:::

There are several mechanisms to connect to the system running on the QEMU emulator:

> 有几种机制可以连接到运行在 QEMU 模拟器上的系统：

- QEMU provides a framebuffer interface that makes standard consoles available.
- Generally, headless embedded devices have a serial port. If so, you can configure the operating system of the running image to use that port to run a console. The connection uses standard IP networking.

> -通常，无头嵌入式设备有一个串行端口。如果有，您可以配置运行映像的操作系统使用该端口运行控制台。连接使用标准 IP 网络。

- SSH servers are available in some QEMU images. The `core-image-sato` QEMU image has a Dropbear secure shell (SSH) server that runs with the root password disabled. The `core-image-full-cmdline` and `core-image-lsb` QEMU images have OpenSSH instead of Dropbear. Including these SSH servers allow you to use standard `ssh` and `scp` commands. The `core-image-minimal` QEMU image, however, contains no SSH server.

> -SSH 服务器在一些 QEMU 映像中可用。“核心镜像 sato”QEMU 镜像有一个 Dropbear 安全外壳（SSH）服务器，该服务器在禁用根密码的情况下运行。“核心图像完整 cmdline”和“核心图像 lsb”QEMU 图像具有 OpenSSH 而不是 Dropbear。包括这些 SSH 服务器允许您使用标准的“SSH”和“scp”命令。然而，“核心镜像最小”QEMU 镜像不包含 SSH 服务器。

- You can use a provided, user-space NFS server to boot the QEMU session using a local copy of the root filesystem on the host. In order to make this connection, you must extract a root filesystem tarball by using the `runqemu-extract-sdk` command. After running the command, you must then point the `runqemu` script to the extracted directory instead of a root filesystem image file. See the \"`dev-manual/qemu:running under a network file system (nfs) server`{.interpreted-text role="ref"}\" section for more information.

> -您可以使用提供的用户空间 NFS 服务器，使用主机上根文件系统的本地副本来启动 QEMU 会话。为了建立这种连接，必须使用“runqemu extract-sdk”命令提取根文件系统 tarball。运行该命令后，必须将“runqemu”脚本指向提取的目录，而不是根文件系统映像文件。有关详细信息，请参阅\“`devmanual/qemu:在网络文件系统（nfs）服务器下运行`{.depreted text role=“ref”}\”一节。
> :::

# QEMU Command-Line Syntax

The basic `runqemu` command syntax is as follows:

> “runqemu”命令的基本语法如下：

```
$ runqemu [option ] [...]
```

Based on what you provide on the command line, `runqemu` does a good job of figuring out what you are trying to do. For example, by default, QEMU looks for the most recently built image according to the timestamp when it needs to look for an image. Minimally, through the use of options, you must provide either a machine name, a virtual machine image (`*wic.vmdk`), or a kernel image (`*.bin`).

> 根据您在命令行上提供的内容，“runqemu”可以很好地弄清楚您要做什么。例如，默认情况下，QEMU 在需要查找图像时，会根据时间戳查找最近构建的图像。至少，通过使用选项，您必须提供机器名称、虚拟机映像（`*wic.vmdk`）或内核映像（`*.bin`）。

Following is the command-line help output for the `runqemu` command:

> 以下是“runqemu”命令的命令行帮助输出：

```
$ runqemu --help

Usage: you can run this script with any valid combination
of the following environment variables (in any order):
  KERNEL - the kernel image file to use
  ROOTFS - the rootfs image file or nfsroot directory to use
  MACHINE - the machine name (optional, autodetected from KERNEL filename if unspecified)
  Simplified QEMU command-line options can be passed with:
    nographic - disable video console
    serial - enable a serial console on /dev/ttyS0
    slirp - enable user networking, no root privileges required
    kvm - enable KVM when running x86/x86_64 (VT-capable CPU required)
    kvm-vhost - enable KVM with vhost when running x86/x86_64 (VT-capable CPU required)
    publicvnc - enable a VNC server open to all hosts
    audio - enable audio
    [*/]ovmf* - OVMF firmware file or base name for booting with UEFI
  tcpserial=<port> - specify tcp serial port number
  biosdir=<dir> - specify custom bios dir
  biosfilename=<filename> - specify bios filename
  qemuparams=<xyz> - specify custom parameters to QEMU
  bootparams=<xyz> - specify custom kernel parameters during boot
  help, -h, --help: print this text

Examples:
  runqemu
  runqemu qemuarm
  runqemu tmp/deploy/images/qemuarm
  runqemu tmp/deploy/images/qemux86/<qemuboot.conf>
  runqemu qemux86-64 core-image-sato ext4
  runqemu qemux86-64 wic-image-minimal wic
  runqemu path/to/bzImage-qemux86.bin path/to/nfsrootdir/ serial
  runqemu qemux86 iso/hddimg/wic.vmdk/wic.qcow2/wic.vdi/ramfs/cpio.gz...
  runqemu qemux86 qemuparams="-m 256"
  runqemu qemux86 bootparams="psplash=false"
  runqemu path/to/<image>-<machine>.wic
  runqemu path/to/<image>-<machine>.wic.vmdk
```

# `runqemu` Command-Line Options

Following is a description of `runqemu` options you can provide on the command line:

> 以下是您可以在命令行上提供的“runqemu”选项的说明：

::: note
::: title
Note
:::

If you do provide some \"illegal\" option combination or perhaps you do not provide enough in the way of options, `runqemu` provides appropriate error messaging to help you correct the problem.

> 如果您确实提供了一些“非法”选项组合，或者您可能没有提供足够的选项，“runqemu”会提供适当的错误消息来帮助您更正问题。
> :::

- \`QEMUARCH\`: The QEMU machine architecture, which must be \"qemuarm\", \"qemuarm64\", \"qemumips\", \"qemumips64\", \"qemuppc\", \"qemux86\", or \"qemux86-64\".

> -\`QEMUARCH\`：QEMU 机器体系结构，必须是\“qemuarm\”、\“qemcuarm64\”、“qemumips\”、\“qemuips64\”，\“qemoppc\”、\n“qemux86\”或\“qemex86-64\”。

- \`VM\`: The virtual machine image, which must be a `.wic.vmdk` file. Use this option when you want to boot a `.wic.vmdk` image. The image filename you provide must contain one of the following strings: \"qemux86-64\", \"qemux86\", \"qemuarm\", \"qemumips64\", \"qemumips\", \"qemuppc\", or \"qemush4\".

> -\`VM\`：虚拟机映像，它必须是 `.wic.vmdk` 文件。如果要启动 `.wic.vmdk` 映像，请使用此选项。您提供的图像文件名必须包含以下字符串之一：\“qemux86-64\”、\“qemox86\”、\“qemuarm\”、“qemuips64\”、\n“qemumips\”、\nqemuppc\”或\“qemsh4\”。

- \`ROOTFS\`: A root filesystem that has one of the following filetype extensions: \"ext2\", \"ext3\", \"ext4\", \"jffs2\", \"nfs\", or \"btrfs\". If the filename you provide for this option uses \"nfs\", it must provide an explicit root filesystem path.

> -\`ROOTFS\`：具有以下文件类型扩展名之一的根文件系统：\“ext2\”、\“ext3\”、\”ext4\“、\”jffs2\“、\”nfs\“或\”btrfs\“。如果为该选项提供的文件名使用\“nfs\”，则它必须提供一个明确的根文件系统路径。

- \`KERNEL\`: A kernel image, which is a `.bin` file. When you provide a `.bin` file, `runqemu` detects it and assumes the file is a kernel image.

> -\`KERNEL\`：内核映像，它是一个 `.bin` 文件。当您提供一个“.bin”文件时，“runqemu”会检测到它，并假定该文件是内核映像。

- \`MACHINE\`: The architecture of the QEMU machine, which must be one of the following: \"qemux86\", \"qemux86-64\", \"qemuarm\", \"qemuarm64\", \"qemumips\", \"qemumips64\", or \"qemuppc\". The MACHINE and QEMUARCH options are basically identical. If you do not provide a MACHINE option, `runqemu` tries to determine it based on other options.

> -\`MACHINE\`：QEMU 机器的体系结构，必须是以下其中之一：\“qemux86\”、\“qemox86-64\”、“qemuarm\”、\“qemumarm64\”、\n“qemumips\”、\t“qemuips64\”或\“qemeppc\”。MACHINE 和 QEMUARCH 选项基本相同。如果不提供 MACHINE 选项，“runqemu”会尝试根据其他选项来确定它。

- `ramfs`: Indicates you are booting an `Initramfs`{.interpreted-text role="term"} image, which means the `FSTYPE` is `cpio.gz`.

> -`ramfs`：表示您正在启动 `Initramfs`｛.explored text role=“term”｝映像，这意味着 `FSTYPE` 是 `cpio.gz`。

- `iso`: Indicates you are booting an ISO image, which means the `FSTYPE` is `.iso`.
- `nographic`: Disables the video console, which sets the console to \"ttys0\". This option is useful when you have logged into a server and you do not want to disable forwarding from the X Window System (X11) to your workstation or laptop.

> -`nographic`：禁用视频控制台，将控制台设置为“ttys0\”。当您登录到服务器并且不想禁用从 X Window System（X11）到工作站或笔记本电脑的转发时，此选项非常有用。

- `serial`: Enables a serial console on `/dev/ttyS0`.
- `biosdir`: Establishes a custom directory for BIOS, VGA BIOS and keymaps.
- `biosfilename`: Establishes a custom BIOS name.
- `qemuparams=\"xyz\"`: Specifies custom QEMU parameters. Use this option to pass options other than the simple \"kvm\" and \"serial\" options.

> -`qemuparams=\“xyz\”`：指定自定义 QEMU 参数。使用此选项可以传递除简单的“kvm”和“serial”选项之外的其他选项。

- `bootparams=\"xyz\"`: Specifies custom boot parameters for the kernel.
- `audio`: Enables audio in QEMU. The MACHINE option must be either \"qemux86\" or \"qemux86-64\" in order for audio to be enabled. Additionally, the `snd_intel8x0` or `snd_ens1370` driver must be installed in linux guest.

> -`audio`：在 QEMU 中启用音频。MACHINE 选项必须是“qemux86\”或“qemu x86-64\”才能启用音频。此外，“snd_intel8x0”或“snd_ens1370”驱动程序必须安装在 linux guest 中。

- `slirp`: Enables \"slirp\" networking, which is a different way of networking that does not need root access but also is not as easy to use or comprehensive as the default.

> -`slirp`：启用\“slirp\”联网，这是一种不同的联网方式，不需要 root 访问权限，但也不像默认情况那样易于使用或全面。

Using `slirp` by default will forward the guest machine\'s 22 and 23 TCP ports to host machine\'s 2222 and 2323 ports (or the next free ports). Specific forwarding rules can be configured by setting `QB_SLIRP_OPT` as environment variable or in `qemuboot.conf` in the `Build Directory`{.interpreted-text role="term"} `deploy/image` directory. Examples:

> 默认情况下使用“slirp”将把客户机的 22 和 23 个 TCP 端口转发到主机的 2222 和 2323 个端口（或下一个空闲端口）。可以通过将 `QB_SLIRP_OPT` 设置为环境变量或在 `Build Directory`｛.depreted text role=“term”｝`deploy/image` 目录中的 `qemuboot.conf` 中配置特定转发规则。示例：

```
QB_SLIRP_OPT="-netdev user,id=net0,hostfwd=tcp::8080-:80"

QB_SLIRP_OPT="-netdev user,id=net0,hostfwd=tcp::8080-:80,hostfwd=tcp::2222-:22"
```

The first example forwards TCP port 80 from the emulated system to port 8080 (or the next free port) on the host system, allowing access to an http server running in QEMU from `http://<host ip>:8080/`.

> 第一个示例将 TCP 端口 80 从模拟系统转发到主机系统上的端口 8080（或下一个空闲端口），允许从“http://<host-ip>:8080/”访问运行在 QEMU 中的 http 服务器。

The second example does the same, but also forwards TCP port 22 on the guest system to 2222 (or the next free port) on the host system, allowing ssh access to the emulated system using `ssh -P 2222 <user>@<host ip>`.

> 第二个例子也做了同样的事情，但也将客户系统上的 TCP 端口 22 转发到主机系统上的 2222（或下一个空闲端口），允许 ssh 使用“ssh-P 2222<user>@<host-ip>”访问模拟系统。

Keep in mind that proper configuration of firewall software is required.

> 请记住，需要正确配置防火墙软件。

- `kvm`: Enables KVM when running \"qemux86\" or \"qemux86-64\" QEMU architectures. For KVM to work, all the following conditions must be met:

> -`kvm`：在运行\“qemux86\”或\“qemox86-64\”QEMU 体系结构时启用 kvm。KVM 要工作，必须满足以下所有条件：

- Your MACHINE must be either qemux86\" or \"qemux86-64\".
- Your build host has to have the KVM modules installed, which are `/dev/kvm`.
- The build host `/dev/kvm` directory has to be both writable and readable.
- `kvm-vhost`: Enables KVM with VHOST support when running \"qemux86\" or \"qemux86-64\" QEMU architectures. For KVM with VHOST to work, the following conditions must be met:

> -`kvm-vhost`：在运行\“qemux86\”或\“qemox86-64\”QEMU 体系结构时，启用支持 vhost 的 kvm。要使带 VHOST 的 KVM 工作，必须满足以下条件：

- `kvm` option conditions defined above must be met.
- Your build host has to have virtio net device, which are `/dev/vhost-net`.
- The build host `/dev/vhost-net` directory has to be either readable or writable and \"slirp-enabled\".

> -生成主机“/dev/vhost-net'”目录必须可读或可写，并且“slirp enabled”。

- `publicvnc`: Enables a VNC server open to all hosts.
