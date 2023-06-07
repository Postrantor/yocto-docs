---
tip: translate by baidu@2023-06-07 17:18:37
...
---
title: Creating Partitioned Images Using Wic
--------------------------------------------

Creating an image for a particular hardware target using the OpenEmbedded build system does not necessarily mean you can boot that image as is on your device. Physical devices accept and boot images in various ways depending on the specifics of the device. Usually, information about the hardware can tell you what image format the device requires. Should your device require multiple partitions on an SD card, flash, or an HDD, you can use the OpenEmbedded Image Creator, Wic, to create the properly partitioned image.

> 使用 OpenEmbedded 构建系统为特定硬件目标创建映像并不一定意味着您可以在设备上按原样启动该映像。物理设备根据设备的具体情况以各种方式接受和引导映像。通常，有关硬件的信息可以告诉您设备需要什么图像格式。如果您的设备需要 SD 卡、闪存或 HDD 上的多个分区，您可以使用 OpenEmbedded Image Creator Wic 创建正确分区的图像。

The `wic` command generates partitioned images from existing OpenEmbedded build artifacts. Image generation is driven by partitioning commands contained in an OpenEmbedded kickstart file (`.wks`) specified either directly on the command line or as one of a selection of canned kickstart files as shown with the `wic list images` command in the \"`dev-manual/wic:generate an image using an existing kickstart file`{.interpreted-text role="ref"}\" section. When you apply the command to a given set of build artifacts, the result is an image or set of images that can be directly written onto media and used on a particular system.

> “wic”命令从现有的 OpenEmbedded 构建工件生成分区图像。图像生成是由包含在 OpenEmbedded 启动文件（`.wks`）中的分区命令驱动的，该文件直接在命令行上指定，或者作为一组选定的启动文件之一指定，如“dev manual/wic:使用现有启动文件生成图像”部分中的“wic list images”命令所示。当您将该命令应用于给定的构建工件集时，结果是可以直接写入介质并在特定系统上使用的图像或图像集。

::: note
::: title
Note
:::

For a kickstart file reference, see the \"``ref-manual/kickstart:openembedded kickstart (\`\`.wks\`\`) reference``{.interpreted-text role="ref"}\" Chapter in the Yocto Project Reference Manual.

