---
tip: translate by openai@2023-06-07 22:08:23
...
---
title: Images
-------------

The OpenEmbedded build system provides several example images to satisfy different needs. When you issue the `bitbake` command you provide a \"top-level\" recipe that essentially begins the build for the type of image you want.

> 开放嵌入式构建系统提供了几个示例镜像来满足不同的需求。当您发出 `bitbake` 命令时，您提供了一个“顶级”配方，它基本上开始了您想要的镜像的构建。

::: note
::: title
Note
:::

Building an image without GNU General Public License Version 3 (GPLv3), GNU Lesser General Public License Version 3 (LGPLv3), and the GNU Affero General Public License Version 3 (AGPL-3.0) components is only supported for minimal and base images. Furthermore, if you are going to build an image using non-GPLv3 and similarly licensed components, you must make the following changes in the `local.conf` file before using the BitBake command to build the minimal or base image:

> 在没有 GNU 通用公共许可证版本 3(GPLv3)，GNU 小通用公共许可证版本 3(LGPLv3)和 GNU Affero 通用公共许可证版本 3(AGPL-3.0)组件的情况下构建镜像仅支持最小和基本镜像。此外，如果要使用非 GPLv3 和类似许可的组件构建镜像，在使用 BitBake 命令构建最小或基本镜像之前，必须在 `local.conf` 文件中做出以下更改：

1. Comment out the `EXTRA_IMAGE_FEATURES` line
2. Set `INCOMPATIBLE_LICENSE` to \"GPL-3.0\* LGPL-3.0\* AGPL-3.0\*\"
   :::

From within the `poky` Git repository, you can use the following command to display the list of directories within the `Source Directory` that contain image recipe files:

> 从 `poky` Git 存储库中，您可以使用以下命令来显示包含镜像配方文件的 `源目录` 中的目录列表：

```
$ ls meta*/recipes*/images/*.bb
```

Following is a list of supported recipes:

