---
tip: translate by baidu@2023-06-07 17:08:22
...
---
title: Flashing Images Using `bmaptool`
-----------------------------

A fast and easy way to flash an image to a bootable device is to use Bmaptool, which is integrated into the OpenEmbedded build system. Bmaptool is a generic tool that creates a file\'s block map (bmap) and then uses that map to copy the file. As compared to traditional tools such as dd or cp, Bmaptool can copy (or flash) large files like raw system image files much faster.

> 将图像闪存到可引导设备的一种快速简便的方法是使用 Bmaptool，它集成到 OpenEmbedded 构建系统中。Bmaptool 是一种通用工具，用于创建文件的块映射（bmap），然后使用该映射来复制文件。与 dd 或 cp 等传统工具相比，Bmaptool 可以更快地复制（或闪存）像原始系统映像文件这样的大文件。

::: note
::: title
Note
:::

- If you are using Ubuntu or Debian distributions, you can install the `bmap-tools` package using the following command and then use the tool without specifying `PATH` even from the root account:

> -如果您使用的是 Ubuntu 或 Debian 发行版，您可以使用以下命令安装“bmap tools”包，然后使用该工具，甚至不需要从根帐户指定“PATH”：

```
$ sudo apt install bmap-tools
```

- If you are unable to install the `bmap-tools` package, you will need to build Bmaptool before using it. Use the following command:

> -如果您无法安装“bmap tools”包，则需要先构建 Bmaptool，然后再使用它。请使用以下命令：

```
$ bitbake bmap-tools-native
```

:::

Following, is an example that shows how to flash a Wic image. Realize that while this example uses a Wic image, you can use Bmaptool to flash any type of image. Use these steps to flash an image using Bmaptool:

> 下面是一个示例，展示了如何闪烁 Wic 图像。要意识到，虽然本例使用 Wic 图像，但您可以使用 Bmaptool 来闪烁任何类型的图像。使用以下步骤使用 Bmaptool 闪烁图像：

1. *Update your local.conf File:* You need to have the following set in your `local.conf` file before building your image:

> 1.*更新 local.conf 文件：*在构建映像之前，您需要在“local.conf”文件中设置以下内容：

```

> ```

IMAGE_FSTYPES += "wic wic.bmap"

> IMAGE_FSTYPES+=“wic-wic.bmap”

```

> ```
> ```

2. *Get Your Image:* Either have your image ready (pre-built with the `IMAGE_FSTYPES`{.interpreted-text role="term"} setting previously mentioned) or take the step to build the image:

> 2.*获取您的图像：*准备好您的图像（使用前面提到的 `Image_FSTTYPE`｛.explored text role=“term”｝设置预先构建）或采取步骤构建图像：

```

> ```

$ bitbake image

> $bitbake图像

```

> ```
> ```

3. *Flash the Device:* Flash the device with the image by using Bmaptool depending on your particular setup. The following commands assume the image resides in the `Build Directory`{.interpreted-text role="term"}\'s `deploy/images/` area:

> 3.*闪存设备：*根据您的特定设置，使用 Bmaptool 使用图像闪存设备。以下命令假定映像位于 `Build Directory`｛.depreted text role=“term”｝\的 `deploy/images/` 区域中：

- If you have write access to the media, use this command form:

> -如果您有对介质的写访问权限，请使用以下命令形式：

```
```

$ oe-run-native bmap-tools-native bmaptool copy build-directory/tmp/deploy/images/machine/image.wic /dev/sdX

```
```

- If you do not have write access to the media, set your permissions first and then use the same command form:

> -如果您没有对介质的写访问权限，请先设置您的权限，然后使用相同的命令形式：

```
```

$ sudo chmod 666 /dev/sdX
$ oe-run-native bmap-tools-native bmaptool copy build-directory/tmp/deploy/images/machine/image.wic /dev/sdX

```
```

For help on the `bmaptool` command, use the following command:

> 有关“bmaptool”命令的帮助，请使用以下命令：

```
$ bmaptool --help
```