> 有关 kickstart 文件参考，请参阅 Yocto 项目参考手册中的“``ref manual/kickstart:openembedded kickstart（\`\`.wks\`\`）reference``｛.depreted text role=”ref“｝”一章。
> :::

The `wic` command and the infrastructure it is based on is by definition incomplete. The purpose of the command is to allow the generation of customized images, and as such, was designed to be completely extensible through a plugin interface. See the \"`dev-manual/wic:using the wic plugin interface`{.interpreted-text role="ref"}\" section for information on these plugins.

> 根据定义，“wic”命令及其所基于的基础设施是不完整的。该命令的目的是允许生成自定义图像，因此，它被设计为通过插件接口完全可扩展。有关这些插件的信息，请参阅\“`devmanual/wic:using the wic plugin interface`｛.explored text role=“ref”｝\”一节。

This section provides some background information on Wic, describes what you need to have in place to run the tool, provides instruction on how to use the Wic utility, provides information on using the Wic plugins interface, and provides several examples that show how to use Wic.

> 本节提供了有关 Wic 的一些背景信息，描述了运行该工具所需的内容，提供了如何使用 Wic 实用程序的说明，提供了有关使用 Wic 插件界面的信息，并提供了几个示例来展示如何使用 Wic。

# Background

This section provides some background on the Wic utility. While none of this information is required to use Wic, you might find it interesting.

> 本节提供 Wic 实用程序的一些背景知识。虽然使用 Wic 不需要这些信息，但你可能会觉得很有趣。

- The name \"Wic\" is derived from OpenEmbedded Image Creator (oeic). The \"oe\" diphthong in \"oeic\" was promoted to the letter \"w\", because \"oeic\" is both difficult to remember and to pronounce.

> -“Wic”这个名称来源于 OpenEmbedded Image Creator（oeic）。“oeic\”中的“oe\”双元音被提升为字母“w”，因为“oeic”既难以记忆又难以发音。

- Wic is loosely based on the Meego Image Creator (`mic`) framework. The Wic implementation has been heavily modified to make direct use of OpenEmbedded build artifacts instead of package installation and configuration, which are already incorporated within the OpenEmbedded artifacts.

> -Wic 松散地基于 Meego Image Creator（“mic”）框架。Wic 实现经过了大量修改，直接使用 OpenEmbedded 构建工件，而不是包安装和配置，这些已经包含在 OpenEmbedded 工件中。

- Wic is a completely independent standalone utility that initially provides easier-to-use and more flexible replacements for an existing functionality in OE-Core\'s `ref-classes-image-live`{.interpreted-text role="ref"} class. The difference between Wic and those examples is that with Wic the functionality of those scripts is implemented by a general-purpose partitioning language, which is based on Redhat kickstart syntax.

> -Wic 是一个完全独立的实用程序，最初为 OE Core 的“ref classes image live”类中的现有功能提供更易于使用和更灵活的替代。Wic 和这些例子之间的区别在于，使用 Wic，这些脚本的功能是由基于 Redhat kickstart 语法的通用分区语言实现的。

# Requirements

In order to use the Wic utility with the OpenEmbedded Build system, your system needs to meet the following requirements:

> 为了在 OpenEmbedded Build 系统中使用 Wic 实用程序，您的系统需要满足以下要求：

- The Linux distribution on your development host must support the Yocto Project. See the \"`system-requirements-supported-distros`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual for the list of distributions that support the Yocto Project.

> -开发主机上的 Linux 发行版必须支持 Yocto 项目。有关支持 Yocto 项目的发行版列表，请参阅 Yocto Project Reference Manual 中的\“`系统需求支持的发行版`｛.explored text role=”ref“｝\”一节。

- The standard system utilities, such as `cp`, must be installed on your development host system.
- You must have sourced the build environment setup script (i.e. `structure-core-script`{.interpreted-text role="ref"}) found in the `Build Directory`{.interpreted-text role="term"}.

> -您必须来源于在 `build Directory`｛.depreted text role=“term”｝中找到的生成环境设置脚本（即 `structure core script`｛.repreted text role=“ref”｝）。

- You need to have the build artifacts already available, which typically means that you must have already created an image using the OpenEmbedded build system (e.g. `core-image-minimal`). While it might seem redundant to generate an image in order to create an image using Wic, the current version of Wic requires the artifacts in the form generated by the OpenEmbedded build system.

> -您需要已经有可用的构建工件，这通常意味着您必须已经使用 OpenEmbedded 构建系统创建了映像（例如“核心映像最小值”）。虽然为了使用 Wic 创建图像而生成图像似乎是多余的，但当前版本的 Wic 需要 OpenEmbedded 构建系统生成的形式的工件。

- You must build several native tools, which are built to run on the build system:

  ```
  $ bitbake parted-native dosfstools-native mtools-native
  ```
- Include \"wic\" as part of the `IMAGE_FSTYPES`{.interpreted-text role="term"} variable.
- Include the name of the `wic kickstart file <openembedded-kickstart-wks-reference>`{.interpreted-text role="ref"} as part of the `WKS_FILE`{.interpreted-text role="term"} variable. If multiple candidate files can be provided by different layers, specify all the possible names through the `WKS_FILES`{.interpreted-text role="term"} variable instead.

> -将 `wic kickstart file<openembedded kickstart-wks-reference>`{.depreced text role=“ref”}的名称作为 `wks_file`{.depreded text role=“term”}变量的一部分。如果不同的层可以提供多个候选文件，请改为通过 `WKS_files`{.depreted text role=“term”}变量指定所有可能的名称。

# Getting Help

You can get general help for the `wic` command by entering the `wic` command by itself or by entering the command with a help argument as follows:

> 您可以通过单独输入“wic”命令或通过以下方式输入带有帮助参数的命令来获得“wic”命令的常规帮助：

```
$ wic -h
$ wic --help
$ wic help
```

Currently, Wic supports seven commands: `cp`, `create`, `help`, `list`, `ls`, `rm`, and `write`. You can get help for all these commands except \"help\" by using the following form:

> 目前，Wic 支持七个命令：“cp”、“create”、“help”、“list”、“ls”、“rm”和“write”。您可以使用以下表单获得除“help”之外的所有这些命令的帮助：

```
$ wic help command
```

For example, the following command returns help for the `write` command:

> 例如，以下命令返回“write”命令的帮助：

```
$ wic help write
```

Wic supports help for three topics: `overview`, `plugins`, and `kickstart`. You can get help for any topic using the following form:

> Wic 支持三个主题的帮助：“概述”、“插件”和“启动”。您可以使用以下表格获得任何主题的帮助：

```
$ wic help topic
```

For example, the following returns overview help for Wic:

> 例如，以下返回 Wic 的概述帮助：

```
$ wic help overview
```

There is one additional level of help for Wic. You can get help on individual images through the `list` command. You can use the `list` command to return the available Wic images as follows:

> Wic 还有一个额外的帮助级别。您可以通过“list”命令获得有关单个图像的帮助。您可以使用“list”命令返回可用的 Wic 图像，如下所示：

```
$ wic list images
  genericx86                         Create an EFI disk image for genericx86*
  edgerouter                         Create SD card image for Edgerouter
  beaglebone-yocto                   Create SD card image for Beaglebone
  qemux86-directdisk                 Create a qemu machine 'pcbios' direct disk image
  systemd-bootdisk                   Create an EFI disk image with systemd-boot
  mkhybridiso                        Create a hybrid ISO image
  mkefidisk                          Create an EFI disk image
  sdimage-bootpart                   Create SD card image with a boot partition
  directdisk-multi-rootfs            Create multi rootfs image using rootfs plugin
  directdisk                         Create a 'pcbios' direct disk image
  directdisk-bootloader-config       Create a 'pcbios' direct disk image with custom bootloader config
  qemuriscv                          Create qcow2 image for RISC-V QEMU machines
  directdisk-gpt                     Create a 'pcbios' direct disk image
  efi-bootdisk
```

Once you know the list of available Wic images, you can use `help` with the command to get help on a particular image. For example, the following command returns help on the \"beaglebone-yocto\" image:

> 一旦你知道了可用 Wic 图像的列表，你就可以在命令中使用“help”来获得特定图像的帮助。例如，以下命令返回关于“beaglebone yocto”图像的帮助：

```
$ wic list beaglebone-yocto help

