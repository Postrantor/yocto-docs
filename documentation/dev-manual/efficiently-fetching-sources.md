---
tip: translate by baidu@2023-06-07 17:11:52
...
---
title: Efficiently Fetching Source Files During a Build
-------------------------------------------------------

The OpenEmbedded build system works with source files located through the `SRC_URI`{.interpreted-text role="term"} variable. When you build something using BitBake, a big part of the operation is locating and downloading all the source tarballs. For images, downloading all the source for various packages can take a significant amount of time.

> OpenEmbedded 构建系统使用通过 `SRC_URI`｛.explored text role=“term”｝变量定位的源文件。当您使用 BitBake 构建东西时，操作的很大一部分是定位和下载所有源 tarball。对于图像，下载各种包的所有源代码可能会花费大量时间。

This section shows you how you can use mirrors to speed up fetching source files and how you can pre-fetch files all of which leads to more efficient use of resources and time.

> 本节将向您展示如何使用镜像来加快获取源文件的速度，以及如何预获取文件，所有这些都将提高资源和时间的使用效率。

# Setting up Effective Mirrors

A good deal that goes into a Yocto Project build is simply downloading all of the source tarballs. Maybe you have been working with another build system for which you have built up a sizable directory of source tarballs. Or, perhaps someone else has such a directory for which you have read access. If so, you can save time by adding statements to your configuration file so that the build process checks local directories first for existing tarballs before checking the Internet.

> Yocto 项目构建的一个好方法就是简单地下载所有的源 tarball。也许您一直在使用另一个构建系统，为其构建了一个相当大的源 tarball 目录。或者，也许其他人有这样一个目录，您可以对其进行读取访问。如果是这样的话，您可以通过向配置文件中添加语句来节省时间，这样构建过程在检查 Internet 之前首先检查本地目录中的现有 tarball。

Here is an efficient way to set it up in your `local.conf` file:

> 以下是在“local.conf”文件中设置它的有效方法：

```
SOURCE_MIRROR_URL ?= "file:///home/you/your-download-dir/"
INHERIT += "own-mirrors"
BB_GENERATE_MIRROR_TARBALLS = "1"
# BB_NO_NETWORK = "1"
```

In the previous example, the `BB_GENERATE_MIRROR_TARBALLS`{.interpreted-text role="term"} variable causes the OpenEmbedded build system to generate tarballs of the Git repositories and store them in the `DL_DIR`{.interpreted-text role="term"} directory. Due to performance reasons, generating and storing these tarballs is not the build system\'s default behavior.

> 在上一个示例中，`BB_GENERATE_MIRROR_TARBALLS`｛.depreced text role=“term”｝变量使 OpenEmbedded 构建系统生成 Git 存储库的 tarball，并将其存储在 `DL_DIR`｛.epreced textrole=”term“｝目录中。由于性能原因，生成和存储这些 tarball 不是构建系统的默认行为。

You can also use the `PREMIRRORS`{.interpreted-text role="term"} variable. For an example, see the variable\'s glossary entry in the Yocto Project Reference Manual.

> 您也可以使用 `PREMIRRORS`｛.explored text role=“term”｝变量。例如，请参阅 Yocto 项目参考手册中变量的词汇表条目。

# Getting Source Files and Suppressing the Build

Another technique you can use to ready yourself for a successive string of build operations, is to pre-fetch all the source files without actually starting a build. This technique lets you work through any download issues and ultimately gathers all the source files into your download directory `structure-build-downloads`{.interpreted-text role="ref"}, which is located with `DL_DIR`{.interpreted-text role="term"}.

> 另一种可以用来为连续的一系列构建操作做好准备的技术是，在不实际启动构建的情况下预取所有源文件。这项技术使您能够解决任何下载问题，并最终将所有源文件收集到下载目录“structure-build-downloads”｛.depredicted text role=“ref”｝中，该目录与“DL_DIR”｛.epredicted text-role=“term”｝位于一起。

Use the following BitBake command form to fetch all the necessary sources without starting the build:

> 使用以下 BitBake 命令表单在不启动构建的情况下获取所有必要的源：

```
$ bitbake target --runall=fetch
```

This variation of the BitBake command guarantees that you have all the sources for that BitBake target should you disconnect from the Internet and want to do the build later offline.

> 如果您断开与 Internet 的连接并希望稍后离线进行构建，BitBake 命令的这种变体可以确保您拥有该 BitBake 目标的所有源。
