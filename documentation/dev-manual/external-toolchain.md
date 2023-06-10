---
tip: translate by openai@2023-06-10 10:48:18
...
---
title: Optionally Using an External Toolchain
---------------------------------------------

You might want to use an external toolchain as part of your development. If this is the case, the fundamental steps you need to accomplish are as follows:

> 你可能需要使用外部工具链作为你的开发的一部分。如果是这种情况，你需要完成的基本步骤如下：

- Understand where the installed toolchain resides. For cases where you need to build the external toolchain, you would need to take separate steps to build and install the toolchain.

> 了解已安装的工具链所在的位置。对于需要构建外部工具链的情况，您需要采取单独的步骤来构建和安装工具链。

- Make sure you add the layer that contains the toolchain to your `bblayers.conf` file through the `BBLAYERS` variable.

> 确保通过 `BBLAYERS` 变量将包含工具链的层添加到 `bblayers.conf` 文件中。

- Set the `EXTERNAL_TOOLCHAIN` variable in your `local.conf` file to the location in which you installed the toolchain.

> 在你的 `local.conf` 文件中设置 `EXTERNAL_TOOLCHAIN` 变量，指向你安装工具链的位置。

The toolchain configuration is very flexible and customizable. It is primarily controlled with the `TCMODE`.

> 配置工具链非常灵活且可定制化。它主要由 `TCMODE` 变量控制。此变量控制从 `源目录` 中的 `meta/conf/distro/include` 目录中包含的 `tcmode-* .inc` 文件。

The default value of `TCMODE` is \"default\", which tells the OpenEmbedded build system to use its internally built toolchain (i.e. `tcmode-default.inc`). However, other patterns are accepted. In particular, \"external-\*\" refers to external toolchains. One example is the Mentor Graphics Sourcery G++ Toolchain. Support for this toolchain resides in the separate `meta-sourcery` layer at [https://github.com/MentorEmbedded/meta-sourcery/](https://github.com/MentorEmbedded/meta-sourcery/). See its `README` file for details about how to use this layer.

> 默认值 `TCMODE` 是"default"，它告诉 OpenEmbedded 构建系统使用其内部构建的工具链(即 `tcmode-default.inc`)。但是，也接受其他模式。特别是"external-*"指的是外部工具链。一个例子是 Mentor Graphics Sourcery G++ Toolchain。此工具链的支持位于独立的 `meta-sourcery` 层，位于 [https://github.com/MentorEmbedded/meta-sourcery/](https://github.com/MentorEmbedded/meta-sourcery/)。有关如何使用此层的详细信息，请参阅其 `README` 文件。

Another example of external toolchain layer is :yocto_[git:%60meta-arm-toolchain](git:%60meta-arm-toolchain) \</meta-arm/tree/meta-arm-toolchain/\>\` supporting GNU toolchains released by ARM.

> 另一个外部工具链层的例子是：yocto_[git:`meta-arm-toolchain`](git:%60meta-arm-toolchain%60)</meta-arm/tree/meta-arm-toolchain/>，它支持 ARM 发布的 GNU 工具链。

You can find further information by reading about the `TCMODE` variable in the Yocto Project Reference Manual\'s variable glossary.

> 你可以通过阅读 Yocto 项目参考手册中的变量词汇表中的 `TCMODE` 变量来获得更多信息。