Creates a partitioned SD card image for Beaglebone.
Boot files are located in the first vfat partition.
```

# Operational Modes

You can use Wic in two different modes, depending on how much control you need for specifying the OpenEmbedded build artifacts that are used for creating the image: Raw and Cooked:

> 您可以在两种不同的模式下使用 Wic，这取决于您需要多少控制来指定用于创建图像的 OpenEmbedded 构建工件：Raw 和 Cooked：

- *Raw Mode:* You explicitly specify build artifacts through Wic command-line arguments.
- *Cooked Mode:* The current `MACHINE`{.interpreted-text role="term"} setting and image name are used to automatically locate and provide the build artifacts. You just supply a kickstart file and the name of the image from which to use artifacts.

> -*烹饪模式：*当前 `MACHINE`｛.explored text role=“term”｝设置和图像名称用于自动定位和提供构建工件。您只需提供一个 kickstart 文件和要使用工件的图像的名称。

Regardless of the mode you use, you need to have the build artifacts ready and available.

> 无论使用何种模式，都需要准备好构建工件并使其可用。

## Raw Mode

Running Wic in raw mode allows you to specify all the partitions through the `wic` command line. The primary use for raw mode is if you have built your kernel outside of the Yocto Project `Build Directory`{.interpreted-text role="term"}. In other words, you can point to arbitrary kernel, root filesystem locations, and so forth. Contrast this behavior with cooked mode where Wic looks in the `Build Directory`{.interpreted-text role="term"} (e.g. `tmp/deploy/images/` machine).

> 在原始模式下运行 Wic 允许您通过“Wic”命令行指定所有分区。原始模式的主要用途是，如果您在 Yocto 项目 `Build Directory`｛.depreted text role=“term”｝之外构建了内核。换句话说，您可以指向任意内核、根文件系统位置等等。将这种行为与熟模式进行对比，在熟模式下，Wic 在 `Build Directory`｛.explored text role=“term”｝（例如 `tmp/deploy/images/` machine）中查找。

The general form of the `wic` command in raw mode is:

> 原始模式下“wic”命令的一般形式为：

```
$ wic create wks_file options ...

  Where:

     wks_file:
        An OpenEmbedded kickstart file.  You can provide
        your own custom file or use a file from a set of
        existing files as described by further options.

     optional arguments:
       -h, --help            show this help message and exit
       -o OUTDIR, --outdir OUTDIR
                             name of directory to create image in
       -e IMAGE_NAME, --image-name IMAGE_NAME
                             name of the image to use the artifacts from e.g. core-
                             image-sato
       -r ROOTFS_DIR, --rootfs-dir ROOTFS_DIR
                             path to the /rootfs dir to use as the .wks rootfs
                             source
       -b BOOTIMG_DIR, --bootimg-dir BOOTIMG_DIR
                             path to the dir containing the boot artifacts (e.g.
                             /EFI or /syslinux dirs) to use as the .wks bootimg
                             source
       -k KERNEL_DIR, --kernel-dir KERNEL_DIR
                             path to the dir containing the kernel to use in the
                             .wks bootimg
       -n NATIVE_SYSROOT, --native-sysroot NATIVE_SYSROOT
                             path to the native sysroot containing the tools to use
                             to build the image
       -s, --skip-build-check
                             skip the build check
       -f, --build-rootfs    build rootfs
       -c {gzip,bzip2,xz}, --compress-with {gzip,bzip2,xz}
                             compress image with specified compressor
       -m, --bmap            generate .bmap
       --no-fstab-update     Do not change fstab file.
       -v VARS_DIR, --vars VARS_DIR
                             directory with <image>.env files that store bitbake
                             variables
       -D, --debug           output debug information
```

::: note
::: title
Note
:::

You do not need root privileges to run Wic. In fact, you should not run as root when using the utility.

> 运行 Wic 不需要 root 权限。事实上，使用该实用程序时不应该以 root 用户身份运行。
> :::

## Cooked Mode

Running Wic in cooked mode leverages off artifacts in the `Build Directory`{.interpreted-text role="term"}. In other words, you do not have to specify kernel or root filesystem locations as part of the command. All you need to provide is a kickstart file and the name of the image from which to use artifacts by using the \"-e\" option. Wic looks in the `Build Directory`{.interpreted-text role="term"} (e.g. `tmp/deploy/images/` machine) for artifacts.

> 在熟模式下运行 Wic 会利用“构建目录”中的工件｛.explored text role=“term”｝。换句话说，您不必指定内核或根文件系统位置作为命令的一部分。您只需要提供一个 kickstart 文件和使用\“-e\”选项使用工件的图像名称。Wic 在 `Build Directory`｛.explored text role=“term”｝（例如 `tmp/deploy/images/` machine）中查找工件。

The general form of the `wic` command using Cooked Mode is as follows:

> 使用烹饪模式的“wic”命令的一般形式如下：

```
$ wic create wks_file -e IMAGE_NAME

  Where:

     wks_file:
        An OpenEmbedded kickstart file.  You can provide
        your own custom file or use a file from a set of
        existing files provided with the Yocto Project
        release.

     required argument:
        -e IMAGE_NAME, --image-name IMAGE_NAME
                             name of the image to use the artifacts from e.g. core-
                             image-sato
```

# Using an Existing Kickstart File

If you do not want to create your own kickstart file, you can use an existing file provided by the Wic installation. As shipped, kickstart files can be found in the `overview-manual/development-environment:yocto project source repositories`{.interpreted-text role="ref"} in the following two locations:

> 如果您不想创建自己的 kickstart 文件，可以使用 Wic 安装提供的现有文件。出厂时，kickstart 文件可以在以下两个位置的 `overview manual/development environment:yocto project source repositories`{.depreted text role=“ref”}中找到：

```
poky/meta-yocto-bsp/wic
poky/scripts/lib/wic/canned-wks
```

Use the following command to list the available kickstart files:

> 使用以下命令列出可用的启动文件：

```
$ wic list images
  genericx86                         Create an EFI disk image for genericx86*
  beaglebone-yocto                   Create SD card image for Beaglebone
  edgerouter                         Create SD card image for Edgerouter
  qemux86-directdisk                 Create a QEMU machine 'pcbios' direct disk image
  directdisk-gpt                     Create a 'pcbios' direct disk image
  mkefidisk                          Create an EFI disk image
  directdisk                         Create a 'pcbios' direct disk image
  systemd-bootdisk                   Create an EFI disk image with systemd-boot
  mkhybridiso                        Create a hybrid ISO image
  sdimage-bootpart                   Create SD card image with a boot partition
  directdisk-multi-rootfs            Create multi rootfs image using rootfs plugin
  directdisk-bootloader-config       Create a 'pcbios' direct disk image with custom bootloader config
