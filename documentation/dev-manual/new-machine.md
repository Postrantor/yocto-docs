---
tip: translate by baidu@2023-06-07 17:13:27
...
---
title: Adding a New Machine
---------------------------

Adding a new machine to the Yocto Project is a straightforward process. This section describes how to add machines that are similar to those that the Yocto Project already supports.

> 在 Yocto 项目中添加一台新机器是一个简单的过程。本节介绍如何添加与 Yocto 项目已经支持的机器类似的机器。

::: note
::: title
Note
:::

Although well within the capabilities of the Yocto Project, adding a totally new architecture might require changes to `gcc`/`glibc` and to the site information, which is beyond the scope of this manual.

> 尽管完全在 Yocto 项目的能力范围内，但添加一个全新的架构可能需要更改“gcc”/“glibc”和站点信息，这超出了本手册的范围。
> :::

For a complete example that shows how to add a new machine, see the \"``bsp-guide/bsp:creating a new bsp layer using the \`\`bitbake-layers\`\` script``{.interpreted-text role="ref"}\" section in the Yocto Project Board Support Package (BSP) Developer\'s Guide.

> 有关如何添加新机器的完整示例，请参阅 Yocto Project Board Support Package（bsp）Developer\’s guide 中的“``nbsp guide/nbsp：使用\`\`bitbake layers\`\`script``｛.depreted text role=”ref“｝”一节创建新的 bsp 层。

# Adding the Machine Configuration File

To add a new machine, you need to add a new machine configuration file to the layer\'s `conf/machine` directory. This configuration file provides details about the device you are adding.

> 要添加一台新机器，您需要将一个新的机器配置文件添加到层的“conf/machine”目录中。此配置文件提供有关要添加的设备的详细信息。

The OpenEmbedded build system uses the root name of the machine configuration file to reference the new machine. For example, given a machine configuration file named `crownbay.conf`, the build system recognizes the machine as \"crownbay\".

> OpenEmbedded 构建系统使用机器配置文件的根名称来引用新机器。例如，给定一个名为“crownbay.conf”的机器配置文件，构建系统会将该机器识别为“crown bay”。

The most important variables you must set in your machine configuration file or include from a lower-level configuration file are as follows:

> 您必须在机器配置文件中设置或从较低级别的配置文件中包括的最重要的变量如下：

- `TARGET_ARCH`{.interpreted-text role="term"} (e.g. \"arm\")
- `PREFERRED_PROVIDER_virtual/kernel`
- `MACHINE_FEATURES`{.interpreted-text role="term"} (e.g. \"apm screen wifi\")

You might also need these variables:

> 您可能还需要这些变量：

- `SERIAL_CONSOLES`{.interpreted-text role="term"} (e.g. \"115200;ttyS0 115200;ttyS1\")
- `KERNEL_IMAGETYPE`{.interpreted-text role="term"} (e.g. \"zImage\")
- `IMAGE_FSTYPES`{.interpreted-text role="term"} (e.g. \"tar.gz jffs2\")

You can find full details on these variables in the reference section. You can leverage existing machine `.conf` files from `meta-yocto-bsp/conf/machine/`.

> 您可以在参考资料部分找到这些变量的全部详细信息。您可以利用“meta yocto nbsp/conf/machine/”中的现有计算机“.conf”文件。

# Adding a Kernel for the Machine

The OpenEmbedded build system needs to be able to build a kernel for the machine. You need to either create a new kernel recipe for this machine, or extend an existing kernel recipe. You can find several kernel recipe examples in the Source Directory at `meta/recipes-kernel/linux` that you can use as references.

> OpenEmbedded 构建系统需要能够为机器构建内核。您需要为这台机器创建一个新的内核配方，或者扩展现有的内核配方。您可以在源目录中的“meta/precipes kernel/linux”中找到几个内核配方示例，这些示例可以用作参考。

If you are creating a new kernel recipe, normal recipe-writing rules apply for setting up a `SRC_URI`{.interpreted-text role="term"}. Thus, you need to specify any necessary patches and set `S`{.interpreted-text role="term"} to point at the source code. You need to create a `ref-tasks-configure`{.interpreted-text role="ref"} task that configures the unpacked kernel with a `defconfig` file. You can do this by using a `make defconfig` command or, more commonly, by copying in a suitable `defconfig` file and then running `make oldconfig`. By making use of `inherit kernel` and potentially some of the `linux-*.inc` files, most other functionality is centralized and the defaults of the class normally work well.

> 如果您正在创建一个新的内核配方，则通常的配方编写规则适用于设置 `SRC_URI`｛.explored text role=“term”｝。因此，您需要指定任何必要的修补程序，并将 `S`{.depreted text role=“term”}设置为指向源代码。您需要创建一个 `ref tasks configure`｛.explored text role=“ref”｝任务，该任务使用 `defconfig` 文件配置解压缩的内核。您可以使用“make-defconfig”命令，或者更常见的方法是，复制一个合适的“defconfig”文件，然后运行“make-oldconfig”。通过使用“inherit kernel”和一些可能的“linux-*.inc”文件，大多数其他功能都是集中化的，类的默认值正常工作。

If you are extending an existing kernel recipe, it is usually a matter of adding a suitable `defconfig` file. The file needs to be added into a location similar to `defconfig` files used for other machines in a given kernel recipe. A possible way to do this is by listing the file in the `SRC_URI`{.interpreted-text role="term"} and adding the machine to the expression in `COMPATIBLE_MACHINE`{.interpreted-text role="term"}:

> 如果要扩展现有的内核配方，通常需要添加一个合适的“defconfig”文件。该文件需要添加到一个类似于给定内核配方中其他机器使用的“defconfig”文件的位置。实现这一点的一种可能方法是在 `SRC_URI`｛.depreted text role=“term”｝中列出文件，并将机器添加到 `COMPATIBLE_machine`｛.repreted text role=”term“｝中的表达式中：

```
COMPATIBLE_MACHINE = '(qemux86|qemumips)'
```

For more information on `defconfig` files, see the \"`kernel-dev/common:changing the configuration`{.interpreted-text role="ref"}\" section in the Yocto Project Linux Kernel Development Manual.

> 有关 `defconfig` 文件的更多信息，请参阅 Yocto Project Linux 内核开发手册中的\“`kernel dev/common:更改配置`{.depreted text role=“ref”}\”一节。

# Adding a Formfactor Configuration File

A formfactor configuration file provides information about the target hardware for which the image is being built and information that the build system cannot obtain from other sources such as the kernel. Some examples of information contained in a formfactor configuration file include framebuffer orientation, whether or not the system has a keyboard, the positioning of the keyboard in relation to the screen, and the screen resolution.

> 形状因子配置文件提供了关于正在为其构建映像的目标硬件的信息，以及构建系统无法从诸如内核之类的其他源获得的信息。形状因子配置文件中包含的信息的一些示例包括帧缓冲区方向、系统是否具有键盘、键盘相对于屏幕的位置以及屏幕分辨率。

The build system uses reasonable defaults in most cases. However, if customization is necessary, you need to create a `machconfig` file in the `meta/recipes-bsp/formfactor/files` directory. This directory contains directories for specific machines such as `qemuarm` and `qemux86`. For information about the settings available and the defaults, see the `meta/recipes-bsp/formfactor/files/config` file found in the same area.

> 在大多数情况下，构建系统使用合理的默认值。但是，如果需要自定义，则需要在“meta/precipes nbsp/formfactor/filess”目录中创建一个“machconfig”文件。此目录包含特定机器的目录，如“qemuarm”和“qemux86”。有关可用设置和默认设置的信息，请参阅同一区域中的“meta/recipes nbsp/formfactor/files/config”文件。

Following is an example for \"qemuarm\" machine:

> 以下是“qemuarm”机器的示例：

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
