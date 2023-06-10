---
tip: translate by openai@2023-06-07 20:47:56
...
---
title: Yocto Project Profiling and Tracing Manual
-------------------------------------------------

# Introduction

Yocto bundles a number of tracing and profiling tools \-\-- this \'HOWTO\' describes their basic usage and shows by example how to make use of them to examine application and system behavior.

> Yocto 捆绑了许多跟踪和分析工具--本“如何使用”描述了它们的基本用法，并通过示例展示如何利用它们来检查应用程序和系统行为。

The tools presented are for the most part completely open-ended and have quite good and/or extensive documentation of their own which can be used to solve just about any problem you might come across in Linux. Each section that describes a particular tool has links to that tool\'s documentation and website.

> 所提供的工具大多完全开放，并具有非常好的或广泛的文档，可用于解决 Linux 中出现的任何问题。每个描述特定工具的部分都有链接到该工具的文档和网站。

The purpose of this \'HOWTO\' is to present a set of common and generally useful tracing and profiling idioms along with their application (as appropriate) to each tool, in the context of a general-purpose \'drill-down\' methodology that can be applied to solving a large number (90%?) of problems. For help with more advanced usages and problems, please see the documentation and/or websites listed for each tool.

> 本“如何”的目的是介绍一组普遍有用的跟踪和分析习语，并且根据每个工具的适用情况适当地应用它们，构成一种通用的“逐层下钻”方法，用于解决大量（90％？）问题。 如需获得更高级用法和问题的帮助，请参阅每个工具的文档和/或网站。

The final section of this \'HOWTO\' is a collection of real-world examples which we\'ll be continually adding to as we solve more problems using the tools \-\-- feel free to add your own examples to the list!

> 最后一节是这个“HOWTO”的集合，我们将不断添加更多的例子，以便使用这些工具解决更多问题 - 欢迎您把自己的例子添加到列表中！

# General Setup

Most of the tools are available only in \'sdk\' images or in images built after adding \'tools-profile\' to your local.conf. So, in order to be able to access all of the tools described here, please first build and boot an \'sdk\' image e.g. :

> 大多数工具只能在'sdk'镜像中获得，或者在添加'tools-profile'到本地配置文件后构建的镜像中获得。因此，为了能够访问这里描述的所有工具，请先构建和启动一个'sdk'镜像，例如：

```
$ bitbake core-image-sato-sdk
```

or alternatively by adding \'tools-profile\' to the `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} line in your local.conf:

```
EXTRA_IMAGE_FEATURES = "debug-tweaks tools-profile"
```

If you use the \'tools-profile\' method, you don\'t need to build an sdk image -the tracing and profiling tools will be included in non-sdk images as well e.g.:

> 如果你使用'tools-profile'方法，你不需要构建 sdk 图像-跟踪和分析工具也将包含在非 sdk 图像中，例如：

```
$ bitbake core-image-sato
```

::: note
::: title
Note
:::

By default, the Yocto build system strips symbols from the binaries it packages, which makes it difficult to use some of the tools.

> 默认情况下，Yocto 构建系统会从打包的二进制文件中剥离符号，这使得使用一些工具变得困难。

You can prevent that by setting the `INHIBIT_PACKAGE_STRIP`{.interpreted-text role="term"} variable to \"1\" in your `local.conf` when you build the image:

> 你可以在构建镜像时，在你的 local.conf 中将 INHIBIT_PACKAGE_STRIP 变量设置为"1"，以防止此情况发生。

```
INHIBIT_PACKAGE_STRIP = "1"
```

The above setting will noticeably increase the size of your image.

> 以上设置会显著增加您图像的大小。
> :::

If you\'ve already built a stripped image, you can generate debug packages (xxx-dbg) which you can manually install as needed.

> 如果你已经构建了一个精简版的镜像，你可以生成调试包（xxx-dbg），可以根据需要手动安装。

To generate debug info for packages, you can add dbg-pkgs to `EXTRA_IMAGE_FEATURES`{.interpreted-text role="term"} in local.conf. For example:

> 在 local.conf 中添加 EXTRA_IMAGE_FEATURES，可以为包生成调试信息。例如：

```
EXTRA_IMAGE_FEATURES = "debug-tweaks tools-profile dbg-pkgs"
```

Additionally, in order to generate the right type of debuginfo, we also need to set `PACKAGE_DEBUG_SPLIT_STYLE`{.interpreted-text role="term"} in the `local.conf` file:

> 此外，为了生成正确类型的调试信息，我们还需要在 `local.conf` 文件中设置 `PACKAGE_DEBUG_SPLIT_STYLE`。

```
PACKAGE_DEBUG_SPLIT_STYLE = 'debug-file-directory'
```
