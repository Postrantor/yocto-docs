---
tip: translate by openai@2023-06-10 09:54:51
title: Flashing Images Using `bmaptool`
---
A fast and easy way to flash an image to a bootable device is to use Bmaptool, which is integrated into the OpenEmbedded build system. Bmaptool is a generic tool that creates a file\'s block map (bmap) and then uses that map to copy the file. As compared to traditional tools such as dd or cp, Bmaptool can copy (or flash) large files like raw system image files much faster.

> 使用 Bmaptool 快速而轻松地将镜像刷入可引导设备，Bmaptool 已集成到 OpenEmbedded 构建系统中。Bmaptool 是一个通用工具，可以创建文件的块映射(bmap)，然后使用该映射来复制文件。与传统工具(如 dd 或 cp)相比，Bmaptool 可以更快地复制(或刷入)大文件，如原始系统映像文件。

::: note
::: title
Note
:::

- If you are using Ubuntu or Debian distributions, you can install the `bmap-tools` package using the following command and then use the tool without specifying `PATH` even from the root account:

> 如果您使用 Ubuntu 或 Debian 发行版，您可以使用以下命令安装 `bmap-tools` 软件包，然后即使以 root 账户也可以不指定 `PATH` 即可使用该工具：

```
$ sudo apt install bmap-tools
```

- If you are unable to install the `bmap-tools` package, you will need to build Bmaptool before using it. Use the following command:

  ```
  $ bitbake bmap-tools-native
  ```

:::

Following, is an example that shows how to flash a Wic image. Realize that while this example uses a Wic image, you can use Bmaptool to flash any type of image. Use these steps to flash an image using Bmaptool:

> 以下是一个示例，展示了如何使用 Bmaptool 刷入 Wic 镜像。请注意，虽然本示例使用的是 Wic 镜像，但你也可以使用 Bmaptool 刷入任何类型的镜像。使用以下步骤使用 Bmaptool 刷入镜像：

1. _Update your local.conf File:_ You need to have the following set in your `local.conf` file before building your image:

   ```
   IMAGE_FSTYPES += "wic wic.bmap"
   ```
2. _Get Your Image:_ Either have your image ready (pre-built with the `IMAGE_FSTYPES` setting previously mentioned) or take the step to build the image:

> 2. _获取您的镜像：_ 准备好您的镜像(使用之前提到的 `IMAGE_FSTYPES` 设置预先构建)，或者采取步骤构建镜像：

```
$ bitbake image
```

3. _Flash the Device:_ Flash the device with the image by using Bmaptool depending on your particular setup. The following commands assume the image resides in the `Build Directory`\'s `deploy/images/` area:

> 3. 刷新设备：使用 Bmaptool 根据您的特定设置刷新设备。以下命令假定镜像位于 `Build Directory` 的 `deploy/images/` 区域：

- If you have write access to the media, use this command form:

  ```
  $ oe-run-native bmap-tools-native bmaptool copy build-directory/tmp/deploy/images/machine/image.wic /dev/sdX
  ```
- If you do not have write access to the media, set your permissions first and then use the same command form:

  ```
  $ sudo chmod 666 /dev/sdX
  $ oe-run-native bmap-tools-native bmaptool copy build-directory/tmp/deploy/images/machine/image.wic /dev/sdX
  ```

For help on the `bmaptool` command, use the following command:

```
$ bmaptool --help
```
