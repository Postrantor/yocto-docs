---
tip: translate by baidu@2023-06-07 17:12:17
...
---
title: The Yocto Project Development Tasks Manual
-------------------------------------------------

# Welcome

Welcome to the Yocto Project Development Tasks Manual. This manual provides relevant procedures necessary for developing in the Yocto Project environment (i.e. developing embedded Linux images and user-space applications that run on targeted devices). This manual groups related procedures into higher-level sections. Procedures can consist of high-level steps or low-level steps depending on the topic.

> 欢迎使用 Yocto 项目开发任务手册。本手册提供了在 Yocto 项目环境中进行开发所需的相关程序（即开发在目标设备上运行的嵌入式 Linux 映像和用户空间应用程序）。本手册将相关程序分为较高级别的部分。过程可以由高级步骤或低级步骤组成，具体取决于主题。

This manual provides the following:

> 本手册提供了以下内容：

- Procedures that help you get going with the Yocto Project; for example, procedures that show you how to set up a build host and work with the Yocto Project source repositories.

> -帮助您着手 Yocto 项目的程序；例如，向您展示如何设置生成主机和使用 Yocto 项目源代码存储库的过程。

- Procedures that show you how to submit changes to the Yocto Project. Changes can be improvements, new features, or bug fixes.

> -向您展示如何向 Yocto 项目提交更改的过程。更改可以是改进、新功能或错误修复。

- Procedures related to \"everyday\" tasks you perform while developing images and applications using the Yocto Project, such as creating a new layer, customizing an image, writing a new recipe, and so forth.

> -与使用 Yocto 项目开发图像和应用程序时执行的“日常”任务相关的过程，如创建新层、自定义图像、编写新配方等。

This manual does not provide the following:

> 本手册未提供以下内容：

- Redundant step-by-step instructions: For example, the `/sdk-manual/index`{.interpreted-text role="doc"} manual contains detailed instructions on how to install an SDK, which is used to develop applications for target hardware.

> -多余的分步说明：例如，`/sdk manual/index`{.depreced text role=“doc”}手册包含有关如何安装 sdk 的详细说明，该 sdk 用于为目标硬件开发应用程序。

- Reference or conceptual material: This type of material resides in an appropriate reference manual. As an example, system variables are documented in the `/ref-manual/index`{.interpreted-text role="doc"}.

> -参考材料或概念材料：此类材料存在于适当的参考手册中。例如，系统变量记录在 `/ref manual/index`{.depreted text role=“doc”}中。

- Detailed public information not specific to the Yocto Project: For example, exhaustive information on how to use the Git version control system is better covered with Internet searches and official Git documentation than through the Yocto Project documentation.

> -并非 Yocto 项目特有的详细公共信息：例如，关于如何使用 Git 版本控制系统的详尽信息最好通过互联网搜索和官方 Git 文档来覆盖，而不是通过 Yocto Project 文档。

# Other Information

Because this manual presents information for many different topics, supplemental information is recommended for full comprehension. For introductory information on the Yocto Project, see the :yocto_home:[Yocto Project Website \<\>]{.title-ref}. If you want to build an image with no knowledge of Yocto Project as a way of quickly testing it out, see the `/brief-yoctoprojectqs/index`{.interpreted-text role="doc"} document.

> 由于本手册提供了许多不同主题的信息，因此建议您提供补充信息以便于全面理解。有关 Yocto 项目的介绍性信息，请参阅：Yocto_home:[Yocto Project Website\<\>]｛.title ref｝。如果您想在不了解 Yocto 工程的情况下构建一个图像，作为快速测试它的一种方法，请参阅 `/brief yoctoprojectqs/index`｛.depreted text role=“doc”｝文档。

For a comprehensive list of links and other documentation, see the \"`ref-manual/resources:links and related documentation`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual.

> 有关链接和其他文档的综合列表，请参阅 Yocto 项目参考手册中的\“`ref manual/resources:links and related documentation`｛.depreted text role=”ref“｝\”一节。
