---
tip: translate by baidu@2023-06-07 17:10:18
...
---
title: Creating Your Own Distribution
-------------------------------------

When you build an image using the Yocto Project and do not alter any distribution `Metadata`{.interpreted-text role="term"}, you are creating a Poky distribution. If you wish to gain more control over package alternative selections, compile-time options, and other low-level configurations, you can create your own distribution.

> 当您使用 Yocto 项目构建图像并且不更改任何分发 `Metadata`｛.depredicted text role=“term”｝时，您正在创建一个 Poky 分发。如果您希望获得对包替代选择、编译时选项和其他低级配置的更多控制，您可以创建自己的分发版。

To create your own distribution, the basic steps consist of creating your own distribution layer, creating your own distribution configuration file, and then adding any needed code and Metadata to the layer. The following steps provide some more detail:

> 要创建自己的分发，基本步骤包括创建自己的发布层，创建自己的发行配置文件，然后向该层添加任何所需的代码和元数据。以下步骤提供了更多详细信息：

- *Create a layer for your new distro:* Create your distribution layer so that you can keep your Metadata and code for the distribution separate. It is strongly recommended that you create and use your own layer for configuration and code. Using your own layer as compared to just placing configurations in a `local.conf` configuration file makes it easier to reproduce the same build configuration when using multiple build machines. See the \"``dev-manual/layers:creating a general layer using the \`\`bitbake-layers\`\` script``{.interpreted-text role="ref"}\" section for information on how to quickly set up a layer.

> -*为您的新发行版创建一个层：*创建您的发行版层，以便您可以将发行版的元数据和代码分开。强烈建议您为配置和代码创建并使用自己的层。与只将配置放在“local.conf”配置文件中相比，使用自己的层可以更容易地在使用多个构建机器时重现相同的构建配置。有关如何快速设置层的信息，请参阅“``dev manual/layers:create a general layers using the \`\`bitbake layers\`\`script``{.depreted text role=“ref”}\”一节。

- *Create the distribution configuration file:* The distribution configuration file needs to be created in the `conf/distro` directory of your layer. You need to name it using your distribution name (e.g. `mydistro.conf`).

> -*创建分发配置文件：*分发配置文件需要在您所在层的“conf/distro”目录中创建。您需要使用您的分发名称（例如“mydistro.conf”）对其进行命名。

::: note
::: title

Note

> 笔记
> :::

The `DISTRO`{.interpreted-text role="term"} variable in your `local.conf` file determines the name of your distribution.

> `local.conf` 文件中的 `DISTRO`｛.explored text role=“term”｝变量决定了分发的名称。
> :::

You can split out parts of your configuration file into include files and then \"require\" them from within your distribution configuration file. Be sure to place the include files in the `conf/distro/include` directory of your layer. A common example usage of include files would be to separate out the selection of desired version and revisions for individual recipes.

> 您可以将配置文件的一部分拆分为包含文件，然后从分发配置文件中“要求”这些文件。请确保将 include 文件放在层的“conf/disro/include”目录中。include 文件的一个常见示例是将所需版本的选择和单个食谱的修订分开。

Your configuration file needs to set the following required variables:

> 您的配置文件需要设置以下必需的变量：

- `DISTRO_NAME`{.interpreted-text role="term"}
- `DISTRO_VERSION`{.interpreted-text role="term"}

These following variables are optional and you typically set them from the distribution configuration file:

> 以下变量是可选的，通常可以从分发配置文件中进行设置：

- `DISTRO_FEATURES`{.interpreted-text role="term"}
- `DISTRO_EXTRA_RDEPENDS`{.interpreted-text role="term"}
- `DISTRO_EXTRA_RRECOMMENDS`{.interpreted-text role="term"}
- `TCLIBC`{.interpreted-text role="term"}

::: tip
::: title

Tip

> 提示
> :::

If you want to base your distribution configuration file on the very basic configuration from OE-Core, you can use `conf/distro/defaultsetup.conf` as a reference and just include variables that differ as compared to `defaultsetup.conf`. Alternatively, you can create a distribution configuration file from scratch using the `defaultsetup.conf` file or configuration files from another distribution such as Poky as a reference.

> 如果您想将您的分发配置文件建立在 OE Core 的非常基本的配置基础上，您可以使用“conf/distro/defaultsetup.conf”作为参考，并只包含与“defaultsetup.conf“不同的变量。或者，您可以使用“defaultsetup.conf”文件或其他发行版（如 Poky）的配置文件作为参考，从头开始创建发行版配置文件。
> :::

- *Provide miscellaneous variables:* Be sure to define any other variables for which you want to create a default or enforce as part of the distribution configuration. You can include nearly any variable from the `local.conf` file. The variables you use are not limited to the list in the previous bulleted item.

> -*提供杂项变量：*请确保定义要为其创建默认值或强制执行的任何其他变量，作为分发配置的一部分。您几乎可以包含“local.conf”文件中的任何变量。您使用的变量不限于上一个项目符号项中的列表。

- *Point to Your distribution configuration file:* In your `local.conf` file in the `Build Directory`{.interpreted-text role="term"}, set your `DISTRO`{.interpreted-text role="term"} variable to point to your distribution\'s configuration file. For example, if your distribution\'s configuration file is named `mydistro.conf`, then you point to it as follows:

> -*指向您的分发配置文件：*在“构建目录”中的“local.conf”文件中，将您的“DISTRO”变量设置为指向分发的配置文件。例如，如果您的发行版的配置文件名为“mydistro.conf”，那么您可以按如下方式指向它：

```
DISTRO = "mydistro"
```

- *Add more to the layer if necessary:* Use your layer to hold other information needed for the distribution:

> -*如有必要，在图层中添加更多信息：*使用图层保存分发所需的其他信息：

- Add recipes for installing distro-specific configuration files that are not already installed by another recipe. If you have distro-specific configuration files that are included by an existing recipe, you should add an append file (`.bbappend`) for those. For general information and recommendations on how to add recipes to your layer, see the \"`dev-manual/layers:creating your own layer`{.interpreted-text role="ref"}\" and \"`dev-manual/layers:following best practices when creating layers`{.interpreted-text role="ref"}\" sections.

> -添加用于安装其他配方尚未安装的发行版特定配置文件的配方。如果现有配方中包含特定于发行版的配置文件，则应为其添加一个附加文件（`.bappend`）。有关如何将配方添加到层中的一般信息和建议，请参阅\“`dev manual/layers:创建您自己的层`{.depreted text role=“ref”}\”和\“` dev manual-layers:创建层时遵循的最佳实践`{.deverted text rol=“ref”}\”部分。

- Add any image recipes that are specific to your distribution.
- Add a `psplash` append file for a branded splash screen, using the `SPLASH_IMAGES`{.interpreted-text role="term"} variable.

> -使用 `splash_IMAGES`｛.explored text role=“term”｝变量，为品牌启动屏幕添加 `pslash` 附加文件。

- Add any other append files to make custom changes that are specific to individual recipes.

For information on append files, see the \"`dev-manual/layers:appending other layers metadata with your layer`{.interpreted-text role="ref"}\" section.

> 有关附加文件的信息，请参阅\“`dev manual/layers:appending other layers metadata with your layer`｛.depreted text role=“ref”｝\”一节。
