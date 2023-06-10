---
tip: translate by openai@2023-06-07 20:47:55
...
---
title: Customizing the Standard SDK
-----------------------------------

This appendix presents customizations you can apply to the standard SDK.

> 这个附录介绍了你可以应用到标准 SDK 上的定制功能。

# Adding Individual Packages to the Standard SDK

When you build a standard SDK using the `bitbake -c populate_sdk`, a default set of packages is included in the resulting SDK. The `TOOLCHAIN_HOST_TASK`{.interpreted-text role="term"} and `TOOLCHAIN_TARGET_TASK`{.interpreted-text role="term"} variables control the set of packages adding to the SDK.

> 当您使用 `bitbake -c populate_sdk` 构建标准 SDK 时，结果 SDK 中将包含一组默认的软件包。`TOOLCHAIN_HOST_TASK`{.interpreted-text role="term"}和 `TOOLCHAIN_TARGET_TASK`{.interpreted-text role="term"}变量控制将要添加到 SDK 中的软件包集合。

If you want to add individual packages to the toolchain that runs on the host, simply add those packages to the `TOOLCHAIN_HOST_TASK`{.interpreted-text role="term"} variable. Similarly, if you want to add packages to the default set that is part of the toolchain that runs on the target, add the packages to the `TOOLCHAIN_TARGET_TASK`{.interpreted-text role="term"} variable.

> 如果你想在运行在主机上的工具链中添加单独的包，只需将这些包添加到 `TOOLCHAIN_HOST_TASK` 变量中即可。 类似地，如果要向工具链的默认集合中添加包，请将包添加到 `TOOLCHAIN_TARGET_TASK` 变量中。

# Adding API Documentation to the Standard SDK

You can include API documentation as well as any other documentation provided by recipes with the standard SDK by adding \"api-documentation\" to the `DISTRO_FEATURES`{.interpreted-text role="term"} variable: DISTRO_FEATURES:append = \" api-documentation\" Setting this variable as shown here causes the OpenEmbedded build system to build the documentation and then include it in the standard SDK.

> 你可以通过将“api-documentation”添加到 `DISTRO_FEATURES` 变量来包括 API 文档以及由食谱提供的任何其他文档：DISTRO_FEATURES：append =“ api-documentation”将此变量设置为如上所示，将导致 OpenEmbedded 构建系统构建文档，然后将其包含在标准 SDK 中。