```

When you use an existing file, you do not have to use the `.wks` extension. Here is an example in Raw Mode that uses the `directdisk` file:

> 使用现有文件时，不必使用“.wks”扩展名。以下是在原始模式下使用“directdisk”文件的示例：

```
$ wic create directdisk -r rootfs_dir -b bootimg_dir \
      -k kernel_dir -n native_sysroot
```

Here are the actual partition language commands used in the `genericx86.wks` file to generate an image:

> 以下是“genericx86.wks”文件中用于生成映像的实际分区语言命令：

```
# short-description: Create an EFI disk image for genericx86*
# long-description: Creates a partitioned EFI disk image for genericx86* machines
part /boot --source bootimg-efi --sourceparams="loader=grub-efi" --ondisk sda --label msdos --active --align 1024
part / --source rootfs --ondisk sda --fstype=ext4 --label platform --align 1024 --use-uuid
part swap --ondisk sda --size 44 --label swap1 --fstype=swap

bootloader --ptable gpt --timeout=5 --append="rootfstype=ext4 console=ttyS0,115200 console=tty0"
```

# Using the Wic Plugin Interface

You can extend and specialize Wic functionality by using Wic plugins. This section explains the Wic plugin interface.

> 您可以使用 Wic 插件扩展和专门化 Wic 功能。本节介绍 Wic 插件接口。

::: note
::: title
Note
:::

Wic plugins consist of \"source\" and \"imager\" plugins. Imager plugins are beyond the scope of this section.

> Wic 插件由“源”和“成像器”插件组成。Imager 插件超出了本节的范围。
> :::

Source plugins provide a mechanism to customize partition content during the Wic image generation process. You can use source plugins to map values that you specify using `--source` commands in kickstart files (i.e. `*.wks`) to a plugin implementation used to populate a given partition.

> 源插件提供了一种在 Wic 图像生成过程中自定义分区内容的机制。您可以使用源插件将您在 kickstart 文件（即“*.wks”）中使用“--source”命令指定的值映射到用于填充给定分区的插件实现。

::: note
::: title
Note
:::

If you use plugins that have build-time dependencies (e.g. native tools, bootloaders, and so forth) when building a Wic image, you need to specify those dependencies using the `WKS_FILE_DEPENDS`{.interpreted-text role="term"} variable.

> 如果在构建 Wic 映像时使用具有构建时依赖关系的插件（例如，本机工具、引导加载程序等），则需要使用 `WKS_FILE_DEPENDS`{.depredicted text role=“term”}变量指定这些依赖关系。
> :::

Source plugins are subclasses defined in plugin files. As shipped, the Yocto Project provides several plugin files. You can see the source plugin files that ship with the Yocto Project :yocto\_[git:%60here](git:%60here) \</poky/tree/scripts/lib/wic/plugins/source\>\`. Each of these plugin files contains source plugins that are designed to populate a specific Wic image partition.

> 源插件是在插件文件中定义的子类。Yocto 项目提供了几个插件文件。您可以看到 Yocto 项目附带的源插件文件：Yocto\_[git:%60here]（git:%60here）\</poky/tree/scripts/lib/wic/plugins/source\>\`。这些插件文件中的每一个都包含源插件，这些源插件旨在填充特定的 Wic 映像分区。

Source plugins are subclasses of the `SourcePlugin` class, which is defined in the `poky/scripts/lib/wic/pluginbase.py` file. For example, the `BootimgEFIPlugin` source plugin found in the `bootimg-efi.py` file is a subclass of the `SourcePlugin` class, which is found in the `pluginbase.py` file.

> 源插件是“SourcePlugin”类的子类，该类在“poky/scripts/lib/wic/pluginbase.py”文件中定义。例如，在“bootimg efi.py”文件中找到的“BootimgEFIPlugin”源插件是“SourcePlugin”类的子类，该类位于“pluginbase.py”中。

You can also implement source plugins in a layer outside of the Source Repositories (external layer). To do so, be sure that your plugin files are located in a directory whose path is `scripts/lib/wic/plugins/source/` within your external layer. When the plugin files are located there, the source plugins they contain are made available to Wic.

> 您还可以在源存储库（外部层）之外的层中实现源插件。要做到这一点，请确保您的插件文件位于外部层中路径为“scripts/lib/wic/plugins/source/”的目录中。当插件文件位于那里时，它们包含的源插件就可供 Wic 使用。

When the Wic implementation needs to invoke a partition-specific implementation, it looks for the plugin with the same name as the `--source` parameter used in the kickstart file given to that partition. For example, if the partition is set up using the following command in a kickstart file:

> 当 Wic 实现需要调用分区特定的实现时，它会查找与该分区的 kickstart 文件中使用的“--source”参数同名的插件。例如，如果在 kickstart 文件中使用以下命令设置分区：

```
part /boot --source bootimg-pcbios --ondisk sda --label boot --active --align 1024
```

The methods defined as class members of the matching source plugin (i.e. `bootimg-pcbios`) in the `bootimg-pcbios.py` plugin file are used.

> 使用“bootimg pcbios.py”插件文件中定义为匹配源插件（即“bootimg pc bios”）类成员的方法。

To be more concrete, here is the corresponding plugin definition from the `bootimg-pcbios.py` file for the previous command along with an example method called by the Wic implementation when it needs to prepare a partition using an implementation-specific function:

> 更具体地说，这里是上一个命令的“bootimg pcbios.py”文件中对应的插件定义，以及 Wic 实现在需要使用特定于实现的函数准备分区时调用的示例方法：

```
.
.
.
class BootimgPcbiosPlugin(SourcePlugin):
"""
Create MBR boot partition and install syslinux on it.
"""

