---
tip: translate by openai@2023-06-10 10:53:09
...
---
title: The Yocto Project Development Tasks Manual
-------------------------------------------------

# Welcome

Welcome to the Yocto Project Development Tasks Manual. This manual provides relevant procedures necessary for developing in the Yocto Project environment (i.e. developing embedded Linux images and user-space applications that run on targeted devices). This manual groups related procedures into higher-level sections. Procedures can consist of high-level steps or low-level steps depending on the topic.

> 欢迎来到 Yocto 项目开发任务手册。本手册提供了在 Yocto 项目环境中开发（即开发嵌入式 Linux 镜像和在目标设备上运行的用户空间应用程序）所必需的相关程序。本手册将相关程序分组到较高级别的部分中。根据主题，程序可以由高级步骤或低级步骤组成。

This manual provides the following:

- Procedures that help you get going with the Yocto Project; for example, procedures that show you how to set up a build host and work with the Yocto Project source repositories.

> 提供帮助你开始使用 Yocto 项目的程序；例如，显示如何设置构建主机和使用 Yocto 项目源代码库的程序。

- Procedures that show you how to submit changes to the Yocto Project. Changes can be improvements, new features, or bug fixes.
- Procedures related to \"everyday\" tasks you perform while developing images and applications using the Yocto Project, such as creating a new layer, customizing an image, writing a new recipe, and so forth.

> 测试使用 Yocto Project 开发图像和应用程序时执行的与“日常”任务有关的程序，例如创建新层、自定义图像、编写新食谱等。

This manual does not provide the following:

- Redundant step-by-step instructions: For example, the `/sdk-manual/index`{.interpreted-text role="doc"} manual contains detailed instructions on how to install an SDK, which is used to develop applications for target hardware.

> 重复的逐步指示：例如，`/sdk-manual/index`{.interpreted-text role="doc"} 手册包含了安装 SDK 的详细指示，SDK 用于开发针对目标硬件的应用程序。

- Reference or conceptual material: This type of material resides in an appropriate reference manual. As an example, system variables are documented in the `/ref-manual/index`{.interpreted-text role="doc"}.

> 参考或概念性材料：此类材料存放在适当的参考手册中。例如，系统变量在 `/ref-manual/index` 中有文档说明。

- Detailed public information not specific to the Yocto Project: For example, exhaustive information on how to use the Git version control system is better covered with Internet searches and official Git documentation than through the Yocto Project documentation.

> 详细的公共信息不针对 Yocto 项目：例如，如何使用 Git 版本控制系统的详尽信息更好地通过互联网搜索和官方 Git 文档，而不是通过 Yocto 项目文档。

# Other Information

Because this manual presents information for many different topics, supplemental information is recommended for full comprehension. For introductory information on the Yocto Project, see the :yocto_home:[Yocto Project Website \<\>]{.title-ref}. If you want to build an image with no knowledge of Yocto Project as a way of quickly testing it out, see the `/brief-yoctoprojectqs/index`{.interpreted-text role="doc"} document.

> 因为本手册涵盖了很多不同的主题，建议补充信息以充分理解。要了解 Yocto 项目的入门信息，请参阅:yocto_home:[Yocto 项目网站 \<\>]{.title-ref}。如果您想要构建一个没有 Yocto 项目知识的映像以快速测试它，请参阅 `/brief-yoctoprojectqs/index`{.interpreted-text role="doc"}文档。

For a comprehensive list of links and other documentation, see the \"`ref-manual/resources:links and related documentation`{.interpreted-text role="ref"}\" section in the Yocto Project Reference Manual.

> 对于链接和其他文档的全面列表，请参见 Yocto 项目参考手册中的“ref-manual/resources：链接和相关文档”部分。
