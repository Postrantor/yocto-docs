---
tip: translate by baidu@2023-06-07 17:11:59
...
---
title: Optionally Using an External Toolchain
---------------------------------------------

You might want to use an external toolchain as part of your development. If this is the case, the fundamental steps you need to accomplish are as follows:

> 您可能希望使用外部工具链作为开发的一部分。如果是这种情况，您需要完成的基本步骤如下：

- Understand where the installed toolchain resides. For cases where you need to build the external toolchain, you would need to take separate steps to build and install the toolchain.

> -了解已安装的工具链所在的位置。对于需要构建外部工具链的情况，您需要采取单独的步骤来构建和安装工具链。

- Make sure you add the layer that contains the toolchain to your `bblayers.conf` file through the `BBLAYERS`{.interpreted-text role="term"} variable.

> -请确保通过 `bblayers`｛.explored text role=“term”｝变量将包含工具链的层添加到 `bblayers.conf` 文件中。

- Set the `EXTERNAL_TOOLCHAIN`{.interpreted-text role="term"} variable in your `local.conf` file to the location in which you installed the toolchain.

> -将 `local.conf` 文件中的 `EXTERNAL_TOOLCHAIN`｛.depreted text role=“term”｝变量设置为安装工具链的位置。

The toolchain configuration is very flexible and customizable. It is primarily controlled with the `TCMODE`{.interpreted-text role="term"} variable. This variable controls which `tcmode-*.inc` file to include from the `meta/conf/distro/include` directory within the `Source Directory`{.interpreted-text role="term"}.

> 工具链配置非常灵活且可自定义。它主要由 `TCMODE`｛.explored text role=“term”｝变量控制。此变量控制要从“源目录”{.interplated-text role=“term”}中的“meta/conf/distro/include”目录中包含哪个 `tcmode-*.inc` 文件。

The default value of `TCMODE`{.interpreted-text role="term"} is \"default\", which tells the OpenEmbedded build system to use its internally built toolchain (i.e. `tcmode-default.inc`). However, other patterns are accepted. In particular, \"external-\*\" refers to external toolchains. One example is the Mentor Graphics Sourcery G++ Toolchain. Support for this toolchain resides in the separate `meta-sourcery` layer at [https://github.com/MentorEmbedded/meta-sourcery/](https://github.com/MentorEmbedded/meta-sourcery/). See its `README` file for details about how to use this layer.

> `TCMODE`{.interplated-text role=“term”}的默认值为“default”，它告诉 OpenEmbedded 生成系统使用其内部构建的工具链（即 `TCMODE-default.inc`）。但是，也接受其他模式。特别是，“external-\*\”指的是外部工具链。Mentor Graphics Sourcery G++ 工具链就是一个例子。对该工具链的支持位于 [https://github.com/MentorEmbedded/meta-sourcery/](https://github.com/MentorEmbedded/meta-sourcery/)。有关如何使用该层的详细信息，请参阅其“自述”文件。

Another example of external toolchain layer is :yocto\_[git:%60meta-arm-toolchain](git:%60meta-arm-toolchain) \</meta-arm/tree/meta-arm-toolchain/\>\` supporting GNU toolchains released by ARM.

> 外部工具链层的另一个例子是：yocto\_[git:%60meta arm toolchain]（git:%60mata arm toolchain）\</meta arm/tree/meta arm toolchain/\>\`支持 arm 发布的 GNU 工具链。

You can find further information by reading about the `TCMODE`{.interpreted-text role="term"} variable in the Yocto Project Reference Manual\'s variable glossary.

> 您可以通过阅读 Yocto 项目参考手册的变量词汇表中的 `TCMODE`｛.explored text role=“term”｝变量来找到更多信息。