name = 'bootimg-pcbios'
.
.
.
@classmethod
def do_prepare_partition(cls, part, source_params, creator, cr_workdir,
                oe_builddir, bootimg_dir, kernel_dir,
                rootfs_dir, native_sysroot):
"""
Called to do the actual content population for a partition i.e. it
'prepares' the partition to be incorporated into the image.
In this case, prepare content for legacy bios boot partition.
"""
.
.
.
```

If a subclass (plugin) itself does not implement a particular function, Wic locates and uses the default version in the superclass. It is for this reason that all source plugins are derived from the `SourcePlugin` class.

> 如果子类（插件）本身没有实现特定的功能，Wic 会在超类中定位并使用默认版本。正是由于这个原因，所有源插件都是从“SourcePlugin”类派生的。

The `SourcePlugin` class defined in the `pluginbase.py` file defines a set of methods that source plugins can implement or override. Any plugins (subclass of `SourcePlugin`) that do not implement a particular method inherit the implementation of the method from the `SourcePlugin` class. For more information, see the `SourcePlugin` class in the `pluginbase.py` file for details:

> “pluginbase.py”文件中定义的“SourcePlugin”类定义了一组源插件可以实现或覆盖的方法。任何不实现特定方法的插件（“SourcePlugin”的子类）都会从“SourcePlugin'”类继承该方法的实现。有关详细信息，请参阅“pluginbase.py”文件中的“SourcePlugin”类以获取详细信息：

The following list describes the methods implemented in the `SourcePlugin` class:

> 以下列表描述了在“SourcePlugin”类中实现的方法：

- `do_prepare_partition()`: Called to populate a partition with actual content. In other words, the method prepares the final partition image that is incorporated into the disk image.

> -`do_prepare_partion（）`：调用以用实际内容填充分区。换句话说，该方法准备合并到磁盘映像中的最终分区映像。

- `do_configure_partition()`: Called before `do_prepare_partition()` to create custom configuration files for a partition (e.g. syslinux or grub configuration files).

> -`do_configure_partition（）`：在 `do_prepare_partion（）` 之前调用，为分区创建自定义配置文件（例如 syslinux 或 grub 配置文件）。

- `do_install_disk()`: Called after all partitions have been prepared and assembled into a disk image. This method provides a hook to allow finalization of a disk image (e.g. writing an MBR).

> -`do_install_disk（）`：在准备好所有分区并将其组装到磁盘映像中后调用。这种方法提供了一个钩子，允许完成磁盘映像（例如写入 MBR）。

- `do_stage_partition()`: Special content-staging hook called before `do_prepare_partition()`. This method is normally empty.

> -`do_stage_partition（）`：在 `do_prepare_partion（）` 之前调用的特殊内容暂存挂钩。此方法通常为空。

Typically, a partition just uses the passed-in parameters (e.g. the unmodified value of `bootimg_dir`). However, in some cases, things might need to be more tailored. As an example, certain files might additionally need to be taken from `bootimg_dir + /boot`. This hook allows those files to be staged in a customized fashion.

> 通常，分区只使用传入的参数（例如“bootimg_dir”的未修改值）。然而，在某些情况下，事情可能需要更加量体裁衣。例如，某些文件可能还需要取自“bootimg_dir+/boot”。这个钩子允许以自定义的方式暂存这些文件。

::: note
::: title

Note

> 笔记
> :::

`get_bitbake_var()` allows you to access non-standard variables that you might want to use for this behavior.

> `get_bitbake_var（）` 允许您访问可能要用于此行为的非标准变量。
> :::

You can extend the source plugin mechanism. To add more hooks, create more source plugin methods within `SourcePlugin` and the corresponding derived subclasses. The code that calls the plugin methods uses the `plugin.get_source_plugin_methods()` function to find the method or methods needed by the call. Retrieval of those methods is accomplished by filling up a dict with keys that contain the method names of interest. On success, these will be filled in with the actual methods. See the Wic implementation for examples and details.

> 您可以扩展源插件机制。要添加更多挂钩，请在“SourcePlugin”和相应的派生子类中创建更多的源插件方法。调用插件方法的代码使用 `plugin.get_source_plugin_methods（）` 函数来查找调用所需的一个或多个方法。这些方法的检索是通过用包含感兴趣的方法名称的键填充 dict 来完成的。一旦成功，这些将用实际的方法来填充。有关示例和详细信息，请参见 Wic 实现。

# Wic Examples

This section provides several examples that show how to use the Wic utility. All the examples assume the list of requirements in the \"`dev-manual/wic:requirements`{.interpreted-text role="ref"}\" section have been met. The examples assume the previously generated image is `core-image-minimal`.

> 本节提供了几个示例，说明如何使用 Wic 实用程序。所有示例都假定满足了\“`dev manual/wic:requirements`｛.explored text role=”ref“｝\”部分中的要求列表。这些例子假设先前生成的图像是“核心图像最小值”。

## Generate an Image using an Existing Kickstart File

This example runs in Cooked Mode and uses the `mkefidisk` kickstart file:

> 此示例在烹饪模式下运行，并使用“mkefidisk”kickstart 文件：

```
$ wic create mkefidisk -e core-image-minimal
INFO: Building wic-tools...
          .
          .
          .
INFO: The new image(s) can be found here:
  ./mkefidisk-201804191017-sda.direct

The following build artifacts were used to create the image(s):
  ROOTFS_DIR:                   /home/stephano/yocto/build/tmp-glibc/work/qemux86-oe-linux/core-image-minimal/1.0-r0/rootfs
  BOOTIMG_DIR:                  /home/stephano/yocto/build/tmp-glibc/work/qemux86-oe-linux/core-image-minimal/1.0-r0/recipe-sysroot/usr/share
  KERNEL_DIR:                   /home/stephano/yocto/build/tmp-glibc/deploy/images/qemux86
  NATIVE_SYSROOT:               /home/stephano/yocto/build/tmp-glibc/work/i586-oe-linux/wic-tools/1.0-r0/recipe-sysroot-native