- `build-appliance-image`: An example virtual machine that contains all the pieces required to run builds using the build system as well as the build system itself. You can boot and run the image using either the [VMware Player](https://www.vmware.com/products/player/overview.html) or [VMware Workstation](https://www.vmware.com/products/workstation/overview.html). For more information on this image, see the :yocto_home:[Build Appliance \</software-item/build-appliance\>] page on the Yocto Project website.

> 创建应用程序映像：一个示例虚拟机，其中包含使用构建系统运行构建所需的所有部分以及构建系统本身。您可以使用 [VMware Player](https://www.vmware.com/products/player/overview.html) 或 [VMware Workstation](https://www.vmware.com/products/workstation/overview.html) 引导和运行该映像。有关此映像的更多信息，请参阅 Yocto 项目网站上的:yocto_home:[构建应用程序\</software-item/build-appliance\>]页面。

- `core-image-base`: A console-only image that fully supports the target device hardware.
- `core-image-full-cmdline`: A console-only image with more full-featured Linux system functionality installed.
- `core-image-lsb`: An image that conforms to the Linux Standard Base (LSB) specification. This image requires a distribution configuration that enables LSB compliance (e.g. `poky-lsb`). If you build `core-image-lsb` without that configuration, the image will not be LSB-compliant.

> `core-image-lsb`：符合 Linux 标准基础(LSB)规范的镜像。此镜像需要启用 LSB 兼容性的发行配置(例如 `poky-lsb`)。如果没有该配置就构建 `core-image-lsb`，该镜像将不兼容 LSB。

- `core-image-lsb-dev`: A `core-image-lsb` image that is suitable for development work using the host. The image includes headers and libraries you can use in a host development environment. This image requires a distribution configuration that enables LSB compliance (e.g. `poky-lsb`). If you build `core-image-lsb-dev` without that configuration, the image will not be LSB-compliant.

> `core-image-lsb-dev`：适用于使用主机进行开发工作的 `core-image-lsb` 映像。该映像包含可在主机开发环境中使用的头文件和库。此映像需要启用 LSB 兼容性的分发配置(例如 `poky-lsb`)。如果您没有使用该配置构建 `core-image-lsb-dev`，则该映像将不兼容 LSB。

- `core-image-lsb-sdk`: A `core-image-lsb` that includes everything in the cross-toolchain but also includes development headers and libraries to form a complete standalone SDK. This image requires a distribution configuration that enables LSB compliance (e.g. `poky-lsb`). If you build `core-image-lsb-sdk` without that configuration, the image will not be LSB-compliant. This image is suitable for development using the target.

> core-image-lsb-sdk：包含跨工具链中的所有内容，但也包括开发头文件和库以形成完整的独立 SDK 的 core-image-lsb。此镜像需要启用 LSB 兼容性(例如 poky-lsb)的分发配置。如果您没有使用该配置构建 core-image-lsb-sdk，则镜像将不符合 LSB 标准。此镜像适用于使用目标进行开发。

- `core-image-minimal`: A small image just capable of allowing a device to boot.
- `core-image-minimal-dev`: A `core-image-minimal` image suitable for development work using the host. The image includes headers and libraries you can use in a host development environment.

> `core-image-minimal-dev`：适用于使用主机进行开发工作的 `core-image-minimal` 映像。该映像包括可在主机开发环境中使用的头文件和库。

- `core-image-minimal-initramfs`: A `core-image-minimal` image that has the Minimal RAM-based Initial Root Filesystem (`Initramfs` images.

> core-image-minimal-initramfs：一个具有最小 RAM 基础初始根文件系统(Initramfs)作为内核的一部分的 core-image-minimal 镜像，这使得系统更有效地找到第一个“init”程序。有关额外有用信息，请参见 PACKAGE_INSTALL 变量，这有助于使用 Initramfs 镜像。

- `core-image-minimal-mtdutils`: A `core-image-minimal` image that has support for the Minimal MTD Utilities, which let the user interact with the MTD subsystem in the kernel to perform operations on flash devices.

> `核心镜像最小-mtdutils`：一个拥有最小 MTD Utilities 支持的 `核心镜像最小` 镜像，让用户可以与内核中的 MTD 子系统进行交互，以执行闪存设备上的操作。

- `core-image-rt`: A `core-image-minimal` image plus a real-time test suite and tools appropriate for real-time use.
- `core-image-rt-sdk`: A `core-image-rt` image that includes everything in the cross-toolchain. The image also includes development headers and libraries to form a complete stand-alone SDK and is suitable for development using the target.

> `core-image-rt-sdk`：一个包含跨工具链中所有内容的 `core-image-rt` 映像。该映像还包括开发头文件和库，形成一个完整的独立 SDK，适用于使用目标进行开发。

- `core-image-sato`: An image with Sato support, a mobile environment and visual style that works well with mobile devices. The image supports X11 with a Sato theme and applications such as a terminal, editor, file manager, media player, and so forth.

> - `核心镜像Sato`：一个支持 Sato 的镜像，一个与移动设备很好地工作的移动环境和视觉样式。该镜像支持带有 Sato 主题的 X11，以及终端、编辑器、文件管理器、媒体播放器等应用程序。

- `core-image-sato-dev`: A `core-image-sato` image suitable for development using the host. The image includes libraries needed to build applications on the device itself, testing and profiling tools, and debug symbols. This image was formerly `core-image-sdk`.

> `core-image-sato-dev`：一个适合在主机上开发的 `core-image-sato` 映像。该映像包括在设备本身构建应用程序所需的库、测试和分析工具以及调试符号。这个映像以前叫做 `core-image-sdk`。

- `core-image-sato-sdk`: A `core-image-sato` image that includes everything in the cross-toolchain. The image also includes development headers and libraries to form a complete standalone SDK and is suitable for development using the target.

> `core-image-sato-sdk`：一个包含跨工具链中所有内容的 `core-image-sato` 镜像。该镜像还包括开发头文件和库，构成完整的独立 SDK，适用于使用目标进行开发。

- `core-image-testmaster`: A \"controller\" image designed to be used for automated runtime testing. Provides a \"known good\" image that is deployed to a separate partition so that you can boot into it and use it to deploy a second image to be tested. You can find more information about runtime testing in the \"`dev-manual/runtime-testing:performing automated runtime testing`\" section in the Yocto Project Development Tasks Manual.

> - `core-image-testmaster`：一种用于自动运行时测试的“控制器”镜像。提供一个“已知良好”的镜像，它部署到一个单独的分区，以便您可以引导进入并使用它来部署要测试的第二个镜像。有关运行时测试的更多信息，请参见 Yocto 项目开发任务手册中的“dev-manual/runtime-testing：执行自动运行时测试”部分。

- `core-image-testmaster-initramfs`: A RAM-based Initial Root Filesystem (`Initramfs`) image tailored for use with the `core-image-testmaster` image.

> 一个针对 `core-image-testmaster` 镜像设计的基于 RAM 的初始根文件系统(Initramfs)镜像。

- `core-image-weston`: A very basic Wayland image with a terminal. This image provides the Wayland protocol libraries and the reference Weston compositor. For more information, see the \"`dev-manual/wayland:using wayland and weston`\" section in the Yocto Project Development Tasks Manual.

> `-core-image-weston`：一个非常基础的带有终端的 Wayland 镜像。该镜像提供 Wayland 协议库和参考 Weston 组合器。有关更多信息，请参阅 Yocto 项目开发任务手册中的“dev-manual / wayland：使用 Wayland 和 Weston”部分。

- `core-image-x11`: A very basic X11 image with a terminal.
