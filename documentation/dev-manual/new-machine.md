---
tip: translate by openai@2023-06-10 11:15:18
...
---
title: Adding a New Machine
---------------------------

Adding a new machine to the Yocto Project is a straightforward process. This section describes how to add machines that are similar to those that the Yocto Project already supports.

> 加入一台新机器到 Yocto 项目是一个简单的过程。本节描述了如何添加类似于 Yocto 项目已经支持的机器。

::: note
::: title
Note
:::

Although well within the capabilities of the Yocto Project, adding a totally new architecture might require changes to `gcc`/`glibc` and to the site information, which is beyond the scope of this manual.

> 尽管属于 Yocto Project 的能力范围，但添加一个全新的架构可能需要对 gcc/glibc 和站点信息进行更改，这超出了本手册的范围。
> :::

For a complete example that shows how to add a new machine, see the \"``bsp-guide/bsp:creating a new bsp layer using the \`\`bitbake-layers\`\` script``\" section in the Yocto Project Board Support Package (BSP) Developer\'s Guide.

> 对于展示如何添加新机器的完整示例，请参阅 Yocto 项目板支持包(BSP)开发者指南中的“使用 `bitbake-layers` 脚本创建新的 BSP 层”部分。

# Adding the Machine Configuration File

To add a new machine, you need to add a new machine configuration file to the layer\'s `conf/machine` directory. This configuration file provides details about the device you are adding.

> 要添加一台新机器，你需要在层的 `conf/machine` 目录中添加一个新的机器配置文件。这个配置文件提供你要添加的设备的详细信息。

The OpenEmbedded build system uses the root name of the machine configuration file to reference the new machine. For example, given a machine configuration file named `crownbay.conf`, the build system recognizes the machine as \"crownbay\".

> 系统构建系统使用机器配置文件的根名称来引用新机器。例如，给定一个名为“crownbay.conf”的机器配置文件，构建系统将其识别为“crownbay”。

The most important variables you must set in your machine configuration file or include from a lower-level configuration file are as follows:

- `TARGET_ARCH` (e.g. \"arm\")
- `PREFERRED_PROVIDER_virtual/kernel`
- `MACHINE_FEATURES` (e.g. \"apm screen wifi\")

You might also need these variables:

- `SERIAL_CONSOLES` (e.g. \"115200;ttyS0 115200;ttyS1\")
- `KERNEL_IMAGETYPE` (e.g. \"zImage\")
- `IMAGE_FSTYPES` (e.g. \"tar.gz jffs2\")

You can find full details on these variables in the reference section. You can leverage existing machine `.conf` files from `meta-yocto-bsp/conf/machine/`.

> 你可以在参考部分找到有关这些变量的详细信息。您可以从'meta-yocto-bsp / conf / machine /'中利用现有的机器 `。conf` 文件。

# Adding a Kernel for the Machine

The OpenEmbedded build system needs to be able to build a kernel for the machine. You need to either create a new kernel recipe for this machine, or extend an existing kernel recipe. You can find several kernel recipe examples in the Source Directory at `meta/recipes-kernel/linux` that you can use as references.

> 开放嵌入式构建系统需要能够为机器构建内核。您需要为此机器创建一个新的内核配方，或扩展现有的内核配方。您可以在源目录中的 `meta/recipes-kernel/linux` 中找到几个内核配方示例，可以用作参考。

If you are creating a new kernel recipe, normal recipe-writing rules apply for setting up a `SRC_URI` task that configures the unpacked kernel with a `defconfig` file. You can do this by using a `make defconfig` command or, more commonly, by copying in a suitable `defconfig` file and then running `make oldconfig`. By making use of `inherit kernel` and potentially some of the `linux-*.inc` files, most other functionality is centralized and the defaults of the class normally work well.

> 如果您正在创建一个新的内核配方，则需要为设置 `SRC_URI` 任务来使用 `defconfig` 文件配置解压缩的内核。您可以通过使用 `make defconfig` 命令来完成此操作，或者更常见的是，通过复制适当的 `defconfig` 文件，然后运行 `make oldconfig`。通过使用 `inherit kernel` 以及部分 `linux-*.inc` 文件，大多数其他功能都是集中在一起的，类的默认值通常可以很好地工作。

If you are extending an existing kernel recipe, it is usually a matter of adding a suitable `defconfig` file. The file needs to be added into a location similar to `defconfig` files used for other machines in a given kernel recipe. A possible way to do this is by listing the file in the `SRC_URI`:

> 如果您正在扩展现有的内核配方，通常需要添加一个合适的 `defconfig` 文件。该文件需要添加到与给定内核配方中用于其他机器的 `defconfig` 文件相似的位置。可以通过在 `SRC_URI` 表达式中来实现：

```
COMPATIBLE_MACHINE = '(qemux86|qemumips)'
```

For more information on `defconfig` files, see the \"`kernel-dev/common:changing the configuration`\" section in the Yocto Project Linux Kernel Development Manual.

> 对于 `defconfig` 文件的更多信息，请参阅 Yocto Project Linux 内核开发手册中的“kernel-dev/common：更改配置”部分。

# Adding a Formfactor Configuration File

A formfactor configuration file provides information about the target hardware for which the image is being built and information that the build system cannot obtain from other sources such as the kernel. Some examples of information contained in a formfactor configuration file include framebuffer orientation, whether or not the system has a keyboard, the positioning of the keyboard in relation to the screen, and the screen resolution.

> 一个形式因素配置文件为正在构建的映像提供有关目标硬件的信息以及构建系统无法从其他来源(如内核)获得的信息。形式因素配置文件中包含的一些信息的例子包括帧缓冲器方向，系统是否具有键盘，键盘与屏幕之间的位置关系以及屏幕分辨率。

The build system uses reasonable defaults in most cases. However, if customization is necessary, you need to create a `machconfig` file in the `meta/recipes-bsp/formfactor/files` directory. This directory contains directories for specific machines such as `qemuarm` and `qemux86`. For information about the settings available and the defaults, see the `meta/recipes-bsp/formfactor/files/config` file found in the same area.

> 系统构建在大多数情况下都使用合理的默认值。但是，如果需要定制，您需要在 `meta/recipes-bsp/formfactor/files` 目录中创建一个 `machconfig` 文件。该目录包含特定机器(如 `qemuarm` 和 `qemux86`)的目录。有关可用设置和默认值的信息，请参阅同一区域中的 `meta/recipes-bsp/formfactor/files/config` 文件。

Following is an example for \"qemuarm\" machine:

```
HAVE_TOUCHSCREEN=1
HAVE_KEYBOARD=1
DISPLAY_CAN_ROTATE=0
DISPLAY_ORIENTATION=0
#DISPLAY_WIDTH_PIXELS=640
#DISPLAY_HEIGHT_PIXELS=480
#DISPLAY_BPP=16
DISPLAY_DPI=150
DISPLAY_SUBPIXEL_ORDER=vrgb
```