INFO: The image(s) were created using OE kickstart file:
  /home/stephano/yocto/openembedded-core/scripts/lib/wic/canned-wks/mkefidisk.wks
```

The previous example shows the easiest way to create an image by running in cooked mode and supplying a kickstart file and the \"-e\" option to point to the existing build artifacts. Your `local.conf` file needs to have the `MACHINE`{.interpreted-text role="term"} variable set to the machine you are using, which is \"qemux86\" in this example.

> 上一个示例显示了创建图像的最简单方法，方法是在熟模式下运行，并提供一个 kickstart 文件和\“-e\”选项来指向现有的构建工件。您的“local.conf”文件需要将“MACHINE”｛.explored text role=“term”｝变量设置为您正在使用的机器，在本例中为\“qemux86\”。

Once the image builds, the output provides image location, artifact use, and kickstart file information.

> 一旦构建了图像，输出就会提供图像位置、工件使用和启动文件信息。

::: note
::: title
Note
:::

You should always verify the details provided in the output to make sure that the image was indeed created exactly as expected.

> 您应该始终验证输出中提供的详细信息，以确保图像确实按照预期创建。
> :::

Continuing with the example, you can now write the image from the `Build Directory`{.interpreted-text role="term"} onto a USB stick, or whatever media for which you built your image, and boot from the media. You can write the image by using `bmaptool` or `dd`:

> 继续本示例，您现在可以将“构建目录”中的映像｛.depreted text role=“term”｝写入 U 盘或为其构建映像的任何介质，然后从该介质启动。您可以使用“bmaptool”或“dd”写入图像：

```
$ oe-run-native bmap-tools-native bmaptool copy mkefidisk-201804191017-sda.direct /dev/sdX
```

or :

```
$ sudo dd if=mkefidisk-201804191017-sda.direct of=/dev/sdX
```

::: note
::: title
Note
:::

For more information on how to use the `bmaptool` to flash a device with an image, see the \"``dev-manual/bmaptool:flashing images using \`\`bmaptool\`\` ``{.interpreted-text role="ref"}\" section.

> 有关如何使用“bmaptool”为带有图像的设备闪烁的更多信息，请参阅“``dev manual/bmaptool:flash images using \`\`bmaptool\`\` `{.depreted text role=“ref”}\”一节。
> :::

## Using a Modified Kickstart File

Because partitioned image creation is driven by the kickstart file, it is easy to affect image creation by changing the parameters in the file. This next example demonstrates that through modification of the `directdisk-gpt` kickstart file.

> 由于分区图像创建是由 kickstart 文件驱动的，因此很容易通过更改文件中的参数来影响图像创建。下一个示例通过修改“directdisk gpt”kickstart 文件来演示这一点。

As mentioned earlier, you can use the command `wic list images` to show the list of existing kickstart files. The directory in which the `directdisk-gpt.wks` file resides is `scripts/lib/image/canned-wks/`, which is located in the `Source Directory`{.interpreted-text role="term"} (e.g. `poky`). Because available files reside in this directory, you can create and add your own custom files to the directory. Subsequent use of the `wic list images` command would then include your kickstart files.

> 如前所述，您可以使用命令“wic list images”来显示现有启动文件的列表。“directdisk gpt.wks”文件所在的目录是“scripts/lib/image/canted wks/”，该目录位于“源目录”中（例如“poky”）。因为可用文件位于该目录中，所以您可以创建自己的自定义文件并将其添加到该目录中。随后使用“wic list images”命令将包括您的 kickstart 文件。

In this example, the existing `directdisk-gpt` file already does most of what is needed. However, for the hardware in this example, the image will need to boot from `sdb` instead of `sda`, which is what the `directdisk-gpt` kickstart file uses.

> 在本例中，现有的“directdisk gpt”文件已经完成了所需的大部分工作。然而，对于本例中的硬件，映像将需要从“sdb”而不是“sda”启动，这是“directdisk gpt”kickstart 文件所使用的。

The example begins by making a copy of the `directdisk-gpt.wks` file in the `scripts/lib/image/canned-wks` directory and then by changing the lines that specify the target disk from which to boot:

> 该示例首先在“scripts/lib/image/canted wks”目录中复制“directdisk gpt.wks”文件，然后更改指定启动目标磁盘的行：

```
$ cp /home/stephano/yocto/poky/scripts/lib/wic/canned-wks/directdisk-gpt.wks \
     /home/stephano/yocto/poky/scripts/lib/wic/canned-wks/directdisksdb-gpt.wks
```

Next, the example modifies the `directdisksdb-gpt.wks` file and changes all instances of \"`--ondisk sda`\" to \"`--ondisk sdb`\". The example changes the following two lines and leaves the remaining lines untouched:

> 接下来，该示例修改 `directdisksdb-gpt.wks'文件，并将\“`--ondisk-sda `\”的所有实例更改为\“`--ondisk-sdb`\”。该示例更改了以下两行，并保留其余行不变：

```
part /boot --source bootimg-pcbios --ondisk sdb --label boot --active --align 1024
part / --source rootfs --ondisk sdb --fstype=ext4 --label platform --align 1024 --use-uuid
```

Once the lines are changed, the example generates the `directdisksdb-gpt` image. The command points the process at the `core-image-minimal` artifacts for the Next Unit of Computing (nuc) `MACHINE`{.interpreted-text role="term"} the `local.conf`:

> 一旦更改了行，该示例就会生成“directdisksdb-gpt”图像。该命令将进程指向下一个计算单元（nuc）`MACHINE` 的 `core image minimum` 工件 `local.conf`：

```
$ wic create directdisksdb-gpt -e core-image-minimal
INFO: Building wic-tools...
           .
           .
           .
