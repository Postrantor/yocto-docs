---
tip: translate by openai@2023-06-10 10:45:22
...
---
title: Efficiently Fetching Source Files During a Build
-------------------------------------------------------

The OpenEmbedded build system works with source files located through the `SRC_URI` variable. When you build something using BitBake, a big part of the operation is locating and downloading all the source tarballs. For images, downloading all the source for various packages can take a significant amount of time.

> 开放嵌入式构建系统使用通过 `SRC_URI` 变量位置的源文件。当您使用 BitBake 构建时，大部分操作是定位和下载所有源归档文件。对于镜像，下载所有各种软件包的源代码可能需要相当长的时间。

This section shows you how you can use mirrors to speed up fetching source files and how you can pre-fetch files all of which leads to more efficient use of resources and time.

> 这一节将向您展示如何使用镜像来加速获取源文件，以及如何预先获取文件，这一切都有助于更有效地利用资源和时间。

# Setting up Effective Mirrors

A good deal that goes into a Yocto Project build is simply downloading all of the source tarballs. Maybe you have been working with another build system for which you have built up a sizable directory of source tarballs. Or, perhaps someone else has such a directory for which you have read access. If so, you can save time by adding statements to your configuration file so that the build process checks local directories first for existing tarballs before checking the Internet.

> 一个进入 Yocto 项目构建的好办法就是下载所有的源 tarball。也许你一直在使用另一个构建系统，你已经构建了一个相当大的源 tarball 目录。或者，也许其他人有一个你可以访问的目录。如果是这样，你可以通过在你的配置文件中添加语句，让构建过程首先检查本地目录中是否存在 tarball，而不是检查 Internet，以节省时间。

Here is an efficient way to set it up in your `local.conf` file:

```
SOURCE_MIRROR_URL ?= "file:///home/you/your-download-dir/"
INHERIT += "own-mirrors"
BB_GENERATE_MIRROR_TARBALLS = "1"
# BB_NO_NETWORK = "1"
```

In the previous example, the `BB_GENERATE_MIRROR_TARBALLS` directory. Due to performance reasons, generating and storing these tarballs is not the build system\'s default behavior.

> 在前面的例子中，变量 `BB_GENERATE_MIRROR_TARBALLS` 导致 OpenEmbedded 构建系统生成 Git 存储库的 tarballs 并将其存储在 `DL_DIR` 目录中。出于性能原因，生成和存储这些 tarballs 不是构建系统的默认行为。

You can also use the `PREMIRRORS` variable. For an example, see the variable\'s glossary entry in the Yocto Project Reference Manual.

> 您也可以使用 `PREMIRRORS` 变量。有关示例，请参阅 Yocto Project 参考手册中变量的术语条目。

# Getting Source Files and Suppressing the Build

Another technique you can use to ready yourself for a successive string of build operations, is to pre-fetch all the source files without actually starting a build. This technique lets you work through any download issues and ultimately gathers all the source files into your download directory `structure-build-downloads`.

> 你可以使用另一种技术来准备连续构建操作，那就是预先获取所有源文件而不实际开始构建。这种技术可以帮助你解决所有下载问题，并最终将所有源文件收集到位于 `DL_DIR` 文件夹中。

Use the following BitBake command form to fetch all the necessary sources without starting the build:

```
$ bitbake target --runall=fetch
```

This variation of the BitBake command guarantees that you have all the sources for that BitBake target should you disconnect from the Internet and want to do the build later offline.

> 这种 BitBake 命令的变体可以确保，如果您断开互联网连接，想稍后离线进行构建，您将拥有该 BitBake 目标的所有源。