Initialising tasks: 100% |#######################################| Time: 0:00:01
NOTE: Executing SetScene Tasks
NOTE: Executing RunQueue Tasks
NOTE: Tasks Summary: Attempted 1161 tasks of which 1157 didn't need to be rerun and all succeeded.
INFO: Creating image(s)...

INFO: The new image(s) can be found here:
  ./directdisksdb-gpt-201710090938-sdb.direct

The following build artifacts were used to create the image(s):
  ROOTFS_DIR:                   /home/stephano/yocto/build/tmp-glibc/work/qemux86-oe-linux/core-image-minimal/1.0-r0/rootfs
  BOOTIMG_DIR:                  /home/stephano/yocto/build/tmp-glibc/work/qemux86-oe-linux/core-image-minimal/1.0-r0/recipe-sysroot/usr/share
  KERNEL_DIR:                   /home/stephano/yocto/build/tmp-glibc/deploy/images/qemux86
  NATIVE_SYSROOT:               /home/stephano/yocto/build/tmp-glibc/work/i586-oe-linux/wic-tools/1.0-r0/recipe-sysroot-native

INFO: The image(s) were created using OE kickstart file:
  /home/stephano/yocto/poky/scripts/lib/wic/canned-wks/directdisksdb-gpt.wks
```

Continuing with the example, you can now directly `dd` the image to a USB stick, or whatever media for which you built your image, and boot the resulting media:

> 继续本例，您现在可以直接将映像“添加”到 U 盘或为其构建映像的任何介质中，并启动生成的介质：

```
$ sudo dd if=directdisksdb-gpt-201710090938-sdb.direct of=/dev/sdb
140966+0 records in
140966+0 records out
72174592 bytes (72 MB, 69 MiB) copied, 78.0282 s, 925 kB/s
$ sudo eject /dev/sdb
```

## Using a Modified Kickstart File and Running in Raw Mode

This next example manually specifies each build artifact (runs in Raw Mode) and uses a modified kickstart file. The example also uses the `-o` option to cause Wic to create the output somewhere other than the default output directory, which is the current directory:

> 下一个示例手动指定每个构建工件（在原始模式下运行），并使用修改后的 kickstart 文件。该示例还使用“-o”选项使 Wic 在默认输出目录（即当前目录）之外的其他地方创建输出：

```
$ wic create test.wks -o /home/stephano/testwic \
     --rootfs-dir /home/stephano/yocto/build/tmp/work/qemux86-poky-linux/core-image-minimal/1.0-r0/rootfs \
     --bootimg-dir /home/stephano/yocto/build/tmp/work/qemux86-poky-linux/core-image-minimal/1.0-r0/recipe-sysroot/usr/share \
     --kernel-dir /home/stephano/yocto/build/tmp/deploy/images/qemux86 \
     --native-sysroot /home/stephano/yocto/build/tmp/work/i586-poky-linux/wic-tools/1.0-r0/recipe-sysroot-native

INFO: Creating image(s)...

INFO: The new image(s) can be found here:
  /home/stephano/testwic/test-201710091445-sdb.direct

The following build artifacts were used to create the image(s):
  ROOTFS_DIR:                   /home/stephano/yocto/build/tmp-glibc/work/qemux86-oe-linux/core-image-minimal/1.0-r0/rootfs
  BOOTIMG_DIR:                  /home/stephano/yocto/build/tmp-glibc/work/qemux86-oe-linux/core-image-minimal/1.0-r0/recipe-sysroot/usr/share
  KERNEL_DIR:                   /home/stephano/yocto/build/tmp-glibc/deploy/images/qemux86
  NATIVE_SYSROOT:               /home/stephano/yocto/build/tmp-glibc/work/i586-oe-linux/wic-tools/1.0-r0/recipe-sysroot-native

INFO: The image(s) were created using OE kickstart file:
  test.wks
```

For this example, `MACHINE`{.interpreted-text role="term"} did not have to be specified in the `local.conf` file since the artifact is manually specified.

> 对于本例，由于工件是手动指定的，因此不必在 `local.conf` 文件中指定 `MACHINE`｛.explored text role=“term”｝。

## Using Wic to Manipulate an Image

Wic image manipulation allows you to shorten turnaround time during image development. For example, you can use Wic to delete the kernel partition of a Wic image and then insert a newly built kernel. This saves you time from having to rebuild the entire image each time you modify the kernel.

> Wic 图像处理可以缩短图像开发过程中的周转时间。例如，您可以使用 Wic 删除 Wic 映像的内核分区，然后插入新构建的内核。这样可以节省每次修改内核时重建整个映像的时间。

::: note
::: title
Note
:::

In order to use Wic to manipulate a Wic image as in this example, your development machine must have the `mtools` package installed.

> 为了像本例中那样使用 Wic 来操作 Wic 映像，您的开发机器必须安装“mtools”包。
> :::

The following example examines the contents of the Wic image, deletes the existing kernel, and then inserts a new kernel:

> 以下示例检查 Wic 映像的内容，删除现有内核，然后插入新内核：

1. *List the Partitions:* Use the `wic ls` command to list all the partitions in the Wic image:

> 1.*列出分区：*使用“wic-ls”命令列出 wic 映像中的所有分区：

```

> ```

$ wic ls tmp/deploy/images/qemux86/core-image-minimal-qemux86.wic

> $wic ls tmp/deploy/images/qemux86/core-image-minimal-qemux86.wic

Num     Start        End          Size      Fstype

> 数字开始-结束大小Fstype
 1       1048576     25041919     23993344  fat16
 2      25165824     72157183     46991360  ext4

```

> ```
> ```

The previous output shows two partitions in the `core-image-minimal-qemux86.wic` image.

> 上一个输出显示了“core-image-minimal-qemux86.wic”映像中的两个分区。

2. *Examine a Particular Partition:* Use the `wic ls` command again but in a different form to examine a particular partition.

> 2.*检查特定分区：*再次使用“wic-ls”命令，但使用不同的形式检查特定分区。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

You can get command usage on any Wic command using the following form:

> 您可以使用以下表格获取任何 Wic 命令的命令用法：

```

> ```

$ wic help command

> $wic帮助命令

```

> ```
> ```

For example, the following command shows you the various ways to use the wic ls command:

> 例如，以下命令显示了使用 wic-ls 命令的各种方法：

```

> ```

$ wic help ls

> $wic帮助ls

```

> ```
> ```

:::

> ：：：

The following command shows what is in partition one:

> 以下命令显示分区一中的内容：

```

> ```

$ wic ls tmp/deploy/images/qemux86/core-image-minimal-qemux86.wic:1

> $wic-ls-tmp/deploy/images/qemux86/core-image-minial-qemux86。wic:1

Volume in drive : is boot

> 驱动器中的卷：正在启动
 Volume Serial Number is E894-1809

Directory for ::/

> 目录：/


libcom32 c32    186500 2017-10-09  16:06

> 利比亚通讯社32 c32 186500 2017-10-09 16:06

libutil  c32     24148 2017-10-09  16:06

> libutil c32 24148 2017-10-09 16:06

syslinux cfg       220 2017-10-09  16:06

> syslinux cfg 220 2017-10-09 16:06

vesamenu c32     27104 2017-10-09  16:06

> 维萨曼努c32 27104 2017-10-09 16:06

vmlinuz        6904608 2017-10-09  16:06

> vmlinuz 6904608 2017-10-09 16:06
        5 files           7 142 580 bytes
                         16 582 656 bytes free

```

> ```
> ```

The previous output shows five files, with the `vmlinuz` being the kernel.

> 前面的输出显示了五个文件，其中“vmlinuz”是内核。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

If you see the following error, you need to update or create a `~/.mtoolsrc` file and be sure to have the line \"mtools_skip_check=1\" in the file. Then, run the Wic command again:

> 如果您看到以下错误，您需要更新或创建一个 `~/.mtoolsrc` 文件，并确保文件中有“mtools_skip_check=1\”行。然后，再次运行 Wic 命令：

```

> ```

ERROR: _exec_cmd: /usr/bin/mdir -i /tmp/wic-parttfokuwra ::/ returned '1' instead of 0

> 错误：_exec_cmd:/usr/bin/mdir-i/tmp/wic parttfokuwra:：/返回了“1”而不是0
 output: Total number of sectors (47824) not a multiple of sectors per track (32)!
 Add mtools_skip_check=1 to your .mtoolsrc file to skip this test

```

> ```
> ```

:::

> ：：：

3. *Remove the Old Kernel:* Use the `wic rm` command to remove the `vmlinuz` file (kernel):

> 3.*删除旧内核：*使用 `wic-rm` 命令删除 `vmlinuz` 文件（内核）：

```

> ```

$ wic rm tmp/deploy/images/qemux86/core-image-minimal-qemux86.wic:1/vmlinuz

> $wic-rm-tmp/deploy/images/qemux86/core-image-minial-qemux86。wic:1/vmlinuz

```

> ```
> ```

4. *Add In the New Kernel:* Use the `wic cp` command to add the updated kernel to the Wic image. Depending on how you built your kernel, it could be in different places. If you used `devtool` and an SDK to build your kernel, it resides in the `tmp/work` directory of the extensible SDK. If you used `make` to build the kernel, the kernel will be in the `workspace/sources` area.

> 4.*添加新内核：*使用“wic-cp”命令将更新的内核添加到 wic 映像中。根据您构建内核的方式，它可能位于不同的位置。如果您使用“devtool”和 SDK 来构建内核，那么它位于可扩展 SDK 的“tmp/work”目录中。如果使用“make”构建内核，那么内核将位于“workspace/sources”区域。

The following example assumes `devtool` was used to build the kernel:

> 以下示例假设使用了“devtool”来构建内核：

```

> ```

$ wic cp poky_sdk/tmp/work/qemux86-poky-linux/linux-yocto/4.12.12+git999-r0/linux-yocto-4.12.12+git999/arch/x86/boot/bzImage \

> $wic cp poky_sdk/tmp/work/qemux86 poky linux/linux-yocto/4.12.12+git999-r0/linux-yocto-4.212+git999/arch/x86/boot/bzImage\
         poky/build/tmp/deploy/images/qemux86/core-image-minimal-qemux86.wic:1/vmlinuz

```

> ```
> ```

Once the new kernel is added back into the image, you can use the `dd` command or ``bmaptool <dev-manual/bmaptool:flashing images using \`\`bmaptool\`\`>``{.interpreted-text role="ref"} to flash your wic image onto an SD card or USB stick and test your target.

> 一旦将新内核添加回映像中，您就可以使用 `dd` 命令或 `bmaptool<devmanual/bmaptool:flash images using \`\`bmaptTool\`\`>``｛.depreted text role=“ref”｝将 wic 映像闪存到 SD 卡或 U 盘上并测试目标。

::: note

> ：：：注释

::: title

> ：：标题

Note

> 笔记

:::

> ：：：

Using `bmaptool` is generally 10 to 20 times faster than using `dd`.

> 使用“bmaptool”通常比使用“dd”快 10 到 20 倍。

:::

> ：：：
